---
title: Recursos com suporte disponíveis na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de serviços com suporte pela central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529489"
---
# <a name="supported-features-available-in-azure-security-center"></a>Recursos com suporte disponíveis na central de segurança do Azure

> [!NOTE]
>Alguns recursos estão disponíveis apenas com a camada Standard. Se você ainda não tiver se inscrito na camada Standard da central de segurança, um período de avaliação gratuita estará disponível. Para obter mais informações, consulte a [página de preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

As seções a seguir mostram os recursos da central de segurança que estão disponíveis para suas [plataformas com suporte](security-center-os-coverage.md).

* [Máquinas virtuais/servidores](#vm-server-features)
* [Serviços de PaaS](#paas-services)


## Recursos com suporte para máquina virtual/servidor<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Servidor|Windows|||Linux|||Preços|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**Azure**||**Não Azure**|**Azure**||**Não Azure**||
||**Máquina Virtual**|**Conjunto de dimensionamento de máquinas virtuais**||**Máquina Virtual**|**Conjunto de dimensionamento de máquinas virtuais**|
|[Integração do Microsoft defender ATP](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|w.x.y.|w.x.y.|w.x.y.|Standard|
|[Alertas de detecção de ameaças de análise comportamental de máquina virtual](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|[Alertas de detecção de ameaças com arquivo](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|w.x.y.|w.x.y.|w.x.y.|Standard|
|[Alertas de detecção de ameaças baseados em rede](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|w.x.y.|✔|✔|w.x.y.|Standard|
|[Acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|w.x.y.|w.x.y.|✔|w.x.y.|w.x.y.|Standard|
|[Monitoramento de integridade de arquivo](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[Controles de aplicativo adaptáveis](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|w.x.y.|✔|✔|w.x.y.|✔|Standard|
|[Mapa de rede](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|w.x.y.|✔|✔|w.x.y.|Standard|
|[Proteção de rede adaptável](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|w.x.y.|w.x.y.|✔|w.x.y.|w.x.y.|Standard|
|Controles de rede adaptáveis|✔|✔|w.x.y.|✔|✔|w.x.y.|Standard|
|[Painel de conformidade regulatória & relatórios](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|w.x.y.|w.x.y.|w.x.y.|✔|✔|✔|Standard|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|✔|✔|✔|Gratuito|
|Avaliação de incorretas de segurança|✔|✔|✔|✔|✔|✔|Gratuito|
|[Avaliação do Endpoint Protection](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|w.x.y.|w.x.y.|w.x.y.|Gratuito|
|Avaliação de criptografia de disco|✔|✔|w.x.y.|✔|✔|w.x.y.|Gratuito|
|Avaliação de vulnerabilidade de terceiros|✔|w.x.y.|w.x.y.|✔|w.x.y.|w.x.y.|Gratuito|
|[Avaliação de segurança de rede](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|w.x.y.|✔|✔|w.x.y.|Gratuito|

### Soluções de proteção de ponto de extremidade com suporte<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a central de segurança do Azure para instalar cada solução para você.
 - Que a central de segurança das soluções do Endpoint Protection pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a central de segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação da central de segurança | Descoberta da central de segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, interno ao sistema operacional| Sim |
| Endpoint Protection do System Center (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (veja a observação abaixo) | Por meio da extensão | Sim |
| Trend Micro – todas as versões * | Família do Windows Server  | Não | Sim |
| Symantec v 12.1.1100 +| Família do Windows Server  | Não | Sim |
| McAfee v10 + | Família do Windows Server  | Não | Sim |
| McAfee v10 + | Família de servidores Linux  | Não | Sim **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Sim **\***  |

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas. Ele não é refletido no portal da central de segurança do Azure.

> [!NOTE]
>
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell 3,0 (ou uma versão superior).
> - Há suporte para a detecção da proteção Trend Micro para agentes de segurança profundas.  Não há suporte para agentes do OfficeScan.


## Recursos <a name="paas-services"></a> com suporte de serviços de PaaS

Os recursos de PaaS a seguir têm suporte na central de segurança do Azure:

|Serviço|Recomendações (gratuito)|Detecção de ameaças (padrão)|
|----|----|----|
|SQL|✔| ✔|
|PostgreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|w.x.y.| ✔|
|Armazenamento de blob|✔| ✔|
|Conta de armazenamento|✔| w.x.y.|
|Serviço de aplicativo|✔| ✔|
|Funcionamento|✔| w.x.y.|
|Serviço de Nuvem|✔| w.x.y.|
|Virtual|✔| w.x.y.|
|Redes|✔| w.x.y.|
|NIC|✔| w.x.y.|
|NSG|✔| w.x.y.|
|Scriçõe|✔ * *| ✔|
|Conta do Batch|✔| w.x.y.|
|Conta do Service Fabric|✔| w.x.y.|
|Conta de automação|✔| w.x.y.|
|Load Balancer|✔| w.x.y.|
|Pesquisa|✔| w.x.y.|
|Namespace do barramento de serviço|✔| w.x.y.|
|Análise de fluxo|✔| w.x.y.|
|Namespace do hub de eventos|✔| w.x.y.|
|Aplicativos lógicos|✔| w.x.y.|
|Redis|✔| w.x.y.|
|Data Lake Analytics|✔| w.x.y.|
|Repositório Data Lake|✔| w.x.y.|
|Key Vault|✔| w.x.y.|

\* esses recursos têm suporte atualmente na visualização pública.

as recomendações do \* \* Azure Active Directory (AD do Azure) estão disponíveis somente para assinaturas padrão.

## <a name="next-steps"></a>Próximos passos

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a central de segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a segurança e a conformidade do Azure](https://blogs.msdn.com/b/azuresecurity/).
