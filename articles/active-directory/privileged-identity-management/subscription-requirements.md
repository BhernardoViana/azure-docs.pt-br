---
title: Requisitos de licença para usar o Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Descreve os requisitos de licenciamento para uso do Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895108"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisitos de licença para usar o Privileged Identity Management

Para usar o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), um diretório precisa ter uma licença válida. Além disso, as licenças precisam ser atribuídas aos administradores e aos usuários relevantes. Este artigo descreve os requisitos de licença para usar o Privileged Identity Management.

## <a name="prerequisites"></a>Pré-requisitos

Para usar Privileged Identity Management, seu diretório deve ter uma das seguintes licenças pagas ou de avaliação:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Para obter mais informações, confira [O que é Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Quais usuários precisam ter licenças?

Cada administrador ou usuário que interage com ou recebe um benefício de Privileged Identity Management deve ter uma licença. Por exemplo:

- Administradores com funções do Azure AD gerenciadas usando o PIM
- Administradores com funções de recurso do Azure gerenciadas usando o PIM
- Administradores atribuídos à função Administrador com Função com Privilégios
- Usuários atribuídos como qualificados para funções do Azure AD gerenciadas usando o PIM
- Usuários que podem aprovar/rejeitar solicitações no PIM
- Usuários atribuídos a uma função de recurso do Azure com atribuições Just-In-Time ou diretas (por tempo limitado)  
- Usuários atribuídos a uma revisão de acesso
- Usuários que executam revisões de acesso

Para obter informações sobre como atribuir licenças a seus usos, confira [Atribuir ou remover licenças usando o portal do Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se uma licença Azure AD Premium P2, EMS E5 ou de avaliação expirar, Privileged Identity Management recursos não estarão mais disponíveis em seu diretório:

- Atribuições de função permanentes para funções do Azure AD não serão afetadas.
- O serviço de Privileged Identity Management no portal do Azure, bem como os cmdlets do API do Graph e as interfaces do PowerShell do Privileged Identity Management, não estarão mais disponíveis para que os usuários ativem funções privilegiadas, gerenciem o acesso privilegiado ou executem revisões de acesso de funções com privilégios.
- Atribuições de funções qualificadas de funções do Azure AD serão removidas, pois os usuários não poderão mais ativar funções privilegiadas.
- Todas as revisões de acesso em andamento das funções do Azure AD serão encerradas e Privileged Identity Management definições de configuração serão removidas.
- Privileged Identity Management não enviará mais emails sobre alterações de atribuição de função.

## <a name="next-steps"></a>Próximos passos

- [Implantar Privileged Identity Management](pim-deployment-plan.md)
- [Começar a usar o Privileged Identity Management](pim-getting-started.md)
- [Funções que você não pode gerenciar no Privileged Identity Management](pim-roles.md)
