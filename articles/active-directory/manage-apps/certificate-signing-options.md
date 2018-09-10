---
title: Opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory | Microsoft Docs
description: Saiba como usar as opções avançadas de assinatura de certificado no token SAML para aplicativos pré-integrados no Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: e86c7e82203e2ecd571d5ce5e450999704218e33
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579156"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>As opções avançadas de assinatura de certificado no token SAML para aplicativos da galeria no Azure Active Directory
Atualmente, o Azure Active Directory (AD do Azure) oferece suporte a milhares de aplicativos pré-integrados na Galeria de aplicativo do Azure Active Directory. Esse número inclui mais de 500 aplicativos que dão suporte ao logon único usando o protocolo SAML 2.0. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando SAML, o Azure AD envia um token ao aplicativo (por um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML são assinados com o certificado exclusivo que é gerado no Azure AD e por algorítimos padrão específicos.

O Azure AD usa algumas das configurações padrão para os aplicativos de galeria. Os valores padrão são configurados com base nos requisitos do aplicativo.

O Azure AD oferece suporte a configurações avançadas de assinatura de certificado. Para selecionar essas opções, primeiro marque a caixa de seleção **Mostrar configurações avançadas de assinatura de certificado**:

![Mostrar configurações avançadas de assinatura de certificado](./media/certificate-signing-options/saml-advance-certificate.png)

Depois de selecionar essa caixa, você pode configurar as opções de assinatura de certificado e o algoritmo de assinatura de certificado.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

O Azure AD dá suporte a três opções de assinatura de certificado:

* **Assinar declaração SAML**. A opção padrão é definida para a maioria dos aplicativos de galeria. Se essa opção for selecionada, o Azure AD, como um IdP, assinará as Asserções SAML e o certificado com o certificado X509 do aplicativo. Além disso, ele usa o algoritmo de assinatura, que é selecionado na lista suspensa do **algorítimo de assinatura**.

* **Assinar resposta SAML**. Se essa opção for selecionada, o Azure AD, como IdP, assina a resposta SAML com o certificado X509 do aplicativo. Além disso, ele usa o algoritmo de assinatura, que é selecionado na lista suspensa do **algorítimo de assinatura**.

* **Assinar resposta SAML e declaração**. Se essa opção for selecionada, o Azure AD, como IdP, assina todo o toke SAML com o certificado X509 do aplicativo. Além disso, ele usa o algoritmo de assinatura, que é selecionado na lista suspensa do **algorítimo de assinatura**.

    ![Opções de assinatura de certificado](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algoritmo de assinatura de certificado

O Azure AD dá suporte a dois algoritmos de assinatura para assinar resposta SAML:

* **SHA-256**. O Azure AD dá suporte a dois algoritmos de assinatura para assinar resposta SAML. Este é o algoritmo mais recente e é considerado mais seguro do que SHA1. A maioria dos aplicativos dá suporte ao algoritmo SHA-256. Se um aplicativo suporta somente SHA-1 como o algoritmo de assinatura, você pode alterá-lo. Caso contrário, é recomendável usar o algoritmo SHA256 para assinar a resposta SAML.

    ![Algoritmo de assinatura de certificado SHA-256](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**. Este é o algoritmo mais antigo e não é tratado como menos seguro que SH-256. Se o aplicativo der suporte somente a esse algoritmo de assinatura, você poderá selecionar essa opção na lista suspensa de **Algorítimo de Assinatura**. O Azure AD assina a resposta SAML com o algoritmo SHA-1.

    ![Algoritmo de assinatura de certificado SHA-1](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Próximas etapas
* [Índice de artigos para gerenciamento de aplicativos no Azure Active Directory](../active-directory-apps-index.md)
* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Solução de problemas de logon único baseado em SAML](../develop/howto-v1-debug-saml-sso-issues.md)


