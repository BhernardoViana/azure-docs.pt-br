---
title: Exemplo – auditar o Transparent Data Encryption para o Banco de Dados SQL
description: Essa amostra de auditoria de política se o banco de dados SQL não tiver a criptografia de dados transparente ativada.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 98d86d721f028a32d65ab4d336cc5579a0845607
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244684"
---
# <a name="audit-sql-database-encryption"></a>Auditar a criptografia do banco de dados SQL

Essa política interna audita se o banco de dados SQL não tem a Transparent Data Encryption habilitada.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

Você pode implantar este modelo usando o [portal do Azure](#deploy-with-the-portal), com [PowerShell](#deploy-with-powershell) ou com o [CLI do Azure](#deploy-with-azure-cli). Para obter a política interna, use a ID `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Implantar com o portal

Ao atribuir uma política, selecione **Auditar o status da Transparent Data Encryption** nas definições internas disponíveis.

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o seguinte comando para remover a atribuição de política.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o seguinte comando para remover a atribuição de política.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Examine mais exemplos nos [exemplos do Azure Policy](index.md)