---
title: Ocultar um aplicativo da experiência do usuário no Azure Active Directory | Microsoft Docs
description: Como ocultar um aplicativo da experiência do usuário em painéis de acesso do Microsoft Azure Active Directory ou inicializadores do Office 365.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368914"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Ocultar um aplicativo da experiência do usuário no Azure Active Directory

Se você tiver um aplicativo que você não deseje que apareça nos painéis de acesso dos usuários nem em iniciadores do Office 365, há opções para ocultar esse bloco de aplicativo.  As duas opções a seguir estão disponíveis para ocultar aplicativos dos iniciadores de aplicativo do usuário.

- Ocultar um aplicativo de terceiros dos painéis de acesso do usuário e iniciadores de aplicativo do Office 365
- Ocultar todos os aplicativos do Office 365 dos painéis de acesso do usuário

Ao ocultar o aplicativo, os usuários ainda têm permissões para o aplicativo, mas não os verão no inicializador de aplicativo. Você deve ter as permissões apropriadas para gerenciar o aplicativo empresarial, além de ser um administrador global do diretório.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Ocultando um aplicativo com experiências do usuário final
Você pode usar as etapas a seguir, dependendo da sua situação, para ocultar aplicativos do painel de acesso.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Como fazer para ocultar um aplicativo de terceiros do painel de acesso do usuário e dos inicializadores de aplicativo do O365?
Use as seguintes etapas para ocultar um aplicativo do painel de acesso de um usuário e dos inicializadores de aplicativo do Office 365.

1.  Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2.  Selecione **Todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3.  Na tela **Azure Active Directory – *nomedodiretório*** (ou seja, a tela do Azure AD para o diretório que você está gerenciando), escolha **Aplicativos Empresariais**.
![Aplicativos empresariais](./media/hide-application-from-user-portal/app1.png)
4.  Na tela **Aplicativos empresariais**, escolha **Todos os aplicativos**. Você verá uma lista dos aplicativos que pode gerenciar.
5.  Na tela **Aplicativos empresariais – todos os aplicativos**, selecione um aplicativo.</br>
![Aplicativos empresariais](./media/hide-application-from-user-portal/app2.png)
6.  Na tela ***nomedoaplicativo*** (ou seja, a tela com o nome do aplicativo escolhido no título), selecione Propriedades.
7.  Na tela ***nomedoaplicativo* – propriedades**, selecione **Sim** para **Visível para os usuários?**.
![Aplicativos empresariais](./media/hide-application-from-user-portal/app3.png)
8.  Escolha o comando **Salvar** .

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Como fazer para ocultar aplicativos do Office 365 dos painéis de acesso do usuário?

Use as seguintes etapas para ocultar todos os aplicativos do Office 365 do painel de acesso. Esses aplicativos ainda estarão visíveis no portal do Office 365.

1.  Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2.  Selecione **Todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3.  Na tela **Azure Active Directory – *nomedodiretório*** (ou seja, a tela do Azure AD para o diretório que você está gerenciando), escolha **Configurações de usuário**.
4.  Na tela **Configurações de usuário**, em **Aplicativos empresariais**, selecione **Sim** em **Os usuários só poderão ver os aplicativos do Office 365 no portal do Office 365**.

![Aplicativos corporativos](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](remove-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](change-name-or-logo-portal.md)

