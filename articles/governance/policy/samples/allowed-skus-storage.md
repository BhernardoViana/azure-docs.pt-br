---
title: Exemplo – SKUs de VM e de conta de armazenamento permitidas
description: Esta definição de política de exemplo exige que as contas de armazenamento e as máquinas virtuais usem SKUs aprovados.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: fa66dde7c8b6656bb78a7b27ab72cad157029d3b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74071830"
---
# <a name="sample---allowed-skus-for-storage-accounts-and-virtual-machines"></a>Exemplo – SKUs permitidas para contas de armazenamento e máquinas virtuais

Essa política requer que contas de armazenamento e máquinas virtuais usem SKUs aprovados. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../../policy-templates/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.json "Allowed SKUs for Storage Accounts and Virtual Machines")]

É possível implantar este modelo usando o [Portal do Azure](#deploy-with-the-portal) ou com o [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Implantar com o portal

[![Implantar o exemplo de Política no Azure](https://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

$policyset= New-AzPolicySetDefinition -Name "skus-for-multiple-types" -DisplayName "Allowed SKUs for Storage Accounts and Virtual Machines" -Description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" -PolicyDefinition $policydefinitions -Parameter $policysetparameters 
 
New-AzPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentName> -Scope <scope>  -LISTOFALLOWEDSKUS_1 <VM SKUs> -LISTOFALLOWEDSKUS_2 <Storage Account SKUs>
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o comando a seguir para remover a definição e a atribuição de política.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name <assignmentName>
Remove-AzPolicySetDefinitions -Name "skus-for-multiple-types"
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy set-definition create --name "skus-for-multiple-types" --display-name "Allowed SKUs for Storage Accounts and Virtual Machines" --description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" --definitions "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json" --params "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

az policy assignment create --name <assignmentName> --scope <scope> --policy-set-definition "skus-for-multiple-types" --params "{ 'LISTOFALLOWEDSKUS_1': { 'value': <VM SKU Array> }, 'LISTOFALLOWEDSKUS_2': { 'value': <Storage Account SKU Array> } }"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o comando a seguir para remover a definição e a atribuição de política.

```azurecli-interactive
az policy assignment delete --name <assignmentName>
az policy set-definition delete --name "skus-for-multiple-types"
```

## <a name="next-steps"></a>Próximas etapas

- Examine mais exemplos nos [exemplos do Azure Policy](index.md)