---
title: Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs
description: Solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL
services: log-analytics
ms.service: log-analytics
ms.subservice: performance
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 02832ee84e02251239ab4364aac9ad0894c681b9
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884774"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorar o Banco de Dados SQL do Azure usando a Análise de SQL do Azure (Versão Prévia)

![Símbolo da Análise de SQL do Azure](./media/azure-sql/azure-sql-symbol.png)

A Análise de SQL do Azure é uma solução de monitoramento em nuvem avançada para monitorar o desempenho de bancos de dados SQL do Azure, pools elásticos e Instâncias Gerenciadas em escala e em várias assinaturas através de um único painel de vidro. Ela coleta e visualiza importantes métricas de desempenho do Banco de Dados SQL do Azure com inteligência interna para solução de problemas de desempenho.

Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. A solução ajuda a identificar problemas em cada camada da pilha do aplicativo. Ela usa as métricas de Diagnóstico do Azure junto com as exibições do Log Analytics para apresentar dados sobre todos os Bancos de Dados SQL do Azure, pools elásticos e bancos de dados em Instâncias Gerenciadas em um único workspace de Log Analytics. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

Para uma visão geral prática sobre o uso da solução da Análise de SQL do Azure e para cenários de uso típicos, assista o vídeo inserido:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Fontes conectadas

A Análise de SQL do Azure é uma solução de monitoramento somente em nuvem que dá suporte ao streaming de telemetria de diagnóstico para o banco dados SQL do Azure: único, em pool e Instância Gerenciada. Como a solução não usa agentes para se conectar ao serviço Log Analytics, ela não dá suporte ao monitoramento do SQL Server hospedado localmente ou em VMs; veja a tabela de compatibilidade abaixo.

| Fonte Conectada | Com suporte | DESCRIÇÃO |
| --- | --- | --- |
| [Diagnóstico do Azure](../platform/collect-azure-metrics-logs.md) | **Sim** | Os dados de métrica e de log do Azure são enviados ao Log Analytics diretamente pelo Azure. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não  | O Log Analytics não lê os dados pré-existentes de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não  | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não  | Agentes do Linux diretos não são usados pela solução. |
| [Grupo de gerenciamento do System Center Operations Manager](../platform/om-agents.md) | Não  | Uma conexão direta do agente do Operations Manager ao Log Analytics não é necessária. |

## <a name="configuration"></a>Configuração

Realize as etapas a seguir para adicionar a solução Análise de SQL do Azure ao painel do Azure.

1. Adicione a solução Análise de SQL do Azure ao seu workspace do [workspace do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. No portal do Azure, clique em **+ Criar um recurso**, em seguida, procure **análise de SQL do Azure**.  
    ![Monitoramento + Gerenciamento](./media/azure-sql/monitoring-management.png)
3. Selecione **análise de SQL do Azure (Versão prévia)** da lista
4. Na área **Análise de SQL do Azure (versão prévia)**, clique em **Criar**.  
    ![Criar](./media/azure-sql/portal-create.png)
5. Na área **Criar nova solução**, crie uma nova, ou selecione o workspace ao qual deseja adicionar a solução e clique em **Criar**.

    ![adicionar ao workspace](./media/azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Configurar Bancos de Dados SQL do Azure, pools elásticos e Instâncias Gerenciadas para transmitir a telemetria de diagnóstico

Depois que você criou uma solução de Análise de SQL do Azure em seu espaço de trabalho, você precisará **configurar cada** recurso que você deseja monitorar para transmitir sua telemetria de diagnóstico à solução. Siga as instruções detalhadas nesta página:

- Habilite o Diagnóstico do Azure para o Banco de Dados SQL do Azure para [telemetria de diagnóstico de fluxo para a Análise de SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima também fornece instruções sobre como habilitar o suporte para monitorar várias assinaturas do Azure em um único espaço de trabalho da Análise de SQL do Azure como um único painel de controle.

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução ao workspace, o bloco de Análise de SQL do Azure é adicionado ao workspace e aparece na Visão geral. O bloco mostra o número de bancos de dados SQL do Azure, pools elásticos, Instâncias Gerenciadas e bancos de dados em Instâncias gerenciadas dos quais a solução está recebendo a telemetria de diagnóstico.

![Bloco de Análise do SQL Azure](./media/azure-sql/azure-sql-sol-tile.png)

A solução oferece duas exibições separadas – uma para monitorar Bancos de Dados SQL do Azure e pools elásticos e a outra exibição para monitorar a Instância Gerenciada e os bancos de dados em Instâncias Gerenciadas.

Para exibir o painel de monitoramento da Análise de SQL do Azure para Bancos de Dados SQL do Azure e pools elásticos, clique na parte superior do bloco. Para exibir o painel de monitoramento da Análise de SQL do Azure para a Instância Gerenciada e os bancos de dados na Instância Gerenciada, clique na parte inferior do bloco.

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados da Análise de SQL do Azure

O painel inclui a visão geral de todos os bancos de dados monitorados por meio de diferentes perspectivas. Para que as diferentes perspectivas funcionem, é necessário permitir que métricas ou logs adequados em seus recursos SQL sejam transmitidos para o workspace do Azure Log Analytics.

Observe que, se algumas métricas ou logs não forem transmitidos para o Azure Log Analytics, os blocos da solução não serão populados com as informações de monitoramento.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Exibição do Banco de Dados SQL do Azure e do pool elástico

Depois que o bloco da Análise de SQL do Azure para o banco de dados é selecionado, o painel de monitoramento será mostrado.

![Visão geral da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

![Tempos limite da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspectiva nessa exibição fornece resumos sobre a assinatura, o servidor, o pool elástico e o nível de banco de dados. Além disso, cada perspectiva mostrada é específica ao relatório à direita. Selecionar a assinatura, o servidor, o pool ou o banco de dados na lista dá continuidade ao drill down.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>A Instância Gerenciada e os bancos de dados na exibição da Instância Gerenciada

Depois que o bloco da Análise de SQL do Azure para o banco de dados é selecionado, o painel de monitoramento será mostrado.

![Visão geral da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

A seleção da exibição Instância Gerenciada mostra detalhes sobre a utilização da Instância Gerenciada, os bancos de dados que ela contém e a telemetria sobre as consultas executadas na instância.

![Tempos limite da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspectivas

A tabela abaixo descreve as perspectivas compatíveis com duas versões do painel, uma para o Banco de Dados SQL do Azure e os pools elásticos e a outra para a Instância Gerenciada.

| Perspectiva | DESCRIÇÃO | Suporte ao Banco de Dados SQL e aos pools elásticos | Suporte para Instância Gerenciada |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. | SIM | SIM |
| Insights | Fornece uma busca detalhada hierárquica no Intelligent Insights sobre o desempenho. | SIM | SIM |
| Errors | Fornece drill down hierárquico sobre erros do SQL que ocorreram nos bancos de dados. | SIM | SIM |
| Tempos limite | Fornece drill down hierárquico sobre tempos limite do SQL que ocorreram nos bancos de dados. | SIM | Não  |
| Bloqueios | Fornece drill down hierárquico sobre bloqueios do SQL que ocorreram nos bancos de dados. | SIM | Não  |
| Esperas do banco de dados | Fornece drill down hierárquico sobre estatísticas de espera do SQL sobre o nível do banco de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |SIM | SIM |
| Duração da consulta | Fornece drill down hierárquico sobre as estatísticas de execução de consulta como duração da consulta, uso da CPU, uso de E/S dos dados, uso de E/S do log. | SIM | SIM |
| Esperas da consulta | Fornece drill down hierárquico sobre as estatísticas de espera da consulta por categoria de espera. | SIM | SIM |

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligentes

O [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) do Banco de dados SQL do Azure permite que você saiba o que está acontecendo com o desempenho do Banco de Dados SQL do Microsoft Azure. Todos os insights inteligentes coletados podem ser visualizados e acessados por meio da perspectiva de Insights.

![Insights de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Relatórios do pool elástico e do Banco de Dados

Os pools elásticos e os Bancos de Dados SQL têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no horário especificado.

![Banco de dados de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elástico SQL do Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio das perspectivas de Duração da consulta e Espera da consulta, é possível correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para usar a Análise de SQL do Azure, os usuários precisam receber uma permissão mínima da função de leitor no Azure. No entanto, essa função não permite que os usuários vejam o texto de consulta ou realizem quaisquer ações de ajuste automático. As funções mais permissivas no Azure que permitem usar a solução em toda a extensão são Proprietário, Colaborador, Colaborador do banco de dados do SQL ou Colaborador do SQL Server. Também convém considerar a criação de uma função personalizada no portal, com permissões específicas necessárias apenas para usar a Análise de SQL do Azure e sem acesso ao gerenciamento de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criar uma função personalizada no portal

Reconhecendo que algumas organizações impõem controles de permissão estrito no Azure, localize o seguinte script PowerShell, possibilitando a criação de uma função personalizada "Operador de monitoramento da Análise de SQL" no portal do Azure, com, pelo menos, permissões de leitura e gravação necessárias para usar a Análise de SQL do Azure até a extensão máxima.

Substitua "{SubscriptionId}" no script abaixo por sua ID da assinatura do Azure e execute o script conectado como uma função de Proprietário ou Colaborador no Azure.

   ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription {SubscriptionId}
    $role = Get-AzureRmRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzureRmRoleDefinition $role
   ```

Depois de criar a nova função, atribua essa função para cada usuário ao qual você precisa conceder permissões personalizadas para usar a Análise de SQL do Azure.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados na Análise de SQL do Azure baseia-se na [linguagem de programação do Log Analytics](../log-query/get-started-queries.md) para suas consultas personalizadas e relatórios. Leia a descrição dos dados disponíveis coletados do recurso de banco de dados para a consulta personalizada [métricas e logs disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Os alertas automatizados na solução baseiam-se em gravar uma consulta do Log Analytics que dispare um alerta quando uma condição for atendida. Veja abaixo vários exemplos de consultas do Log Analytics após os quais alertas podem ser configurados na solução.

### <a name="creating-alerts-for-azure-sql-database"></a>Criando alertas para o Banco de Dados SQL do Azure

Você pode [criar alertas](../platform/alerts-metric.md) facilmente com os dados provenientes de recursos de Banco de Dados SQL do Azure. Aqui estão algumas consultas de [consultas de logs](../log-query/log-query-overview.md) que podem ser usadas com um alerta do log:

#### <a name="high-cpu-on-azure-sql-database"></a>CPU alta no Banco de Dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam métricas de diagnóstico (opção "Todas as métricas") para a solução.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>CPU alta em pools elásticos do Banco de Dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam métricas de diagnóstico (opção "Todas as métricas") para a solução.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Banco de Dados SQL do Azure em média acima de 95% na última 1 hora

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam métricas de diagnóstico (opção "Todas as métricas") para a solução.
> - Essa consulta exige que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (resultados > 0) da consulta, indicando que a condição existe em alguns bancos de dados. A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.
> - A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.

#### <a name="alert-on-intelligent-insights"></a>Alerta no Intelligent Insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - O pré-requisito para configurar esse alerta é que os bancos de dados monitorados transmitam log de diagnóstico do SQLInsights para a solução.
> - Essa consulta exige que uma regra de alerta seja configurada para ser executada com a mesma frequência que alert_run_interval a fim de evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando houver resultados (resultados > 0) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo a ser verificado se a condição tiver ocorrido em bancos de dados configurados para transmitir log do SQLInsights à solução.
> - Personalize o insights_string para capturar a saída do texto da análise de causa de raiz do Insights. Este é o mesmo texto exibido na interface do usuário da solução, o qual você pode usar dos insights existentes. Como alternativa, você pode usar a consulta a seguir para ver o texto de todos os Insights gerados em sua assinatura. Use a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas no Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Criando alertas para a Instância Gerenciada

#### <a name="managed-instance-storage-is-above-90"></a>O armazenamento da Instância Gerenciada está acima de 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - O pré-requisito de configuração desse alerta é que a Instância Gerenciada monitorada tenha o streaming do log ResourceUsageStats habilitado para a solução.
> - Essa consulta exige que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (resultados > 0) da consulta, indicando que a condição existe na Instância Gerenciada. O resultado é o percentual de consumo de armazenamento na Instância Gerenciada.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>O consumo médio de CPU de Instância Gerenciada está acima de 95% na última 1 hora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - O pré-requisito de configuração desse alerta é que a Instância Gerenciada monitorada tenha o streaming do log ResourceUsageStats habilitado para a solução.
> - Essa consulta exige que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (resultados > 0) da consulta, indicando que a condição existe na Instância Gerenciada. A saída é o consumo médio de porcentagem de utilização da CPU em um período definido na Instância Gerenciada.

### <a name="pricing"></a>Preços

Enquanto a solução for gratuita para usar, aplica-se o consumo de telemetria de diagnóstico acima das unidades gratuitas de ingestão de dados alocados por mês, consulte [Preço do Log Analytics](https://azure.microsoft.com/en-us/pricing/details/monitor). As unidades gratuitas de ingestão de dados fornecidas habilitam o monitoramento gratuito de vários bancos de dados por mês. Observe que os bancos de dados mais ativos, com cargas de trabalho mais pesadas, vão ingerir mais dados em comparação com os bancos de dados ociosos. Você pode monitorar facilmente o consumo de ingestão de dados na solução selecionando o espaço de trabalho do OMS no menu de navegação da Análise de SQL do Azure e, em seguida, selecionando Uso e custos estimados.

## <a name="next-steps"></a>Próximas etapas

- Usar [Pesquisas de Log](../log-query/log-query-overview.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](../learn/tutorial-logs-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.
