---
title: Suporte a AD FS no MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte a Serviços de Federação do Active Directory (AD FS) (AD FS) na MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b5124712e4002c9a3a6740986cafc18c12dbb98
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921951"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Suporte a Serviços de Federação do Active Directory (AD FS) no MSAL.NET
O Serviços de Federação do Active Directory (AD FS) (AD FS) no Windows Server permite adicionar o OpenID Connect e a autenticação e autorização baseadas em OAuth 2,0 a aplicativos que você está desenvolvendo. Esses aplicativos podem, então, autenticar os usuários diretamente no AD FS. Para obter mais informações, leia [AD FS cenários para desenvolvedores](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

A MSAL.NET (biblioteca de autenticação da Microsoft para .NET) dá suporte a dois cenários de autenticação no AD FS:

- MSAL.NET conversa com Azure Active Directory, que é *federado* com AD FS.
- MSAL.NET conversa **diretamente** com uma autoridade do ADFS. Isso só tem suporte do AD FS 2019 e superior. Um dos cenários aos quais este destaque é [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) suporte


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL conecta-se ao Azure AD, que é federado com AD FS
O MSAL.NET dá suporte à conexão com o Azure AD, que entra em usuários gerenciados (usuários gerenciados no Azure AD) ou usuários federados (usuários gerenciados por outro provedor de identidade, como o AD FS). O MSAL.NET não sabe sobre o fato de que os usuários são federados. No que diz respeito, ele conversa com o Azure AD.

A [autoridade](msal-client-application-configuration.md#authority) que você usa nesse caso é a autoridade usual (nome do host da autoridade + locatário, comum ou organizações).

### <a name="acquiring-a-token-interactively"></a>Adquirindo um token interativamente
Quando você chama o método `AcquireTokenInteractive`, a experiência do usuário normalmente é:

1. O usuário insere a ID da conta.
2. O Azure AD exibe brevemente a mensagem "levando você para a página da sua organização".
3. O usuário é redirecionado para a página de entrada do provedor de identidade. A página de entrada geralmente é personalizada com o logotipo da organização.

As versões AD FS com suporte nesse cenário federado são AD FS v2, AD FS v3 (Windows Server 2012 R2) e AD FS V4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Adquirindo um token usando AcquireTokenByIntegratedAuthentication ou AcquireTokenByUsernamePassword
Ao adquirir um token usando os métodos `AcquireTokenByIntegratedAuthentication` ou `AcquireTokenByUsernamePassword`, MSAL.NET obtém o provedor de identidade para entrar em contato com base no nome de usuário.  MSAL.NET recebe um [token SAML 1,1](reference-saml-tokens.md) depois de contatar o provedor de identidade.  Em seguida, MSAL.NET fornece o token SAML ao Azure AD como uma declaração de usuário (semelhante ao [fluxo em nome de](msal-authentication-flows.md#on-behalf-of)) para obter um JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL conecta-se diretamente a AD FS
O MSAL.NET dá suporte à conexão com o AD FS 2019, que é o Open ID Connect em conformidade e compreende PKCE e escopos. Esse suporte requer que um service pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) seja aplicado ao Windows Server. Ao se conectar diretamente ao AD FS, a autoridade que você deseja usar para criar seu aplicativo é semelhante à `https://mysite.contoso.com/adfs/`.

No momento, não há planos para dar suporte a uma conexão direta com:

- AD FS 16, pois ele não dá suporte a PKCE e ainda usa recursos, não escopo
- AD FS v2, que não é compatível com OIDC.

 Se você precisar dar suporte a cenários que exigem uma conexão direta com o AD FS 2016, use a versão mais recente da [biblioteca de autenticação Azure Active Directory](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Quando você tiver atualizado seu sistema local para AD FS 2019, poderá usar o MSAL.NET.

## <a name="next-steps"></a>Próximos passos

Para o caso federado, consulte [Configurar o comportamento de entrada Azure Active Directory para um aplicativo usando uma política de descoberta de realm inicial](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
