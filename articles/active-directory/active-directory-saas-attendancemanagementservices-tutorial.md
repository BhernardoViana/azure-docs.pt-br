---
title: 'Tutorial: Integração do Azure Active Directory com o Serviços de Gerenciamento de Participação | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e os Serviços de Gerenciamento de Participação.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: abf882d7b06083080ad5cb3c2a20390a76a48139
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Tutorial: Integração do Azure Active Directory com o Serviços de Gerenciamento de Participação

Neste tutorial, você aprenderá a integrar os Serviços de Gerenciamento de Participação ao Microsoft Azure AD (Active Directory).

A integração dos Serviços de Gerenciamento de Participação ao Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso aos Serviços de Gerenciamento de Participação.
- Você pode permitir que os usuários façam logon automaticamente nos Serviços de Gerenciamento de Participação (Logon Único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD com os Serviços de Gerenciamento de Participação, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único dos Serviços de Gerenciamento de Participação

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Serviços de Gerenciamento de Participação da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-attendance-management-services-from-the-gallery"></a>Adicionar Serviços de Gerenciamento de Participação da galeria
Para configurar a integração dos Serviços de Gerenciamento de Participação com o Microsoft Azure AD, você precisa adicionar os Serviços de Gerenciamento de Participação, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar os Serviços de Gerenciamento de Participação da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Serviços de Gerenciamento de Participação**, selecione **Serviços de Gerenciamento de Participação** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Serviços de Gerenciamento de Participação na lista de resultados](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Microsoft Azure Active Directory com os Serviços de Gerenciamento de Participação, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário dos Serviços de Gerenciamento de Participação é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de link entre um usuário do Microsoft Azure AD e o usuário relacionado nos Serviços de Gerenciamento de Participação deve ser estabelecida.

Para configurar e testar o logon único do Microsoft Azure AD com os Serviços de Gerenciamento de Participação, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste dos Serviços de Gerenciamento de Participação](#create-an-attendance-management-service-test-user)** – para ter um equivalente de Brenda Fernandes nos Serviços de Gerenciamento de Participação que esteja vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no portal do Azure e configurará o logon único em seu aplicativo de Serviços de Gerenciamento de Participação.

**Para configurar o logon único do Microsoft Azure AD com os Serviços de Gerenciamento de Participação, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo de **Serviços de Gerenciamento de Participação**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

3. Na seção **Domínio e URLs dos Serviços de Gerenciamento de Participação**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs dos Serviços de Gerenciamento de Participação](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente de Serviços de Gerenciamento de Participação](http://www.obcnet.jp/) para obter esses valores.

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_400.png)

6. Na seção **Configuração de Serviços de Gerenciamento de Participação**, clique em **Configurar Serviços de Gerenciamento de Participação** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração de Serviços de Gerenciamento de Participação](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

7. Em uma janela de navegador diferente, entre no site de sua empresa dos Serviços de Gerenciamento de Participação como administrador.

8. Clique em **Autenticação SAML** na **seção Gerenciamento de segurança**.

    ![Configuração de Serviços de Gerenciamento de Participação](./media/active-directory-saas-attendancemanagementservices-tutorial/user1.png)

9. Execute as seguintes etapas:

    ![Configuração de Serviços de Gerenciamento de Participação](./media/active-directory-saas-attendancemanagementservices-tutorial/user2.png)

    a. Selecione **Usar autenticação SAML**.

    b. Na caixa de texto **Identificador**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure. 

    c. Na caixa de texto **URL do ponto de extremidade de Autenticação**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    d. Clique em **Selecionar arquivo** para carregar o certificado que você baixou do Microsoft Azure AD.

    e. Selecione **Desabilitar autenticação por senha**.

    f. Clique em **Registro**

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo! Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Criar um usuário de teste de Serviços de Gerenciamento de Participação

Para permitir que os usuários do Microsoft Azure AD façam logon em Serviços de Gerenciamento de Participação, eles devem ser provisionados nos Serviços de Gerenciamento de Participação. No caso de Serviços de Gerenciamento de Participação, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site de sua empresa dos Serviços de Gerenciamento de Participação como administrador.

2. Clique em **Gerenciamento de usuários** na **seção Gerenciamento de segurança**.

    ![Adicionar Funcionário](./media/active-directory-saas-attendancemanagementservices-tutorial/user5.png)

3. Clique em **Novas regras de logon**.

    ![Adicionar Funcionário](./media/active-directory-saas-attendancemanagementservices-tutorial/user3.png)

4. Na seção **Informações de OBCiD**, realize as seguintes etapas:

    ![Adicionar Funcionário](./media/active-directory-saas-attendancemanagementservices-tutorial/user4.png)

    a. Na caixa de texto **OBCiD**, digite o email do usuário como **BrittaSimon@contoso.com**.

    b. Na caixa de texto **Senha**, digite a senha do usuário.

    c. Clique em **Registro**


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso aos Serviços de Gerenciamento de Participação.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes aos Serviços de Gerenciamento de Participação, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Serviços de Gerenciamento de Participação**.

    ![O link de Serviços de Gerenciamento de Participação na lista de aplicativos](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Serviços de Gerenciamento de Participação no Painel de Acesso, você deve fazer logon automaticamente em seu aplicativo de Serviços de Gerenciamento de Participação.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_203.png

