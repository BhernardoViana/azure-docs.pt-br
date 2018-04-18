---
title: Ações de webhook para alertas do log nos Alertas do Azure | Microsoft Docs
description: Este artigo descreve como uma regra de alerta do log utilizando o Log Analytics ou Application Insights efetuará push de dados como o webhook HTTP e fornece detalhes das diferentes personalizações possíveis.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/2/2018
ms.author: vinagara
ms.openlocfilehash: cd289d506cbe22e683392256cce14211a5db0729
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de webhook para regras de alerta do log
Quando um alerta [é criado no Azure ](monitor-alerts-unified-usage.md), você tem a opção de [configurar usando grupos de ações](monitoring-action-groups.md) para executar uma ou mais ações.  Este artigo descreve as diferentes ações do webhook que estão disponíveis e os detalhes sobre a configuração do webhook baseado em JSON personalizado.


## <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem invocar um processo externo por meio de uma única solicitação HTTP POST.  O serviço que está sendo chamado deve dar suporte a webhooks e determinar como ele usa as cargas recebidas.   Exemplos de como usar um webhook em resposta a um alerta de envio de mensagem no [Slack](http://slack.com) ou de criação de um incidente em [PagerDuty](http://pagerduty.com/).  

As ações de webhook exigem as propriedades indicadas na tabela a seguir:

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| URL de Webhook |A URL do webhook. |
| Carga JSON personalizada |Carga personalizada para enviar com o webhook quando esta opção for escolhida durante a criação do alerta. Detalhes disponíveis em [Gerenciar alertas usando Alertas do Azure](monitor-alerts-unified-usage.md) |

> [!NOTE]
> Botão Testar o Webhook junto com a opção *Incluir conteúdo JSON personalizado para webhook* para Alerta do Log disparará chamada fictícia para testar a URL do webhook. Não contém dados reais e representativos do esquema JSON usado para Alertas do Log. 

Webhooks incluem uma URL e uma carga formatada em JSON, que são os dados enviados para um serviço externo.  Por padrão, a carga inclui os valores na tabela a seguir: você pode optar por substituir essa carga por uma personalizada de sua preferência.  Nesse caso, você pode usar as variáveis da tabela para cada um dos parâmetros para incluir seus valores na carga personalizada.


| Parâmetro | Variável | DESCRIÇÃO |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome da regra de alerta. |
| Severity |#severity |Severidade definida para o alerta do log disparado. |
| AlertThresholdOperator |#thresholdoperator |O operador de limite da regra de alerta.  *Greater than (Maior que)* ou *Less than (Menor que)*. |
| AlertThresholdValue |#thresholdvalue |O valor de limite para a regra de alerta. |
| LinkToSearchResults |#linktosearchresults |Vincular a pesquisa de log do Log Analytics que retorna os registros da consulta que criou o alerta. |
| ResultCount |#searchresultcount |Número de registros nos resultados da pesquisa. |
| Hora de término do intervalo de pesquisa |#searchintervalendtimeutc |Hora de término da consulta no formato UTC. |
| Intervalo de pesquisa |#searchinterval |A janela de tempo para a regra de alerta. |
| Hora de início do intervalo de pesquisa |#searchintervalstarttimeutc |Hora de início da consulta no formato UTC. 
| SearchQuery |#searchquery |A consulta da pesquisa de log usada pela regra de alerta. |
| SearchResults |"IncludeSearchResults": true|Registros retornados pela consulta como uma Tabela JSON, limitada aos primeiros 1.000 registros; se "IncludeSearchResults":true for adicionado na definição personalizada do webhook JSON como uma propriedade de nível superior. |
| WorkspaceID |#workspaceid |ID do seu espaço de trabalho de Log Analytics. |
| ID do aplicativo |#applicationid |ID do seu aplicativo Application Insight. |
| ID da assinatura |#subscriptionid |ID da sua assinatura do Azure usado com o Application Insights. 


Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *text*.  O serviço chamado por esse webhook seria esperaria receber esse parâmetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Essa carga de exemplo seria resolvida como algo semelhante ao mostrado a seguir quando enviado para o webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir resultados de pesquisa em um conteúdo personalizado, assegure-se de que **IncudeSearchResults** está definido como uma propriedade de nível superior no conteúdo JSON. 

## <a name="sample-payloads"></a>Cargas de exemplo
Essa seção mostra o conteúdo de exemplo para webhook para Alertas do Log, inclusive quando o conteúdo for padrão e quando for personalizado.

> [!NOTE]
> Para garantir a compatibilidade com versões anteriores, o conteúdo padrão do webhook para alertas usando o Azure Log Analytics é o mesmo que do [Gerenciamento de Alertas do Log Analytics](../log-analytics/log-analytics-alerts-creating.md). Mas para alertas do log usando o [Application Insights](../application-insights/app-insights-analytics.md), o conteúdo do webhook é baseado no esquema do Grupo de Ações.

### <a name="standard-webhook-for-log-alerts"></a>Webhook padrão para Alertas do Log 
Ambos exemplos declararam uma carga fictícia com apenas duas colunas e duas linhas.

#### <a name="log-alert-for-azure-log-analytics"></a>Alerta do Log para Azure Log-Analytics
A seguir, é apresentado uma carga de amostra para uma ação padrão do webhook *sem a opção Json personalizado* sendo utilizada para alertas com base no Log Analytics.

    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
    }
    


#### <a name="log-alert-for-azure-application-insights"></a>Alerta do Log para Azure Application Insights
A seguir, é apresentado uma carga de amostra para um padrão do webhook *sem a opção Json personalizado* quando utilizado para alertas do log com base no Application Insights.
    

    {
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error"
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
    }

> [!NOTE]
> Os alertas de registro para o Application Insights estão atualmente em versão prévia pública - a funcionalidade e a experiência do usuário estão sujeitas a alterações.

#### <a name="log-alert-with-custom-json-payload"></a>Alerta do Log com conteúdo JSON personalizado
Por exemplo, para criar uma carga personalizada que inclui apenas o nome do alerta e os resultados da pesquisa, você poderia usar o seguinte: 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

A seguir, é apresentado um conteúdo de amostra para uma ação do webhook personalizado para qualquer alerta do log.
    

    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }




## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [Alertas de log nos Alertas do Azure](monitor-alerts-unified-log.md)
- Criar e gerenciar [grupos de ações no Azure](monitoring-action-groups.md)
- Saiba mais sobre o [Application Insights](../application-insights/app-insights-analytics.md)
- Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md). 