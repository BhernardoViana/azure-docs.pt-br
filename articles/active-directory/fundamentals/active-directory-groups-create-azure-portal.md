---
title: Criar um grupo para usuários no Azure Active Directory | Microsoft Docs
description: Como criar um grupo no Azure Active Directory e adicionar membros ao grupo
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/04/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3c71c9c49413045e3a730c10e90ea3c12648b4cb
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857714"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Criar um grupo e adicionar membros no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

Este artigo explica como criar e popular um novo grupo no Azure Active Directory. Use um grupo para executar tarefas de gerenciamento, como a atribuição de licenças ou permissões a vários usuários ou dispositivos de uma vez.

## <a name="how-do-i-create-a-group"></a>Como faço para criar um grupo?
1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Todos os serviços**, insira **Usuários e grupos** na caixa de texto e, em seguida, selecione **Enter**.

   ![Abrir o gerenciamento de usuários](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. Na folha **Usuários e grupos**, escolha **Todos os grupos**.

   ![Abrir a folha de grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. Na folha **Usuários e grupos – Todos os grupos**, selecione o comando **Adicionar**.

   ![Selecionando o comando Adicionar](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. Na folha **Grupo** , adicione um nome e uma descrição ao grupo.
6. Para selecionar os membros a serem adicionados ao grupo, selecione **Atribuído** na caixa **Tipo de associação** e, em seguida, selecione **Membros**. Para saber mais sobre como gerenciar a associação de um grupo dinamicamente, consulte [Usar atributos para criar regras avançadas para a associação de grupo](../users-groups-roles/groups-dynamic-membership.md).

   ![Selecionando membros para adicionar](./media/active-directory-groups-create-azure-portal/select-members.png)
7. Na folha **Membros**, escolha um ou mais usuários ou dispositivos para adicionar ao grupo e escolha o botão **Selecionar** na parte inferior da folha para adicioná-los ao grupo. A caixa **Usuário** filtra a exibição com base na correspondência de sua entrada com qualquer parte de um nome de usuário ou dispositivo. Caracteres curinga não são aceitos nessa caixa.
8. Quando terminar de adicionar membros ao grupo, selecione **Criar** na folha **Grupo**.    

   ![Criar a confirmação do grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Consultar grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](../users-groups-roles/groups-dynamic-membership.md)
