---
title: "Criar, alterar ou excluir um endereço IP público do Azure | Microsoft Docs"
description: "Aprenda a criar, alterar ou excluir um endereço IP público."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 8efc0bff4764a7265a5f1bcdd995979af0b22234
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou excluir um endereço IP público

Saiba mais sobre um endereço IP público e como criá-lo, alterá-lo e excluí-lo. Um endereço IP público é um recurso com suas próprias definições configuráveis. A atribuição de um endereço IP público a outros recursos do Azure permite:
- Conectividade de entrada com a Internet para recursos como Máquinas Virtuais do Azure, Conjuntos de Dimensionamento de Máquinas Virtuais do Azure, Gateway de VPN do Azure, gateways de aplicativos e Azure Load Balancers voltados para a Internet. Os recursos do Azure não podem receber comunicação de entrada da Internet sem um endereço IP público atribuído. Embora alguns recursos do Azure sejam inerentemente acessíveis por meio de endereços IP públicos, outros recursos devem ter endereços IP públicos atribuídos a eles para serem acessíveis pela Internet.
- Conectividade de saída à Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode se comunicar pela saída com a Internet sem um endereço IP público atribuído a ela, mas seu endereço é o endereço de rede convertido pelo Azure em um endereço público imprevisível. Atribuir um endereço IP público a um recurso permite que você saiba qual endereço IP é usado para a conexão de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para saber mais, confira [Criar um endereço IP público](#create-a-public-ip-address). Para saber mais sobre conexões de saída usando recursos do Azure, leia o artigo [Entendendo as conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial requer o módulo do Azure PowerShell versão 5.2.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.26 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

Os endereços IP públicos têm um encargo nominal. Para ver os preços, acesse a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

1. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
2. Clique em **+ Adicionar** na folha **Endereço IP Público** que é exibida.
3. Insira ou selecione valores para as seguintes configurações na folha **Criar endereço IP público** que é exibida e clique em **Criar**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |SKU|sim|Todos os endereços IP públicos criados antes da introdução de SKUs são endereços IP públicos de SKU **Básico**.  Você não pode alterar a SKU depois que o endereço IP público é criado. Uma máquina virtual autônoma, máquinas virtuais em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais podem usar SKUs Básicos ou Standard.  Não é permitido misturar SKUs entre máquinas virtuais em conjuntos de disponibilidade e aquelas em conjuntos de dimensionamento. SKU **Básico**: se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Nenhuma* por padrão. Você pode optar por selecionar uma zona de disponibilidade para assegurar uma zona específica para seu endereço IP público. SKU **Standard**: um IP público de SKU Standard pode ser associado a uma máquina virtual ou a um front-end de balanceador de carga. Se você estiver criando um endereço IP público em uma região que dá suporte zonas de disponibilidade, a configuração **Zona de disponibilidade** será definida como *Com redundância de zona* por padrão. Para obter informações sobre zonas de disponibilidade, consulte configuração **Zona de disponibilidade**. O SKU padrão será necessário se você associar o endereço a um Load Balancer Standard. Para saber mais sobre os Load Balancers Standard, consulte [SKU Standard do Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O SKU Standard está em versão prévia. Antes de criar um endereço IP público do SKU Standard, você deve primeiro concluir as etapas em [Registrar-se para a versão prévia do SKU Standard](#register-for-the-standard-sku-preview) e criar o endereço IP público em uma localização (região) com suporte. Para obter uma lista de localizações com suporte, consulte [Disponibilidade da região](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) e monitore a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) para obter suporte de região adicional. Quando você atribui um endereço IP público de SKU padrão ao adaptador de rede de uma máquina virtual, deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.|
    |NOME|sim|O nome deve ser exclusivo no grupo de recursos selecionado.|
    |Versão IP|sim| Selecione IPv4 ou IPv6. Embora endereços IPv4 públicos possam ser atribuídos a vários recursos do Azure, um endereço IP público IPv6 só pode ser atribuído a um balanceador de carga voltado para a Internet. O balanceador de carga pode balancear o tráfego IPv6 para máquinas virtuais do Azure. Saiba mais sobre [balanceamento de carga de tráfego IPv6 para máquinas virtuais](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você tiver selecionado o **SKU Standard**, você não terá a opção de selecionar *IPv6*. Você só pode criar um endereço IPv4 ao usar o **SKU Standard**.|
    |Atribuição de endereço IP|sim|**Dinâmico:** endereços dinâmicos são atribuídos somente depois que o endereço IP público é associado a um adaptador de rede conectado a uma máquina virtual e essa máquina virtual é iniciada pela primeira vez. Os endereços dinâmicos poderão ser alterados se a máquina virtual à qual o adaptador de rede está conectado for interrompida (desalocada). O endereço permanecerá o mesmo se a máquina virtual for reinicializada ou interrompida (mas não desalocada). **Estático:** endereços estáticos são atribuídos quando o endereço IP público é criado. Os endereços estáticos não serão alterados mesmo se a máquina virtual for colocada no estado interrompido (desalocado). O endereço só é liberado quando o adaptador de rede é excluído. Você pode alterar o método de atribuição após a criação do adaptador de rede. Se você selecionar *IPv6* para a **Versão do IP**, o método de atribuição será *Dinâmico*. Se você selecionar *Standard* para **SKU**, o método de atribuição será *Estático*.|
    |Tempo limite de ociosidade (minutos)|Não |Por quantos minutos manter uma conexão TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep alive. Se você selecionar IPv6 para a **versão IP**, esse valor não poderá ser alterado. |
    |Rótulo do nome DNS|Não |Deve ser exclusivo mo local do Azure em que você cria o nome (em todas as assinaturas e todos os clientes). O Azure registra automaticamente o nome e o endereço IP no DNS dele para que você possa se conectar a um recurso com o nome. O Azure acrescenta *localização.cloudapp.azure.com* (em que localização é a localização que você seleciona) ao nome que você fornece para criar o nome DNS totalmente qualificado. Se você optar por criar duas versões de endereço, o mesmo nome DNS será atribuído aos endereços IPv4 e IPv6. O DNS padrão do Azure contém registros de nome IPv4 e IPv6 AAAA e responde com ambos os registros quando o nome DNS é pesquisado. O cliente escolhe com qual endereço (IPv4 ou IPv6) quer se comunicar. Em vez de ou além de usar o rótulo de nome DNS com o sufixo padrão, você pode usar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que seja resolvido para o endereço IP público. Para obter mais informações, consulte [Usar o DNS do Azure com o endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Criar um endereço IPv6 (ou IPv4)|Não | O que é exibido, IPv4 ou IPv6, depende do que você seleciona para a **versão IP**. Por exemplo, se você selecionar **IPv4** para a **versão IP**, **IPv6** será exibido aqui. Se você selecionar *Standard* para **SKU**, você não terá a opção de criar um endereço IPv6.
    |Nome (visível somente se você tiver marcado a caixa de seleção **Criar um endereço IPv6 (ou IPv4)**)|Sim, se você marcar a caixa de seleção **Criar um IPv6** (ou IPv4).|O nome deve ser diferente do nome que você insere para o primeiro **Nome** nessa lista. Se você optar por criar endereços IPv4 e IPv6, o portal criará dois recursos de endereço IP públicos separados, cada um com uma versão do endereço IP atribuída a ele.|
    |Atribuição de endereço IP (visível somente se você tiver marcado a caixa de seleção **Criar um endereço IPv6 (ou IPv4)**)|Sim, se você marcar a caixa de seleção **Criar um IPv6** (ou IPv4).|Se a caixa de seleção diz **Criar um endereço IPv4**, você pode selecionar um método de atribuição. Se a caixa de seleção diz **Criar um endereço IPv6**, não é possível selecionar um método de atribuição, pois ele deve ser **Dinâmico**.|
    |Assinatura|sim|Deve existir na mesma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que o recurso ao qual você deseja associar o endereço IP público.|
    |Grupo de recursos|sim|Pode existir no mesmo ou em outro [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que o recurso ao qual você deseja associar o endereço IP público.|
    |Local padrão|sim|Deve existir na mesma [localização](https://azure.microsoft.com/regions), também referenciada como região, que o recurso ao qual você deseja associar o endereço IP público.|
    |Zona de disponibilidade| Não  | Essa configuração só aparecerá se você selecionar uma localização com suporte. Para obter uma lista de localizações com suporte, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). As zonas de disponibilidade estão atualmente em versão prévia. Antes de selecionar uma opção de zona ou com redundância de zona, você deve concluir as etapas em [Registrar-se para a versão prévia de zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#get-started-with-the-availability-zones-preview). Se você tiver selecionado o SKU **Básico**, *Nenhum* será selecionado automaticamente para você. Se você preferir garantir uma zona específica, você poderá selecionar uma. Qualquer uma dessas opções será sem redundância de zona. Se você tiver selecionado o SKU **Standard**, a opção Com redundância de zona será selecionada automaticamente para você e tornará o seu caminho de dados resiliente a falhas de zona. Se você preferir garantir uma zona específica (que não é resiliente a falhas de zona), você poderá selecionar uma.
  

**Comandos**

Embora o portal forneça a opção de criar dois recursos de endereço IP públicos (um IPv4 e um IPv6), os comandos da CLI e do PowerShell a seguir criam um recurso com um endereço para uma versão IP ou o outro. Se você quiser dois recursos de endereço IP público, uma para cada versão IP, deverá executar o comando duas vezes, especificando nomes e versões diferentes para os recursos de endereço IP público. 

|Ferramenta|Get-Help|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Exibir, alterar as configurações ou excluir um endereço IP público

1. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *endereço ip público*. Quando **Endereços IP públicos** aparecerem nos resultados da pesquisa, clique nele.
2. Na folha **Endereços IP públicos** exibida, clique no nome do endereço IP público que deseja exibir, alterar as configurações ou excluir.
3. Na folha exibida para o endereço IP público, escolha uma das opções a seguir, dependendo da ação que deseja: exibir, excluir ou alterar o endereço IP público.
    - **Exibir**: a seção **Visão Geral** da folha mostra as configurações de chave do endereço IP público, como o adaptador de rede ao qual ele está associado (caso o endereço esteja associado a um adaptador de rede). O portal não mostra a versão do endereço (IPv4 ou IPv6). Para exibir as informações de versão, use o comando do PowerShell ou da CLI para exibir o endereço IP público. Se a versão do endereço IP for IPv6, o endereço atribuído não será exibido pelo portal, pelo PowerShell nem pela CLI. 
    - **Excluir**: para excluir o endereço IP público, clique em **Excluir** na seção **Visão Geral** da folha. Se o endereço estiver associado a uma configuração de IP, ele não poderá ser excluído. Se, no momento, o endereço estiver associado a uma configuração, clique em **Desassociar** para desassociar o endereço da configuração de IP.
    - **Alterar**: clique em **Configuração**. Altere as configurações usando as informações na etapa 4 da seção [Criar um endereço IP público](#create-a-public-ip-address) deste artigo. Para alterar a atribuição para um endereço IPv4 de estático para dinâmico, primeiramente você deve desassociar o endereço IPv4 público da configuração de IP à qual ele está associado. Você pode alterar o método de atribuição para dinâmico e clicar em **Associar** para associar o endereço IP à mesma configuração de IP, a uma configuração diferente ou deixá-lo desassociado. Para desassociar um endereço IP público, na seção **Visão Geral**, clique em **Desassociar**.

>[!WARNING]
>Ao alterar o método de atribuição de estático para dinâmico, você perde o endereço IP que foi atribuído ao endereço IP público. Embora os servidores DNS públicos do Azure mantenham um mapeamento entre endereços estáticos ou dinâmicos e qualquer rótulo de nome DNS (caso tenha definido um), um endereço IP dinâmico pode ser alterado quando a máquina virtual é iniciada depois de ter ficado no estado interrompida (desalocada). Para impedir que o endereço seja alterado, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Get-Help|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_list) para listar endereços IP públicos; [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_show) para mostrar as configurações; [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_update) para atualizar e [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_delete) para excluir|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para recuperar um objeto de endereço IP público e exibir suas configurações; [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para atualizar as configurações e [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) para excluir|

## <a name="register-for-the-standard-sku-preview"></a>Registrar-se para a versão prévia do SKU Standard

> [!NOTE]
> Os recursos na versão prévia podem não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Não há suporte para os recursos na versão prévia, eles podem ter funcionalidades restritas e podem não estar disponíveis em todas as localizações do Azure. 

Antes que você possa criar um endereço IP público do SKU Standard, você deve primeiro registrar-se para a versão prévia. Para se registrar para a versão prévia, conclua as seguintes etapas:

1. No PowerShell, insira o comando a seguir para registrar-se para a versão prévia:
   
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
2. Digite o seguinte comando para confirmar que você está registrado para a versão prévia:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

## <a name="next-steps"></a>Próximas etapas
Atribua endereços IP públicos ao criar os seguintes recursos do Azure:

- Máquinas virtuais [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexão site a site usando um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de Dimensionamento de Máquinas Virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
