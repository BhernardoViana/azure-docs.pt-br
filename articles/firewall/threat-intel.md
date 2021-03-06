---
title: Filtragem baseada em inteligência de ameaças do firewall do Azure
description: A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168663"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtragem baseada em inteligência contra ameaças do firewall do Azure

A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/en-us/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

![Inteligência contra ameaças de firewall](media/threat-intel/firewall-threat.png)

Se você tiver habilitado a filtragem baseada em inteligência contra ameaças, as regras associadas serão processadas antes de qualquer uma das regras de NAT, regras de rede ou regras de aplicativo.

Você pode optar por apenas registrar um alerta quando uma regra é disparada ou pode escolher o modo de alerta e de negação.

Por padrão, a filtragem baseada em inteligência contra ameaças está habilitada no modo de alerta. Você não pode desativar esse recurso ou alterar o modo até que a interface do portal fique disponível em sua região.

![Interface do portal de filtragem baseada em inteligência contra ameaças](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logs

O trecho de log a seguir mostra uma regra disparada:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testando

- **Teste de saída** -alertas de tráfego de saída devem ser uma ocorrência rara, pois isso significa que seu ambiente foi comprometido. Para ajudar a testar os alertas de saída estão funcionando, um FQDN de teste foi criado e dispara um alerta. Use **testmaliciousdomain.eastus.cloudapp.Azure.com** para seus testes de saída.

- **Teste de entrada** -você pode esperar ver alertas sobre o tráfego de entrada se as regras do DNAT estiverem configuradas no firewall. Isso é verdadeiro mesmo se apenas fontes específicas forem permitidas na regra DNAT e o tráfego for negado. O Firewall do Azure não alerta todos os scanners de porta conhecidos; somente em scanners que também são conhecidos por se envolver em atividades mal-intencionadas.

## <a name="next-steps"></a>Próximas etapas

- Consulte [exemplos de log Analytics de firewall do Azure](log-analytics-samples.md)
- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md)
- Examinar o [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)