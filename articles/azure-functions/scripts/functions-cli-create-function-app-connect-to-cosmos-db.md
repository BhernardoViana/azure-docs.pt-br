---
title: Criar um aplicativo de funções com Azure Cosmos DB – CLI do Azure
description: Exemplo de script da CLI do Azure – Criar uma Função do Azure que se conecta a um BD do Azure Cosmos
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: d881a5eb6132f95239418f032da51d002af01905
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532858"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar um Azure Function que se conecta a um Banco de Dados Cosmo do Azure

Este script de exemplo do Azure Functions cria um aplicativo de funções e conecta a função a um banco de dados do Azure Cosmos DB. A configuração de aplicativo criado que contém a conexão pode ser usada com um [Gatilho ou associação do Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se usar a CLI localmente, certifique-se de que você esteja executando a versão 2.0 ou uma versão posterior da CLI do Azure. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e adiciona uma chave de acesso e um ponto de extremidade de BD do Cosmos para configurações do aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Criar um grupo de recursos com local |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crie um banco de dados do Azure Cosmos DB. |
| [az cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Obtém a conexão da conta do banco de dados. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Obtém as chaves para o banco de dados. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Define a cadeia de conexão como uma configuração de aplicativo no aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).




