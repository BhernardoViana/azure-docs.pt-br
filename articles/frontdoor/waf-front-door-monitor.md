---
title: Firewall do aplicativo web do Azure de monitoramento e registro em log
description: Saiba o firewall do aplicativo web (WAF) com FrontDoor monitoramento e registro em log
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478977"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Firewall do aplicativo web do Azure de monitoramento e registro em log 

Monitoramento de WAF (firewall) de aplicativos web do Azure e registro em log são fornecidos por meio de registro em log e integração com o Azure Monitor e o Azure Monitor logs.

## <a name="azure-monitor"></a>Azure Monitor

WAF com FrontDoor log é integrado com [do Azure Monitor](../azure-monitor/overview.md). O Azure Monitor permite controlar informações de diagnóstico, incluindo logs e alertas de WAF. Você pode configurar o monitoramento de WAF no recurso no portal na frente do **diagnóstico** guia ou por meio do Azure Monitor de serviço diretamente.

No portal do Azure, vá para o tipo de recurso de porta da frente. Partir **Monitoring**/**métricas** guia à esquerda, você pode adicionar **WebApplicationFirewallRequestCount** para controlar o número de solicitações que correspondam às regras de WAF. Filtros personalizados podem ser criados com base em tipos de ação e nomes de regra.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Logs e diagnóstico

O WAF com a porta da frente fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado aos Logs de diagnóstico do Azure e os alertas são registrados em um formato json. Esses logs podem ser integrados aos [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra todas as solicitações são encaminhadas para o back-ends do cliente. FrontdoorWebApplicationFirewallLog registra qualquer solicitação que corresponde a uma regra WAF.

A consulta de exemplo a seguir obtém os logs de WAF em solicitações bloqueadas:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

A consulta de exemplo a seguir obtém entradas AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [frente](front-door-overview.md).

