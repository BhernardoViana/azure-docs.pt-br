---
title: Exemplo – Aplicar tag e seu valor padrão
description: Esta definição de política de exemplo acrescenta um nome e um valor de marca especificados se a marca não é fornecida.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: a9ad211ac1ef9889fcff9646f30fe306458c3538
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266640"
---
# <a name="sample---apply-tag-and-its-default-value"></a>Exemplo – Aplicar tag e seu valor padrão

Essa política acrescenta um nome e valor de marca especificados, se a marca não for indicada. Especifique o nome e o valor da marca a serem aplicados.

Você pode implantar essa política de exemplo usando:

- O [Portal do Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [CLI do Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Política de exemplo

### <a name="policy-definition"></a>Definição de política

A definição completa da política JSON composta, usada pela API REST, pelos botões “Implantar no Azure” e manualmente no portal.

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

> [!NOTE]
> Se criar manualmente uma política no portal, use as partes **properties.parameters** e **properties.policyRule** acima. Encapsular as duas seções junto com as chaves `{}` para tornar o JSON válido.

### <a name="policy-rules"></a>Regras de política

O JSON que define as regras da política, usado pela CLI do Azure e pelo Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parâmetros de política

O JSON que define os parâmetros da política, usado pela CLI do Azure e pelo Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json "Policy parameters (JSON)")]

|NOME |Type |Campo |DESCRIÇÃO |
|---|---|---|---|
|tagName |Cadeia de caracteres |marcas |Nome da marca, como costCenter|
|tagValue |Cadeia de caracteres |marcas |O valor da marca, tais como matriz|

Ao criar uma atribuição através do PowerShell ou da CLI do Azure, os valores do parâmetro podem ser passados como JSON através de uma cadeia de caracteres ou de um arquivo usando `-PolicyParameter` (PowerShell) ou `--params` (CLI do Azure).
O PowerShell também suporta `-PolicyParameterObject` que requer que seja passado uma tabela de hash de Nome/Valor para o cmdlet onde **Nome** é o nome do parâmetro e **Valor** é o valor único ou matriz de valores passados durante a atribuição.

Neste parâmetro de exemplo, um _tagName_ de **costCenter** e _tagValue_ de **headquarter** é definido.

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Portal do Azure

[![Implantar o exemplo de Política no Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)
[![Implantar o exemplo de Política no Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Implantar com o Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'apply-default-tag-value' -DisplayName 'Apply tag and its default value Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Remover com o Azure PowerShell

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Explicação do Azure PowerShell

Os scripts de implantação e remoção usam os comandos a seguir. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Cria uma nova definição do Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Obtém um único grupo de recursos. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Cria uma nova atribuição do Azure Policy. Neste exemplo, nós fornecemos uma definição, mas também pode ser usada uma iniciativa. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Remove uma atribuição existente do Azure Policy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Remove uma definição existente do Azure Policy. |

## <a name="azure-cli"></a>CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Remover com a CLI do Azure

Execute os comandos a seguir para remover a definição e a atribuição anteriores:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Explicação da CLI do Azure

| Comando | Observações |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Cria uma nova definição do Azure Policy. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Obtém um único grupo de recursos. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, nós fornecemos uma definição, mas também pode ser usada uma iniciativa. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Remove uma atribuição existente do Azure Policy. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Remove uma definição existente do Azure Policy. |

Há várias ferramentas que podem ser usadas para interagir com a API REST do Gerenciador de recursos, como [ARMClient](https://github.com/projectkudu/ARMClient) ou o PowerShell. Um exemplo de chamada da API REST com o PowerShell pode ser encontrado na seção **Aliases** da [Estrutura da definição de política](../concepts/definition-structure.md#aliases).

## <a name="rest-api"></a>API REST

### <a name="deploy-with-rest-api"></a>Implantar com a API REST

- Criar a definição de política (escopo da Assinatura). Use o JSON de [definição de política](#policy-definition) para o corpo da solicitação.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

- Criar a atribuição de política (escopo do Grupo de Recursos)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

  Use o exemplo de JSON a seguir para o corpo da solicitação:

  ```json
  {
      "properties": {
          "displayName": "Apply tag and its default value Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Remover com a API REST

- Remover a atribuição de política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

- Remover a definição de política

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Explicação da API REST

| Serviço | Agrupar | Operação | Observações |
|---|---|---|---|
| Gerenciamento de recursos | Definições de Política | [Criar](/rest/api/resources/policydefinitions/createorupdate) | Cria uma nova definição do Azure Policy em uma assinatura. Alternativa: [Criar no grupo de gerenciamento](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gerenciamento de recursos | Atribuições de Políticas | [Criar](/rest/api/resources/policyassignments/create) | Cria uma nova atribuição do Azure Policy. Neste exemplo, nós fornecemos uma definição, mas também pode ser usada uma iniciativa. |
| Gerenciamento de recursos | Atribuições de Políticas | [Excluir](/rest/api/resources/policyassignments/delete) | Remove uma atribuição existente do Azure Policy. |
| Gerenciamento de recursos | Definições de Política | [Excluir](/rest/api/resources/policydefinitions/delete) | Remove uma definição existente do Azure Policy. Alternativa: [Excluir no grupo de gerenciamento](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Próximas etapas

- Revisar os outros [exemplos do Azure Policy](index.md)
- Revisar a [estrutura de definição do Azure Policy](../concepts/definition-structure.md)