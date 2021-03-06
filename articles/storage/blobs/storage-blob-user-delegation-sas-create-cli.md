---
title: Usar CLI do Azure para criar uma SAS de delegação de usuário para um contêiner ou BLOB
titleSuffix: Azure Storage
description: Saiba como criar uma SAS de delegação de usuário (versão prévia) com Azure Active Directory credenciais usando CLI do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 85f49799472c92770cc8a503a5a1be0b496387f7
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892542"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Criar uma SAS de delegação de usuário para um contêiner ou BLOB com a CLI do Azure (versão prévia)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (AD do Azure) para criar uma SAS de delegação de usuário para um contêiner ou BLOB com a CLI do Azure (versão prévia).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Instale a versão mais recente do CLI do Azure

Para usar o CLI do Azure para proteger uma SAS com as credenciais do Azure AD, primeiro verifique se você instalou a versão mais recente do CLI do Azure. Para obter mais informações sobre como instalar o CLI do Azure, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Entrar com as credenciais do Azure AD

Entre no CLI do Azure com suas credenciais do Azure AD. Para obter mais informações, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Atribuir permissões com o RBAC

Para criar uma SAS de delegação de usuário do Azure PowerShell, a conta do Azure AD usada para entrar no CLI do Azure deve ser atribuída a uma função que inclua a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . Essa permissão permite que a conta do Azure AD solicite a *chave de delegação do usuário*. A chave de delegação de usuário é usada para assinar a SAS de delegação de usuário. A função que fornece a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** deve ser atribuída no nível da conta de armazenamento, no grupo de recursos ou na assinatura.

Se você não tiver permissões suficientes para atribuir funções de RBAC a uma entidade de segurança do Azure AD, talvez seja necessário solicitar ao proprietário da conta ou ao administrador que atribua as permissões necessárias.

O exemplo a seguir atribui a função de **colaborador de dados de blob de armazenamento** , que inclui a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . A função tem o escopo no nível da conta de armazenamento.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obter mais informações sobre as funções internas que incluem a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** , consulte [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Usar as credenciais do Azure AD para proteger uma SAS

Quando você cria uma SAS de delegação de usuário com a CLI do Azure, a chave de delegação de usuário usada para assinar a SAS é criada implicitamente. A hora de início e a hora de expiração que você especifica para a SAS também são usadas como a hora de início e a hora de expiração da chave de delegação do usuário.

Como o intervalo máximo em que a chave de delegação de usuário é válida é de 7 dias a partir da data de início, você deve especificar um tempo de expiração para a SAS que está dentro de 7 dias da hora de início. A SAS é inválida depois que a chave de delegação do usuário expira, portanto, uma SAS com um tempo de expiração superior a 7 dias ainda será válida por sete dias.

Ao criar uma SAS de delegação de usuário, o `--auth-mode login` e `--as-user parameters` são necessários. Especifique o *logon* para o parâmetro `--auth-mode` para que as solicitações feitas ao armazenamento do Azure sejam autorizadas com suas credenciais do Azure AD. Especifique o parâmetro `--as-user` para indicar que a SAS retornada deve ser uma SAS de delegação de usuário.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Criar uma SAS de delegação de usuário para um contêiner

Para criar uma SAS de delegação de usuário para um contêiner com a CLI do Azure, chame o comando [AZ Storage container Generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) .

As permissões com suporte para uma SAS de delegação de usuário em um contêiner incluem adicionar, criar, excluir, listar, ler e gravar. As permissões podem ser especificadas isoladamente ou combinadas. Para obter mais informações sobre essas permissões, consulte [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas).

O exemplo a seguir retorna um token SAS de delegação de usuário para um contêiner. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

O token SAS de delegação de usuário retornado será semelhante a:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Criar uma SAS de delegação de usuário para um blob

Para criar uma SAS de delegação de usuário para um blob com o CLI do Azure, chame o comando [AZ Storage blob Generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) .

As permissões com suporte para uma SAS de delegação de usuário em um blob incluem adicionar, criar, excluir, ler e gravar. As permissões podem ser especificadas isoladamente ou combinadas. Para obter mais informações sobre essas permissões, consulte [criar uma SAS de delegação de usuário](/rest/api/storageservices/create-user-delegation-sas).

A sintaxe a seguir retorna uma SAS de delegação de usuário para um blob. O exemplo especifica o parâmetro `--full-uri`, que retorna o URI do blob com o token SAS anexado. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

O URI de SAS de delegação de usuário retornado será semelhante a:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Uma SAS de delegação de usuário não dá suporte à definição de permissões com uma política de acesso armazenada.

## <a name="revoke-a-user-delegation-sas"></a>Revogar uma SAS de delegação de usuário

Para revogar uma SAS de delegação de usuário do CLI do Azure, chame o comando [AZ Storage Account REVOKE-Delegation-Keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) . Esse comando revoga todas as chaves de delegação de usuário associadas à conta de armazenamento especificada. Todas as assinaturas de acesso compartilhado associadas a essas chaves são invalidadas.

Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Tanto a chave de delegação do usuário quanto as atribuições de função do RBAC são armazenadas em cache pelo armazenamento do Azure, portanto, pode haver um atraso entre quando você inicia o processo de revogação e quando uma SAS de delegação de usuário existente se torna inválida.

## <a name="next-steps"></a>Próximos passos

- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Operação de obtenção de chave de delegação de usuário](/rest/api/storageservices/get-user-delegation-key)
