---
title: Criar um aplicativo de funções com a implantação do GitHub – CLI do Azure
description: Criar um aplicativo de funções e implantar o código de função de um repositório GitHub usando o Azure Functions.
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 37dc0235b258f6d47f8813546fe953e92799454a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532813"
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Criar um aplicativo de funções no Azure que é implantado no GitHub

Este script de exemplo do Azure Functions cria um aplicativo de funções usando o [plano de consumo](../functions-scale.md#consumption-plan), junto com seus recursos relacionados. O script também configura o código de função para implantação contínua de um repositório do GitHub. 

Neste exemplo, você precisa de:

* Um repositório do GitHub com um código de funções, para o qual você tem permissões administrativas.
* Um [PAT (Token de Acesso Pessoal)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) para sua conta do GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você preferir usar a CLI do Azure localmente, será necessário usar a versão 2.0 ou uma versão posterior. Para determinar a versão da CLI do Azure, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e implanta o código da função do GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando na tabela redireciona para a documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para o aplicativo de funções. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções sem o servidor [plano de consumo](../functions-scale.md#consumption-plan) e a associa a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
