---
title: Criar um gateway de aplicativo com regras de roteamento baseadas em caminhos de URL - portal do Azure
description: Saiba como criar regras de roteamento baseadas em caminhos de URL para um gateway de aplicativo e conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: victorh
ms.openlocfilehash: 10bc4e4c440e5495afd820f588270b7990108b68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135259"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Criar um gateway de aplicativo com regras de roteamentos com base no caminho usando o portal do Azure

Você pode usar o portal do Azure para configurar [regras de roteamento baseadas em caminhos de URL](application-gateway-url-route-overview.md) quando você cria um [gateway de aplicativo](application-gateway-introduction.md). Neste tutorial, você criará pools de back-end usando máquinas virtuais. Em seguida, você criará regras de roteamento para certificar-se de que o tráfego da Web chega aos servidores apropriados nos pools.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um Gateway de Aplicativo
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar um ouvinte de back-end
> * Criar uma regra de roteamento baseada em caminho

![Exemplo de roteamento de URL](./media/application-gateway-create-url-route-portal/scenario.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Uma rede virtual é necessária para a comunicação entre os recursos que você criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicativo e a outra para os servidores de back-end. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo.

1. Clique em **Novo** no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Insira esses valores para o gateway de aplicativo:

   - *myAppGateway* - para o nome do gateway de aplicativo.
   - *myResourceGroupAG* - para o novo grupo de recursos.

     ![Criar novo gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
5. Clique em **Escolher uma rede virtual**, clique em **Criar novo** e insira esses valores para a rede virtual:

   - *myVNet* – para o nome da rede virtual.
   - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
   - *myAGSubnet* – para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar rede virtual](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **Escolher um endereço IP público**, clique em **Criar novo** e digite o nome do endereço IP público. Neste exemplo, o endereço IP público é denominado *myAGPublicIPAddress*. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
8. Aceite os valores padrão para a Configuração do ouvinte, deixe o firewall do aplicativo Web desabilitado e, em seguida, clique em **OK**.
9. Examine as configurações na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicativo. A criação do gateway de aplicativo pode levar vários minutos, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myVNet** da lista de recursos.
2. Clique em **Sub-redes** e, em seguida, clique em **Sub-rede**.

    ![Criar sub-rede](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* para o nome da sub-rede e depois clique em **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, você criará três máquinas virtuais a serem usadas como servidores de back-end para o gateway de aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.

1. Clique em **Novo**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Insira esses valores para a máquina virtual:

    - *myVM1* - para o nome da máquina virtual.
    - *azureuser* – para o nome de usuário do administrador.
    - *Azure123456!* para a senha.
    - Clique em **Usar existente** e selecione *myResourceGroupAG*.

4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
6. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**. 
7. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
8. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar a extensão personalizada](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Execute o comando a seguir para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Crie duas máquinas virtuais mais e instale o IIS usando as etapas que você acabou de concluir. Insira os nomes *myVM2* e *myVM3* para os nomes e os valores de VMName em Set-AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Criar pools de back-end com as máquinas virtuais

1. Clique em **Todos os recursos** e, em seguida, clique em **myAppGateway**.
2. Clique em **Pools de back-end**. Um pool padrão foi criado automaticamente com o gateway de aplicativo. Clique em **appGatewayBackendPool**.
3. Clique em **Adicionar destino** para adicionar *myVM1* ao appGatewayBackendPool.

    ![Adicionar servidores de back-end](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Clique em **Salvar**.
5. Clique em **Pools de back-end** e, em seguida, clique em **Adicionar**.
6. Insira um nome de *imagesBackendPool* e adicione *myVM2* usando **Adicionar destino**.
7. Clique em **OK**.
8. Clique em **Adicionar** novamente para adicionar outro pool de back-end com o nome de *videoBackendPool* e adicione *myVM3* a ele.

## <a name="create-a-backend-listener"></a>Criar um ouvinte de back-end

1. Clique em **Ouvintes** e clique em **Básico**.
2. Digite *myBackendListener* para o nome, *myFrontendPort* para o nome da porta de front-end e, em seguida, *8080* como a porta do ouvinte.
3. Clique em **OK**.

## <a name="create-a-path-based-routing-rule"></a>Criar uma regra de roteamento baseada em caminho

1. Clique em **Regras** e, em seguida, clique em **Baseadas no caminho**.
2. Digite *rule2* para o nome.
3. Digite *Imagens* para o nome do primeiro caminho. Insira */images/* \* para o caminho. Selecione **imagesBackendPool** para o pool de back-end.
4. Digite *Vídeo* para o nome do segundo caminho. Insira */video/* \* para o caminho. Selecione **videoBackendPool** para o pool de back-end.

    ![Criar uma regra baseada em caminho](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Clique em **OK**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Clique em **Todos os recursos** e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registrar o endereço IP público do gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Por exemplo, http:\//40.121.222.19.

    ![Testar a URL de base no gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Altere a URL para http://&lt;ip-address&gt;:8080/images/test.htm, substituindo &lt;ip-address&gt; pelo seu endereço IP e você verá algo parecido com o exemplo a seguir:

    ![Testar a URL de imagens no gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Altere a URL para http://&lt;ip-address&gt;:8080/video/test.htm, substituindo &lt;ip-address&gt; pelo seu endereço IP e você verá algo parecido com o exemplo a seguir:

    ![Testar a URL de vídeo no gateway de aplicativo](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como

> [!div class="checklist"]
> * Criar um Gateway de Aplicativo
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar um ouvinte de back-end
> * Criar uma regra de roteamento baseada em caminho

Para saber mais sobre os gateways de aplicativo e seus recursos associados, continue para os artigos de instrução.
