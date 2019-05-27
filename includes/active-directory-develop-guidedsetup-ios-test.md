---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: 4a898aab9b1eab24b60337428128083255c32b7a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967600"
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testar a consulta da API do Microsoft Graph de seu aplicativo iOS

Para executar o código no simulador, pressione **Comando** + **R**.

![Testar o aplicação no simulador](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

Quando estiver pronto para testar, selecione **Chamar a API do Microsoft Graph**. Quando solicitado, digite seu nome de usuário e senha.

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez em que entrar no seu aplicativo, você também será solicitado a fornecer o consentimento para permitir que o aplicativo acesse seu perfil e entrar no:

![Forneça sua autorização para acesso do aplicativo](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Depois de entrar, você deverá ver suas informações de perfil de usuário retornadas pela chamada da API do Microsoft Graph na seção de **Log**.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo **user.read** para ler o perfil do usuário. Este escopo é automaticamente adicionado por padrão em cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo **Calendars.Read** para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada **Calendars.Read** às informações de registro do aplicativo. Em seguida, adicione o escopo **Calendars.Read** à chamada **acquireTokenSilent**.

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
