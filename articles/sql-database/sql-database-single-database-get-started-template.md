---
title: 'Azure Resource Manager: Criar um banco de dados individual'
description: Crie um banco de dados individual no Banco de Dados SQL do Azure usando o modelo do Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 5d090add7bdb2c3ee08f4c186bd57d63f14ab113
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422561"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Início Rápido: Crie um banco de dados individual no Banco de Dados SQL do Azure usando o modelo do Azure Resource Manager

A criação de um [banco de dados individual](sql-database-single-database.md) é a opção de implantação mais rápida e simples para criação de um Banco de Dados SQL do Azure. Este início rápido mostra como criar um banco de dados individual usando o modelo do Azure Resource Manager. Para saber mais, confira [Azure Resource Manager documentation](/azure/azure-resource-manager/) (Documentação do Azure Resource Manager).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Criar um banco de dados individual

Um banco de dados individual tem um conjunto definido de recursos de computação, memória, E/S e armazenamento usando um dos dois [modelos de compra](sql-database-purchase-models.md). Quando você cria um banco de dados individual, você também define um [servidor do Banco de Dados SQL](sql-database-servers.md) para gerenciá-lo e colocá-lo no [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) em uma região especificada.

O seguinte arquivo JSON é o modelo usado neste artigo. O modelo está armazenado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Mais exemplos do modelo do banco de dados SQL do Azure podem ser encontrados nos [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

Selecione **Experimentar** no seguinte bloco de código do PowerShell para abrir o Azure Cloud Shell.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'adminUser=' + $adminUser \
                 'adminPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="query-the-database"></a>Consultar o banco de dados

Para consultar o banco de dados, confira [Consultar o banco de dados](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Limpar recursos

Mantenha esse grupo de recursos, o servidor de banco de dados e o banco de dados individual caso deseje ir para as [Próximas etapas](#next-steps). As próximas etapas mostram como se conectar e consultar seu banco de dados usando diferentes métodos.

Para excluir o grupo de recursos:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Próximas etapas

- Crie uma regra de firewall no nível do servidor para se conectar ao banco de dados individual por meio de ferramentas locais ou remotas. Para obter mais informações, consulte [Criar uma regra de firewall no nível do servidor](sql-database-server-level-firewall-rule.md).
- Depois de criar uma regra de firewall no nível do servidor, [conecte-se e consulte](sql-database-connect-query.md) seu banco de dados usando várias ferramentas e linguagens diferentes.
  - [Conectar e consultar usando o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar um banco de dados individual usando a CLI do Azure, confira [Amostras da CLI do Azure](sql-database-cli-samples.md).
- Para criar um banco de dados individual usando o Azure PowerShell, confira [Amostras do Azure PowerShell](sql-database-powershell-samples.md).
