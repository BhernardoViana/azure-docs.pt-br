---
title: Mover o aplicativo de página única para produção-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única (mover para produção)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba41b29cfcea2d9dfcab2cd552040eaffa4e90ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965272"
---
# <a name="single-page-application-move-to-production"></a>Aplicativo de página única: mover para produção

Agora que você sabe como adquirir um token para chamar APIs da Web, saiba como mover para produção.

## <a name="improve-your-app"></a>Aprimore seu aplicativo

[Habilite o registro em log](msal-logging.md) para tornar a produção do aplicativo pronta.

## <a name="test-your-integration"></a>Testar sua integração

Teste sua integração seguindo a [lista de verificação de integração da plataforma de identidade da Microsoft](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Próximos passos

Aprofunde-se no exemplo de início rápido, que explica o código de como conectar usuários e obter um token de acesso para chamar a API de Microsoft Graph usando o MSAL. js:

> [!div class="nextstepaction"]
> [Tutorial do SPA do JavaScript](./tutorial-v2-javascript-spa.md)

Exemplo que demonstra como obter tokens para sua própria API Web de back-end usando MSAL. js:

> [!div class="nextstepaction"]
> [SPA com um back-end ASP.NET](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

Exemplo que mostra como usar MSAL. js para conectar usuários em um aplicativo que está registrado com Azure Active Directory B2C (Azure AD B2C):

> [!div class="nextstepaction"]
> [SPA com Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
