---
title: Funções que você não pode gerenciar em PIM-Azure Active Directory | Microsoft Docs
description: Descreve as funções que não podem ser gerenciadas no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d66d433d9de537358777e54e3c7d5489c25c849b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804076"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Funções que não podem ser gerenciadas no PIM

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) permite que você gerencie todas as [funções do Azure ad](../users-groups-roles/directory-assign-admin-roles.md) e todas as [funções de recursos do Azure](../../role-based-access-control/built-in-roles.md). Essas funções também incluem as funções personalizadas anexadas a grupos de gerenciamento, assinaturas, grupos de recursos e recursos. No entanto, há algumas poucas funções que não podem ser gerenciadas. Este artigo descreve as funções que você não pode gerenciar no PIM.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador da assinatura clássica

Não é possível gerenciar as seguintes funções de administrador de assinatura clássica no PIM:

- Administrador da Conta
- Administrador de Serviços
- Coadministrador

Para saber mais sobre as funções administrador de assinatura clássica, confira o artigo [Funções de administrador de assinatura clássica, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>E as funções de administrador do Office 365?

As funções no Exchange Online ou no SharePoint Online, exceto de administrador do Exchange e de administrador do SharePoint, não são representadas no Azure AD e, portanto, não podem ser gerenciadas no PIM. Para saber mais sobre esses serviços do Office 365, confira [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> O administrador do SharePoint tem acesso administrativo ao SharePoint Online por meio do Centro de Administração do SharePoint Online e pode executar quase qualquer tarefa no SharePoint Online. Os usuários qualificados podem enfrentar atrasos usando essa função no SharePoint depois de ativar o PIM.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
