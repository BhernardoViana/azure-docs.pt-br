---
title: Guia de início rápido – criar um registro particular do Docker no Azure com o PowerShell
description: Aprenda rapidamente a criar um registro de contêiner particular do Docker com o PowerShell.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ae21fc7ab016071d075324bf813243cef58dcd04
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Início Rápido: criar um Registro de Contêiner do Azure usando o PowerShell

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância do Registro de Contêiner do Azure usando o PowerShell, o envio por push de uma imagem de contêiner para o registro e, por fim, a implantação do contêiner a partir do registro nas ACI (Instâncias de Contêiner do Azure).

Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que o configuram facilmente em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Crie uma instância do ACR usando o comando [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir o nome *myContainerRegistry007* é usado. Atualize-o para um valor exclusivo.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Fazer logon no ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Primeiro, use o comando [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) para obter as credenciais de administrador da instância do ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Depois, use o comando [logon do docker][docker-login] para fazer logon na instância do ACR.

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

O comando retorna `Login Succeeded` na conclusão. Você também poderá ver um aviso de segurança recomendando usar o parâmetro `--password-stdin`. Embora seu uso esteja fora do escopo deste artigo, é recomendável seguir essa prática recomendada. Consulte a referência do comando [docker login][docker-login] para ver mais informações.

## <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se necessário, execute o seguinte comando para efetuar pull de uma imagem pré-criada do Hub do Docker.

```powershell
docker pull microsoft/aci-helloworld
```

A imagem precisa ser marcada com o nome do servidor de logon do ACR. Use o comando [docker tag] [ docker-tag] para fazer isso.

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Por fim, use [docker push][docker-push] para enviar a imagem por push para o ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Implantar imagem nas ACI
Para implantar a imagem como uma instância de contêiner nas ACI (Instâncias de Contêiner do Azure), primeiro converta a credencial do registro em uma PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Para implantar a imagem de contêiner do registro de contêiner com 1 núcleo de CPU e 1 GB de memória, execute o seguinte comando:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Você deve obter uma resposta inicial do Azure Resource Manager com detalhes sobre o contêiner. Para monitorar o status do contêiner e verificar quando ele está em execução, repita o comando [Get-AzureRmContainerGroup] [ Get-AzureRmContainerGroup]. Isso deve levar menos de um minuto.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Saída de exemplo: `Succeeded`

## <a name="view-the-application"></a>Exibir o aplicativo
Depois que a implantação nas ACI for bem-sucedida, recupere o endereço IP público do contêiner com o comando [Get-AzureRmContainerGroup] [ Get-AzureRmContainerGroup]:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Saída de exemplo: `"13.72.74.222"`

Para ver o aplicativo em execução, navegue até o endereço IP público no seu navegador favorito. O resultado deve ser semelhante a esse:

![Aplicativo Olá, Mundo no navegador][qs-portal-15]

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] para remover o grupo de recursos, o Registro de Contêiner do Azure e todas as Instâncias de Contêiner do Azure.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com a CLI do Azure e iniciou uma instância dele nas Instâncias de Contêiner do Azure. Continue com o tutorial das Instâncias de Contêiner do Azure a fim de ver as ACI com mais detalhes.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
