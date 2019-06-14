---
title: Preparar a alteração de formato dos logs de diagnóstico do Azure Monitor
description: Os Logs de Diagnóstico do Azure serão movidos para usar blobs de acréscimo em 1º de novembro de 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ab5fba6bbbf6ade83c7699edec937ba02b222939
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237652"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Preparar a alteração de formato dos logs de diagnóstico do Azure Monitor arquivados em uma conta de armazenamento

> [!WARNING]
> Se você estiver enviando [métricas ou logs de diagnóstico de recurso do Azure para uma conta de armazenamento usando as configurações de diagnóstico de recurso](./../../azure-monitor/platform/archive-diagnostic-logs.md) ou [logs de atividades para uma conta de armazenamento usando perfis de log](./../../azure-monitor/platform/archive-activity-log.md), o formato dos dados na conta de armazenamento será alterado para Linhas JSON em 1º de novembro de 2018. As instruções abaixo descrevem o impacto e como atualizar suas ferramentas para manipular o novo formato. 
>
> 

## <a name="what-is-changing"></a>O que está mudando

O Azure Monitor oferece um recurso que permite que você envie dados de diagnóstico de recursos e dados de log de atividade em uma conta de armazenamento do Azure, o namespace de Hubs de eventos, ou em um espaço de trabalho do Log Analytics no Azure Monitor. Para resolver um problema de desempenho do sistema, em **1º de novembro de 2018 às 24h (meia-noite) UTC**, o formato dos dados de log enviados para o armazenamento de blobs será alterado. Caso tenha ferramentas que leem dados fora do armazenamento de blobs, você precisará atualizá-las para que elas reconheçam o novo formato de dados.

* Na quinta-feira, 1º de novembro de 2018 às 24h (meia-noite) UTC, o formato de blob será alterado para [Linhas JSON](http://jsonlines.org/). Isso significa que cada registro será delimitado por uma nova linha, sem nenhuma matriz de registros externa e sem vírgulas entre os registros JSON.
* O formato de blob é alterado para todas as configurações de diagnóstico em todas as assinaturas ao mesmo tempo. O primeiro arquivo PT1H.json emitido para 1º de novembro usará esse novo formato. Os nomes de blob e de contêiner permanecem os mesmos.
* A definição de uma configuração de diagnóstico entre hoje e 1º de novembro continua emitindo os dados no formato atual até 1º de novembro.
* Essa alteração ocorrerá ao mesmo tempo em todas as regiões de nuvem pública. A alteração ainda não ocorrerá nas nuvens do Azure China, Azure Alemanha ou Azure Governamental.
* Essa alteração afeta os seguintes tipos de dados:
  * [Logs de diagnóstico de recurso do Azure](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([veja a lista de recursos aqui](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [Métricas de recursos do Azure exportadas pelas configurações de diagnóstico](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [Dados de Log de atividades do Azure exportados pelos perfis de log](./../../azure-monitor/platform/archive-activity-log.md)
* Essa alteração não afeta:
  * Logs de fluxo de rede
  * Logs de serviço do Azure ainda não disponibilizados por meio do Azure Monitor (por exemplo, logs de diagnóstico do Serviço de Aplicativo do Azure, logs de análise de armazenamento)
  * Roteamento de logs de diagnóstico e logs de atividades do Azure para outros destinos (Hubs de Eventos, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Como ver se você foi afetado

Você só é afetado por essa alteração se:
1. Está enviando dados de log para uma conta de armazenamento do Azure usando uma configuração de diagnóstico de recurso; e
2. Tem ferramentas que dependem da estrutura JSON desses logs no armazenamento.
 
Para identificar se você tem configurações de diagnóstico de recurso que estão enviando dados para uma conta de armazenamento do Azure, navegue para a seção **Monitor** do portal, clique em **Configurações de Diagnóstico** e identifique os recursos que têm o **Status de Diagnóstico** definido como **Habilitado**:

![Folha Configurações de Diagnóstico do Azure Monitor](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Se o Status de Diagnóstico estiver definido como habilitado, você terá uma configuração de diagnóstico ativa nesse recurso. Clique no recurso para ver se há alguma configuração de diagnóstico que está enviando dados para uma conta de armazenamento:

![Conta de armazenamento habilitada](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Se você tiver recursos que estão enviando dados para uma conta de armazenamento usando essas configurações de diagnóstico de recurso, o formato dos dados nessa conta de armazenamento será afetado por essa alteração. A menos que você tenha ferramentas personalizadas que operem fora dessas contas de armazenamento, a alteração de formato não afetará você.

### <a name="details-of-the-format-change"></a>Detalhes da alteração de formato

O formato atual do arquivo PT1H.json no armazenamento de blobs do Azure usa uma matriz de registros JSON. Esta é uma amostra de um arquivo de log do KeyVault agora:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

O novo formato usa [Linhas JSON](http://jsonlines.org/), em que cada evento é uma linha, e o caractere de nova linha indica um novo evento. Esta será a aparência da amostra acima no arquivo PT1H.json após a alteração:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Esse novo formato permite que o Azure Monitor efetue push de arquivos de log usando [blobs de acréscimo](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), que são mais eficientes para o acréscimo contínuo de novos dados de evento.

## <a name="how-to-update"></a>Como atualizar

Você só precisará fazer atualizações se tiver ferramentas personalizadas que ingerem esses arquivos de log para processamento adicional. Se você estiver usando uma ferramenta externa de SIEM ou de análise de logs, recomendamos o [uso de hubs de eventos para a ingestão desses dados](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). A integração aos hubs de eventos é mais fácil em termos de processamento de logs de muitos serviços e de indicação do local em determinado log.

As ferramentas personalizadas devem ser atualizadas para manipular o formato atual e o formato Linhas JSON descrito acima. Isso garantirá que, quando os dados começarem a ser exibidos no novo formato, as ferramentas não sejam interrompidas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [arquivar logs de diagnóstico de recurso em uma conta de armazenamento](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Saiba mais sobre como [arquivar dados de log de atividades em uma conta de armazenamento](./../../azure-monitor/platform/archive-activity-log.md)

