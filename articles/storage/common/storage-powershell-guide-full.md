---
title: Usando o Azure PowerShell com o Armazenamento do Azure | Microsoft Docs
description: Saiba como usar os cmdlets do Azure PowerShell para o Armazenamento do Azure.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: rogarana
ms.openlocfilehash: ae1c6628492de208f7c11e0b4e9d3133774ae184
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="using-azure-powershell-with-azure-storage"></a>Usando o PowerShell do Azure com o Armazenamento do Azure

O Azure PowerShell é usado para criar e gerenciar recursos da linha de comando do PowerShell ou em scripts. No Armazenamento do Azure, esses cmdlets se enquadram em duas categorias: o plano de controle e o plano de dados. Os cmdlets do plano de controle são usados para gerenciar a conta de armazenamento, a fim de criá-las, definir propriedades, excluí-las, girar as chaves de acesso e assim por diante. Os cmdlets do plano de dados são usados para gerenciar os dados armazenados *na* conta de armazenamento. Por exemplo, carregar blobs, criar compartilhamentos de arquivos e adicionar mensagens a uma fila.

Este artigo de instruções abrange operações que usam os cmdlets do plano de gerenciamento para gerenciar contas de armazenamento. Você aprenderá como: 

> [!div class="checklist"]
> * Listar contas de armazenamento
> * Obter uma referência para uma conta de armazenamento existente
> * Criar uma conta de armazenamento 
> * Definir as propriedades da conta de armazenamento
> * Recuperar e regenerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento 
> * Habilitar a Análise do Armazenamento

Este artigo fornece links para vários outros artigos do PowerShell sobre Armazenamento, como a forma de habilitar e acessar o Storage Analytics, como usar os cmdlets do plano de dados e como acessar as nuvens independentes do Azure, como a Nuvem da China, a Nuvem da Alemanha e a Nuvem Governamental.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este exercício exige o módulo do Azure PowerShell da versão 4.4 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Neste exercício, é possível digitar os comandos em uma janela normal do PowerShell ou usar o [ISE (Ambiente de Script Integrado) do Windows PowerShell](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) e digitar os comandos em um editor e, em seguida, testar um ou mais comandos de cada vez conforme os exemplos são analisados. É possível realçar as linhas a serem executadas e clicar em Executar Selecionado para executar apenas esses comandos.

Para saber mais sobre as contas de armazenamento, consulte [Introdução ao Armazenamento](storage-introduction.md) e [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Listar as contas de armazenamento na assinatura

Execute o cmdlet [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) para recuperar a lista de contas de armazenamento na assinatura atual. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obtenha uma referência a uma conta de armazenamento

Em seguida, será necessária uma referência a uma conta de armazenamento. É possível criar uma nova conta de armazenamento ou obter uma referência a uma existente. A seção a seguir mostram os dois métodos. 

### <a name="use-an-existing-storage-account"></a>Usar uma conta de armazenamento existente 

Para recuperar uma conta de armazenamento existente, é necessário o nome do grupo de recursos e o nome da conta de armazenamento. Defina as variáveis para os dois campos e, em seguida, use o cmdlet [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount). 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Agora, você tem $storageAccount, que aponta para uma conta de armazenamento existente.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

O script a seguir mostra como criar uma conta de armazenamento de uso geral usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Depois de criar a conta, recupere seu contexto, que pode ser usado em comandos subsequentes em vez de especificar a autenticação com cada chamada.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

O script usa os seguintes cmdlets do PowerShell: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation): recupera uma lista dos locais válidos. O exemplo usa `eastus` como local.

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup): cria um novo grupo de recursos. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O nosso se chama `teststoragerg`. 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount): cria a conta de armazenamento real. O exemplo usa `testpshstorage`.

O nome da SKU indica o tipo de replicação da conta de armazenamento, como LRS (armazenamento com redundância local). Para saber mais sobre a replicação, consulte [Replicação do Armazenamento do Azure](storage-redundancy.md).

> [!IMPORTANT]
> O nome da conta de armazenamento deve ser exclusivo dentro do Azure e deve estar em minúsculas. Para saber mais sobre as convenções de nomenclatura, consulte [Nomenclatura e referência a contêineres, Blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Agora, você tem uma nova conta de armazenamento e uma referência a ela. 

## <a name="manage-the-storage-account"></a>Gerenciar a conta de armazenamento

Agora que você tem uma referência a uma nova conta de armazenamento ou uma existente, a seção a seguir mostra alguns dos comandos que podem ser usados para gerenciá-la.

### <a name="storage-account-properties"></a>Propriedades da conta de armazenamento

Para alterar as configurações de uma conta de armazenamento, use [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Embora não seja possível alterar o local de uma conta de armazenamento ou o grupo de recursos em que ela reside, é possível alterar muitas outras propriedades. A seguir estão listadas algumas das propriedades que podem ser alteradas usando o PowerShell.

* O **domínio personalizado** atribuído à conta de armazenamento.

* A **marca** atribuída à conta de armazenamento. Geralmente, as marcas são usadas para categorizar recursos para fins de cobrança.

* A **SKU** é a configuração de replicação da conta de armazenamento, como LRS para armazenamento com redundância local. Por exemplo, é possível alterar o \_LRS Padrão\_ para o \_GRS Padrão ou RAGRS Padrão. Observe que não é possível alterar o ZRS\_Standard ou o LRS\_Premium para outras SKUs ou vice-versa.

* A **camada de acesso** das contas de armazenamento de Blobs. O valor da camada de acesso está definido como **ativa** ou **moderada**, e permite reduzir custos selecionando a camada de acesso que se alinha com o uso que você faz da conta de armazenamento. Para saber mais, consulte [Camada de acesso aos arquivos ativa e moderada](../blobs/storage-blob-storage-tiers.md).

* Permitir somente o tráfego HTTPS. 

### <a name="manage-the-access-keys"></a>Gerenciar as chaves de acesso

Uma conta de armazenamento do Azure é fornecida com duas chaves de conta. Para recuperar as chaves, use [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Este exemplo recupera a primeira chave. Para recuperar a outra, use `Value[1]` em vez de `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Para regenerar a chave, use [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Para regenerar a outra chave, use `key2` como o nome da chave em vez de `key1`.

Regenere uma das chaves e, em seguida, recupere-a novamente para ver o novo valor.

> [!NOTE] 
> Planeje cuidadosamente antes de regenerar a chave para uma conta de armazenamento de produção. Regenerar uma ou ambas as chaves invalidará o acesso a qualquer aplicativo que use a chave regenerada. Para saber mais, veja [Regenerar chaves de acesso de armazenamento](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento 

Para excluir uma conta de armazenamento, use [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Ao excluir uma conta de armazenamento, todos os ativos armazenados também são excluídos. Se uma conta for excluída acidentalmente, ligue para o suporte imediatamente e abra um chamado para restaurar a conta de armazenamento. Não há garantia de recuperação de dados, mas, às vezes, ela funciona. Não crie uma nova conta de armazenamento com o mesmo nome da antiga até que o tíquete de suporte seja resolvido. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Proteger a conta de armazenamento usando VNets e firewalls

Por padrão, todas as contas de armazenamento são acessíveis por qualquer rede que tenha acesso à Internet. No entanto, é possível configurar regras de rede para permitir que somente aplicativos de redes virtuais específicas acessem uma conta de armazenamento. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](storage-network-security.md). 

O artigo mostra como gerenciar essas configurações usando os seguintes cmdlets do PowerShell:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Usar a análise de armazenamento  

A [Análise de Armazenamento do Azure](storage-analytics.md) é composta das [Métricas da Análise de Armazenamento](/rest/api/storageservices/about-storage-analytics-metrics) e do [Registro em Log da Análise de Armazenamento](/rest/api/storageservices/about-storage-analytics-logging). 

As **Métricas da Análise de Armazenamento** são usadas para coletar métricas para contas de armazenamento do Azure que podem ser usadas para monitorar a integridade de uma conta de armazenamento. As métricas podem ser habilitadas para blobs, arquivos, tabelas e filas.

O **Registro em Log da Análise de Armazenamento** ocorre no lado do servidor e permite registrar detalhes de solicitações bem-sucedidas e com falhas na conta de armazenamento. Esses registros permitem ver detalhes de operações de leitura, gravação e exclusão em suas tabelas, filas e blobs bem como as razões para solicitações com falha. O registro em log não está disponível para os Arquivos do Azure.

É possível configurar o monitoramento usando o [Portal do Azure](https://portal.azure.com), o Windows PowerShell ou de forma programática usando a biblioteca de clientes de armazenamento. 

> [!NOTE]
> É possível habilitar a análise de minuto usando o PowerShell. Essa funcionalidade não está disponível no portal.
>

* Para saber como habilitar e exibir dados de Métricas de Armazenamento usando o PowerShell, consulte [Como habilitar métricas do Armazenamento do Azure usando o PowerShell](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Para saber como habilitar e recuperar dados de log de armazenamento usando o PowerShell, confira [How to enable Storage Logging using PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) (Como habilitar o log de armazenamento usando o PowerShell) e [Finding your Storage Logging log data](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data) (Localizando os dados do log de armazenamento).

* Para obter informações detalhadas sobre o uso de métricas de armazenamento e do armazenamento de log para solucionar problemas de armazenamento, consulte [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Gerenciar os dados na conta de armazenamento

Agora que você sabe como gerenciar sua conta de armazenamento com o PowerShell, poderá usar os artigos a seguir para aprender a acessar os objetos de dados na conta de armazenamento.

* [Como gerenciar blobs com o PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Como gerenciar arquivos com o PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Como gerenciar filas com o PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Executar operações de armazenamento de Tabelas do Azure com o PowerShell](../../cosmos-db/table-storage-how-to-use-powershell.md)

A API de Tabela do Azure Cosmos DB fornece recursos premium para o armazenamento de tabelas, como distribuição global turnkey, leituras e gravações de baixa latência, indexação secundária automática e taxa de transferência dedicada. 

* Para obter mais informações, consulte o [API de Tabela do Azure Cosmos DB](../../cosmos-db/table-introduction.md). 
* Para saber como usar o PowerShell para realizar operações de API de tabela do Azure Cosmos DB, consulte [Realizar operações de API de tabela do Azure Cosmos DB com o PowerShell](../../cosmos-db/table-powershell.md).

## <a name="independent-cloud-deployments-of-azure"></a>Implantações de nuvem independentes do Azure

A maioria das pessoas usa a Nuvem Pública do Azure em suas implantações globais do Azure. Há também algumas implantações independentes do Microsoft Azure por motivos de soberania e assim por diante. Essas implantações independentes são chamadas de “ambientes”. Estes são os ambientes disponíveis:

* [Nuvem do Azure Governamental](https://azure.microsoft.com/features/gov/)
* [Nuvem do Azure na China operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem Alemã do Azure](../../germany/germany-welcome.md)

Para saber mais sobre como acessar essas nuvens e seu armazenamento com o PowerShell, veja [Gerenciamento de Armazenamento nas nuvens independentes do Azure usando o PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e uma conta de armazenamento para este exercício, você poderá remover todos os ativos que criou ao remover o grupo de recursos. Isso também exclui todos os recursos contidos no grupo. Nesse caso, ele remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Próximas etapas

Este artigo de instruções abrange operações que usam os cmdlets do plano de gerenciamento para gerenciar contas de armazenamento. Você aprendeu como: 

> [!div class="checklist"]
> * Listar contas de armazenamento
> * Obter uma referência para uma conta de armazenamento existente
> * Criar uma conta de armazenamento 
> * Definir as propriedades da conta de armazenamento
> * Recuperar e regenerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento 
> * Habilitar a Análise do Armazenamento

Este artigo fornece também referências a vários outros artigos, relacionados a como gerenciar os objetos de dados, como habilitar o Storage Analytics e como acessar as nuvens independentes do Azure, como a Nuvem da China, a Nuvem da Alemanha e a Nuvem Governamental. Veja mais artigos e recursos relacionados para referência:

* [Cmdlets do PowerShell do plano de controle do Armazenamento do Azure](/powershell/module/AzureRM.Storage/)
* [Cmdlets do PowerShell do plano de dados do Armazenamento do Azure](/powershell/module/azure.storage/)
* [Referência do Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)
