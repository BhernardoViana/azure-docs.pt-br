---
title: Configurar a inscrição e a entrada com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e23c79b1e09f3e3a7aaa21b9257bfe6bd43f7e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950455"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para usar uma conta do Google como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Google, poderá se inscrever em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Entre no [Console de Desenvolvedores do Google](https://console.developers.google.com/) com suas credenciais de conta do Google.
1. No canto superior esquerdo da página, selecione a lista projeto e, em seguida, selecione **novo projeto**.
1. Insira um **Nome do Projeto**, clique em **Criar**e verifique se você está usando o novo projeto.
1. Selecione **Credenciais** no menu à esquerda e, em seguida, selecione **Criar Credenciais** > **ID do cliente Oauth**.
1. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
1. Insira um **Nome** para seu aplicativo, insira `https://your-tenant-name.b2clogin.com` em **Origens de JavaScript autorizadas** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar o Google como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar a conta do Google como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Google**.
1. Insira um **Nome**. Por exemplo, *Google*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo do Google que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Clique em **Salvar**.
