---
title: 'Azure Active Directory B2C: autenticação multifator | Microsoft Docs'
description: Como habilitar a Autenticação Multifator em aplicativos voltados para o consumidor protegidos pelo Active Directory B2C do Azure
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 0f560a02a51239cff0a7d4057ec7beb2f0f5c5c5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: habilitar a Autenticação Multifator nos seus aplicativos voltados para o consumidor
O Azure AD (Azure Active Directory) B2C integra-se diretamente ao [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md) para facilitar a adição de uma segunda camada de segurança para as experiências de inscrição e entrada nos seus aplicativos voltados para o consumidor. E você pode fazer isso sem escrever uma única linha de código. No momentos damos suporte à verificação por ligação telefônica e mensagem de texto. Se você já tiver criado as políticas de credenciais e de entrada, ainda poderá habilitar a Autenticação Multifator.

> [!NOTE]
> A Autenticação Multifator também pode ser habilitada durante a criação de políticas de credenciais e de entrada, não apenas na edição de políticas existentes.
> 
> 

Esse recurso ajuda os aplicativos a lidarem com cenários como os seguintes:

* Você não exige Autenticação Multifator para acessar um aplicativo, mas para acessar outro sim. Por exemplo, o consumidor pode entrar em um aplicativo de seguro automobilístico com uma conta local ou social, porém precisa verificar o número de telefone para poder acessar o aplicativo de seguro residencial registrado no mesmo diretório.
* Você não exige a Autenticação Multifator para acessar um aplicativo em geral, mas sim para acessar partes confidenciais dele. Por exemplo, o consumidor pode entrar em um aplicativo bancário com uma conta local ou social e consultar o saldo da conta, porém precisa verificar o número de telefone para poder tentar realizar uma transferência bancária.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificar a política de inscrição para habilitar a Autenticação Multifator
1. Siga estas etapas para [navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **Políticas de inscrição**.
3. Clique na sua política de inscrição (por exemplo, "B2C_1_SiUp") para abri-la.
4. Clique em **Autenticação multifator** e ative o **Estado** colocando em **ON**. Clique em **OK**.
5. Clique em **Salvar** na parte superior da folha.

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Confirme o seguinte:

Uma conta de consumidor é criada no diretório antes da etapa de Autenticação Multifator. Durante a etapa, o consumidor é solicitado a fornecer seu número de telefone e a verificá-lo. Se a verificação for bem-sucedida, o número de telefone será anexado à conta de consumidor para uso posterior. Mesmo que o consumidor cancele a ação ou saia, pode ser solicitado que ele confirme um número de telefone novamente durante a próxima conexão (com a Autenticação Multifator habilitada).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificar a política de entrada para habilitar a Autenticação Multifator
1. Siga estas etapas para [navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **Políticas de entrada**.
3. Clique na sua política de entrada (por exemplo, "B2C_1_SiIn") para abri-la. Clique em **Editar** na parte superior da folha.
4. Clique em **Autenticação multifator** e ative o **Estado** colocando em **ON**. Clique em **OK**.
5. Clique em **Salvar** na parte superior da folha.

Você pode usar o recurso "Executar agora" da política para verificar a experiência do consumidor. Confirme o seguinte:

Quando o consumidor entra (usando uma conta local ou social), se um número de telefone verificado está anexado à conta de consumidor, ele deve verificá-lo. Se nenhum número de telefone estiver anexado, o consumidor é solicitado a fornecer seu número de telefone e a confirmá-lo. Se a verificação for bem-sucedida, o número de telefone será anexado à conta do consumidor para uso posterior.

## <a name="multi-factor-authentication-on-other-policies"></a>Autenticação Multifator em outras políticas
Conforme descrito para as políticas de inscrição e entrada acima, também é possível habilitar a autenticação multifator nas políticas de inscrição ou entrada e nas políticas de redefinição de senha. Ela estará disponível em breve nas políticas de edição de perfil.

