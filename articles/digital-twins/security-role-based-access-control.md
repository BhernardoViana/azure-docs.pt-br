---
title: Entender o controle de acesso baseado em função – gêmeos digital do Azure | Microsoft Docs
description: Saiba mais sobre o controle de acesso baseado em função e o gerenciamento de permissões no Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: efa19cdd1dd0827fd0e88f533b94f69bd7b99ca9
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307222"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Controle de acesso baseado em função em Gêmeos Digitais do Azure

O Azure digital gêmeos permite o controle de acesso preciso sobre dados, recursos e ações específicos em seu grafo espacial. Ele faz isso por meio de função granular e gerenciamento de permissões chamado de RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/) ). O RBAC consiste em _funções_ e _atribuições de funções_. Funções de identificam o nível de permissões. As atribuições de função associam uma função com um usuário ou dispositivo.

Usando o RBAC, pode ser concedida permissão para:

- Um usuário.
- Um dispositivo.
- Uma entidade de serviço.
- Uma função definida pelo usuário.
- Todos os usuários que pertencem a um domínio.
- Um locatário.

O grau de acesso também pode ser ajustado.

O RBAC é único, pois as permissões são herdadas no gráfico espacial.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Um desenvolvedor pode usar o RBAC para:

- Conceda ao usuário a capacidade de gerenciar dispositivos para um prédio inteiro ou apenas para uma sala ou andar específico.
- Conceda a um administrador acesso global a todos os nós de gráfico espacial para um gráfico inteiro ou apenas para uma seção do gráfico.
- Conceda um acesso de leitura de especialista em suporte ao gráfico, exceto para as chaves de acesso.
- Conceda a todos os membros de um domínio de acesso de leitura a todos os objetos de gráfico.

## <a name="rbac-best-practices"></a>Práticas recomendadas de RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de função

Uma definição de função é uma coleção de permissões e outros atributos que constituem uma função. Uma definição de função lista as operações permitidas, que incluem *CRIAR*, *LER*, *ATUALIZAR* e *EXCLUIR* que qualquer objeto com essa função pode executar. Ele também especifica a quais tipos de objeto as permissões se aplicam.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Para recuperar as definições completas para funções anteriores, consulte as sistema/funções de API.
> Saiba mais, lendo [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Tipos de identificador de objeto

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Saiba como conceder permissões à entidade de serviço, lendo [Criar e gerenciar atribuição de função](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

Os seguintes artigos de documentação de referência descrevem:

- Como [Consultar a ID de objeto de um usuário](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Como [Obter a ID de objeto para uma entidade de serviço](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Como [Recuperar a ID do objeto de um locatário do Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Atribuições de função

Uma atribuição de função do Azure Digital Twins associa um objeto, como um usuário ou um locatário dos Gêmeos Digitais do Azure, a uma função e um espaço. As permissões são concedidas a todos os objetos que pertencem a esse espaço. O espaço inclui todo o gráfico espacial abaixo dele.

Por exemplo, um usuário recebe uma atribuição de função com a função `DeviceInstaller` para o nó raiz de um gráfico espacial, que representa um edifício. O usuário pode então ler e atualizar dispositivos para esse nó e todos os outros espaços filhos no edifício.

Para conceder permissões a um destinatário, crie uma atribuição de função. Para revogar permissões, remova a atribuição de função.

>[!IMPORTANT]
> Saiba mais sobre atribuições de funções, lendo [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como criar e gerenciar atribuições de função dos Gêmeos Digitais do Azure, leia [Criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).

- Leia mais sobre o [RBAC para Azure](https://docs.microsoft.com/azure/role-based-access-control/).
