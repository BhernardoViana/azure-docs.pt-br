---
title: Cenário de aplicativo de página única JavaScript – plataforma Microsoft Identity | Azure
description: Saiba como criar um aplicativo de página única (visão geral do cenário) usando a plataforma de identidade da Microsoft.
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ab7b0f1e59504eae64422fd4bbaf6f9e0804ed4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919810"
---
# <a name="scenario-single-page-application"></a>Cenário: aplicativo de página única

Saiba tudo o que você precisa para criar um aplicativo de página única (SPA).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Você pode criar seu primeiro aplicativo seguindo o início rápido de SPA do JavaScript:

> [!div class="nextstepaction"]
> [Início rápido: aplicativo de página única](./quickstart-v2-javascript.md)

## <a name="overview"></a>Visão Geral

Muitos aplicativos Web modernos são criados como aplicativos de página única do lado do cliente. Os desenvolvedores os gravam usando JavaScript ou uma estrutura SPA como angular, Vue. js e reagir. js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web tradicionais do lado do servidor. 

A plataforma de identidade da Microsoft permite que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs da Web usando o [fluxo implícito do OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado e também os tokens de acesso necessários para chamar APIs protegidas.

![Aplicativos de página única](./media/scenarios/spa-app.svg)

Esse fluxo de autenticação não inclui cenários de aplicativos que usam estruturas JavaScript de plataforma cruzada, como o ar-nativo e o reajam, são nativos. Eles exigem mais recursos para interação com as plataformas nativas.

## <a name="specifics"></a>Especificações

Para habilitar esse cenário para seu aplicativo, você precisa de:

* Registro de aplicativo com Azure Active Directory (Azure AD). Esse registro envolve habilitar o fluxo implícito e definir um URI de redirecionamento para o qual os tokens são retornados.
* Configuração de aplicativo com as propriedades do aplicativo registrado, como ID do aplicativo.
* Usando a MSAL (biblioteca de autenticação da Microsoft) para fazer o fluxo de autenticação para entrar e adquirir tokens.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Registro do Aplicativo](scenario-spa-app-registration.md)
