---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159265"
---
As SKUs herdadas (antigas) do gateway de VPN são:

* Basic
* Standard
* HighPerformance

O gateway de VPN não usa a SKU do gateway UltraPerformance. Para saber mais sobre a SKU do UltraPerformance, veja a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Ao trabalhar com os SKUs herdados, considere o seguinte:

* Se quiser usar um tipo PolicyBased VPN, você deverá usar a SKU Básica. VPNs PolicyBased (anteriormente chamadas de Roteamento Estático) não têm suporte em qualquer outro tipo de SKU.
* BGP não tem suporte na SKU Básica.
* Configurações de coexistência de ExpressRoute-Gateway de VPN não têm suporte na SKU Básica.
* As conexões de Gateway de VPN S2S ativa-ativa só podem ser configuradas na SKU HighPerformance.