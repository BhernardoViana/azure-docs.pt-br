---
title: 'Azure Active Directory B2C: configuração do Twitter | Microsoft Docs'
description: Forneça inscrição e credenciais para consumidores com contas do Twitter em seus aplicativos protegidos pelo Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/06/2017
ms.author: davidmu
ms.openlocfilehash: ee2d82f8c90b88a898428973a1febaa21034a14f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C: fornecer inscrição e credenciais para consumidores com contas do Twitter

## <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Para usar o Twitter como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisa criar um aplicativo Twitter e fornecer a ele os parâmetros certos. Você precisa de uma conta de desenvolvedor do Twitter para fazer isso. Se você não tiver uma, é possível obtê-la em [https://dev.twitter.com/](https://dev.twitter.com/).

1. Vá para o [site do desenvolvedor do Twitter](https://dev.twitter.com/) e entre com suas credenciais.
2. Clique em **Meus aplicativos** em **Ferramentas e Suporte** e, em seguida, clique em **Criar Novo Aplicativo**. 
3. No formulário, forneça um valor para o **Nome**, **Descrição**, e **Site**.
4. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` como o valor da **URL de Retorno de Chamada**. Certifique-se de substituir **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com).
5. Marque a caixa para concordar com o **Contrato de Desenvolvedor** e clique em **Criar seu aplicativo do Twitter**.
6. Quando o aplicativo for criado, clique na guia **Chaves e Tokens de acesso**.
7. Copie o valor de **Chave do consumidor** e **Segredo do consumidor**. Você precisará de ambos para configurar o Twitter como um provedor de identidade no seu locatário.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário
1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** , na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "Twitter".
5. Clique em **Tipo de provedor de identidade**, selecione **Twitter** e clique em **OK**.
6. Clique em **Configurar esse provedor de identidade** e insira a **Chave do Consumidor** do Twitter para a **ID do cliente** e o **Segredo do Consumidor** do Twitter para o **Segredo do cliente**.
7. Clique em **OK** e em **Criar** para salvar sua configuração do Twitter.

## <a name="next-steps"></a>Próximas etapas

Criar ou editar uma [política interna](active-directory-b2c-reference-policies.md) e adicionar o Twitter como provedor de identidade.