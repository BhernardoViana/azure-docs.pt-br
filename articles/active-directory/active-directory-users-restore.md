---
title: Restaurar ou remover permanentemente um usuário excluído recentemente no Azure Active Directory | Microsoft Docs
description: Como restaurar um usuário excluído, exibir usuários restauráveis ou excluir permanentemente um usuário no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/28/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 9eea61cc1b735c6505df87526cd526a9a4a6eed8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Restaurar um usuário excluído no Azure Active Directory

Este artigo contém instruções para restaurar ou excluir permanentemente um usuário excluído anteriormente. Quando você exclui um usuário no Azure AD (Azure Active Directory), o usuário excluído é mantido por 30 dias a partir da data de exclusão. Durante esse período, o usuário e suas propriedades podem ser restauradas. 

> [!wARNING]
> Após ser excluído permanentemente, o usuário não poderá ser restaurado.


## <a name="how-to-restore-a-recently-deleted-user"></a>Como restaurar um usuário excluído recentemente
Quando um usuário é excluído recentemente, todas as informações de diretório são preservadas. Se o usuário for restaurado, essas informações também são restauradas.

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **Usuários e grupos** &gt; **Todos os usuários**. 
2. Em **Mostrar**, filtrar a página para mostrar **Usuários excluídos recentemente**. 
3. Selecione um ou mais usuários excluídos recentemente.
4. Selecione **Restaurar usuário**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Como excluir permanentemente um usuário excluído recentemente

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **Usuários e grupos** &gt; **Todos os usuários**. 
2. Em **Mostrar**, filtrar a página para mostrar **Usuários excluídos recentemente**. 
3. Selecione um ou mais usuários excluídos recentemente.
4. Selecione **Excluir permanentemente**.

## <a name="required-permissions"></a>Permissões necessárias
As seguintes permissões são suficientes para restaurar um usuário.

Função  | Permissões 
--------- | ---------
Administradores de Empresa<p>Suporte de camada 1 do parceiro<p>Suporte de camada 2 do parceiro<p>Administrador da conta de usuário | Pode restaurar usuários excluídos 
Administradores de Empresa<p>Suporte de camada 1 do parceiro<p>Suporte de camada 2 do parceiro<p>Administrador da conta de usuário | Pode excluir permanentemente os usuários

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre gerenciamento de usuário do Azure Active Directory.

* [Início rápido: Adicionar ou excluir usuários do Azure Active Directory](add-users-azure-active-directory.md)
* [Gerenciar perfis de usuário](active-directory-users-profile-azure-portal.md)
* [Adicionar usuários convidados de outro diretório](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Atribuir um usuário a uma função no Azure AD](active-directory-users-assign-role-azure-portal.md)
