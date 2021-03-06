---
title: Modelos de Azure Resource Manager para Azure Cosmos DB API para MongoDB
description: Use modelos de Azure Resource Manager para criar e configurar a API de Azure Cosmos DB para MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: e0d28de67082c4b4d582f62653ad474e9ad5dadd
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960573"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerenciar Azure Cosmos DB recursos de API do MongoDB usando modelos de Azure Resource Manager

Este artigo descreve como executar operações diferentes para automatizar o gerenciamento de suas contas de Azure Cosmos DB, bancos de dados e contêineres usando modelos de Azure Resource Manager. Este artigo tem exemplos para a API de Azure Cosmos DB somente para MongoDB, para encontrar exemplos para outras contas de tipo de API, consulte: usar modelos de Azure Resource Manager com a API de Azure Cosmos DB para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), artigos de [tabela](manage-table-with-resource-manager.md) .

## Criar Azure Cosmos DB API para a conta, banco de dados e coleção do MongoDB<a id="create-resource"></a>

Crie Azure Cosmos DB recursos usando um modelo de Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para a API do MongoDB com duas coleções que compartilham a taxa de transferência de 400 RU/s no nível do banco de dados. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!NOTE]
> Os nomes de conta devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar RU/s, envie novamente o modelo com valores de propriedade de produtividade atualizados.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-the-azure-cli"></a>Implantar por meio do CLI do Azure

Para implantar o modelo de Azure Resource Manager usando o CLI do Azure, **Copie** o script e selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O comando `az cosmosdb show` mostra a conta recém-criada do Azure Cosmos depois que ela é provisionada. Se você optar por usar uma versão instalada localmente do CLI do Azure em vez de usar Cloud Shell, consulte o artigo [CLI do Azure](/cli/azure/) .

## <a name="next-steps"></a>Próximas etapas

Estes são alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)