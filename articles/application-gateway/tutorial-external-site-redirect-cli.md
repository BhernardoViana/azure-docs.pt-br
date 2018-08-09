---
title: Criar um gateway de aplicativo com redirecionamento de tráfego externo – CLI do Azure | Microsoft Docs
description: Saiba como criar um gateway de aplicativo que redireciona o tráfego interno da Web ao pool adequado, usando a CLI do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: 819676f7815cda6d3d3f84037766d7134ac72eb7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431742"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Criar um gateway de aplicativo com redirecionamento externo usando a CLI do Azure

Você pode usar a CLI do Azure para configurar o [redirecionamento do tráfego da Web](application-gateway-multi-site-overview.md) ao criar um [gateway de aplicativo](application-gateway-introduction.md). Neste tutorial, você configura uma regra e um ouvinte que redirecionam para um site externo o tráfego da Web que chega no gateway de aplicativo.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Configurar a rede
> * Criar uma regra de redirecionamento e um ouvinte
> * Criar um Gateway de Aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Criar um grupo de recursos usando [az group create](/cli/azure/group#create).

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Criar a rede virtual denominada *myVNet* e a sub-rede denominada *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet#az-net). Crie o endereço IP público denominado *myAGPublicIPAddress* usando [az network public-ip create](/cli/azure/public-ip#az-network_public_ip_create). Esses recursos são usados para fornecer conectividade de rede ao gateway de aplicativo e seus recursos associados.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Você pode usar [az network application-gateway create](/cli/azure/application-gateway#create) para criar o gateway do aplicativo denominado *myAppGateway*. Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. O gateway de aplicativo é atribuído a *myAGSubnet* e *myPublicIPAddress* que você criou anteriormente. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 8080 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Pode levar vários minutos para o gateway de aplicativo ser criado. Depois de criar o gateway de aplicativo, você pode ver esses novos recursos:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra padrão de roteamento que está associada ao *appGatewayHttpListener*.

### <a name="add-the-redirection-configuration"></a>Adicionar a configuração de redirecionamento

Adicione a configuração de redirecionamento que envia o tráfego do gateway de aplicativo para o *bing.com* usando [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az-network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Adicionar um ouvinte e uma regra de roteamento

Um ouvinte é necessário para permitir ao gateway de aplicativo rotear o tráfego de maneira adequada. Crie o ouvinte usando [az network application-gateway http-listener create](/cli/azure/application-gateway#az-network_application_gateway_http_listener_create) com a porta de front-end criada com [az network application-gateway frontend-port create](/cli/azure/application-gateway#az-network_application_gateway_frontend_port_create). Uma regra é necessária para que o ouvinte saiba para onde enviar o tráfego de entrada. Crie uma regra básica, denominada *redirectRule*, usando [az network application-gateway rule create](/cli/azure/application-gateway#az-network_application_gateway_rule_create) com a configuração de redirecionamento.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Para obter o endereço IP público do gateway de aplicativo, você pode usar [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

Você verá o *bing.com* aparecer no seu navegador.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> * Configurar a rede
> * Criar uma regra de redirecionamento e um ouvinte
> * Criar um Gateway de Aplicativo

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com o gateway de aplicativo](./application-gateway-introduction.md)