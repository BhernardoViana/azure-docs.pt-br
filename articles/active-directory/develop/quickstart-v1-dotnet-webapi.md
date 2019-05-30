---
title: Criar um .NET Web API que se integre ao Azure AD para autenticação e autorização | Microsoft Docs
description: Como compilar uma API Web do .NET MVC que se integre ao Azure AD para autenticação e autorização.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2eca253bc5d1495d26506e0e6f8a83762e8bc5
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001098"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Início Rápido: Criar uma API Web do .NET que se integre ao Azure AD para autenticação e autorização

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Se você estiver criando um aplicativo que fornece acesso a recursos protegidos, você precisará saber como proteger esses recursos de acessos não garantidos. O Azure AD (Azure Active Directory) faz com que seja simples e direto ajudar a proteger uma API Web usando tokens de acesso de portador do OAuth 2.0 com apenas algumas linhas de código.

Em aplicativos Web ASP.NET, você pode executar essa proteção usando a implementação da Microsoft do middleware OWIN voltado à comunidade, incluído no .NET Framework 4.5. Aqui, você usará o OWIN para criar uma API Web de "Lista de Tarefas Pendentes" que:

* Designa quais APIs estão protegidas.
* Valida que as chamadas à API Web contêm um token de acesso válido.

Neste início rápido, você criará a API de lista de tarefas pendentes e aprenderá a:

1. Registrar um aplicativo com o Azure AD.
2. Configurar o aplicativo para usar o pipeline de autenticação OWIN.
3. Configurar um aplicativo cliente para chamar a API Web.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos:

* [Baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [baixe a amostra completa](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Cada um é uma solução do Visual Studio 2013.
* Tenha um locatário do Azure AD no qual registrar seu aplicativo. Se você ainda não tiver um locatário, [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Etapa 1: Registrar um aplicativo com o Active Directory do Azure

Para proteger seu aplicativo, você primeiro precisará criar um aplicativo em seu locatário e fornecer algumas informações cruciais ao Azure AD.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Escolha seu locatário do Azure AD selecionando sua conta no canto superior direito da página, selecione a navegação **Mudar diretório** e, em seguida, selecione o locatário apropriada.
    * Ignore esta etapa, se você tiver apenas um locatário do Microsoft Azure Active Directory em sua conta ou se você já selecionou apropriado locatário do Azure AD.

3. No painel de navegação esquerdo, selecione **Azure Active Directory**.
4. Selecione **Registros de aplicativo** e **Novo registro**.
5. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
6. Selecione a plataforma **Web** na seção **URI de Redirecionamento** e defina o valor como `https://localhost:44321/` (o local para o qual o Microsoft Azure AD retornará os tokens).
7. Ao terminar, selecione **Registrar**. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** .
6. Selecione **Expor uma API** e, em seguida, clique em **Definir** para atualizar o URI da ID do Aplicativo. Insira um identificador específico ao locatário. Por exemplo, insira: `https://contoso.onmicrosoft.com/TodoListService`.
7. Salve a configuração. Deixe o portal aberto, pois você também precisará registrar seu aplicativo cliente em breve.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Etapa 2: Configurar o aplicativo para usar o pipeline de autenticação OWIN

Para validar tokens e solicitações de entrada, você precisa configurar seu aplicativo para se comunicar com o Azure AD.

1. Para começar, abra a solução e adicione os pacotes do NuGet de middleware OWIN ao projeto TodoListService usando o Console do Gerenciador de Pacotes.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adicionar uma classe de inicialização OWIN no projeto TodoListService chamado `Startup.cs`.  Clique com o botão direito do mouse no projeto, selecione **Adicionar > Novo Item** e pesquise por **OWIN**. O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.

3. Altere a declaração de classe para `public partial class Startup`. Já implementamos parte dessa classe para você em outro arquivo. No método `Configuration(…)`, faça uma chamada para `ConfgureAuth(…)` para configurar a autenticação para seu aplicativo Web.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(…)`. Os parâmetros que você fornecer em `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o Azure AD. Para usá-los, é necessário usar classes no namespace de `System.IdentityModel.Tokens`.

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Use os atributos `[Authorize]` para proteger os controladores e as ações com a autenticação de portador JWT (Token Web JSON). Decore a classe `Controllers\TodoListController.cs` com uma marca de autorização, que força o usuário a entrar antes de acessar essa página.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Quando um chamador autorizado invoca com êxito uma das `TodoListController` APIs, a ação pode precisar ter acesso às informações sobre o chamador. O OWIN fornece acesso às declarações dentro do token de portador por meio do objeto `ClaimsPrincpal` .  

6. Um requisito comum para APIs Web é validar os "escopos" presentes no token para garantir que o usuário final concedeu as permissões necessárias para acessar o serviço de lista de tarefas pendentes.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Abra o arquivo `web.config` na raiz do projeto TodoListService e insira os valores de configuração na seção `<appSettings>`.
    * `ida:Tenant` é o nome do seu locatário do Azure AD – por exemplo, contoso.onmicrosoft.com.
    * `ida:Audience` é o URI de ID do aplicativo que você inseriu no Portal do Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Etapa 3: Configurar um aplicativo cliente e executar o serviço

Antes de poder ver o serviço de Lista de Tarefas Pendentes funcionando, você precisa configurar o cliente de Lista de Tarefas Pendentes para que ele possa obter tokens do Azure AD e fazer chamadas para o serviço.

1. Retorne ao [Portal do Azure](https://portal.azure.com).
1. Crie um novo registro de aplicativo em seu locatário do Microsoft Azure AD.  Insira um **nome** que descreva o aplicativo para os usuários, insira `http://TodoListClient/` como valor do **URI de Redirecionamento** e selecione **Cliente público (móvel e da área de trabalho)** , na lista suspensa.
1. Depois de concluir o registro, o Azure AD atribui uma identificação exclusiva do aplicativo ao seu aplicativo. Você precisará desse valor nas próximas etapas, portanto, copie-o da página do aplicativo.
1. Selecione **Permissões de API** e **Adicionar uma permissão**.  Localize e selecione o serviço de Lista de Tarefas Pendentes, adicione a permissão **user_impersonation Access TodoListService**, em **Permissões Delegadas**, e selecione **Adicionar permissões**.
1. No Visual Studio, abra `App.config` no projeto TodoListClient e insira seus valores de configuração na seção `<appSettings>`.

    * `ida:Tenant` é o nome do seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com.
    * `ida:ClientId` é a ID do aplicativo que você copiou do Portal do Azure.
    * `todo:TodoListResourceId` é o URI da ID do aplicativo de serviço de Lista de Tarefas Pendentes que você inseriu no Portal do Azure.

1. Limpe, crie e execute cada projeto.
1. Se você ainda não fez isso, crie um novo usuário em seu locatário com um domínio *.onmicrosoft.com.
1. Entre no cliente de lista de tarefas com esse usuário e adicione algumas tarefas na lista de tarefas pendentes do usuário.

## <a name="next-steps"></a>Próximas etapas

* Para referência, baixe a amostra concluída (sem seus valores de configuração) do [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Agora, você pode seguir para outros cenários de identidade.
