---
title: Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o RBAC (controle de acesso baseado em função) do Azure e o portal do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707858"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] este artigo descreve como atribuir funções usando o portal do Azure.

Se você precisar atribuir funções de administrador no Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Visão geral do controle de acesso (IAM)

O **iam (controle de acesso)** é a folha que você usa para atribuir funções. Ele também é conhecido como gerenciamento de identidade e acesso e aparece em vários locais na portal do Azure. O exemplo a seguir mostra um exemplo de folha de controle (IAM) de acesso para uma assinatura.

![Folha IAM (controle) de acesso para uma assinatura](./media/role-assignments-portal/access-control-subscription.png)

Para ser o mais eficaz com a folha de controle de acesso (IAM), ele ajuda se você pode responder às três perguntas a seguir ao tentar atribuir uma função:

1. **Quem precisa de acesso?**

    Quem se refere a um usuário, grupo, entidade de serviço ou identidade gerenciada. Isso também é chamado de *entidade de segurança*.

1. **De que função eles precisam?**

    As permissões são agrupadas em funções. Você pode selecionar em uma lista de várias [funções internas](built-in-roles.md) ou usar suas próprias funções personalizadas.

1. **Onde eles precisam de acesso?**

    Em que se refere ao conjunto de recursos ao qual o acesso se aplica. Onde pode ser um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um único recurso, como uma conta de armazenamento. Isso é chamado de *escopo*.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

Siga estas etapas para atribuir uma função em escopos diferentes.

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída a função no escopo selecionado.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um usuário como um administrador de uma assinatura

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](built-in-roles.md#owner) no escopo da assinatura. A função proprietário dá ao usuário acesso completo a todos os recursos na assinatura, incluindo a permissão para conceder acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique na assinatura em que você deseja adicionar uma atribuição de função.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um usuário. Se o usuário não estiver na lista, digite na caixa **Selecionar** para pesquisar no diretório os nomes de exibição e os endereços de email.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, o usuário é atribuído à função Proprietário no escopo da assinatura.

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC, para remover o acesso, você deve remover uma atribuição de função. Siga estas etapas para remover uma atribuição de função.

1. Abra **Controle de acesso (IAM)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

    As atribuições herdadas não podem ser removidas. Se você precisar remover uma atribuição de função herdada, precisará fazê-lo no escopo em que a atribuição de função foi criada. No **escopo** coluna, próximo a **(herdado)** há um link que leva você para o escopo em que essa função foi atribuída. Vá para o escopo listado a fim de remover a atribuição de função.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Próximos passos

- [Listar atribuições de função usando o RBAC do Azure e o portal do Azure](role-assignments-list-portal.md)
- [Tutorial: conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
- [Solução de problemas com o RBAC para recursos do Azure](troubleshooting.md)
- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)
