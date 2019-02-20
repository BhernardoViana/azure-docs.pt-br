---
title: 'Tutorial: Integração do Azure Active Directory com o Kanbanize | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199673"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Tutorial: Integração do Azure Active Directory com o Kanbanize

Neste tutorial, você aprenderá a integrar o Kanbanize ao Azure AD (Azure Active Directory).

A integração do Kanbanize com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Kanbanize.
- Você pode habilitar os usuários a fazerem logon automaticamente no Kanbanize (logon único) usando suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Kanbanize, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Kanbanize habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Kanbanize da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-kanbanize-from-the-gallery"></a>Adicionar o Kanbanize da Galeria
Para configurar a integração do Kanbanize com o Azure AD, você precisará adicionar o Kanbanize da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Kanbanize da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Kanbanize**, selecione **Kanbanize** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Kanbanize na lista de resultados](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Kanbanize com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Kanbanize é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kanbanize.

Para configurar e testar o logon único do Azure AD com o Kanbanize, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Kanbanize](#create-a-kanbanize-test-user)** – para ter um equivalente de Brenda Fernandes no Kanbanize que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Kanbanize.

**Para configurar o logon único do Azure AD com o Kanbanize, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Kanbanize**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Na seção **Domínio e URLs do Kanbanize**, execute as seguintes etapas se quiser configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Marque **Mostrar configurações de URL avançadas**.

    d.  Na caixa de texto **Estado de Retransmissão**, digite uma URL: `/ctrl_login/saml_login`

    e. Se você deseja configurar o aplicativo no modo iniciado pelo **SP**, na caixa de texto **URL de Logon** digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [Equipe de suporte ao cliente do Kanbanize](mailto:support@ms.kanbanize.com) para obter esses valores. 

5. Seu aplicativo Kanbanize espera as declarações de SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML . A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador de Usuário** é **user.userprincipalname**, mas o Kanbanize espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user.mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização
    
    ![Configurar o logon único](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Na seção **Configuração do Kanbanize**, clique em **Configurar Kanbanize** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. Em uma janela diferente do navegador da Web, faça logon no Kanbanize como Administrador de Segurança. 

10. No lado superior direito da página, clique no logotipo de **Configurações**.

    ![Configurações do Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Na página do painel de Administração do lado esquerdo do menu, clique em **Integrações** e, em seguida, habilite o **Logon Único**. 

    ![Integrações do Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Na seção Integrações, clique em **CONFIGURAR** para abrir a página **Integração de Logon Único**.

    ![Configuração do Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Na página **Integração de Logon Único**, em **Configurações**, execute as seguintes etapas:

    ![Integrações do Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

     a. Na caixa de texto **ID da Entidade de Idp**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    b. Na caixa de texto **Ponto de Extremidade de Logon do IdP**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    c. Na caixa de texto **Ponto de Extremidade de Logoff do IdP**, cole o valor da **URL de Saída** copiado do portal do Azure.

    d. Na caixa de texto **Nome de atributo de Email**, insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Na caixa de texto **Nome de atributo de Primeiro Nome**, insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Na caixa de texto **Nome de atributo de Sobrenome**, insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Você pode obter esses valores combinando os valores de namespace e nome do respectivo atributo da seção de Atributos de usuário do portal do Azure.

    g. No Bloco de notas, abra o certificado codificado em Base 64 baixado no portal do Azure, copie o conteúdo (sem os marcadores de início e fim) e cole-o na caixa  **Certificado X.509** .

    h. Marque **Habilitar logon com SSO e Kanbanize**.
    
    i. Clique em **Salvar Configurações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/kanbanize-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-kanbanize-test-user"></a>Crie um usuário de teste do Kanbanize

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Kanbanize. O Kanbanize dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Kanbanize, caso ele ainda não exista.

>[!Note]
>Se precisar criar um usuário manualmente, entre em contato com a  [Equipe de suporte ao cliente do Kanbanize](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo a ela acesso ao Kanbanize.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Kanbanize, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Kanbanize**.

    ![O link do Kanbanize na lista de Aplicativos](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Kanbanize no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Kanbanize.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

