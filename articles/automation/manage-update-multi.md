---
title: Gerenciar atualizações para várias máquinas virtuais do Azure
description: Este artigo descreve como gerenciar atualizações de máquinas virtuais do Azure.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 66d50c94f2aad15e0d4a1b7500e8a4aeb45b1742
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214246"
---
# <a name="manage-updates-for-multiple-machines"></a>Gerenciar atualizações de vários computadores

É possível usar a solução Gerenciamento de Atualizações para gerenciar atualizações e patches para as máquinas virtuais do Windows e Linux. A partir da sua conta da [Automação do Azure](automation-offering-get-started.md), é possível:

- Integrar máquinas virtuais
- Avaliar o status das atualizações disponíveis
- Agendar a instalação de atualizações necessárias
- Examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito em todas as máquinas virtuais nas quais o Gerenciamento de Atualizações está habilitado

## <a name="prerequisites"></a>pré-requisitos

Para usar o Gerenciamento de Atualizações, você precisa de:

- Uma execução de Automação do Azure como conta. Para saber como criar uma, consulte [Introdução à Automação do Azure](automation-offering-get-started.md).
- Uma máquina virtual ou um computador com um dos sistemas operacionais com suporte instalado.

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

O Gerenciamento de Atualizações dá suporte aos seguintes sistemas operacionais:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior     |Windows PowerShell 4.0 ou posterior é necessário. ([Baixar WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1é recomendado para maior confiabilidade. ([Baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 12.04 LTS, 14.04 LTS e 16.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

> [!NOTE]
> Para prevenir que atualizações sejam aplicadas fora de uma janela de manutenção no Ubuntu, reconfigure o pacote de atualização automática para desabilitar as atualizações automáticas. Para obter mais informações, consulte o [tópico de Atualizações automáticas no Guia do servidor Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Os agentes do Linux devem ter acesso a um repositório de atualização.

Essa solução não dá suporte para um Agente do OMS (Operations Management Suite) para Linux configurado para gerar relatórios em vários espaços de trabalho do Azure Log Analytics.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Habilitar o Gerenciamento de Atualizações para máquinas virtuais do Azure

No portal do Azure, abra a conta de Automação e selecione **Gerenciamento de Atualizações**.

Selecione **Adicionar VM do Azure**.

![Adicione tab Azure VM](./media/manage-update-multi/update-onboard-vm.png)

Selecione uma máquina virtual a ser carregada. 

Em **Habilitar Gerenciamento de Atualizações**, selecione **Habilitar** para integrar a máquina virtual.

![Caixa de diálogo Habilitar Gerenciamento de Atualizações](./media/manage-update-multi/update-enable.png)

Quando a integração é concluída, o Gerenciamento de Atualizações é habilitado para a máquina virtual.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Habilitar o Gerenciamento de Atualizações para computadores e máquinas virtuais que não são Azure

Para saber como habilitar o Gerenciamento de Atualizações para computadores e máquinas virtuais do Windows que não são Azure, consulte [Conectar computadores Windows ao serviço Log Analytics no Azure](../log-analytics/log-analytics-windows-agent.md).

Para saber como habilitar o Gerenciamento de Atualizações para máquinas virtuais e computadores Linux que não são Azure, consulte [Conectar computadores Linux ao Log Analytics](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Exibir computadores conectados à sua conta de Automação

Após habilitar o Gerenciamento de Atualizações para as máquinas, você poderá exibir as informações da máquina, selecionando **Computadores**. É possível exibir as informações sobre *nome do computador*, *status de conformidade*, *ambiente*, *tipo de SO*, *atualizações críticas e de segurança instaladas*, *outras atualizações instaladas* e *preparação para atualização do agente* dos computadores.

  ![Guia Exibir computadores](./media/manage-update-multi/update-computers-tab.png)

Computadores que foram habilitados recentemente para o Gerenciamento de Atualizações podem ainda não terem sido avaliados. O status do estado de conformidade para esses computadores é **Não avaliado**. Aqui, é apresentada uma lista de possíveis valores para o estado de conformidade:

- **Compatível**: computadores com todas as atualizações críticas ou de segurança.

- **Não compatível**: computadores que não têm pelo menos uma atualização crítica ou de segurança.

- **Não avaliado**: os dados da avaliação de atualização não foram recebidos do computador dentro do período de tempo esperado. Para computadores Linux, o intervalo de tempo esperado é nas últimas 3 horas. Para computadores Windows, o período de tempo esperado é nas últimas 12 horas.

Para exibir o status do agente, selecione o link na coluna **PREPARAÇÃO PARA ATUALIZAÇÃO DO AGENTE**. Selecionar essa opção abre o painel **Hybrid Worker** e exibe o status do Hybrid Worker. A imagem a seguir mostra um exemplo de um agente que não foi conectado ao Gerenciamento de Atualizações por um longo período de tempo:

![Guia Exibir computadores](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Exibir uma avaliação de atualização

Depois que o Gerenciamento de Atualizações for habilitado, o painel **Gerenciamento de Atualizações** será aberto. Você pode ver uma lista de atualizações ausentes na guia **Atualizações ausentes**.

## <a name="collect-data"></a>Coletar dados

Os agentes instalados em máquinas virtuais e computadores coletam dados sobre atualizações. Os agentes enviam os dados para o Gerenciamento de Atualizações do Azure.

### <a name="supported-agents"></a>Agentes com suporte

A tabela a seguir descreve as fontes conectadas às quais essa solução dá suporte:

| Fonte conectada | Com suporte | DESCRIÇÃO |
| --- | --- | --- |
| Agentes do Windows |sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação das atualizações necessárias. |
| Agentes do Linux |sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes para Linux e, em seguida, inicia a instalação das atualizações necessárias nas distribuições com suporte. |
| Grupo de gerenciamento do Operations Manager |sim |O Gerenciamento de Atualizações coleta informações sobre atualizações do sistema de agentes em um grupo de gerenciamento conectado. |
| Conta de Armazenamento do Azure |Não  |O Armazenamento do Microsoft Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência de coleta

Um exame é executado duas vezes por dia para cada computador Windows gerenciado. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status for alterado, uma verificação de conformidade será iniciada. Uma exame é executado a cada 3 horas para cada computador Linux gerenciado.

Pode demorar entre 30 minutos e 6 horas para o painel exibir os dados atualizados dos computadores gerenciados.

## <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que alinhe-se com a agenda de liberação e período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Para agendar uma nova implantação de atualização para uma ou mais máquinas virtuais, em **Gerenciamento de Atualizações**, selecione **Agendar implantação de atualizações**.

No painel **Nova implantação de atualização**, especifique as seguintes informações:

- **Nome**: insira um nome exclusivo para identificar a implantação de atualizações.
- **Sistema operacional**: selecione **Windows** ou **Linux**.
- **Computadores a atualizar**: selecione as máquinas virtuais que você deseja atualizar. A preparação do computador é mostrada no **PREPARAÇÃO para atualização do agente** coluna. É possível ver o estado de integridade do computador antes de agendar a implantação de atualização.

  ![Painel da nova implantação de atualizações](./media/manage-update-multi/update-select-computers.png)

- **Classificação da atualização**: selecione os tipos de software para incluir na implantação de atualização. Para obter uma descrição dos tipos de classificação, consulte [Classificações de atualização](automation-update-management.md#update-classifications). Os tipos de classificação são:
  - Atualizações críticas
  - Atualizações de segurança
  - Pacotes cumulativos de atualização
  - Feature packs
  - Service packs
  - Atualizações de definição
  - Ferramentas
  - Atualizações

- **Atualizações a serem excluídas**: Selecionar essa opção abre a página **Excluir**. Insira os artigos da Base de Dados ou nomes de pacotes a serem excluídos.

- **Configurações de agenda**: você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual. Também é possível especificar uma hora diferente.

   Você também pode especificar se a implantação ocorre uma única vez ou em um agendamento recorrente. Para configurar um agendamento recorrente, em **Recorrente**, selecione **Recorrência**.

   ![Caixa de diálogo Configurações de agendamento](./media/manage-update-multi/update-set-schedule.png)
- **Janela de manutenção (minutos)**: especifique o período de tempo em que deseja que a implantação de atualização ocorra. Essa configuração ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

Ao terminar de configurar o agendamento, selecione o botão **Criar** para retornar ao painel de status. A tabela **Agendado** mostra o agendamento de implantação que você criou.

> [!WARNING]
> Para atualizações que exigem um reinício, a máquina virtual será reiniciada automaticamente.

## <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** em **Gerenciamento de Atualizações**.

Se a implantação estiver em execução no momento, seu status será **Em andamento**. Depois que a implementação for concluída com êxito, o status será alterado para **Êxito**.

Se uma ou mais atualizações falharem na implantação, o status será **Falhou parcialmente**.

![Status da implantação da atualização](./media/manage-update-multi/update-view-results.png)

Para ver o painel de uma implantação de atualização, selecione a implantação concluída.

O painel **Resultados da atualização** mostra o número total de atualizações e os resultados da implantação para a máquina virtual. A tabela à direita fornece uma análise detalhada de cada atualização e os resultados da instalação. Os resultados de instalação podem ser um dos seguintes valores:

- **Nenhuma tentativa**: a atualização não foi instalada pois não havia tempo suficiente disponível com base na janela de manutenção definida.
- **Êxito**: a atualização foi bem-sucedida.
- **Falha**: a atualização falhou.

Para ver todas as entradas de log que a implantação criou, selecione **Todos os logs**.

Para ver o fluxo de trabalho do runbook que gerencia a implantação de atualização na máquina virtual de destino, selecione o bloco de saída.

Para ver informações detalhadas sobre quaisquer erros da implantação, selecione **Erros**.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Gerenciamento de Atualizações, incluindo logs, saída e erros, consulte [Solução Gerenciamento de Atualizações no Azure](../operations-management-suite/oms-solution-update-management.md).
