---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory à AWS (Amazon Web Services) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AWS (Amazon Web Services).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2fea3bca40d8a5783448e68ea89c3b238a0104d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory à AWS (Amazon Web Services)

Neste tutorial, você aprenderá a integrar a AWS (Amazon Web Services) ao Azure AD (Azure Active Directory). Quando você integra a AWS (Amazon Web Services) com o Azure Active Directory, pode:

* Controlar no Azure Active Directory quem tem acesso à AWS (Amazon Web Services).
* Habilitar seus usuários a fazer logon automaticamente na AWS (Amazon Web Services) usando as próprias contas do Azure Active Directory.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagrama do relacionamento do Azure AD e do AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias. Por exemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com esses valores, o Azure AD remove o valor de **#** e envia o valor correto `https://signin.aws.amazon.com/saml` como a URL de público no token SAML.

É recomendável essa abordagem pelos seguintes motivos:

- Cada aplicativo fornece a você um certificado X509 exclusivo. Cada instância de uma instância de aplicativo da AWS pode ter uma data de expiração do certificado diferente, que pode ser gerenciada individualmente por conta da AWS. A rolagem geral do certificado é mais fácil nesse caso.

- Você pode ativar o aprovisionamento de usuário com um aplicativo da AWS no Azure AD e, em seguida, nosso serviço busca todas as funções dessa conta da AWS. Você não precisa adicionar ou atualizar manualmente as funções da AWS no aplicativo.

- Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo. Essa pessoa pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Use apenas o aplicativo da galeria.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do AWS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* A AWS (Amazon Web Services) dá suporte ao SSO iniciado por **SP e IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria

Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **AWS (Amazon Web Services)** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Configurar e testar o logon único do Azure AD para a AWS (Amazon Web Services)

Configure e teste o SSO do Azure AD com a AWS (Amazon Web Services) usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na AWS (Amazon Web Services).

Para configurar e testar o SSO do Azure AD com a AWS (Amazon Web Services), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO da AWS (Amazon Web Services)](#configure-amazon-web-services-aws-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da AWS (Amazon Web Services)](#create-amazon-web-services-aws-test-user)** – para ter um equivalente de B.Fernandes na AWS (Amazon Web Services) que esteja vinculado à representação de usuário do Azure AD.
    1. **[Como configurar o provisionamento de função na AWS (Amazon Web Services)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **AWS (Amazon Web Services)** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. O usuário precisa salvar a configuração selecionando **Salvar**.

1. Quando você estiver configurando mais de uma instância, forneça um valor de identificador. Da segunda instância em diante, use o seguinte formato, incluindo um sinal **#** para especificar um valor SPN exclusivo.

    `https://signin.aws.amazon.com/saml#2`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar a AWS (Amazon Web Services)** , copie a URL apropriada, com base em suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso à AWS (Amazon Web Services).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AWS (Amazon Web Services)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-amazon-web-services-aws-sso"></a>Configurar o SSO da AWS (Amazon Web Services)

1. Em uma janela diferente do navegador, entre no site da empresa do AWS como administrador.

2. Selecione **Página inicial do AWS**.

    ![Captura de tela do site da empresa AWS, com o ícone Página inicial do AWS realçado][11]

3. Selecione **Gerenciamento de acesso e identidade**.

    ![Captura de tela da página de serviços do AWS, com IAM realçado][12]

4. Selecione **Provedores de Identidade** > **Criar Provedor**.

    ![Captura de tela da página IAM, com Provedores de Identidade e Criar Provedor realçados][13]

5. Na página **Configurar Provedor**, execute as seguintes etapas:

    ![Captura de tela de Configurar Provedor][14]

    a. Em **Tipo de provedor**, selecione **SAML**.

    b. Em **Nome do Provedor**, digite um nome para o provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, selecione **Escolher Arquivo**.

    d. Selecione **Próxima etapa**.

6. Na página **Verificar Informações do Provedor**, selecione **Criar**.

    ![Captura de tela de Verificar Informações do Provedor, com Criar realçado][15]

7. Selecione **Funções** > **Criar função**.

    ![Captura de tela da página Funções][16]

8. Na página **Criar função**, realize as seguintes etapas:  

    ![Captura de tela da página Criar função][19]

    a. Em **Selecionar tipo de entidade confiável**, selecione **Federação do SAML 2.0**.

    b. Em **Escolher um provedor do SAML 2.0**, selecione o **Provedor do SAML** criado anteriormente (por exemplo: *WAAD*).

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.
  
    d. Selecione **Avançar: Permissões.**

9. Na caixa de diálogo **Anexar políticas de permissão**, anexe a política adequada conforme sua organização. Em seguida, selecione **Avançar: Análise**.  

    ![Captura de tela da caixa de diálogo Anexar política de permissões][33]

10. Na caixa de diálogo **Examinar**, execute as seguintes etapas:

    ![Captura de tela da caixa de diálogo Examinar][34]

    a. Em **Nome da função**, insira o nome da função.

    b. Em **Descrição da função**, insira a descrição.

    c. Selecione **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o provedor de identidade.

11. Use as credenciais de conta de serviço do AWS para buscar as funções da conta AWS no provisionamento de usuário do Azure AD. Para isso, abra a página inicial do console AWS.

12. Selecione **Serviços**. Em **Segurança, Identidade e Conformidade**, selecione **IAM**.

    ![Captura de tela da página inicial do console do AWS, com Serviços e IAM realçados](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Na seção IAM, selecione **Políticas**.

    ![Captura de tela da seção IAM, com Políticas realçado](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma política selecionando **Criar política** para buscar as funções da conta AWS no provisionamento de usuário do Azure AD.

    ![Captura de tela da página Criar função, com Criar política realçado](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas AWS.

    ![Captura de tela da página Criar política, com JSON realçado](./media/amazon-web-service-tutorial/policy1.png)

    a. Na **Criar a política**, selecione a guia **JSON**.

    b. No documento de política, adicione o seguinte JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Selecione **Examinar política** para validar a política.

    ![Captura de tela da página Criar política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a nova política.

    ![Captura de tela da página Criar política, com os campos Nome e Descrição realçados](./media/amazon-web-service-tutorial/policy2.png)

    a. Para **Nome**, insira **AzureAD_SSOUserRole_Policy**.

    b. Para **Descrição**, insira **Esta política permitirá buscar as funções de contas do AWS**.

    c. Selecione **Criar política**.

17. Crie uma conta de usuário no serviço de IAM do AWS.

    a. No console IAM do AWS, selecione **Usuários**.

    ![Captura de tela do console IAM do AWS, com Usuários realçado](./media/amazon-web-service-tutorial/policy3.png)

    b. Para criar um usuário, selecione **Adicionar usuário**.

    ![Captura de tela do botão Adicionar usuário](./media/amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário**:

    ![Captura de tela da página Adicionar usuário, com Nome de usuário e Tipo de acesso realçados](./media/amazon-web-service-tutorial/adduser1.png)

    * Digite o nome de usuário como **AzureADRoleManager**.

    * Para o tipo de acesso, selecione **Acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Selecione **Próximas Permissões**.

18. Crie uma política para este usuário.

    ![Captura de tela de Adicionar usuário](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selecione **Anexar políticas existentes diretamente**.

    b. Pesquise a política recém-criada na seção filtro **AzureAD_SSOUserRole_Policy**.

    c. Selecione a política e, em seguida, **Próximo: Análise**.

19. Examine a política para o usuário anexado.

    ![Captura de tela da página Adicionar usuário, com Criar usuário realçado](./media/amazon-web-service-tutorial/adduser3.png)

    a. Examine o nome de usuário, tipo de acesso e política mapeados para o usuário.

    b. Selecione **Criar usuário**.

20. Baixe as credenciais do usuário de um usuário.

    ![Captura de tela de Adicionar usuário](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **ID de chave de acesso** do usuário e a **Chave de acesso secreta**.

    b. Insira essas credenciais na seção de provisionamento de usuário do Azure AD para buscar as funções do console AWS.

    c. Selecione **Fechar**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Como configurar o provisionamento de função na AWS (Amazon Web Services)

1. No portal de gerenciamento do Azure AD, no aplicativo AWS, acesse **Provisionamento**.

    ![Captura de tela do aplicativo AWS, com Provisionamento realçado](./media/amazon-web-service-tutorial/provisioning.png)

2. Insira a chave de acesso e o segredo nos campos **clientsecret** e **Token do Segredo**, respectivamente.

    ![Captura de tela da caixa de diálogo Credenciais de Administrador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Insira a chave de acesso do usuário do AWS no campo **Segredo do cliente**.

    b. Insira o segredo do usuário do AWS no campo **Segredo do Token**.

    c. Selecione **Testar Conexão**.

    d. Salve a configuração selecionando **Salvar**.

3. Na seção **Configurações**, para **Status de Provisionamento**, selecione **Ativado**. Em seguida, selecione **Salvar**.

    ![Captura de tela da seção Configurações, com Ativado realçado](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> O serviço de provisionamento só importará funções da AWS para o Azure AD. Esse serviço não provisionará usuários e grupos do Azure AD novamente para a AWS.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar usuário de teste da AWS (Amazon Web Services)

O objetivo desta seção é criar uma usuária chamada B.Fernandes na AWS (Amazon Web Services). O AWS (Amazon Web Services) não precisa que um usuário seja criado em seu sistema para o SSO, portanto você não precisa realizar nenhuma ação aqui.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco AWS (Amazon Web Services) no Painel de Acesso, você deve fazer logon automaticamente na AWS (Amazon Web Services) para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conhecidos

 * Na seção **Provisionamento**, a subseção **Mapeamentos** mostra uma mensagem "Carregando..." e nunca exibe os mapeamentos de atributo. O único fluxo de trabalho de provisionamento compatível hoje é a importação de funções do AWS no Azure AD para seleção durante a atribuição de um usuário ou grupo. Os mapeamentos de atributo para isso são predeterminados e não são configuráveis.

 * A seção **Provisionamento** só dá suporte a um conjunto de credenciais para um locatário do AWS por vez. Todas as funções importadas são escritas na propriedade `appRoles` do [`servicePrincipal` objeto](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do Azure AD para o locatário do AWS.

   Vários locatários AWS (representados por `servicePrincipals`) podem ser adicionados ao Azure AD da galeria para provisionamento. Há um problema conhecido, no entanto, sem ser possível gravar automaticamente todas as funções importadas de vários `servicePrincipals` do AWS usados para provisionamento em um único `servicePrincipal` usado para SSO.

   Como alternativa, você pode usar a [API do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) para extrair todo o `appRoles` importado em cada `servicePrincipal` do AWS em que o provisionamento é configurado. Posteriormente, você pode adicionar essas cadeias de caracteres de função ao `servicePrincipal` do AWS em que o SSO está configurado.

* As funções deverão atender aos seguintes requisitos para que sejam elegíveis a serem importadas do AWS para o Azure AD:

  * As funções devem ter exatamente um provedor SAML definido no AWS

  * O comprimento combinado total do ARN da função e do ARN do provedor SAML para uma função que está sendo importada deve ser de até 119 caracteres

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar a AWS (Amazon Web Services) com o Azure AD](https://aad.portal.azure.com/)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png