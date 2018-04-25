---
title: Personalização de redefinição de senha por autoatendimento – Azure Active Directory
description: Opções de personalização para redefinição de senha por autoatendimento do Azure AD
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: f000e59195155f645d137233a9fa9896e21102b7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade de Autoatendimento de Redefinição de Senha do Azure AD

Os profissionais de TI que desejam implantar a redefinição de senha de autoatendimento (SSPR) no Azure Active Directory (AD do Azure) podem personalizar a experiência para atender às necessidades de seus usuários.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o link "Contate o administrador"

Mesmo que a SSPR não esteja habilitada, os usuários ainda verão um link "Contate o administrador" no portal de redefinição de senha. Se um usuário selecionar este link, ele executará uma das seguintes ações:
   * Enviará um email aos administradores pedindo ajuda para alterar a senha do usuário. 
   * Envia seus usuários a uma URL que você especifica para obter assistência. 

É recomendável definir esse contato para algo como um endereço de email ou site os quais os usuários já estão acostumados a usar para obter suporte.

![Contato][Contact]

Esse contato é enviado para os seguintes destinatários na seguinte ordem:

1. Se a função **Administrador de senhas** for atribuída, os administradores com essa função serão notificados.
2. Se nenhum administrador de senhas for atribuído, os administradores com a função **administrador de usuários** serão notificados.
3. Se nenhuma das funções anteriores foram atribuídas, os **administradores globais** serão notificados.

Em todos os casos, no máximo 100 destinatários serão notificados.

Para obter mais informações sobre as diferentes funções de administrador e sobre como atribuí-las, consulte [Atribuindo funções de administrador no Azure Active Directory](../active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Desabilitar os emails "Contate o administrador"

Caso sua organização não queira que os administradores recebam solicitações de redefinição de senha, você poderá habilitar a configuração a seguir:

* Habilite o autoatendimento de redefinição de senha para todos os usuários finais. Essa opção pode ser encontrada em **Redefinição de Senha** > **Propriedades**.
  
  Se você não quiser que os usuários redefinam as próprias senhas, poderá definir o escopo de acesso como um grupo vazio. *Não recomendamos essa opção.*
* Personalizar o link de assistência técnica para fornecer uma URL da Web ou um endereço mailto: que os usuários podem usar para obter assistência. Essa opção pode ser encontrada em **Redefinição de Senha** > **Personalização** > **URL ou email de assistência técnica personalizados**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalize a página de entrada do AD FS para SSPR

Os administradores do Active Directory Federation Services (AD FS) podem adicionar um link à página de entrada deles usando diretrizes no artigo [Adicionar descrição da página de entrada](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Para adicionar um link à página de entrada do AD FS, use o seguinte comando no servidor do AD FS. Os usuários podem usar essa página para inserir o fluxo de trabalho de SSPR.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar a página de entrada e a aparência do painel de acesso

Você pode personalizar a página de entrada. Você pode adicionar um logotipo que aparece junto com a imagem adequada à identidade visual de sua empresa.

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois que um usuário inserir seu nome de usuário
* Se o usuário acessar a URL personalizada:
    * Passando o parâmetro *whr* para a página de redefinição de senha, como “https://login.microsoftonline.com/?whr=contoso.com”
    * Passando o parâmetro *username* para a página de redefinição de senha, como “https://login.microsoftonline.com/?username=admin@contoso.com”

Encontre detalhes sobre como configurar a identidade visual da empresa no artigo [Adicionar uma identidade visual da empresa à página de entrada do Azure AD](../customize-branding.md).

### <a name="directory-name"></a>Nome do diretório

Você pode alterar o atributo de nome de diretório em **Azure Active Directory** > **Propriedades**. Você pode mostrar um nome amigável de organização que aparece no portal e nas comunicações automatizadas. Essa opção fica mais visível nos emails automatizados nos formatos a seguir:

* O nome amigável no email, por exemplo, "Microsoft em nome da demonstração da CONTOSO"
* A linha do assunto no email, por exemplo, "Código de verificação de email da conta de demonstração da CONTOSO"

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Contate o administrador para obter ajuda para redefinir o exemplo de email de senha"
