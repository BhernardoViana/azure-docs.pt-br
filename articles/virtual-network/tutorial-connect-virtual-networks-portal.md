---
title: Conectar redes virtuais ao emparelhamento de rede virtual – tutorial – Portal do Azure | Microsoft Docs
description: Neste tutorial, você aprende a conectar redes virtuais com o emparelhamento de rede virtual usando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: e66747cd350d10a5a66ec54b9aae9e9b485b0ba2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014484"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutorial: Conectar redes virtuais ao emparelhamento de rede virtual usando o portal do Azure

Você pode conectar redes virtuais entre si ao emparelhamento de rede virtual. Essas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecido como emparelhamento VNet Global). Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar duas redes virtuais
> * Conectar duas redes virtuais a um emparelhamento de rede virtual
> * Implementar uma VM (máquina virtual) em cada rede virtual
> * Comunicação entre VMs

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](tutorial-connect-virtual-networks-cli.md) ou o [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em https://portal.azure.com.

## <a name="create-virtual-networks"></a>Criar redes virtuais

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVirtualNetwork1|
    |Espaço de endereço|10.0.0.0/16|
    |Assinatura| Selecione sua assinatura.|
    |Grupo de recursos| Selecione **Criar novo** e insira *myResourceGroup*.|
    |Local padrão| Selecione **Leste dos EUA**.|
    |Nome da sub-rede|Subnet1|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|

      ![Criar uma rede virtual](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Conclua as etapas 1 a 3 novamente, com as seguintes alterações:

    |Configuração|Valor|
    |---|---|
    |NOME|myVirtualNetwork2|
    |Espaço de endereço|10.1.0.0/16|
    |Grupo de recursos| Clique em **Usar existente** e selecione **myResourceGroup**.|
    |Intervalo de endereços da sub-rede|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

1. Nas caixa Pesquisar na parte superior do portal, comece digitando *MyVirtualNetwork1*. Quando **myVirtualNetwork1** aparecer nos resultados da pesquisa, selecione-o.
2. Em **Emparelhamentos**, selecione **CONFIGURAÇÕES** e, em seguida, selecione **+ Add**, como mostrado na imagem a seguir:

    ![Criar emparelhamento](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Insira, ou selecione, as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **OK**.

    |Configuração|Valor|
    |---|---|
    |NOME|myVirtualNetwork1-myVirtualNetwork2|
    |Assinatura| Selecione sua assinatura.|
    |Rede virtual|myVirtualNetwork2 - Para selecionar a rede virtual *myVirtualNetwork2*, selecione **Rede virtual**, em seguida, selecione **myVirtualNetwork2**. Você pode selecione uma rede virtual na mesma região ou em uma região diferente.|

    ![Configurações de emparelhamento](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    O **STATUS DO EMPARELHAMENTO** é *iniciadi*, conforme mostrado na figura a seguir:

    ![Status de emparelhamento](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Se você não vir o status, atualize seu navegador.

4. Nas caixa **Pesquisar** na parte superior do portal, comece digitando *MyVirtualNetwork2*. Quando **myVirtualNetwork2** aparecer nos resultados da pesquisa, selecione-o.
5. Conclua as etapas 2 a 3 novamente, com as seguintes alterações, e em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVirtualNetwork2-myVirtualNetwork1|
    |Rede virtual|myVirtualNetwork1|

    O **ESTADO DE EMPARELHAMENTO** é *Conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork2-myVirtualNetwork1* de *Iniciado* para *Conectado.* O emparelhamento de rede virtual não é estabelecido com êxito até que o status de emparelhamento para ambas as redes virtuais seja *Conectado.* 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual para que seja possível comunicar-se entre elas em uma etapa posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. É possível selecionar um sistema operacional diferente, mas as etapas restantes assumirão que está selecionado o **Windows Server 2016 Datacenter**. 
3. Insira, ou selecione, as informações a seguir para **Princípios básicos**, aceite os padrões para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVm1|
    |Nome de usuário| Insira um nome de usuário de sua escolha.|
    |Senha| Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Clique em **Usar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **Leste dos EUA**.|
4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Configurações** e selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual| myVirtualNetwork1 - Se ainda não estiver selecionada, selecione a **Rede Virtual** e, em seguida, selecione **myVirtualNetwork1** em **Escolher rede virtual**.|
    |Sub-rede| Subnet1 - Se ainda não estiver selecionada, selecione **Sub-rede** e, em seguida, selecione **Subnet1** em **Escolher sub-rede**.|
    
    ![Configurações da máquina virtual](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua as etapas 1 a 6 novamente, com as seguintes alterações:

|Configuração|Valor|
|---|---|
|NOME | myVm2|
|Rede virtual | myVirtualNetwork2|

As VMs podem levar alguns minutos para serem criadas. Não continue com as etapas restantes até que ambas as VMs sejam criadas.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

1. Na caixa *Pesquisar* na parte superior do portal, comece digitando *myVm1*. Selecione **myVm1** quando aparecer nos resultados da pesquisa.
2. Crie uma conexão de área de trabalho remota para a VM *myVm1*, selecionando **Conectar**, conforme mostrado na imagem a seguir:

    ![Conectar-se à máquina virtual](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Para conectar-se à VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
4. Insira o nome de usuário e senha especificados ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
6. Em uma etapa posterior, o ping será usado para comunicar-se com a VM *myVm2* da VM *myVm1*. O ping usa o protocolo ICMP que, por padrão, não é permitido através do Firewall do Windows. Sobre a VM *myVm1*, habilite o ICMP por meio do Firewall do Windows, para que você possa executar ping nessa VM pela *myVm2* em uma etapa posterior, usando o PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Embora o ping seja usado para realizar a comunicação entre VMs neste tutorial, não é recomendável a permissão de ICMP através do Firewall do Windows para implantações de produção.

7. Para conectar a VM *myVm2*, insira o seguinte comando a partir de um prompt de comando na VM *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Desde que você tenha habilitado o ping em *myVm1*, você pode agora executar o ping-lo pelo endereço IP:

    ```
    ping 10.0.0.4
    ```
    
9. Desconecte as sessões RDP para ambas *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar duas redes na mesma região do Azure com o emparelhamento de rede virtual. Você também pode emparelhar redes virtuais em diferentes [regiões com suporte](virtual-network-manage-peering.md#cross-region), em [diferentes assinaturas do Azure](create-peering-different-subscriptions.md#portal) e também criar [designs de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com emparelhamento. Para saber mais sobre o emparelhamento de rede virtual, consulte [Visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md) e [Gerenciamento de emparelhamentos de rede virtual](virtual-network-manage-peering.md).

Para conectar seu próprio computador a uma rede virtual por meio de um VPN e intereja com os recursos de uma rede virtual ou de redes virtuais emparelhadas, consulte [Conectar seu computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
