---
title: Usar uma identidade gerenciada atribuída pelo usuário da VM do Windows para acessar o Azure Resource Manager
description: Um tutorial que orienta você durante o processo de uso de uma identidade gerenciada atribuída pelo usuário em uma VM do Windows para acessar o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 4b84876db86f5930acd95a2e1a45055282fa37fa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622101"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo usuário em uma VM do Windows para acessar o Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Este tutorial explica como criar a identidade atribuída pelo usuário, atribuí-la a uma VM (Máquina Virtual) do Windows e então usar essa identidade para acessar a API do Azure Resource Manager. Identidade do Serviço Gerenciado são gerenciadas automaticamente pelo Azure. Elas permitem que a autenticação para serviços compatíveis com a autenticação do Azure AD, sem a necessidade de inserir as credenciais em seu código. 

Você aprenderá como:

> [!div class="checklist"]
> * Criar uma identidade gerenciada atribuída ao usuário
> * Atribuir sua identidade atribuída pelo usuário à sua VM do Windows
> * Conceder à identidade atribuída pelo usuário acesso a um Grupo de Recursos no Azure Resource Manager 
> * Obtenha um token de acesso usando a identidade atribuída pelo usuário e use-o para chamar o Azure Resource Manager 
> * Leia as propriedades de um grupo de recursos

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Entrar no portal do Azure](https://portal.azure.com)

- [Criar uma máquina virtual do Windows](/azure/virtual-machines/windows/quick-create-portal)

- Para executar as etapas necessárias de criação de recursos e gerenciamento de funções neste tutorial, sua conta precisa das permissões de "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos). Caso você precise de ajuda com a atribuição de função, consulte [Usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de assinatura do Azure](/azure/role-based-access-control/role-assignments-portal).
- Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Se estiver executando o PowerShell localmente, também será possível: 
    - Execute `Login-AzureRmAccount` para criar uma conexão com o Azure.
    - Instale a [versão mais recente do PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (talvez você precise fazer `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `AzureRM.ManagedServiceIdentity`).
    - Execute `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` para instalar a versão de pré-lançamento do módulo `AzureRM.ManagedServiceIdentity` para executar as operações de identidade atribuídas pelo usuário neste artigo.

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Uma identidade atribuída pelo usuário é criada como um recurso autônomo do Azure. Usando o [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), o Azure cria uma identidade em seu locatário do Azure AD que pode ser atribuído a uma ou mais instâncias de serviço do Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

A resposta contém detalhes para a identidade atribuída pelo usuário criada, semelhante ao exemplo a seguir. Anote os valores de `Id` e `ClientId` para a sua identidade atribuída pelo usuário, pois eles serão usados nas próximas etapas:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Atribuir a identidade atribuída pelo usuário a uma VM do Windows

Uma identidade atribuída pelo usuário pode ser usada por clientes em vários recursos do Azure. Use os comandos a seguir para atribuir a identidade atribuída pelo usuário a uma única VM. Use a propriedade `Id` retornada na etapa anterior para o valor do parâmetro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder à sua identidade atribuída pelo usuário acesso a um Grupo de Recursos no Azure Resource Manager 

Identidades gerenciadas para recursos do Azure fornecem identidades que seu código pode usar para solicitar tokens de acesso para autenticação em APIs de recursos que dão suporte à autenticação do Microsoft Azure AD. Neste tutorial, seu código acessará a API do Azure Resource Manager. 

Antes que seu código possa acessar a API, você precisa conceder o acesso de identidade a um recurso no Azure Resource Manager. Nesse caso, o grupo de recursos no qual a VM está contida. Atualize os valores de `<SUBSCRIPTION ID>` conforme apropriado para seu ambiente.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

A resposta contém detalhes para a atribuição de função criada, semelhante ao exemplo a seguir:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obter um token de acesso utilizando a identidade da VM e usá-la para chamar o Resource Manager 

Para o restante do tutorial, você trabalhará a partir da VM que criamos anteriormente.

1. Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

2. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**.

3. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows.

4. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota.

5. Usando o `Invoke-WebRequest` do PowerShell, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o Azure Resource Manager.  O valor `client_id` retorna na [criação da identidade gerenciada atribuída pelo usuário](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Leia as propriedades de um grupo de recursos

Use o token de acesso recuperado na etapa anterior para acessar o Azure Resource Manager e ler as propriedades do Grupo de Recursos ao qual você concedeu acesso para a identidade atribuída pelo usuário. Substituir <SUBSCRIPTION ID> com a id da assinatura do seu ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A resposta contém as informações de grupo de recursos específicas, semelhantes ao exemplo a seguir:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar uma identidade atribuída pelo usuário e a anexá-la a uma Máquina Virtual do Azure para acessar a API do Azure Resource Manager.  Para saber mais sobre o Azure Resource Manager, confira:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)