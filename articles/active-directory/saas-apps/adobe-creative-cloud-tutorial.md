---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory à Adobe Creative Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory à Adobe Creative Cloud

Neste tutorial, você aprenderá a integrar a Adobe Creative Cloud ao Azure AD (Azure Active Directory). Ao integrar a Adobe Creative Cloud ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso à Adobe Creative Cloud.
* Permitir que os usuários sejam conectados automaticamente à Adobe Creative Cloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da Adobe Creative Cloud.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Adobe Creative Cloud dá suporte ao SSO iniciado por **SP**





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando o Adobe Creative Cloud da Galeria

Para configurar a integração da Adobe Creative Cloud com o Azure AD, será necessário adicionar a Creative Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Adobe Creative Cloud** na caixa de pesquisa.
1. Selecione **Adobe Creative Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Configurar e testar o logon único do Azure AD para a Adobe Creative Cloud

Configure e teste o SSO do Azure AD com a Adobe Creative Cloud usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado na Adobe Creative Cloud.

Para configurar e testar o SSO do Azure AD com a Adobe Creative Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO da Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** – para ter um equivalente de B.Fernandes na Adobe Creative Cloud que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **Adobe Creative Cloud**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon**, digite uma URL: `https://adobe.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o Identificador real. Contate a [equipe de suporte ao cliente do Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Adobe Creative Cloud espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Adobe Creative Cloud espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | NOME | Atributo de Origem|
    |----- | --------- |
    | Nome | user.givenname |
    | Sobrenome | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > Os usuários precisam ter uma licença válida do Office 365 ExO para valor de declaração de email a ser preenchida na resposta SAML.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar a Adobe Creative Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso à Adobe Creative Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adobe Creative Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-adobe-creative-cloud-sso"></a>Configurar o SSO da Adobe Creative Cloud

1. Em outra janela do navegador da Web, entre no [Console de Administração do Adobe](https://adminconsole.adobe.com) como administrador.

2. Acesse **Configurações** na barra de navegação superior e, em seguida, escolha **Identidade**. A lista de domínios será aberta. Clique no link **Configurar** no domínio. Em seguida, siga as etapas na seção **Configuração do logon único necessária**. Para obter mais informações, consulte [Configurar um domínio](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Configurações](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Configurações")

    a. Clique em **Procurar** para carregar o certificado baixado do Azure AD para o **Certificado do IdP**.

    b. Na caixa de texto **Emissor do IDP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon do IDP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Selecione **Redirecionamento HTTP** como **Associação IdP**.

    e. Selecione **Endereço de email** como **Configuração de logon do usuário**.

    f. Clique no botão **Salvar** .

3. Agra o painel apresentará o arquivo XML **"Baixar metadados"** . Ele contém a URL EntityDescriptor e a URL AssertionConsumerService da Adobe. Abra o arquivo e configure-os no aplicativo Azure AD.

    ![Configurar o logon único no lado do aplicativo](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use o valor EntityDescriptor que a Adobe forneceu a você para **Identificador** na caixa de diálogo **Definir configurações de aplicativo**.

    b. Use o valor AssertionConsumerService que a Adobe forneceu a você para **URL de resposta** na caixa de diálogo **Definir configurações de aplicativo**.

### <a name="create-adobe-creative-cloud-test-user"></a>Criar um usuário de teste da Adobe Creative Cloud

Para que os usuários do Azure AD possam entrar na Adobe Creative Cloud, eles devem ser provisionados na Adobe Creative Cloud. No caso da Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1. Entre no site [Console de Administração do Adobe](https://adminconsole.adobe.com) como administrador.

2. Adicione o usuário no console da Adobe como uma ID Federada e atribua-o a um Perfil de Produto. Para obter informações detalhadas sobre como adicionar usuários, consulte [Adicionar usuários no Console de Administração do Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Neste ponto, digite seu endereço de email/upn no formulário de entrada da Adobe, pressione tab e você deve ser federado de volta ao Azure AD:
   * Acesso via Web: www\.adobe.com > sign-in
   * No utilitário de aplicativo de área de trabalho > entrar
   * No aplicativo > ajuda > entrar

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adobe Creative Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao Adobe Creative Cloud no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Adobe Creative Cloud com o Azure AD](https://aad.portal.azure.com/)

- [Configurar um domínio (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configurar o Azure para uso com o SSO do Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

