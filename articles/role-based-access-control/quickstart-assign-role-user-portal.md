---
title: Tutorial – Permitir acesso a um usuário aos recursos do Azure usando o RBAC e o portal do Azure | Microsoft Docs
description: Saiba como permitir acesso a um usuário aos recursos do Azure usando o RBAC (controle de acesso baseado em função) no portal do Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 41f1c6dc8904f167f34ea72aeb9b3866504b7087
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341293"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Tutorial: Permitir acesso a um usuário aos recursos do Azure usando o RBAC e o portal do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira usada para gerenciar o acesso aos recursos no Azure. Neste tutorial, você concede acesso a um usuário para criar e gerenciar máquinas virtuais em um grupo de recursos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conceder acesso a um usuário em um escopo do grupo de recursos
> * Remover acesso

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em http://portal.azure.com.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha **Adicionar** para abrir a folha **Grupo de recursos**.

   ![Adicionar um novo grupo de recursos](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Para o **Nome do grupo de recursos**, insira **rbac-resource-group**.

1. Selecione uma assinatura e localização.

1. Escolha **Criar** para criar o grupo de recursos.

1. Escolha **Atualizar** para atualizar a lista de grupos de recursos.

   O novo grupo de recursos é exibido em sua lista de grupos de recursos.

   ![Lista de grupos de recursos](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, você cria uma atribuição de função.

1. Na lista de **Grupos de recursos**, escolha o novo grupo de recursos **rbac-resource-group**.

1. Clique em **Controle de acesso (IAM)**.

1. Escolha a aba **Atribuições de função** para ver a lista atual das atribuições de função.

   ![Folha Controle de acesso (IAM) para o grupo de recursos](./media/quickstart-assign-role-user-portal/access-control.png)

1. Escolha **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar painel de atribuição de função](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione você mesmo ou outro usuário.

1. Escolha **Salvar** para criar a atribuição de função.

   Após alguns instantes, a função Colaborador da Máquina Virtual é atribuída ao usuário no escopo do grupo de recursos rbac-resource-group.

   ![Atribuição de função Colaborador da Máquina Virtual](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você deve remover uma atribuição de função.

1. Na lista de atribuições de função, adicione uma marca de seleção ao lado do usuário com a função de Colaborador da Máquina Virtual.

1. Escolha **Remover**.

   ![Remover mensagem de atribuição de função](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Na mensagem de remoção de atribuição de função que aparece, escolha **Sim**.

## <a name="clean-up"></a>Limpar

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha **rbac-resource-group** para abrir o grupo de recursos.

1. Escolha **Excluir grupo de recursos** para excluir o grupo de recursos.

   ![Excluir grupo de recursos](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Na folha **Tem certeza de que deseja excluir**, digite o nome do grupo de recursos: **rbac-resource-group**.

1. Escolha **Excluir** para excluir o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Permitir acesso a um usuário aos recursos do Azure usando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)

