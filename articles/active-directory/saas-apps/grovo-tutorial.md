---
title: 'Tutorial: Integração do Azure Active Directory com Grovo | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Grovo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: 057775f7818d1a6dc521fe81b01748fa40085cc8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174237"
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Tutorial: Integração do Azure Active Directory com Grovo

Neste tutorial, você aprende a integrar o Grovo ao Azure AD (Azure Active Directory).

A integração de Grovo ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Grovo.
- Você pode permitir que seus usuários façam logon automaticamente no Grovo (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Grovo, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Grovo

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Grovo da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-grovo-from-the-gallery"></a>Adição do Grovo da galeria
Para configurar a integração do Grovo ao Azure AD, você precisa adicionar o Grovo por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Grovo da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Grovo**, selecione **Grovo** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Grovo na lista de resultados](./media/grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Grovo, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Grovo é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Grovo.

No Grovo, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Microsoft Azure AD com o Grovo, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Grovo](#create-a-grovo-test-user)** – para ter um equivalente de Brenda Fernandes no Grovo que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Grovo.

**Para configurar o logon único do Azure AD com o Grovo, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Grovo**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/grovo-tutorial/tutorial_grovo_samlbase.png)

1. Na seção **Domínio e URLs do Grovo**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único em Domínio e URLs do Grovo](./media/grovo-tutorial/tutorial_grovo_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

1. Marque **Mostrar configurações de URL avançadas** e realize a seguinte etapa:

    ![Informações de logon único em Domínio e URLs do Grovo](./media/grovo-tutorial/tutorial_grovo_url1.png)

     a. Na caixa de texto **Estado de retransmissão**, digite uma URL usando o seguinte padrão: `https://<subdomain>.grovo.com`

    b. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Grovo](./media/grovo-tutorial/tutorial_grovo_url2.png)
    
    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta, a URL de Logon e o Estado de retransmissão reais. Entre em contato com a [equipe de suporte do Grovo](https://www.grovo.com/contact-us) para obter esses valores.
 
1. O aplicativo Grovo espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. Mapeie o **Identificador de Usuário** com **user.mail** e configure outros atributos, conforme mostrado na captura de tela a seguir.
    
    ![Configurar logon único attb](./media/grovo-tutorial/tutorial_grovo_attribute.png)
    
1. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | Nome          | user.givenname |
    | Sobrenome           | user.surname |
    | Endereço de Email       | user.mail    |
    | employeeID          | user.employeeid |

     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/grovo-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/grovo-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.


1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/grovo-tutorial/tutorial_grovo_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/grovo-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Grovo**, clique em **Configurar Grovo** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon no Grovo como Administrador.

1. Vá para **Integrações** > **do Administrador**.
 
    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_admin.png) 

1. Clique em **CONFIGURAR** na seção **SAML 2.0 iniciado por SP**.

    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_setup.png)

1. Na janela pop-up **SAML 2.0 iniciado por SP**, execute as seguintes etapas:

    ![Configuração do Grovo](./media/grovo-tutorial/tutorial_grovo_saml.png)

     a. Na caixa de texto **ID da Entidade**, cole o valor da **ID da Entidade do SAML** copiado no Portal do Azure.

    b. Na caixa de texto **Ponto de extremidade de serviço de logon único**, cole o valor da **URL de Serviço de Logon Único do SAML** copiado no Portal do Azure.

    c. Selecione **Associação de ponto de extremidade do serviço de Logon único** como `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Abra o **certificado codificado com Base64** baixado do Portal do Azure no bloco de notas, cole-o na caixa de texto **Chave pública**.

    e. Clique em **Próximo**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/grovo-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/grovo-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/grovo-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/grovo-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-grovo-test-user"></a>Criar um usuário de teste do Grovo

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Grovo. O Grovo dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Grovo, caso ele ainda não exista.
>[!Note]
>Se for necessário criar um usuário manualmente, Contate a  [equipe de suporte do Grovo](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Grovo.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Grovo, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Grovo**.

    ![O link do Grovo na lista de Aplicativos](./media/grovo-tutorial/tutorial_grovo_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Grovo no Painel de Acesso, deverá fazer logon automaticamente em seu aplicativo do Grovo.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/grovo-tutorial/tutorial_general_01.png
[2]: ./media/grovo-tutorial/tutorial_general_02.png
[3]: ./media/grovo-tutorial/tutorial_general_03.png
[4]: ./media/grovo-tutorial/tutorial_general_04.png

[100]: ./media/grovo-tutorial/tutorial_general_100.png

[200]: ./media/grovo-tutorial/tutorial_general_200.png
[201]: ./media/grovo-tutorial/tutorial_general_201.png
[202]: ./media/grovo-tutorial/tutorial_general_202.png
[203]: ./media/grovo-tutorial/tutorial_general_203.png

