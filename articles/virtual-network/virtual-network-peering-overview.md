---
title: Emparelhamento de Rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o emparelhamento de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: e6c5a9aa3e4e173ecfc79f4072d091493677afed
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489974"
---
# <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

Emparelhamento de rede virtual permite que você se conecte remotamente do Azure [redes virtuais](virtual-networks-overview.md). Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre as máquinas virtuais na mesma rede virtual somente por endereços IP *privados*. O Azure oferece suporte a:
* Emparelhamento de rede virtual - conexão de redes virtuais na mesma região do Azure
* Emparelhamento de rede virtual global - conexão de redes virtuais em regiões do Azure

Os benefícios do uso do emparelhamento de rede virtual, seja local ou global, incluem:

* O tráfego de rede entre redes virtuais emparelhadas é particular. O tráfego entre as redes virtuais é mantido na rede de backbone da Microsoft. Não são necessários a Internet pública, os gateways ou a criptografia na comunicação entre as redes virtuais.
* Baixa latência, conexão com largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos em uma rede virtual para se comunicar com recursos em uma rede virtual diferente quando as redes virtuais estão pareadas.
* A capacidade de transferir dados entre assinaturas e modelos de implantação do Azure e entre regiões do Azure.
* A capacidade de emparelhar redes virtuais criadas por meio do Azure Resource Manager ou emparelhar uma rede virtual criada pelo Gerenciador de Recursos com uma rede virtual criada por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Nenhum tempo de inatividade para recursos em qualquer rede virtual ao criar o emparelhamento ou depois que o emparelhamento é criado.

## <a name="connectivity"></a>Conectividade

Ao parear as redes virtuais, os recursos de qualquer uma delas pode se conectar diretamente com os recursos da rede virtual emparelhada.

A latência de rede entre as máquinas virtuais em redes virtuais emparelhadas na mesma região é a mesma latência de uma única rede virtual. A taxa de transferência da rede tem base na largura de banda permitida para a máquina virtual, que é proporcional ao seu tamanho. Não existe restrição adicional quanto à largura de banda no emparelhamento.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente pela infraestrutura de backbone da Microsoft, não por um gateway ou pela Internet pública.

Grupos de segurança de rede podem ser aplicados em qualquer rede virtual a fim de bloquear o acesso a outras redes virtuais, ou sub-redes se for desejado.
Ao configurar o emparelhamento de rede virtual, você pode abrir ou fechar as regras de grupo de segurança de rede entre as redes virtuais. Se você abrir a conectividade total entre as redes virtuais emparelhadas (opção padrão), poderá aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas para bloquear ou negar o acesso específico. Para saber mais sobre grupos de segurança de rede, confira [Visão geral dos grupos de segurança de rede](security-overview.md).

## <a name="service-chaining"></a>Encadeamento de serviços

Você pode configurar rotas definidas pelo usuário que apontam para máquinas virtuais em redes virtuais emparelhadas como o endereço IP de *próximo salto*, ou para gateways de rede virtual, para habilitar o encadeamento de serviços. O encadeamento de serviços permite que você direcione o tráfego de uma rede virtual para uma solução de virtualização, ou um gateway de rede virtual, em uma rede virtual emparelhada através de rotas definidas pelo usuário.

Você pode implantar redes do tipo hub e spoke, nos quais a rede virtual do hub pode hospedar componentes de infraestrutura, como uma solução de virtualização ou gateway de VPN de rede. Todas as redes virtuais contadas podem emparelhar com a rede virtual do hub. O tráfego pode fluir por meio de soluções de virtualização ou gateways de VPN de rede que estejam na rede virtual do hub. 

O emparelhamento de redes virtuais permite que o próximo salto em uma rota definida pelo usuário seja o endereço IP de uma máquina virtual na rede virtual emparelhada, ou o gateway de VPN. No entanto, não é possível rotear entre redes virtuais com uma rota definida pelo usuário especificando um gateway de ExpressRoute como o tipo de próximo salto. Para saber mais sobre as rotas definidas pelo usuário, confira [Visão geral de rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Para saber como criar uma topologia de rede de hub e spoke, consulte [Topologia de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conectividade local

Cada rede virtual, independentemente de estar emparelhada com outra rede virtual ou não, ainda pode ter seu próprio gateway e usá-lo para se conectar a uma rede local. Você também pode configurar a [conexão rede virtual para rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando gateways, mesmo que as redes virtuais estejam emparelhadas.

Quando as duas opções para a interconectividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais fluirá através da configuração do emparelhamento (isto é, por meio do backbone do Azure).

Quando as redes virtuais estiverem emparelhadas, você também poderá configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual pode ter apenas um gateway. O gateway pode ser um gateway local ou remoto (na rede virtual emparelhada), conforme mostrado na seguinte figura:

![trânsito de emparelhamento de rede virtual](./media/virtual-networks-peering-overview/figure04.png)

Há suporte para o trânsito de gateway para o emparelhamento de rede virtual e o emparelhamento VNet Global (visualização). Você pode usar gateways remotos ou permitir trânsito de gateway em redes virtuais emparelhadas globalmente na versão prévia. A visualização está disponível em todas as regiões do Azure, regiões de nuvem da China e regiões de nuvem do governo. Nenhuma lista de permissões é necessária. Você pode testar no modo de visualização por meio da CLI, PowerShell, modelos ou API. Não há suporte para o portal na visualização.
Gateway de trânsito entre redes virtuais criadas por meio de diferentes modelos de implantação (Resource Manager e clássico) tem suporte apenas se o gateway está na rede virtual (Resource Manager). Para saber mais sobre como usar um gateway para trânsito, consulte [Configurar um gateway de VPN para trânsito em um emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando as redes virtuais que compartilham uma única conexão de Azure ExpressRoute forem emparelhadas, o tráfego entre elas passará pela relação de emparelhamento (isto é, pela rede de backbone do Azure). Você ainda pode usar gateways locais em cada rede virtual para se conectar ao circuito local. Como alternativa, você pode usar um gateway compartilhado e configurar o trânsito para conectividade local.

## <a name="troubleshoot"></a>Solução de problemas

Para confirmar um emparelhamento de rede virtual, você pode [verificar rotas efetivas](diagnose-network-routing-problem.md) para uma interface de rede em qualquer sub-rede em uma rede virtual. Se um emparelhamento de rede virtual existir, todas as sub-redes na rede virtual terão rotas com o tipo de próximo salto *Emparelhamento VNet*, para cada espaço de endereço em cada rede virtual emparelhada.

Você também pode solucionar os problemas de conectividade com uma máquina virtual em uma rede virtual emparelhada usando a [verificação de conectividade](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede. A verificação de conectividade permite que você veja como o tráfego é roteado da interface de rede de uma máquina virtual de origem até a interface de rede de uma máquina virtual de destino.

Você também pode experimentar a [Solução de problemas para problemas de emparelhamento de rede virtual](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Requisitos e restrições

As seguintes restrições se aplicam somente quando as redes virtuais são emparelhadas globalmente:
- Recursos em uma rede virtual não podem se comunicar com o endereço IP de front-end de um balanceador interno de carga básico em uma rede virtual emparelhada globalmente. Só existe suporte para Load Balancer Basic dentro da mesma região. Existe suporte para o balanceador de carga padrão para o emparelhamento VNet Global.

Para saber mais sobre requisitos e restrições, confira [Restrições e requisitos de emparelhamento de redes virtuais](virtual-network-manage-peering.md#requirements-and-constraints). Para saber mais sobre os limites de número de emparelhamentos que você pode criar para uma rede virtual, confira [Limites de rede do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Permissões

Para saber mais sobre as permissões necessárias para criar um emparelhamento de redes virtuais, confira [Permissões de emparelhamento de redes virtuais](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preços

Há um custo nominal para tráfego de entrada e saída que utiliza uma conexão de emparelhamento de rede virtual. Para obter mais informações sobre o emparelhamento de VNet e preços do emparelhamento VNet Global, consulte a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

Tráfego de gateway é uma propriedade de emparelhamento que permite que uma rede virtual para utilizar o gateway de VPN em uma rede virtual emparelhada para conectividade entre locais e VNET a VNET. O tráfego que passa por um gateway remoto neste cenário está sujeito à [encargos de gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) e não incorre em [encargos de emparelhamento de Vnet.](https://azure.microsoft.com/pricing/details/virtual-network) Por exemplo, se VNetA tem um gateway de VPN para conectividade local e VNetB VNetA é emparelhada com as propriedades apropriadas configuradas, o tráfego da VNetB para o local é cobrado apenas egresso por preços de gateway VPN. Encargos de emparelhamento de VNet não se aplicam. Saiba como [configurar o tráfego do gateway de VPN para o emparelhamento de rede virtual.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>Próximos passos

* Um emparelhamento de rede virtual é criado entre redes virtuais criadas com modelos de implantação iguais ou diferentes que existem nas mesmas assinaturas ou em assinaturas diferentes. Conclua um tutorial para um dos seguintes cenários:

    |Modelo de implantação do Azure             | Assinatura  |
    |---------                          |---------|
    |Ambos Resource Manager              |[Idêntico](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Um Resource Manager, um clássico  |[Idêntico](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Saiba como criar uma [topologia de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Saiba mais sobre todas as [configurações de emparelhamento de rede virtual e como alterá-las](virtual-network-manage-peering.md).
* Obtenha respostas para perguntas comuns de emparelhamento VNet e o emparelhamento VNet Global por meio de nossas [Perguntas frequentes sobre o Emparelhamento VNet](virtual-networks-faq.md#vnet-peering)
