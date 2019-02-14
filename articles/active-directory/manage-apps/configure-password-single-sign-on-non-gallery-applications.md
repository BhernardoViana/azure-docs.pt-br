---
title: Como configurar o logon único com senha para um aplicativo inexistente na galeria | Microsoft Docs
description: Como configurar um aplicativo personalizado inexistente na galeria para logon único baseado em senha quando ele não está listado na Galeria de Aplicativos do Azure AD
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: barbkess
ms.openlocfilehash: 934996f1573520a6fba92ce09f8a14fc4795de6c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812441"
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo inexistente na galeria

Além das opções encontradas na Galeria de Aplicativos do Azure AD, você também tem a opção de adicionar um **aplicativo inexistente na galeria** quando o aplicativo desejado não estiver nela. Usando esse recurso, é possível adicionar qualquer aplicativo que já existe em sua organização ou qualquer aplicativo de terceiros que você usa e que é de um fornecedor que ainda não faz parte da [Galeria de Aplicativos do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Após adicionar um aplicativo inexistente na galeria, você pode configurar o método de logon único usado por esse aplicativo selecionando o item de navegação **Logon único** em um aplicativo empresarial no [Portal do Azure](https://portal.azure.com/).

Um dos métodos de logon único disponíveis para você é a opção [Logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Com a experiência de **adicionar um aplicativo inexistente na galeria**, você pode integrar qualquer aplicativo que renderiza um campo de entrada de nome de usuário e senha em HTML, mesmo que ele não esteja em nosso conjunto de aplicativos pré-integrados.

Isso funciona com uma tecnologia de verificação de página que faz parte da extensão do Painel de Acesso e que permite a detecção automática de campos de entrada de usuário e senha, bem como seu armazenamento seguro para a instância do aplicativo específico. Em seguida, reproduza com segurança os nomes de usuário e senhas nesses campos quando um usuário navegar para o aplicativo no painel de acesso do aplicativo.

Trata-se de uma ótima maneira de começar a integrar rapidamente qualquer tipo de aplicativo no Azure AD, e que lhe permite:

-   Integrar **qualquer aplicativo do mundo** a seu locatário do Azure AD, desde que ele renderize um campo de entrada de nome de usuário e senha em HTML

-   Habilitar o **logon único para os usuários** armazenando de forma segura e reproduzindo nomes de usuário e senhas do aplicativo integrado ao Azure AD

-   **Detectar automaticamente campos de entrada** para qualquer aplicativo e permitir que você detecte manualmente esses campos usando a extensão de Navegador do Painel de Acesso, caso a detecção automática não os encontre

-   **Dar suporte a aplicativos que exigem vários campos de entrada**, para aplicativos que exigem mais do que apenas os campos de nome de usuário e senha para entrar

-   **Personalizar os rótulos** dos campos de entrada de nome de usuário e senha que seus usuários veem no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando digitam suas credenciais

-   Permitir que os **usuários** forneçam seus próprios nomes de usuário e senhas para as contas de aplicativos existentes que eles estiverem digitando manualmente no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócios** especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso de [Autoatendimento de Acesso ao Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que um **administrador** especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso Atualizar Credenciais ao atribuir um usuário a um aplicativo

-   Permitir que um **administrador** especifique a senha ou nome de usuário compartilhado por um grupo de pessoas usando o recurso Atualizar Credenciais ao [atribuir um grupo a um aplicativo](#assign-an-application-to-a-group-directly)

A seção a seguir descreve como você pode habilitar o [Logon único baseado em senha](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) para qualquer aplicativo que você adicionar usando a experiência de **adicionar um aplicativo inexistente na galeria**.

## <a name="overview-of-steps-required"></a>Visão geral das etapas necessárias

Para configurar um aplicativo da galeria do Azure AD será necessário:

-   [Adicionar um aplicativo inexistente na galeria](#add-a-non-gallery-application)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

-   Atribuir o aplicativo a um usuário ou um grupo

    -   [Atribuir um usuário diretamente a um aplicativo](#assign-a-user-to-an-application-directly)

    -   [Atribuir um aplicativo diretamente a um grupo](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na galeria

Para adicionar um aplicativo da Galeria do Azure Active Directory, siga estas etapas:

1.  Abra o [Portal do Azure](https://portal.azure.com) e entre como um **Administrador Global** ou **Coadministrador**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  clique em **Aplicativo inexistente na galeria**.

7.  Insira o nome do aplicativo na caixa de texto **Nome**. Selecione **Adicionar.**

Após um curto período de tempo, você poderá ver o painel de configuração do aplicativo.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8.  Selecione o modo **Logon baseado em senha.**

9.  Insira a **URL de Logon**. Trata-se da URL em que os usuários inserem o nome de usuário e senha para entrar. Verifique se os campos de entrada estão visíveis na URL.

10. Atribuir usuários a um aplicativo.

11. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.


## <a name="assign-a-user-to-an-application-directly"></a>Atribuir um usuário diretamente a um aplicativo

Para atribuir um ou mais usuários a um aplicativo diretamente, siga estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7.  Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8.  Abra o painel **Adicionar Atribuição** clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos**.

9.  Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

## <a name="assign-an-application-to-a-group-directly"></a>Atribuir um aplicativo diretamente a um grupo

Para atribuir um ou mais usuários diretamente a um aplicativo, siga estas etapas:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7.  Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8.  Abra o painel **Adicionar Atribuição** clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos**.

9.  Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo do grupo** que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **grupo** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção próxima ao logotipo ou ao perfil do grupo para adicionar o usuário na lista **Selecionado**.

12. **Opcional:** Caso queira **adicionar mais de um grupo**, digite outro **nome de grupo completo** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse grupo à lista **Selecionado**.

13. Ao concluir a seleção dos grupos, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos grupos selecionados.

Após um breve período, os usuários selecionados poderão iniciar esses aplicativos no Painel de Acesso.

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
