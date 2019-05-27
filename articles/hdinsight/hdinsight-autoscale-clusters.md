---
title: Dimensionar automaticamente os clusters de HDInsight do Azure (visualização)
description: Use o recurso de dimensionamento automático do HDInsight para dimensionar automaticamente os clusters
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000097"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Dimensionar automaticamente os clusters de HDInsight do Azure (visualização)

> [!Important]
> O recurso de dimensionamento automático funciona somente para clusters de Spark, Hive e MapReduce criados após 8 de maio de 2019. 

Recurso de dimensionamento automático de cluster do HDInsight do Azure dimensiona automaticamente o número de nós de trabalho em um cluster de cima para baixo. Outros tipos de nós no cluster não podem ser dimensionados no momento.  Durante a criação de um novo cluster HDInsight, um número mínimo e um número máximo de nós de trabalho podem ser definidos. Dimensionamento automático, em seguida, monitora os requisitos de recursos da carga de análise e dimensiona o número de nós de trabalho para cima ou para baixo. Não há nenhum custo adicional para esse recurso.

## <a name="cluster-compatibility"></a>Compatibilidade de cluster

A tabela a seguir descreve os tipos de cluster e versões que são compatíveis com o recurso de dimensionamento automático.

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sem ESP | Sim | sim | Não | Não | Não | Não | Não |
| HDInsight 4.0 sem ESP | Sim | sim | Não | Não | Não | Não | Não |
| HDInsight 3.6 com ESP | Sim | sim | Não | Não | Não | Não | Não |
| HDInsight 3.6 com ESP | Sim | sim | Não | Não | Não | Não | Não |

## <a name="how-it-works"></a>Como funciona

Você pode escolher dimensionamento com base em carga ou com base no agendamento de dimensionamento para seu cluster HDInsight. Dimensionamento com base em carga altera o número de nós no cluster, dentro de um intervalo que você definir, para garantir que a utilização da CPU ideal e minimizar os custos em execução.

Alterações de dimensionamento com base em agendamento o número de nós no cluster com base nas condições que entrarão em vigor em horários específicos. Essas condições dimensionar o cluster para um número desejado de nós.

### <a name="metrics-monitoring"></a>Monitoramento de métricas

O dimensionamento automático monitora o cluster continuamente e coleta as seguintes métricas:

* **Total de CPU pendente**: o número total de núcleos necessários para iniciar a execução de todos os contêineres pendentes.
* **Total de memória pendente**: o total de memória (em MB) necessária para iniciar a execução de todos os contêineres pendentes.
* **Total de CPU livre**: a soma de todos os núcleos não utilizados em nós de trabalho ativos.
* **Total de memória livre**: A soma de memória não usada (em MB) em nós de trabalho ativos.
* **Memória usada por nó**: A carga em um nó de trabalho. Um nó de trabalho no qual 10 GB de memória são usados é considerado como estando sob uma carga maior que um nó de trabalho com 2 GB de memória usada.
* **Número de aplicativos mestres por nó**: O número de contêineres de aplicativo mestre (AM) em execução em um nó de trabalho. Um nó de trabalho que está hospedando dois contêineres de AM, é considerado mais importante do que um nó de trabalho que está hospedando o zero contêineres AM.

As métricas acima são verificadas a cada 60 segundos. Dimensionamento automático toma decisões de ampliação e redução com base nessas métricas.

### <a name="load-based-cluster-scale-up"></a>Escalar verticalmente com base em carga de cluster

Quando as condições a seguir são detectadas, o dimensionamento automático emitirá uma solicitação de aumento de escala:

* Total pendente da CPU é maior que o total de CPU gratuita por mais de 3 minutos.
* Total de memória pendentes é maior que a memória livre total por mais de 3 minutos.

O serviço HDInsight calcula quantos novos nós de trabalho são necessários para atender aos requisitos de memória e CPU atual e, em seguida, emite uma solicitação de aumento de escala para adicionar o número necessário de nós.

### <a name="load-based-cluster-scale-down"></a>Cluster baseado em carga redução de escala

Quando as condições a seguir são detectadas, o dimensionamento automático emitirá uma solicitação de redução de escala:

* O total de CPU pendente é menor que o total de CPU livre por mais de 10 minutos.
* O total de memória pendente é menor que o total de memória livre por mais de 10 minutos.

O dimensionamento automático com base no número de contêineres de AM por nó e os requisitos de memória e CPU atual, emite uma solicitação para remover um determinado número de nós. O serviço também detecta que nós são candidatas à remoção com base na execução de trabalho atual. A operação de redução decommissions primeiro os nós e, em seguida, remove do cluster.

## <a name="get-started"></a>Introdução

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Criar um cluster com dimensionamento automático baseado em carga

Para habilitar o recurso de dimensionamento automático com o dimensionamento com base em carga, conclua as etapas a seguir como parte do processo de criação de cluster normal:

1. Selecione **Personalizado (tamanho, configurações, aplicativos)** em vez de **Criação rápida**.
1. Na **personalizado** etapa 5 (**tamanho do Cluster**), verifique o **dimensionamento automático de nó do trabalhador** caixa de seleção.
1. Selecione a opção **com base em carga** sob **tipo de dimensionamento automático**.
1. Insira os valores desejados para as seguintes propriedades:  

    * **Número inicial de nós de trabalho**.  
    * O número **mínimo** de nós de trabalho.  
    * O número **máximo** de nós de trabalho.  

    ![Habilitar a opção de dimensionamento automático baseado em carga de nó de trabalho](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Esse valor define o tamanho inicial do cluster quando ele é criado. O número mínimo de nós de trabalho deve ser maior que zero.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Criar um cluster com dimensionamento automático baseado em agendamento

Para habilitar o recurso de dimensionamento automático com o dimensionamento com base em agendamento, conclua as etapas a seguir como parte do processo de criação de cluster normal:

1. Selecione **Personalizado (tamanho, configurações, aplicativos)** em vez de **Criação rápida**.
1. Na **personalizado** etapa 5 (**tamanho do Cluster**), verifique o **dimensionamento automático de nó do trabalhador** caixa de seleção.
1. Insira o **número de nós de trabalho**, que controla o limite para escalar verticalmente o cluster.
1. Selecione a opção **baseado em agendamento** sob **tipo de dimensionamento automático**.
1. Clique em **configurar** para abrir o **configuração de dimensionamento automático** janela.
1. Selecione seu fuso horário e, em seguida, clique em **+ Adicionar condição**
1. Selecione os dias da semana em que a nova condição deve ser aplicada.
1. Edite a hora em que a condição deve levar em vigor e o número de nós de cluster deve ser dimensionado.
1. Adicione mais condições, se necessário.

    ![Habilitar a opção de dimensionamento automático baseado em agendamento do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

O número de nós deve ser entre 1 e o número de nós de trabalho que você inseriu antes de adicionar condições.

### <a name="final-creation-steps"></a>Etapas de criação final

Com base em carga tanto com base no agendamento de dimensionamento, selecione o tipo VM para nós de trabalho clicando **velikost Pracovního uzlu** e **tamanho do nó de cabeçalho**. Depois de escolher o tipo VM para cada tipo de nó, você pode ver o intervalo de custo estimado para o cluster inteiro. Ajuste os tipos VM de acordo com seu orçamento.

![Habilitar a opção de dimensionamento automático baseado em agendamento do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

Sua assinatura tem uma cota de capacidade para cada região. O número total de núcleos de seus nós de cabeçalho combinado com o número máximo de nós de trabalho não pode exceder a cota de capacidade. No entanto, essa cota é um limite flexível; você sempre pode criar um tíquete de suporte para aumentá-lo facilmente.

> [!Note]  
> Se você exceder o limite de cota de núcleo totais, você receberá uma mensagem de erro dizendo 'o nó máximo excedido os núcleos disponíveis nessa região, escolha outra região ou entre em contato com o suporte para aumentar a cota.'

Para obter mais informações sobre a criação de clusters HDInsight usando o portal do Azure, confira [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

#### <a name="load-based-autoscaling"></a>Dimensionamento automático baseado em carga

Você pode criar um cluster HDInsight com o dimensionamento automático de carga com base em um modelo do Azure Resource Manager, adicionando um `autoscale` nó o `computeProfile`  >  `workernode` seção com as propriedades `minInstanceCount` e `maxInstanceCount` como como mostrado no trecho de json abaixo.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Para obter mais informações sobre como criar cluster com modelos do Resource Manager, confira [Criar clusters do Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Dimensionamento automático baseado em agendamento

Você pode criar um cluster HDInsight com dimensionamento automático baseado em agendamento de um modelo do Azure Resource Manager, adicionando um `autoscale` nó o `computeProfile`  >  `workernode` seção. O `autoscale` nó contém um `recurrence` que tem um `timezone` e `schedule` que descreve quando a alteração ocorrerá.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Habilitar e desabilitar o dimensionamento automático para um cluster em execução

Para habilitar o dimensionamento automático em um cluster em execução, selecione **tamanho do Cluster** sob **configurações**. Em seguida, clique em **habilitar o dimensionamento automático**. Selecione o tipo de dimensionamento automático que você deseja e insira as opções para dimensionar com base em carga ou baseado em agendamento. Por fim, clique em **Salvar**.

![Habilitar a opção de dimensionamento automático baseado em agendamento do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>Práticas recomendadas

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Escolhendo o dimensionamento com base em carga ou baseadas em agenda

Considere os seguintes fatores antes de tomar uma decisão sobre qual modo deve escolher:

* Variação de carga: a carga do cluster seguem um padrão consistente em momentos específicos, em dias específicos. Caso contrário, a carga com base em agendamento é uma opção melhor.
* Requisitos de SLA: Dimensionamento do dimensionamento automático é reativo, em vez de previsão. Haverá um atraso suficiente entre quando o carregamento começa a aumentar e quando o cluster deve estar em seu tamanho de destino? Se há requisitos de SLA estritos e a carga é um padrão conhecido fixo, 'com base em agendamento' é uma opção melhor.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerar a latência de dimensionamento para cima ou reduzir verticalmente operações

Pode levar 10 a 20 minutos para concluir uma operação de dimensionamento. Ao configurar uma agenda personalizada, planeje esse atraso. Por exemplo, se você precisar que o tamanho do cluster seja 20 às 9 horas, defina o gatilho de agendamento cedo como 8H30 para que a operação de dimensionamento tiver sido concluído por 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Preparação para reduzir verticalmente

Durante o processo de dimensionamento de clusters, o dimensionamento automático será desativar os nós para atender ao tamanho de destino. Se estiver executando tarefas em nós, o dimensionamento automático aguardará até que as tarefas são concluídas. Uma vez que cada nó de trabalho também serve a uma função no HDFS, os dados temporários serão deslocados para os nós restantes. Portanto, verifique se há espaço suficiente em nós restantes para hospedar todos os dados temporários. 

Os trabalhos em execução continuarão a executar e concluir. Os trabalhos pendentes irá esperar para ser agendada como prioridade normal com menos nós de trabalho disponíveis.

## <a name="monitoring"></a>Monitoramento

### <a name="cluster-status"></a>Status do cluster

O status do cluster listado no portal do Azure pode ajudá-lo a monitorar as atividades de dimensionamento automático.

![Habilitar a opção de dimensionamento automático baseado em carga de nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todas as mensagens de status do cluster que você pode ver são explicadas na lista abaixo.

| Status do cluster | Explicação |
|---|---|
| Em execução | O cluster está funcionando normalmente. Todas as atividades de dimensionamento automático anteriores foram concluídas com êxito. |
| Atualizando  | A configuração de dimensionamento automático do cluster está sendo atualizada.  |
| Configuração do HDInsight  | Um cluster de escalar verticalmente ou reduzir verticalmente a operação está em andamento.  |
| Erro de atualização  | HDInsight problemas encontrados durante a atualização de configuração de dimensionamento automático. Os clientes podem escolher repetir a atualização ou desabilitar o dimensionamento automático.  |
| Erro  | Algo está errado com o cluster e não é utilizável. Excluir este cluster e criar um novo.  |

Para exibir o número atual de nós no cluster, vá para o **tamanho do Cluster** do gráfico na **visão geral** página para seu cluster, ou clique em **tamanho do Cluster** sob  **Configurações**.

### <a name="operation-history"></a>Histórico da operação

Você pode exibir o histórico de ampliação e redução de escala do cluster como parte das métricas de cluster. Você também pode listar todas as ações de dimensionamento ao longo do último dia, semana ou outro período de tempo.

Selecione **métricas** sob **monitoramento**. Em seguida, clique em **adicionar métrica** e **número de operadores ativos** do **métrica** caixa suspensa. Clique no botão no canto superior direito para alterar o intervalo de tempo.

![Habilitar a opção de dimensionamento automático baseado em agendamento do nó de trabalho](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Próximas etapas

* Leia sobre as práticas recomendadas para dimensionar os clusters manualmente em [Práticas recomendadas de dimensionamento](hdinsight-scaling-best-practices.md)
