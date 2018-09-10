---
title: 'Tutorial: Integração do Azure Active Directory com o Teamwork.com | Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o Teamwork.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd4413c2-0d7c-41a7-aba4-b7a7a28c9448
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 6be2c9a17e719fb53ab257af77605c49ffea9e86
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437467"
---
# <a name="tutorial-azure-active-directory-integration-with-teamworkcom"></a>Tutorial: Integração do Azure Active Directory com o Teamwork.com

Neste tutorial, você aprende como integrar o Teamwork.com com o Azure Active Directory (Azure AD).

A integração do Teamwork.com com o Azure Active Directory fornece os seguintes benefícios:

- No Azure Active Directory, é possível controlar quem tem acesso ao Teamwork.com.
- Você pode permitir que seus usuários façam logon automaticamente no Teamwork.com (logon único) com as contas do Azure Active Directory deles
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory ao Teamwork.com, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Teamwork.com habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Teamwork.com da Galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-teamworkcom-from-the-gallery"></a>Adicionando Teamwork.com da Galeria
Para configurar a integração do Teamwork.com ao Azure Active Directory, você precisa adicionar o Teamwork.com por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Teamwork.com a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Teamwork.com**, selecione **Teamwork.com** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Teamwork.com na lista de resultados](./media/teamwork-tutorial/tutorial_teamwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o Teamwork.com, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Teamwork.com é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure Active Directory e o usuário relacionado no Teamwork.com.

No Teamwork.com, atribua o valor do **nome de usuário** no Azure Active Directory como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure Active Directory com o Teamwork.com, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Teamwork.com](#create-a-teamworkcom-test-user)** – para ter um equivalente de Brenda Fernandes no Teamwork.com vinculado à representação do usuário no Azure Active Directory.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure Active Directory no Portal do Azure e configura o logon único no seu aplicativo Teamwork.com.

**Para configurar o logon único do Azure Active Directory com o Teamwork.com, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Teamwork.com**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/teamwork-tutorial/tutorial_teamwork_samlbase.png)

1. Na seção **Domínio e URLs do Teamwork.com**, siga as etapas a seguir:

    ![Informações de logon único de Domínio e URLs do Teamwork.com](./media/teamwork-tutorial/tutorial_teamwork_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.teamwork.com`

    b. Na caixa de texto **Identificador**, digite a URL:

    |||
    |-|-|
    | `https://teamwork.com/saml`|
    | `https://eu.teamwork.com/saml`|

    > [!NOTE] 
    > Esse valor de URL de Entrada não é real. Atualize esse valor com a URL de Logon real. Para obter esse valor, entre em contato com a [equipe de suporte do Teamwork.com](mailto:support@teamwork.com). 

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/teamwork-tutorial/tutorial_teamwork_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/teamwork-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do **Teamwork.com**, é necessário enviar o **XML de Metadados** baixado para a [equipe de suporte Teamwork.com](mailto:support@teamwork.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/teamwork-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/teamwork-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/teamwork-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/teamwork-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-teamworkcom-test-user"></a>Criar um usuário de teste Teamwork.com

Nesta seção, você cria um usuário chamado Brenda Fernandes no Teamwork.com. Trabalhe com a [Equipe de suporte Teamwork.com](mailto:support@teamwork.com) para adicionar os usuários na plataforma Teamwork.com. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Teamwork.com.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Teamwork.com, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Teamwork.com**.

    ![O link do Teamwork.com na lista de Aplicativos](./media/teamwork-tutorial/tutorial_teamwork_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Teamwork.com no Painel de Acesso, você deverá ser conectado automaticamente no seu aplicativo Teamwork.com.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamwork-tutorial/tutorial_general_01.png
[2]: ./media/teamwork-tutorial/tutorial_general_02.png
[3]: ./media/teamwork-tutorial/tutorial_general_03.png
[4]: ./media/teamwork-tutorial/tutorial_general_04.png

[100]: ./media/teamwork-tutorial/tutorial_general_100.png

[200]: ./media/teamwork-tutorial/tutorial_general_200.png
[201]: ./media/teamwork-tutorial/tutorial_general_201.png
[202]: ./media/teamwork-tutorial/tutorial_general_202.png
[203]: ./media/teamwork-tutorial/tutorial_general_203.png

