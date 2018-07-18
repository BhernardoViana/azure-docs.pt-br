---
title: Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs
description: A solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 722a10e853f6d61bb5349e92754954e3bb199225
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorar o Banco de Dados SQL do Azure usando a Análise do Azure SQL (Visualização) no Log Analytics

![Símbolo da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

A solução Análise de SQL do Azure do Azure Log Analytics coleta e visualiza métricas de desempenho importantes do SQL Azure. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. E você pode monitorar o Banco de Dados SQL e métricas de pool elástico em várias assinaturas do Azure e pools elásticos e visualizá-los. A solução também ajuda a identificar problemas em cada camada da pilha do aplicativo.  Ele usa [métricas de Diagnóstico do Azure](log-analytics-azure-storage.md) junto com modos de exibição de Log Analytics para apresentar dados sobre todos os bancos de dados do Azure SQL e pools elásticos em um único espaço de trabalho de Log Analytics.

Atualmente, essa solução de visualização dá suporte a até 5.000 Bancos de Dados do Azure SQL e 150.000 Pools Elásticos por espaço de trabalho.

A solução Análise de SQL do Azure, assim como outras disponíveis para o Log Analytics, ajuda você a monitorar e receber notificações sobre a integridade dos recursos do Azure – neste caso, o Banco de Dados SQL do Azure. O Banco de Dados SQL do Microsoft Azure é um serviço de banco de dados relacional escalonável que fornece recursos semelhantes aos recursos familiares do SQL Server para aplicativos em execução na nuvem do Azure. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

Para uma visão geral prática sobre o uso da solução da Análise de SQL do Azure e para cenários de uso típicos, assista o vídeo inserido:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Fontes conectadas

A solução Análise de SQL do Azure não usa agentes para se conectar ao serviço Log Analytics.

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suporte | DESCRIÇÃO |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agent.md) | Não  | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não  | Agentes do Linux diretos não são usados pela solução. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não  | Uma conexão direta do agente do SCOM ao Log Analytics não é usada pela solução. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não  | O Log Analytics não lê os dados pré-existentes de uma conta de armazenamento. |
| [Diagnóstico do Azure](log-analytics-azure-storage.md) | sim | Os dados de métrica e de log do Azure são enviados ao Log Analytics diretamente pelo Azure. |

## <a name="prerequisites"></a>pré-requisitos

- Uma assinatura do Azure. Se não tiver uma, você poderá criá-la [grátis](https://azure.microsoft.com/free/).
- Um espaço de trabalho do Log Analytics. Você pode usar um existente ou pode [criar um novo](log-analytics-quick-create-workspace.md) para começar a usar essa solução.
- Habilite o Diagnóstico do Azure para os bancos de dados do Azure SQL e pools elásticos e [configure-os para enviar os dados para o Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

## <a name="configuration"></a>Configuração

Realize as etapas a seguir para adicionar a solução Análise de SQL do Azure ao seu espaço de trabalho.

1. Adicione a solução de análise do SQL do Azure do [marketplace do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).
2. No Portal do Azure, clique em **Criar um recurso** > **Monitoramento + Gerenciamento**.  
    ![Monitoramento + Gerenciamento](./media/log-analytics-azure-sql/monitoring-management.png)
3. Na lista **Monitoramento + Gerenciamento**, clique em **Ver todos**.
4. Na lista **Recomendado**, clique em **Mais** e, na nova lista, localize **Análise de SQL do Azure (versão prévia)** e selecione essa opção.  
    ![Solução de Análise do Azure SQL](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. Na área **Análise de SQL do Azure (versão prévia)**, clique em **Criar**.  
    ![Criar](./media/log-analytics-azure-sql/portal-create.png)
6. Na área **Criar nova solução**, selecione o espaço de trabalho ao qual deseja adicionar a solução e clique em **Criar**.  
    ![adicionar ao espaço de trabalho](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Para configurar várias assinaturas do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Forneça a ID de recurso do espaço de trabalho como um parâmetro ao executar o script para enviar dados de diagnóstico de recursos em uma assinatura do Azure a um espaço de trabalho em outra assinatura do Azure.

**Exemplo**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução ao espaço de trabalho, o bloco de Análise do Azure SQL é adicionado ao espaço de trabalho e aparece na Visão geral. O bloco mostra o número de bancos de dados do Azure SQL e pools elásticos do Azure SQL aos quais a solução está conectada.

![Bloco de Análise do SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados da Análise de SQL do Azure

Clique no bloco **Análise de SQL do Azure** para abrir o painel da Análise de SQL do Azure. O painel inclui a visão geral de todos os bancos de dados monitorados por meio de diferentes perspectivas. Para que as diferentes perspectivas funcionem, é necessário permitir que métricas ou logs adequados em seus recursos SQL sejam transmitidos para o espaço de trabalho do Azure Log Analytics.

![Visão geral da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

![Tempos limite da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Cada perspectiva fornece resumos sobre a assinatura, o servidor, o pool elástico e o nível de banco de dados. Além disso, cada perspectiva mostrada é específica ao relatório à direita. Selecionar a assinatura, o servidor, o pool ou o banco de dados na lista dá continuidade ao drill down.

| Perspectiva | DESCRIÇÃO |
| --- | --- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. O drill down fornece um resumo das métricas DTU e GB. |
| Insights | Fornece o drill down hierárquico para Insights inteligentes. Saiba mais sobre insights inteligentes. |
| Errors | Fornece drill down hierárquico sobre erros do SQL que ocorreram nos bancos de dados. |
| Tempos limite | Fornece drill down hierárquico sobre tempos limite do SQL que ocorreram nos bancos de dados. |
| Bloqueios | Fornece drill down hierárquico sobre bloqueios do SQL que ocorreram nos bancos de dados. |
| Esperas do banco de dados | Fornece drill down hierárquico sobre estatísticas de espera do SQL sobre o nível do banco de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |
| Duração da consulta | Fornece drill down hierárquico sobre as estatísticas de execução de consulta como duração da consulta, uso da CPU, uso de E/S dos dados, uso de E/S do log. |
| Esperas da consulta | Fornece drill down hierárquico sobre as estatísticas de espera da consulta por categoria de espera. |

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligentes

O [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) do Banco de dados SQL do Azure permite que você saiba o que está acontecendo com o desempenho do banco de dados. Todos os insights inteligentes coletados podem ser visualizados e acessados por meio da perspectiva de Insights.

![Insights de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Relatórios do pool elástico e do banco de dados

Os pools elásticos e os bancos de dados têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no horário especificado.

![Banco de dados de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool Elástico de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio das perspectivas de Duração da consulta e Espera da consulta, é possível correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

Você pode [criar alertas](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) facilmente com os dados provenientes de recursos de Banco de Dados SQL do Azure. Estas são algumas das consultas de [pesquisa de logs](log-analytics-log-searches.md) úteis que você pode usar com um alerta do log:



*DTU alta no Banco de Dados SQL do Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Alta DTU no pool elástico do Banco de Dados SQL do Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Próximas etapas

- Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](log-analytics-alerts.md) quando ocorrerem eventos específicos do Azure SQL.
