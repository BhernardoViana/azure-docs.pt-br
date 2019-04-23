---
title: Migrar uma política clássica que exige autenticação multifator no portal do Azure
description: Este artigo mostra como migrar uma política clássica que exige autenticação multifator no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6661cee8ba6176bd706d31a10a8f20549e29e4d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795042"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrar uma política clássica que exige autenticação multifator no portal do Azure

Este tutorial mostra como migrar uma política clássica que exige **autenticação multifator** para um aplicativo de nuvem. Embora não seja um pré-requisito, recomendamos que você leia [Migrar políticas clássicas no portal do Azure](policy-migration.md) antes de iniciar a migração de suas políticas clássicas.

## <a name="overview"></a>Visão geral

O cenário neste artigo mostra como migrar uma política clássica que exige **autenticação multifator** para um aplicativo de nuvem.

![Azure Active Directory](./media/policy-migration/33.png)

O processo de migração consiste nas seguintes etapas:

1. [Abra a política clássica](#open-a-classic-policy) para obter as definições de configuração.
1. Crie uma nova política de acesso condicional do Azure AD para substituir a política clássica. 
1. Desabilite a política clássica.

## <a name="open-a-classic-policy"></a>Abrir uma política clássica

1. No [portal do Azure](https://portal.azure.com), na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

   ![Acesso condicional](./media/policy-migration-mfa/02.png)

1. Na seção **Gerenciar**, clique em **Políticas clássicas (visualização)**.

   ![Políticas clássicas](./media/policy-migration-mfa/12.png)

1. Na lista de políticas clássicas, clique na política que exige **autenticação multifator** para um aplicativo de nuvem.

   ![Políticas clássicas](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional

1. No [portal do Azure](https://portal.azure.com), na barra de navegação à esquerda, clique em **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em **Acesso condicional**.

   ![Acesso condicional](./media/policy-migration/02.png)

1. Na página **Acesso condicional**, para abrir a página **Novo**, na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![Acesso condicional](./media/policy-migration/03.png)

1. Na página **Novo**, na caixa de texto **Nome**, digite um nome para a política.

   ![Acesso condicional](./media/policy-migration/29.png)

1. Na seção **Atribuições**, clique em **Usuários e grupos**.

   ![Acesso condicional](./media/policy-migration/05.png)

   1. Se você tiver todos os usuários selecionados em sua política clássica, clique em **Todos os usuários**. 

   ![Acesso condicional](./media/policy-migration/35.png)

   1. Se você tiver todos os grupos selecionados em sua política clássica, clique em **Selecionar usuários e grupos** e, em seguida, selecione os usuários e grupos desejados.

   ![Acesso condicional](./media/policy-migration/36.png)

   1. Se você tiver os grupos excluídos, clique na guia **Excluir** e, em seguida, selecione os usuários e grupos necessários. 

   ![Acesso condicional](./media/policy-migration/37.png)

1. Na página **Novo**, para abrir a página **Aplicativos de nuvem**, na seção **Atribuição**, clique em **Aplicativos de nuvem**.

1. Na página **Aplicativos de nuvem**, execute as seguintes etapas:

   ![Acesso condicional](./media/policy-migration/08.png)

   1. Clique em **Selecionar aplicativos**.

   1. Clique em **Selecionar**.

   1. Na página **Selecionar**, selecione seu aplicativo de nuvem e, em seguida, clique em **Selecionar**.

   1. Na página **Aplicativos de nuvem**, clique em **Concluído**.

1. Se você tiver selecionado **Exigir autenticação multifator**:

   ![Acesso condicional](./media/policy-migration/26.png)

   1. Na seção **Controles de acesso**, clique em **Conceder**.

   ![Acesso condicional](./media/policy-migration/27.png)

   1. Na página **Conceder**, clique em **Conceder acesso** e, em seguida, clique em **Exigir autenticação multifator**.

   1. Clique em **Selecionar**.

1. Clique em **Ativar** para habilitar a sua política.

   ![Acesso condicional](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Desabilite a política clássica

Para desabilitar a sua política clássica, clique em **Desabilitar** no modo de exibição **Detalhes**.

![Políticas clássicas](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a migração de política clássica, consulte [Migrar políticas clássicas no portal do Azure](policy-migration.md).
- Se você quiser saber como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md).
