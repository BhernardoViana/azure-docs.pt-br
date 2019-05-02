---
title: Exemplo de Script da CLI do Azure – Implantar um aplicativo gerenciado | Microsoft Docs
description: Exemplo de Script da CLI do Azure – Implantar uma definição de aplicativo gerenciado | Microsoft Docs
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f09d9dcb60370c2cc51f5652def92e5ad9c9b512
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226354"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Implantar um aplicativo gerenciado no catálogo de serviços com a CLI do Azure

Este script implanta uma definição de aplicativo gerenciado do catálogo de serviços. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o comando a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | Crie um aplicativo gerenciado. Forneça a ID de definição e os parâmetros para o modelo. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
