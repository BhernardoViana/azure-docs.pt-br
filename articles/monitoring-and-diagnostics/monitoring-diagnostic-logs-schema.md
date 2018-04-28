---
title: Serviços e esquemas com suporte para os logs de diagnóstico do Azure | Microsoft Docs
description: Compreenda o esquema de serviços e eventos com suporte para Logs de Diagnóstico do Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: johnkem
ms.openlocfilehash: 91c3f1507bb4fb64d5395917e8e431951f77e72b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Serviços, esquemas e categorias com suporte para os logs de diagnóstico do Azure

Os [logs de diagnóstico de recursos do Azure](monitoring-overview-of-diagnostic-logs.md) são emitidos por seus recursos do Azure que descrevem a operação do recurso. Esses logs são específicos do tipo de recurso. Neste artigo, vamos descrever o conjunto de serviços com suporte e o esquema de evento para os eventos emitidos por cada serviço. Este artigo também inclui uma lista completa das categorias de log disponíveis por tipo de recurso.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Serviços e esquemas com suporte para os logs de diagnóstico de recurso
O esquema para os logs de diagnóstico de recurso varia dependendo do recurso e da categoria do log.   

| Serviço | Esquema e Documentos |
| --- | --- |
| Serviços de análise | Esquema não disponível. |
| Gerenciamento da API | [Logs de Diagnóstico de Gerenciamento de API](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways do Aplicativo |[Log de diagnóstico do Gateway de Aplicativo](../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Logs de diagnóstico do Lote do Azure](../batch/batch-diagnostics.md) |
| Customer Insights | Esquema não disponível. |
| Rede de Distribuição de Conteúdo | Esquema não disponível. |
| CosmosDB | [Registro em log do Azure Cosmos DB](../cosmos-db/logging.md) |
| Data Lake Analytics |[Acessando os logs de diagnóstico do Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Repositório Data Lake |[Acessando os logs de diagnóstico do Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de evento |[Logs de diagnóstico dos Hubs de Eventos do Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| Hub IoT | [Operações do Hub IoT](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Cofre da Chave |[Logs do Cofre da Chave do Azure](../key-vault/key-vault-logging.md) |
| Balanceador de carga |[Log Analytics para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Aplicativos Lógicos |[Esquema de controle personalizado dos Aplicativos Lógicos B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de segurança de rede |[Análise de logs para NSGs (grupos de segurança de rede)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | Esquema não disponível. |
| Serviços de Recuperação | [Modelo de dados para os Backup do Azure](../backup/backup-azure-reports-data-model.md)|
| Search |[Habilitação e uso da análise de tráfego de pesquisa](../search/search-traffic-analytics.md) |
| Gerenciamento do Servidor | Esquema não disponível. |
| Barramento de Serviço |[Logs de diagnóstico do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Banco de dados SQL | [Log de diagnósticos do Banco de Dados SQL do Azure](../sql-database/sql-database-metrics-diag-logging.md) |
| Análise de fluxo |[Logs de diagnóstico do trabalho](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso
|Tipo de recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Logs relacionados ao Gateway ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Transmissões de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Registro de execuções de atividade de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Registro de execuções de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Registro de execuções de gatilho|
|Microsoft.DataLakeAnalytics/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Solicitações|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeStore/accounts|Requests|Logs de solicitação|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Logs do PostgreSQL Server|
|Microsoft.Devices/IotHubs|conexões|conexões|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria de Dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2C|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operações de Identidade do Dispositivo|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operações de Upload de Arquivo|
|Microsoft.Devices/IotHubs|Rotas|Rotas|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2CTwinOperations|Operações de Gêmeos C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas de Gêmeos|
|Microsoft.Devices/IotHubs|JobsOperations|Operações de Trabalhos|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos diretos|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnóstico E2E (versão prévia)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operações do Dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operações de serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logs de Escala Automática|
|Microsoft.KeyVault/vaults|AuditEvent|Logs de Auditoria|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de tempo de execução do fluxo de trabalho|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Network Security Group Event|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificações de proteção contra DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção da VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Registro de Diagnóstico de Gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Registro de Diagnóstico de Túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Registro de Diagnóstico de Rota|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Logs de Diagnóstico P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de Resultados de Integridade de Investigação do Gerenciador de Tráfego|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabela de contadores GWM|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Estatísticas de Replicação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Pontos de Recuperação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taxa de Carregamento de Dados de Replicação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Rotatividade de Dados de Disco Protegido do Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Logs de operação|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estatísticas de Tempo de Execução do Repositório de Consultas|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estatísticas de Espera do Banco de Dados|
|Microsoft.Sql/servers/databases|Tempos limite|Tempos limite|
|Microsoft.Sql/servers/databases|Bloqueios|Bloqueios|
|Microsoft.Sql/servers/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/servers/databases|Audit|Logs de Auditoria|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md)
* [Transmitir logs de diagnóstico de recurso os **Hubs de Eventos**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Alterar as configurações de diagnóstico do recurso usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md)
