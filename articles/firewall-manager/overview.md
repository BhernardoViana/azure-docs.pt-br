---
title: O que é a Versão prévia do Gerenciador de Firewall do Azure?
description: Saiba mais sobre os recursos do Gerenciador de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 12/06/2019
ms.author: victorh
ms.openlocfilehash: cf8e6ca3a532dea29a413b1afdfc684ac8f08f17
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869554"
---
# <a name="what-is-azure-firewall-manager-preview"></a>O que é a Versão prévia do Gerenciador de Firewall do Azure?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A Versão prévia do Gerenciador de Firewall do Azure é um serviço de gerenciamento de segurança que fornece a política de segurança central e o gerenciamento de rotas para parâmetros de segurança baseados em nuvem. Ele funciona com o [Hub de WAN Virtual do Azure](../virtual-wan/virtual-wan-about.md#resources), um recurso gerenciado pela Microsoft que possibilita criar arquiteturas de hub e spoke facilmente. Quando as políticas de segurança e roteamento são associadas a esse hub, ele é chamado de *[hub virtual seguro](secured-virtual-hub.md)* . 

![gerenciador de firewall](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Recursos da Versão prévia do Gerenciador de Firewall do Azure

A Versão prévia do Gerenciador de Firewall do Azure oferece os seguintes recursos:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Implantação e configuração central do Firewall do Azure

Você pode implantar e configurar centralmente várias instâncias do Firewall do Azure que abrangem diferentes regiões e assinaturas do Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Políticas hierárquicas (globais e locais)

Você pode usar a Versão prévia do Gerenciador de Firewall do Azure para gerenciar de um só lugar as políticas de firewall do Azure em vários hubs virtuais seguros. Suas equipes de TI centralizadas podem criar políticas globais de firewall para impor políticas de firewall em toda a organização entre as equipes. As políticas de firewall criadas localmente proporcionam um modelo de autoatendimento do DevOps para oferecer maior agilidade.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integração com a segurança como serviço de terceiros para oferecer segurança avançada

Além do Firewall do Azure, você pode integrar provedores de SECaaS (segurança como serviço) de terceiros para fornecer proteção adicional de rede para suas conexões de Internet de VNet e branch.

- Filtragem de tráfego de VNet para Internet (V2I)

   - Filtre o tráfego de rede virtual de saída com seu provedor de segurança terceirizado preferido.
   - Aproveite a proteção avançada da Internet com reconhecimento de usuário para suas cargas de trabalho na nuvem em execução no Azure.

- Filtragem de tráfego de Branch para Internet (B2I)

   Aproveite a conectividade do Azure e a distribuição global para adicionar facilmente a filtragem de terceiros para cenários de branch para Internet.

Para saber mais sobre provedores de segurança confiáveis, consulte [O que são parceiros de segurança confiáveis do Gerenciador de Firewall do Azure (versão prévia)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gerenciamento de rota centralizado

Encaminhe facilmente o tráfego para o hub seguro para filtragem e registro, sem a necessidade de configurar manualmente as Rotas Definidas pelo Usuário (UDR) nas redes virtuais de spoke. Você pode usar provedores de terceiros para filtrar o tráfego de Branch para Internet (B2I), lado a lado com o Firewall do Azure para Branch para VNet (B2V), VNet para VNet (V2V) e VNet para Internet (V2I). Também pode usar provedores de terceiros para filtrar o tráfego V2I, desde que o Firewall do Azure não seja necessário para B2V ou V2V. 

## <a name="region-availability"></a>Disponibilidade de região

As seguintes regiões têm suporte para a versão prévia pública:

- Oeste da Europa, Norte da Europa, França Central, Sul da França, Sul do Reino Unido, Oeste do Reino Unido
- Leste da Austrália, Austrália Central, Austrália Central 2, Sudeste da Austrália
- Canadá Central
- Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Centro-Sul dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA, Centro-oeste dos EUA

As políticas do Firewall do Azure só podem ser criadas nessas regiões, mas podem ser usadas entre regiões. Por exemplo, você pode criar uma política no oeste dos EUA e usá-la no leste dos EUA. 

## <a name="known-issues"></a>Problemas conhecidos

A Versão prévia do Gerenciador de Firewall do Azure tem os seguintes problemas conhecidos:

|Problema  |DESCRIÇÃO  |Redução  |
|---------|---------|---------|
|Não há suporte para VNets centrais criadas manualmente|Atualmente, o Gerenciador de Firewall do Azure oferece suporte a redes criadas com hubs virtuais. O uso de sua própria VNet de hub criada manualmente ainda não é compatível.|Por enquanto, use o Gerenciador de Firewall do Azure com redes de hub e spoke criadas com Hubs Virtuais.<br>Conserto em andamento.
|Limitações da filtragem de terceiros|A filtragem de tráfego V2I com provedores de terceiros não tem suporte no Firewall do Azure B2V e V2V.|Em investigação.|
|Divisão de tráfego sem suporte no momento|A divisão de tráfego do Office 365 e PaaS público do Azure não tem suporte no momento. Dessa forma, selecionar um provedor de terceiros para V2I ou B2I também envia todo o tráfego de PaaS público do Azure e do Office 365 por meio do serviço do parceiro.|Atualmente investigando a divisão de tráfego no hub.
|Um hub por região|Você não pode ter mais de um hub por região|Crie várias WANs virtuais em uma região.|
|As políticas básicas devem estar na mesma região que a política local|Crie todas as políticas locais na mesma região que a política básica. Você ainda pode aplicar uma política que foi criada em uma região em um hub seguro de outra região.|Em investigação.|
|A comunicação entre os hubs não está funcionando com o hub virtual seguro|A comunicação do hub virtual seguro com o hub virtual seguro ainda não tem suporte.|Em investigação.|
|Todos os Hubs Virtuais Seguros que compartilham a mesma WAN virtual devem estar no mesmo grupo de recursos.|Esse comportamento está alinhado aos Hubs da WAN Virtual hoje.|Crie várias WANs Virtuais para permitir que os Hubs Virtuais Seguros sejam criados em diferentes grupos de recursos.|

## <a name="next-steps"></a>Próximas etapas

- Examine a [Visão geral sobre a implantação da Versão prévia do Gerenciador de Firewall do Azure](deployment-overview.md)
- Saiba mais sobre [Hubs virtuais seguros](secured-virtual-hub.md).