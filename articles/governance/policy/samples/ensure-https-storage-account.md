---
title: Exemplo – Assegurar tráfego HTTPS somente para a conta de armazenamento
description: Este exemplo de política exige que contas de armazenamento usem tráfego HTTPS.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: fb708b016bb2372af71b564c502185623b1717eb
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56241629"
---
# <a name="ensure-https-traffic-only-for-storage-account"></a>Assegurar tráfego HTTPS somente para conta de armazenamento

Esta política exige que contas de armazenamento usem tráfego HTTPS.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../../policy-templates/samples/Storage/https-traffic-only/azurepolicy.json "Ensure https traffic only for storage account")]

Você pode implantar este modelo usando o [portal do Azure](#deploy-with-the-portal), com [PowerShell](#deploy-with-powershell) ou com o [CLI do Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implantar com o portal

[![Implantar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FStorage%2Fhttps-traffic-only%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "https-traffic-only" -DisplayName "Ensure https traffic only for storage account" -description "Ensure https traffic only for storage account" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'https-traffic-only' --display-name 'Ensure https traffic only for storage account' --description 'Ensure https traffic only for storage account' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/https-traffic-only/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "https-traffic-only"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

- Examine mais exemplos nos [exemplos do Azure Policy](index.md)