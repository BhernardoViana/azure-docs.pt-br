---
title: Criar um aplicativo de funções com armazenamento conectado – CLI do Azure
description: Amostra de script da CLI do Azure – Criar uma função do Azure que se conecta a um armazenamento Azure
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc
ms.openlocfilehash: 9c2a70066e673bfa164a58884891f29e220c7bd6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532828"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Criar um aplicativo de funções com uma conexão de conta de Armazenamento nomeada 

Este script de exemplo do Azure Functions cria um aplicativo de funções e conecta a função a uma conta de Armazenamento do Azure. A configuração do aplicativo criado que contém a conexão pode ser usada com um [gatilho ou associação de armazenamento](../functions-bindings-storage-blob.md). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se usar a CLI localmente, certifique-se de que você esteja executando a versão 2.0 ou uma versão posterior da CLI do Azure. Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e adiciona a cadeia de conexão de armazenamento para uma configuração de aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crie um grupo de recursos com local. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az storage account show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | Obtém a cadeia de conexão para a conta. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Define a cadeia de conexão como uma configuração de aplicativo no aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
