---
title: Emparelhamento de rede virtual do Azure | Microsoft Docs
description: Saiba mais sobre o emparelhamento de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 23281067021dd6e4b8959fe73f3c8a11a651d9d2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

O emparelhamento de rede virtual permite que você conecte duas [redes virtuais](virtual-networks-overview.md) do Azure sem interrupção. Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre as máquinas virtuais na mesma rede virtual somente por endereços IP *privados*. 

Os benefícios do uso do emparelhamento de rede virtual incluem:

* O tráfego de rede entre redes virtuais emparelhadas é particular. O tráfego entre as redes virtuais é mantido na rede de backbone da Microsoft. Não são necessários a Internet pública, os gateways ou a criptografia na comunicação entre as redes virtuais.
* Baixa latência, conexão com largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos em uma rede virtual para se comunicar com recursos em uma rede virtual diferente quando as redes virtuais estão pareadas.
* A capacidade de transferir dados entre assinaturas do Azure, modelos de implantação e entre regiões do Azure (versão prévia).
* A capacidade de emparelhar redes virtuais criadas por meio do Azure Resource Manager ou emparelhar uma rede virtual criada pelo Gerenciador de Recursos com uma rede virtual criada por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Nenhum tempo de inatividade para recursos em qualquer rede virtual ao criar o emparelhamento ou depois que o emparelhamento é criado.

## <a name="requirements-constraints"></a>Requisitos e restrições

* O emparelhamento de redes virtuais na mesma região está disponível ao público em geral. O emparelhamento de redes virtuais em regiões diferentes está em versão prévia no Centro-oeste dos EUA, Centro do Canadá, Oeste dos EUA 2, Coreia do Sul, Sul do Reino Unido, Oeste do Reino Unido, Leste do Canadá, Sul da Índia, Índia Central e Índia Ocidental. Antes de fazer o emparelhamento das redes virtuais em regiões diferentes, você deve primeiro [registrar sua assinatura](tutorial-connect-virtual-networks-powershell.md#register) para a versão prévia. A tentativa de criar um emparelhamento entre redes virtuais em regiões diferentes falhará se você não tiver concluído o registro para a versão prévia.
    > [!WARNING]
    > Os emparelhamentos de rede virtual criados entre regiões podem não ter o mesmo nível de disponibilidade e confiabilidade encontrado em emparelhamentos em uma versão de disponibilidade geral. Emparelhamentos de rede virtual podem ter funcionalidades restringidas e podem não estar disponíveis em todas as regiões do Azure. Para ver as notificações mais recentes sobre disponibilidade e o status desse recurso, verifique a página [Atualizações da Rede Virtual](https://azure.microsoft.com/updates/?product=virtual-network) .

* As redes virtuais emparelhadas devem ter espaços de endereço IP não sobrepostos.
* Os intervalos de endereços não podem ser adicionados ao ou excluídos do espaço de endereço de uma rede virtual depois que ela é emparelhada com outra rede virtual. Se você precisar adicionar intervalos de endereços ao espaço de endereço de uma rede virtual emparelhada, remova o emparelhamento, adicione o espaço de endereço e, em seguida, adicione o emparelhamento.
* O emparelhamento de rede virtual é entre duas redes virtuais. Não há nenhuma relação transitiva derivada entre emparelhamentos. Por exemplo, se a virtualNetworkA está emparelhada com a virtualNetworkB, e a virtualNetworkB está emparelhada com a virtualNetworkC, então, a virtualNetworkA *não* está emparelhada com virtualNetworkC.
* Você pode emparelhar redes virtuais existentes em duas assinaturas diferentes, desde que um usuário com privilégios de ambas as assinaturas (confira [permissões específicas](create-peering-different-deployment-models-subscriptions.md#permissions)) autorize o emparelhamento e as assinaturas estejam associadas ao mesmo locatário do Azure Active Directory. Você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar redes virtuais em assinaturas associadas a locatários diferentes do Active Directory.
* Redes virtuais podem ser emparelhadas de duas maneiras: quando ambas são criadas usando o modelo de implantação do Gerenciador de Recursos; ou quando uma é criada usando o modelo de implantação do Gerenciador de Recursos e a outra, o modelo de implantação clássico. No entanto, as redes virtuais criadas por meio do modelo de implantação clássico não podem ser emparelhadas uma à outra. Você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar redes virtuais criadas usando o modelo de implantação clássico.
* Embora a comunicação entre máquinas virtuais em redes virtuais emparelhadas não tenha restrição de largura de banda adicional, há uma largura de banda de rede máxima, dependendo do tamanho da máquina virtual que ainda é aplicável. Para saber mais sobre a largura de banda de rede máxima para diferentes tamanhos de máquina virtual, leia os artigos sobre tamanhos de máquina virtual do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Emparelhamento básico de redes virtuais](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Conectividade

Ao parear as redes virtuais, os recursos de qualquer uma delas pode se conectar diretamente com os recursos da rede virtual emparelhada.

A latência de rede entre as máquinas virtuais em redes virtuais emparelhadas na mesma região é a mesma latência de uma única rede virtual. A taxa de transferência da rede tem base na largura de banda permitida para a máquina virtual, que é proporcional ao seu tamanho. Não existe restrição adicional quanto à largura de banda no emparelhamento.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente pela infraestrutura de backbone da Microsoft, não por um gateway ou pela Internet pública.

As máquinas virtuais em uma rede virtual podem acessar o balanceador de carga interno na rede virtual emparelhada da mesma região. O suporte ao balanceador de carga interno não se estende às redes virtuais emparelhadas globalmente (versão prévia). A versão de disponibilidade geral do emparelhamento de rede virtual global terá suporte para o balanceador de carga interno.

Grupos de segurança de rede podem ser aplicados em qualquer rede virtual a fim de bloquear o acesso a outras redes virtuais, ou sub-redes se for desejado.
Ao configurar o emparelhamento de rede virtual, você pode abrir ou fechar as regras de grupo de segurança de rede entre as redes virtuais. Se você abrir a conectividade total entre as redes virtuais emparelhadas (opção padrão), poderá aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas para bloquear ou negar o acesso específico. Para saber mais sobre grupos de segurança de rede, confira [Visão geral dos grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Encadeamento de serviços

Você pode configurar rotas definidas pelo usuário que apontam para máquinas virtuais em redes virtuais emparelhadas como o endereço IP de *próximo salto*, ou para gateways de rede virtual, para habilitar o encadeamento de serviços. O encadeamento de serviços permite que você direcione o tráfego de uma rede virtual para uma solução de virtualização, ou um gateway de rede virtual, em uma rede virtual emparelhada através de rotas definidas pelo usuário.

Você pode implantar redes do tipo hub e spoke, nos quais a rede virtual do hub pode hospedar componentes de infraestrutura, como uma solução de virtualização ou gateway de VPN de rede. Todas as redes virtuais contadas podem emparelhar com a rede virtual do hub. O tráfego pode fluir por meio de soluções de virtualização ou gateways de VPN de rede que estejam na rede virtual do hub. 

O emparelhamento de redes virtuais permite que o próximo salto em uma rota definida pelo usuário seja o endereço IP de uma máquina virtual na rede virtual emparelhada, ou o gateway de VPN. No entanto, não é possível rotear entre redes virtuais com uma rota definida pelo usuário especificando um gateway de ExpressRoute como o tipo de próximo salto. Para saber mais sobre as rotas definidas pelo usuário, confira [Visão geral de rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Para saber como criar uma topologia de rede de hub e spoke, consulte [Topologia de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conectividade local

Cada rede virtual, independentemente de estar emparelhada com outra rede virtual ou não, ainda pode ter seu próprio gateway e usá-lo para se conectar a uma rede local. Você também pode configurar a [conexão rede virtual para rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando gateways, mesmo que as redes virtuais estejam emparelhadas.

Quando as duas opções para a interconectividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais fluirá através da configuração do emparelhamento (isto é, por meio do backbone do Azure).

Quando as redes virtuais estiverem emparelhadas na mesma região, você também poderá configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual pode ter apenas um gateway. O gateway pode ser um gateway local ou remoto (na rede virtual emparelhada), conforme mostrado na seguinte figura:

![trânsito de emparelhamento de rede virtual](./media/virtual-networks-peering-overview/figure04.png)

Não há suporte para o trânsito de gateway na relação de emparelhamento entre redes virtuais criadas por meio de diferentes modelos de implantação ou regiões. Ambas as redes virtuais na relação de emparelhamento devem ter sido criadas pelo Gerenciador de Recursos e precisam estar na mesma região para que um gateway de trânsito funcione. As redes virtuais emparelhadas globalmente atualmente não dão suporte ao trânsito de gateway.

Quando as redes virtuais que compartilham uma única conexão de Azure ExpressRoute forem emparelhadas, o tráfego entre elas passará pela relação de emparelhamento (isto é, pela rede de backbone do Azure). Você ainda pode usar gateways locais em cada rede virtual para se conectar ao circuito local. Como alternativa, você pode usar um gateway compartilhado e configurar o trânsito para conectividade local.

## <a name="permissions"></a>Permissões

O emparelhamento de rede virtual é uma operação privilegiada. É uma função separada sob o namespace da VirtualNetworks. Um usuário pode receber direitos específicos para autorizar o emparelhamento. Um usuário que tem acesso de leitura e gravação para a rede virtual herda automaticamente esses direitos.

Um usuário que é um administrador ou um usuário com privilégios da capacidade de emparelhamento pode iniciar uma operação de emparelhamento em outra rede virtual. O nível mínimo de permissão necessário é Colaborador de Rede. Se houver uma solicitação correspondente para o emparelhamento no outro lado e outros requisitos forem atendidos, o emparelhamento será estabelecido.

Por exemplo, se você pretende emparelhar redes virtuais chamadas myVirtualNetworkA e myVirtualNetworkB, sua conta deve ser atribuída à seguinte função ou permissões mínimas para cada rede virtual:

|Rede virtual|Modelo de implantação|Função|Permissões|
|---|---|---|---|
|myVirtualNetworkA|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVirtualNetworkB|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Monitoramento

Ao emparelhar duas redes virtuais criadas por meio do Resource Manager, um emparelhamento deve ser configurado para cada rede virtual no emparelhamento. Você pode monitorar o status de sua conexão de emparelhamento. O status do emparelhamento pode ser um dos seguintes:

* **Iniciado**: o estado exibido quando você cria o emparelhamento a partir da primeira rede virtual para a segunda rede virtual.
* **Conectado**: o estado exibido após a criação do emparelhamento a partir da segunda rede virtual para a primeira rede virtual. O estado de emparelhamento da primeira rede virtual muda de *Iniciado* para *Conectado*. Um emparelhamento de rede virtual não é estabelecido com êxito até que o estado dos dois emparelhamentos de rede virtual seja *Conectado*.
* **Desconectado**: o estado mostrado se um emparelhamento de uma rede virtual para outra for excluído após o estabelecimento de um emparelhamento entre duas redes virtuais.

## <a name="troubleshoot"></a>Solucionar problemas

Para confirmar um emparelhamento de rede virtual, você pode [verificar rotas efetivas](virtual-network-routes-troubleshoot-portal.md) para uma interface de rede em qualquer sub-rede em uma rede virtual. Se um emparelhamento de rede virtual existir, todas as sub-redes na rede virtual terão rotas com o tipo de próximo salto *Emparelhamento VNet*, para cada espaço de endereço em cada rede virtual emparelhada.

Você também pode solucionar os problemas de conectividade com uma máquina virtual em uma rede virtual emparelhada usando a [verificação de conectividade](../network-watcher/network-watcher-connectivity-portal.md) do Observador de Rede. A verificação de conectividade permite que você veja como o tráfego é roteado da interface de rede de uma máquina virtual de origem até a interface de rede de uma máquina virtual de destino.

## <a name="limits"></a>limites

Há limites no número de emparelhamentos permitidos para uma única rede virtual. Para obter detalhes, veja [Limites de rede do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="pricing"></a>Preços

Há um custo nominal para tráfego de entrada e saída que utiliza uma conexão de emparelhamento de rede virtual. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Próximas etapas

* Conclua o tutorial de emparelhamento de rede virtual. Um emparelhamento de rede virtual é criado entre redes virtuais criadas com modelos de implantação iguais ou diferentes que existem nas mesmas assinaturas ou em assinaturas diferentes. Conclua um tutorial para um dos seguintes cenários:

    |Modelo de implantação do Azure  | Assinatura  |
    |---------|---------|
    |Ambos Resource Manager |[Idêntica](tutorial-connect-virtual-networks-portal.md)|
    | |[Diferente](create-peering-different-subscriptions.md)|
    |Um Resource Manager, um clássico     |[Idêntica](create-peering-different-deployment-models.md)|
    | |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Saiba como criar uma [topologia de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)
* Saiba mais sobre todas as [configurações de emparelhamento de rede virtual e como alterá-las](virtual-network-manage-peering.md)
