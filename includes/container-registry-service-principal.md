---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para criar uma entidade de serviço com acesso ao registro de contêiner, use o script a seguir. Atualize a variável `ACR_NAME` com o nome do registro de contêiner e, opcionalmente, o valor `--role` no comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para conceder permissões diferentes. Você precisa ter a [CLI do Azure](/cli/azure/install-azure-cli) instalada para usar esse script.

Depois de executar o script, anote a **ID** e a **senha** da entidade de serviço. Quando você tiver suas credenciais, poderá configurar os aplicativos e serviços para se autenticarem no registro de contêiner como a entidade de serviço.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Usar uma entidade de serviço existente

Para conceder acesso de registro a uma entidade de serviço existente, você precisa atribuir uma nova função à entidade de serviço. Assim como ocorre ao criar uma nova entidade de serviço, você pode conceder acesso de pull, push e pull e proprietário.

O script a seguir usa o comando [az role assignment create][az-role-assignment-create] para conceder permissões *pull* a uma entidade de serviço especificada na variável `SERVICE_PRINCIPAL_ID`. Ajuste o valor `--role` se você desejar conceder um nível diferente de acesso.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
