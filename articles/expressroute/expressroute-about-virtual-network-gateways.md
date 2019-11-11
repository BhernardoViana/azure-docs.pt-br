---
title: Sobre gateways de rede virtual do ExpressRoute – Azure | Microsoft Docs
description: Saiba mais sobre os gateways de rede virtual para ExpressRoute. Este artigo inclui informações sobre tipos e SKUs de gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: f12f92294a9c30ddedea3c433ff65de4a635fd4d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889648"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Gateway de rede virtual e FastPath do ExpressRoute
Para conectar sua rede virtual do Azure e sua rede local por meio do ExpressRoute, você deve primeiro criar um gateway de rede virtual. Um gateway de rede virtual tem duas finalidades: rotas de IP do Exchange entre as redes e rotear o tráfego de rede. Este artigo explica os tipos de gateway, as SKUs de gateway e o desempenho estimado por SKU. Este artigo também explica o ExpressRoute [FastPath](#fastpath), um recurso que permite que o tráfego de rede da sua rede local ignore o gateway de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Quando você cria um gateway de rede virtual, precisa especificar várias configurações. Uma das configurações necessárias, '-GatewayType', especifica se o gateway é usado para tráfego de VPN ou ExpressRoute. Os dois tipos de gateway são:

* **Vpn** - Para enviar tráfego criptografado pela Internet pública, use o tipo de gateway 'Vpn'. Isso também é chamado de gateway de VPN. As conexões Site a Site, Ponto a Site e VNet a VNet usam um gateway VPN.

* **ExpressRoute** - Para enviar tráfego em uma conexão privada, use o tipo de gateway 'ExpressRoute'. Isso também é chamado de gateway ExpressRoute e é o tipo de gateway usado na configuração do ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter um gateway de rede virtual que usa - GatewayType Vpn, e outro que usa -GatewayType ExpressRoute.

## <a name="gwsku"></a>SKUs do Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se você quiser atualizar seu gateway para um SKU de gateway mais potente, na maioria dos casos, você pode usar o cmdlet ' Resize-AzVirtualNetworkGateway ' do PowerShell. Isso funcionará em atualizações para as SKUs Standard e HighPerformance. No entanto, para atualizar para a SKU UltraPerformance, você precisará recriar o gateway. Recriar um gateway incorre em tempo de inatividade.

### <a name="aggthroughput"></a>Desempenhos estimados por SKU de gateway
A tabela a seguir mostra os tipos de gateway e os desempenhos estimados. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> O desempenho do aplicativo depende de vários fatores, como a latência de ponta a ponta e o número de fluxos de tráfego abertos pelo aplicativo. Os números na tabela representam o limite superior que o aplicativo, teoricamente, pode atingir em um ambiente ideal.
>
>

## <a name="gwsub"></a>Sub-rede do gateway

Antes de criar um gateway de ExpressRoute, você deve criar uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que as VMs do gateway de rede virtual e os serviços usam. Quando você cria seu gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e configuradas com as configurações de gateway de ExpressRoute necessárias. Nunca implante mais nada (por exemplo, VMs adicionais) para a sub-rede de gateway. A sub-rede do gateway deve ser nomeada como GatewaySubnet para funcionar corretamente. Chamar a sub-rede de gateway de 'GatewaySubnet' permite que o Azure saiba que essa é a sub-rede para implantação nas VMs de gateway de rede virtual e nos serviços.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para as VMs de gateway e para os serviços de gateway. Algumas configurações exigem mais endereços IP do que outras. 

Ao planejar o tamanho da sub-rede do gateway, consulte a documentação da configuração que você planeja criar. Por exemplo, a configuração de gateway ExpressRoute/VPN coexistente requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, convém certificar-se de que sua sub-rede de gateway contenha endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora seja possível criar uma sub-rede de gateway tão pequena quanto/29, recomendamos que você crie uma sub-rede de gateway de/27 ou maior (/27,/26, etc.) se tiver o espaço de endereço disponível para fazer isso. Isso irá acomodar a maioria das configurações.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>SKUs de gateway redundantes de zona

Também é possível implantar gateways do ExpressRoute em Zonas de Disponibilidade do Azure. Isso separa fisicamente e logicamente em Zonas de disponibilidade diferentes, protegendo a conectividade de rede local com o Azure de falhas de nível de zona.

![Gateway do ExpressRoute com redundância de zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Gateways com redundância de zona usam novas SKUs de gateways específicas para gateway do ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

As novas SKUs de gateway também dão suporte a outras opções de implantação para melhor atender às suas necessidades. Ao criar um gateway de rede virtual usando as novas SKUs de gateway, você também terá a opção de implantar o gateway em uma zona específica. Isso é referido como um gateway de VPN. Ao implantar um gateway em zona, todas as instâncias do gateway são implantadas na mesma zona de disponibilidade.

## <a name="fastpath"></a>FastPath
O gateway de rede virtual ExpressRoute foi projetado para trocar rotas de rede e rotear o tráfego de rede. O FastPath foi projetado para melhorar o desempenho do caminho de dados entre sua rede local e sua rede virtual. Quando habilitado, o FastPath envia o tráfego de rede diretamente às máquinas virtuais na rede virtual, ignorando o gateway. 

O FastPath está disponível em todos os circuitos do ExpressRoute. Ele ainda requer que um gateway de rede virtual seja criado para trocar rotas entre a rede virtual e a rede local. O gateway de rede virtual deve ser ultra performance ou ErGw3AZ.

O FastPath não dá suporte aos seguintes recursos:
* UDR na sub-rede de gateway: se você aplicar um UDR à sub-rede de gateway de sua rede virtual, o tráfego de rede da rede local continuará a ser enviado para o gateway de rede virtual.
* Emparelhamento VNet: se você tiver outras redes virtuais emparelhadas com aquela que está conectada ao ExpressRoute, o tráfego de rede da sua rede local para as outras redes virtuais (ou seja, a chamada de "spoke" VNets) continuará a ser enviado para a rede virtual Gateway. A solução alternativa é conectar todas as redes virtuais ao circuito do ExpressRoute diretamente.
* Load Balancer básica: se você implantar um balanceador de carga interno básico em sua rede virtual ou o serviço de PaaS do Azure que você implantar em sua rede virtual usar um balanceador de carga interno básico, o tráfego de rede da sua rede local para os IPs virtuais hospedados no O Load Balancer básico será enviado para o gateway de rede virtual. A solução é atualizar o balanceador de carga básico para um [balanceador de carga padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).
* Link privado: se você se conectar a um [ponto de extremidade privado](../private-link/private-link-overview.md) em sua rede virtual a partir de sua rede local, a conexão passará pelo gateway de rede virtual.
 
## <a name="resources"></a>Cmdlets do PowerShell e APIs REST
Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar cmdlets do PowerShell e APIs REST para configurações do gateway de rede virtual, veja as páginas a seguir:

| **Clássico** | **Gerenciador de Recursos** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Próximas etapas
Consulte [Visão geral de ExpressRoute](expressroute-introduction.md) para saber mais sobre as configurações de conexão disponíveis.

Veja [Como criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) para obter mais informações sobre a criação de gateways do ExpressRoute.

Ver [criar um gateway de rede virtual com redundância de zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) para obter mais informações sobre como configurar gateways com redundância de zona.

Consulte [vincular rede virtual ao ExpressRoute](expressroute-howto-linkvnet-arm.md) para obter mais informações sobre como habilitar o FastPath. 
