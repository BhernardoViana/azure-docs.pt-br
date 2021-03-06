---
title: Integrar um cliente no gerenciamento de recursos delegados do Azure
description: Saiba como integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos sejam acessados e gerenciados por meio de seu próprio locatário.
ms.date: 11/7/2019
ms.topic: conceptual
ms.openlocfilehash: 88e75b92ca09f3c6671fe4399c4672240f863f66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689269"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Integrar um cliente no gerenciamento de recursos delegados do Azure

Este artigo explica como você, como um provedor de serviços, pode integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos delegados (assinaturas e/ou grupos de recursos) sejam acessados e gerenciados por meio de seu próprio locatário do Azure Active Directory (Azure AD). Embora possamos nos referir aqui aos provedores de serviços e clientes, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Você pode repetir esse processo se estiver gerenciando recursos para vários clientes. Assim, quando um usuário autorizado entra no seu locatário, pode receber autorizado em vários escopos de locatários do cliente para executar operações de gerenciamento sem precisar entrar em cada locatário individual do cliente.

Você pode associar sua ID do MPN (Microsoft Partner Network) às assinaturas integradas para acompanhar o impacto nas participações dos clientes e receber reconhecimento. Para mais informações, veja [Vincular ID de parceiro a suas contas do Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Observe que você precisará executar essa associação em seu locatário do provedor de serviços.

> [!NOTE]
> Os clientes também podem ser integrados quando comprarem uma oferta de serviços gerenciados (pública ou privada) que você publicou no Azure Marketplace. Para mais informações, veja [Publicar ofertas de Serviços Gerenciados no Azure Marketplace](publish-managed-services-offers.md). Você também pode usar o processo de integração descrito aqui com uma oferta publicada no Azure Marketplace.

O processo de integração requer que as ações sejam executadas dentro do locatário do provedor de serviços e do locatário do cliente. Todas essas etapas são descritas neste artigo.

> [!IMPORTANT]
> No momento não é possível integrar uma assinatura (ou grupo de recursos em uma assinatura) no gerenciamento de recursos delegados do Azure se a assinatura usar o Azure Databricks. Da mesma forma, se uma assinatura for registrada para integração com o provedor de recursos **Microsoft.ManagedServices**, não será possível criar um espaço de trabalho no Databricks para essa assinatura neste momento.

## <a name="gather-tenant-and-subscription-details"></a>Reunir detalhes do locatário e da assinatura

Para integrar o locatário de um cliente, ele deve ter uma assinatura ativa do Azure. Você precisa saber do seguinte:

- A ID do locatário do provedor de serviços (em que você gerenciará os recursos do cliente)
- A ID do locatário do cliente (cujos recursos serão gerenciados pelo provedor de serviços)
- As IDs de cada assinatura específica no locatário do cliente que serão gerenciadas pelo provedor de serviços (ou que contém os grupos de recursos que o provedor de serviços vai gerenciar)

Se você ainda não tiver essas informações, pode recuperá-las com uma das maneiras a seguir.

### <a name="azure-portal"></a>Portal do Azure

Você pode ver a sua ID de locatário passando o mouse sobre o nome da sua conta no lado superior direito do portal do Azure ou selecionando **Mudar diretório**. Para selecionar e copiar sua ID de locatário, pesquise "Azure Active Directory" no portal, selecione **Propriedades** e copie o valor mostrado no campo **ID de diretório**. Para localizar a ID de uma assinatura, pesquise por "Assinaturas" e selecione a ID da assinatura apropriada.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Ao integrar uma assinatura (ou um ou mais grupos de recursos dentro de uma assinatura) usando o processo descrito aqui, o provedor de recursos **Microsoft.ManagedServices** será registrado nessa assinatura.

## <a name="define-roles-and-permissions"></a>Definir funções e permissões do administrador

Como provedor de serviços, talvez você queira executar várias tarefas para um único cliente, o que exige acesso diferente para escopos diferentes. Você pode definir quantas autorizações forem necessárias para atribuir [funções internas de RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) aos usuários em seu locatário.

Para facilitar o gerenciamento, é recomendável usar grupos de usuários do Azure AD para cada função, permitindo que você adicione ou remova usuários individuais do grupo em vez de atribuir as permissões diretamente a esse usuário. Também pode ser que você queira atribuir funções a uma entidade de serviço. Siga o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho. Para saber mais e obter recomendações sobre as funções compatíveis, confira [Locatários, usuários e funções em cenários do Azure Lighthouse](../concepts/tenants-users-roles.md).

Para definir autorizações, você precisará saber os valores das IDs de cada usuário, grupo de usuários ou entidade de serviço para os quais você queira conceder acesso. Você também precisa da ID de definição de função para cada função interna que deseja atribuir. Se ainda não tiver essas informações, você pode recuperá-las de uma das maneiras a seguir.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> É recomendável atribuir a [Função de Exclusão da Atribuição de Registro de Serviços Gerenciados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) ao integrar um cliente, assim os usuários em seu locatário podem [remover o acesso à delegação](#remove-access-to-a-delegation) mais tarde, se necessário. Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo do Azure Resource Manager

Para integrar seu cliente, você precisará criar um modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) para sua oferta com as seguintes informações. Os valores **mspOfferName** e **mspOfferDescription** estão visíveis para o cliente ao exibir detalhes da oferta na [página Provedores de serviço](view-manage-service-providers.md) do portal do Azure.

|Campo  |Definição  |
|---------|---------|
|**mspOfferName**     |Um nome que descreve essa definição. Esse valor é exibido para o cliente como o título da oferta.         |
|**mspOfferDescription**     |Uma breve descrição da sua oferta (por exemplo, "oferta de gerenciamento de VM da Contoso")      |
|**managedByTenantId**     |Sua ID de locatário         |
|**autorizações**     |Os valores de **principalId** dos usuários/grupos/SPNs do seu locatário, cada um com uma **principalIdDisplayName** para ajudar seu cliente a entender a finalidade da autorização, mapeados no valor interno de **roleDefinitionId** para especificar o nível de acesso         |

Para carregar a assinatura de um cliente, use o modelo apropriado do Resource Manager, fornecido em nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/), com um arquivo de parâmetros correspondentes que você modifica para corresponder à sua configuração e definir suas autorizações. Modelos separados são fornecidos caso você esteja integrando uma assinatura inteira, um grupo de recursos ou múltiplos grupos de recursos em uma assinatura. Também fornecemos um modelo que pode ser usado por clientes que compraram uma oferta de serviço gerenciado que você publicou no Azure Marketplace, se você preferir integrar suas assinaturas dessa maneira.

|Para fazer essa integração  |use este modelo do Azure Resource Manager  |e altere esse arquivo de parâmetros |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Múltiplos grupos de recursos em uma assinatura   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Assinatura (ao usar uma oferta publicada no Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> O processo descrito aqui requer uma implantação separada para cada assinatura a ser integrada. Também serão necessárias implantações separadas se você estiver integrando vários grupos de recursos em assinaturas diferentes. No entanto, a integração de múltiplos grupos de recursos em uma única assinatura pode ser feita em uma implantação.
>
> Implantações separadas também são necessárias para várias ofertas que estão sendo aplicadas à mesma assinatura (ou grupos de recursos dentro de uma assinatura). Cada oferta aplicada deve usar um **mspOfferName** diferente.

O exemplo a seguir mostra um arquivo **delegatedResourceManagement.parameters.json** modificado que será usado para integrar uma assinatura. Os arquivos de parâmetro do grupo de recursos, localizados na pasta [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management), são semelhantes, mas também incluem o parâmetro **rgName** para identificar os grupos de recursos específicos a integrar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
A última autorização no exemplo acima adiciona **principalId** com a função de Administrador de Acesso do Usuário (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ao atribuir essa função, você deve incluir a propriedade **delegatedRoleDefinitionIds** e uma ou mais funções internas. O usuário criado nessa autorização poderá atribuir essas funções internas às [identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), necessárias para [implantar políticas que possam ser corrigidas](deploy-policy-remediation.md). Nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implantar os modelos do Azure Resource Manager

Depois de atualizar o arquivo de parâmetros, o cliente deve implantar o modelo do Azure Resource Manager no locatário do cliente como uma implantação em nível de assinatura. Uma implantação separada é necessária para cada assinatura que você deseja integrar ao gerenciamento de recursos delegados do Azure (ou para cada assinatura que contenha grupos de recursos que você deseja integrar).

Como essa é uma implantação em nível de assinatura, ela não pode ser iniciada no portal do Azure. A implantação pode ser feita usando a CLI do PowerShell ou do Azure, como mostrado abaixo.

> [!IMPORTANT]
> A implantação deve ser feita por uma conta que não seja de convidado no locatário do cliente que tem a [função interna de Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) na assinatura que está sendo integrada (ou que contém os grupos de recursos a integrar). Para ver todos os usuários que podem delegar a assinatura, um usuário do locatário do cliente poderá selecionar a assinatura no portal do Azure, abrir o **IAM (Controle de acesso)** e [exibir todos os usuários com a função Proprietário](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Confirmar integração bem-sucedida

Quando uma assinatura de cliente estiver integrada com êxito ao gerenciamento de recursos delegados do Azure, os usuários no locatário do provedor de serviços poderão ver a assinatura e seus recursos (se tiverem recebido acesso a eles por meio do processo acima, seja individualmente ou como um membro de um grupo do Azure AD com as permissões apropriadas). Para confirmar, verifique se a assinatura é exibida de uma das maneiras a seguir.  

### <a name="azure-portal"></a>Portal do Azure

No locatário do provedor de serviços:

1. Navegue até a [página Meus clientes](view-manage-customers.md).
2. Selecione **Clientes**.
3. Confirme que você consegue ver as assinaturas com o nome da oferta fornecido no modelo do Resource Manager.

> [!IMPORTANT]
> Para ver a assinatura delegada em [Meus clientes](view-manage-customers.md), é necessário que a função [Leitor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (ou outra função interna que inclua acesso de Leitor) seja concedida aos usuários no locatário do provedor de serviços quando a assinatura for integrada ao gerenciamento de recursos delegados do Azure.

No locatário do cliente:

1. Navegue até a [página Provedores de serviços](view-manage-service-providers.md).
2. Selecione **Ofertas do provedor de serviços**.
3. Confirme que você consegue ver as assinaturas com o nome da oferta fornecido no modelo do Resource Manager.

> [!NOTE]
> Pode demorar alguns minutos depois da conclusão da implantação até que o portal do Azure reflita as atualizações.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Remover o acesso a uma delegação

Por padrão, um usuário no locatário do cliente que tenha as permissões apropriadas pode remover o acesso a recursos que foram delegados a um provedor de serviços na página [Provedores de serviços](view-manage-service-providers.md#add-or-remove-service-provider-offers) do portal do Azure.

Se você tiver incluído usuários com a [Função de Exclusão de Atribuição de Registro de Serviços Gerenciados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) ao integrar o cliente ao gerenciamento de recursos delegados do Azure, esses usuários em seu locatário também poderão remover a delegação. Caso você tenha feito isso, nenhum usuário no locatário do provedor de serviços poderá acessar os recursos que foram previamente delegados.

O exemplo a seguir mostra uma atribuição que concede a **Função de Exclusão de Atribuição de Registro de Serviços Gerenciados** que pode ser incluída em um arquivo de parâmetro:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Um usuário com essa permissão pode remover uma delegação de uma das maneiras a seguir.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
