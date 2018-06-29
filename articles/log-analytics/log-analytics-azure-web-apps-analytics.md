---
title: Exibir dados analíticos de aplicativos Web do Azure | Microsoft Docs
description: Você pode usar a solução Análise de Aplicativos Web do Azure para obter informações sobre seus aplicativos Web do Azure coletando diferentes métricas entre todos os recursos do aplicativo Web do Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: magoedte
ms.openlocfilehash: 5426c9c5727d76d401c00b6e7338688b8f064ad0
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021230"
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Exibir dados analíticos de métricas entre todos os recursos do aplicativo Web do Azure

![Símbolo dos Aplicativos Web](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  

> [!NOTE]
> A solução de Análise de Aplicativos Web do Azure foi preterida.  Os clientes que já têm instalada a solução podem continuar a usá-la, mas a Análise de Aplicativos Web do Azure não pode ser adicionada a nenhum novo espaço de trabalho.  Para monitorar seu aplicativo web, é recomendável que você use o [Application Insights](../application-insights/app-insights-overview.md). 

A solução Análise de Aplicativos Web do Azure (Versão Prévia) fornece informações sobre seus [Aplicativos Web do Azure](../app-service/app-service-web-overview.md) coletando diferentes métricas entre todos os recursos do aplicativo Web do Azure. Com a solução, você pode analisar e pesquisar dados de métricas de recursos de aplicativos Web.

Usando a solução, você pode exibir:

- Os principais aplicativos Web com o tempo de resposta mais alto
- O número de solicitações em seus aplicativos Web, incluindo solicitações bem-sucedidas e com falha
- Os principais aplicativos Web com o tráfego de entrada e de saída mais alto
- Os principais planos de serviço com alta utilização de CPU e Memória
- Operações de log de atividades de aplicativos Web do Azure

## <a name="connected-sources"></a>Fontes conectadas

Ao contrário da maioria das outras soluções do Log Analytics, os dados não são coletados para os aplicativos Web do Azure por agentes. Todos os dados usados pela solução vêm diretamente do Azure.

| Fonte Conectada | Com suporte | DESCRIÇÃO |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agent.md) | Não  | A solução não coleta informações de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não  | A solução não coleta informações de agentes do Linux. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não  | A solução não coleta informações de agentes em um grupo de gerenciamento de SCOM conectado. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não  | A solução não coleta informações do armazenamento do Azure. |

## <a name="prerequisites"></a>pré-requisitos

- Para acessar informações de métrica de recurso do aplicativo Web do Azure, você deve ter uma assinatura do Azure.

## <a name="configuration"></a>Configuração

Execute as seguintes etapas para configurar a solução Análise de Aplicativos Web do Azure para seus espaços de trabalho.

1. [Habilite o registro em log de métricas de recursos do Azure para o Log Analytics usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

A solução Análise de Aplicativos Web do Azure coleta dois conjuntos de métricas do Azure:

- Métricas de aplicativos Web do Azure
  - Conjunto de Trabalho de Memória Média
  - Tempo Médio de Resposta
  - Bytes recebido s/enviados
  - Tempo de CPU
  - Requests
  - Conjunto de Trabalho de Memória
  - Httpxxx
- Métricas do Plano do Serviço de Aplicativo
  - Bytes recebido s/enviados
  - Percentual de CPU
  - Tamanho da fila do disco
  - Tamanho da Fila de Http
  - Porcentagem de Memória

As métricas do Plano do Serviço de Aplicativo serão coletadas apenas se você estiver usando um plano de serviço dedicado. Isso não se aplica aos Planos do Serviço de Aplicativo gratuitos ou compartilhados.

Depois de configurar a solução, os dados devem começar a fluir para o espaço de trabalho dentro de 15 minutos.

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução Análise de Aplicativos Web do Azure ao espaço de trabalho, o bloco **Análise de Aplicativos Web do Azure** é adicionado ao painel Visão Geral. Esse bloco exibe uma contagem do número de aplicativos Web do Azure aos quais a solução tem acesso na sua assinatura do Azure.

![Bloco Análise de Aplicativos Web do Azure](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Exibir informações do Análise de Aplicativos Web do Azure

Clique no bloco **Análise de Aplicativos Web do Azure** para abrir o painel **Análise de Aplicativos Web do Azure**. O painel inclui as folhas na tabela a seguir. Cada folha lista até dez itens que correspondem aos critérios da folha para o intervalo de tempo e o escopo especificados. É possível executar uma pesquisa de logs que retorna todos os registros clicando em **Ver todos** na parte inferior da folha ou clicando no cabeçalho de folha.


| Coluna | DESCRIÇÃO |
| --- | --- |
| Aplicativos Web do Azure |   |
| Tendências de Solicitações de Aplicativos Web | Mostra um gráfico de linhas de tendência de solicitações de Aplicativos Web para o intervalo de datas selecionado e mostra uma lista das dez principais solicitações da Web. Clique no gráfico de linhas para executar uma pesquisa de logs para <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "Requests" or MetricName startswith_cs "Http") &#124; summarize AggregatedValue = avg(Average) by MetricName, bin(TimeGenerated, 1h)</code> <br>Clique em um item de solicitação da Web para executar uma pesquisa de logs para a tendência de métrica da solicitação da Web daquela solicitação. |
| Tempo de Resposta de Aplicativos Web | Mostra um gráfico de linhas do tempo de resposta de aplicativos Web para o intervalo de datas que você selecionou. Também mostra uma lista dos dez principais tempos de resposta de aplicativos Web. Clique no gráfico para executar uma pesquisa de logs para <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and MetricName == "AverageResponseTime" &#124; summarize AggregatedValue = avg(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Clique em um aplicativo Web para executar uma pesquisa de logs que retorna os tempos de resposta do aplicativo Web. |
| Tráfego de aplicativos Web | Mostra um gráfico de linhas para o tráfego de aplicativos Web, em MB, e lista o principal tráfego de aplicativos Web. Clique no gráfico para executar uma pesquisa de logs para <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "BytesSent" or MetricName == "BytesReceived") &#124; summarize AggregatedValue = sum(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Ela mostra todos os aplicativos Web com o tráfego para o último minuto. Clique em um aplicativo Web para executar uma pesquisa de logs mostrando os bytes recebidos e enviados para o aplicativo Web. |
| Planos do Serviço de Aplicativo do Azure |   |
| Planos de Serviço de Aplicativo com utilização de CPU &gt; 80% | Mostra o número total de Planos do Serviço de Aplicativo que têm a utilização da CPU acima de 80% e lista os dez principais Planos do Serviço de Aplicativo por utilização da CPU. Clique na área total para executar uma pesquisa de logs para <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "CpuPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Ela mostra uma lista de seus Planos do Serviço de Aplicativo e sua utilização da CPU média. Clique em um Plano do Serviço de Aplicativo para executar uma pesquisa de logs mostrando a utilização média da CPU. |
| Planos de Serviço de Aplicativo com utilização de memória &gt; 80% | Mostra o número total de Planos do Serviço de Aplicativo que têm a utilização da memória acima de 80% e lista os dez principais Planos do Serviço de Aplicativo por utilização da memória. Clique na área total para executar uma pesquisa de logs para <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "MemoryPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Ela mostra uma lista de seus Planos do Serviço de Aplicativo e sua utilização da memória média. Clique em um Plano do Serviço de Aplicativo para executar uma pesquisa de logs mostrando a utilização média da memória. |
| Logs de Atividades de Aplicativos Web do Azure |   |
| Auditoria de Atividades de Aplicativos Web do Azure | Mostra o número total de aplicativos Web com [logs de atividade](log-analytics-activity.md) e lista as dez principais operações do log de atividades. Clique na área total para executar uma pesquisa de logs para <code>AzureActivity #124; where ResourceProvider == "Azure Web Sites" #124; summarize AggregatedValue = count() by OperationName</code><br> Ela mostra uma lista das operações do log de atividades. Clique em uma operação do log de atividades para executar uma pesquisa de logs que lista os registros da operação. |



### <a name="azure-web-apps"></a>Aplicativos Web do Azure 

No painel, você pode fazer o drill down para obter mais informações sobre as métricas de aplicativos Web. Esse primeiro conjunto de folhas mostra a tendência das solicitações dos aplicativos Web, o número de erros (por exemplo, HTTP404), tráfego e tempo médio de resposta ao longo do tempo. Ele também mostra uma análise dessas métricas para diferentes aplicativos Web.

![Folhas de aplicativos Web do Azure](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

O principal motivo para a exibição desses dados é para que você possa identificar um aplicativo Web com tempo de resposta alto e investigar para descobrir a causa raiz. Um limite também é aplicado para ajudá-lo a identificar mais facilmente os com problemas.

- Aplicativos Web mostrados em vermelho têm tempo de resposta maior do que 1 segundo.
- Aplicativos Web mostrados em laranja têm um tempo de resposta superior a 0,7 segundo e menor de 1 segundo.
- Aplicativos Web mostrados em verde têm um tempo de resposta menor que 0,7 segundo.

Na imagem de exemplo de pesquisa de logs a seguir, você pode ver que o aplicativo Web *anugup3* teve um tempo de resposta muito maior do que os outros aplicativos Web.

![exemplo de pesquisa de logs](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Planos de serviço de aplicativo

Se estiver usando Planos de Serviço dedicados, você também poderá coletar métricas para seus Planos do Serviço de Aplicativo. Nessa exibição, você vê os Planos do Serviço de Aplicativo com alta utilização de CPU ou memória (&gt; 80%). Ela também mostra os principais Serviços de Aplicativos com alta utilização de CPU ou memória. De maneira semelhante, um limite é aplicado para ajudá-lo a identificar mais facilmente os com problemas.

- Planos do Serviço de Aplicativo mostrados em vermelho têm uma utilização de CPU/memória maior do que 80%.
- Planos do Serviço de Aplicativo mostrados em laranja têm uma utilização de CPU/memória maior do que 60% e menor do que 80%.
- Planos do Serviço de Aplicativo mostrados em verde têm uma utilização de CPU/memória menor do que 60%.

![Folhas de Planos do Serviço de Aplicativo do Azure](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Pesquisas de logs de aplicativos Web do Azure

A **Lista de consultas de pesquisas de aplicativos Web do Azure populares** mostra todos os logs de atividades relacionados para aplicativos Web, o que fornece informações sobre as operações que foram realizadas nos recursos de aplicativos móveis. Ela também lista todas as operações relacionadas e o número de vezes que ocorreram.

Usando qualquer uma das consultas de pesquisa de logs como ponto de partida, você pode criar facilmente um alerta. Por exemplo, você talvez queira criar um alerta quando o tempo de resposta médio de uma métrica for maior que a cada segundo.

## <a name="next-steps"></a>Próximas etapas

- Criar um [alerta](log-analytics-alerts-creating.md) para uma métrica específica.
- Use a [Pesquisa de Logs](log-analytics-log-searches.md) para exibir informações detalhadas de seus logs de atividade.
