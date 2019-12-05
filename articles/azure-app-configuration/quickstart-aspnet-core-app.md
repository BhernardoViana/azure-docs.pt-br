---
title: Início Rápido da Configuração de Aplicativo do Azure com o ASP.NET Core | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 91712b3f730317e65cda7b48c8f5636b2fb9ab2c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185086"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativo do Azure

Neste guia de início rápido, você incorpora a Configuração de Aplicativos do Azure em um aplicativo ASP.NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código. O ASP.NET Core cria um único objeto de configuração baseado no par chave-valor, usando as configurações de uma ou mais fontes de dados especificadas por aplicativo. Essas origens de dados são conhecidas como *provedores de configuração*. Como o cliente .NET Core da Configuração de Aplicativo é implementado como um provedor, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK do .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gerenciador de Configurações** >  **+ Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | Branco |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Preto |
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

## <a name="create-an-aspnet-core-web-app"></a>Criar um aplicativo Web ASP.NET Core

Você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo Web do MVC do ASP.NET Core. A vantagem de usar a CLI do .NET Core em relação ao Visual Studio é que ela está disponível nas plataformas Windows, MacOS e Linux.

1. Crie uma nova pasta para o seu projeto. Para este início rápido, nomeie-o *TestAppConfig*.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo Web do MVC do ASP.NET Core:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Adicionar o Gerenciador de Segredos

Para usar o Gerenciador de Segredos, adicione um elemento `UserSecretsId` ao arquivo *.csproj*.

- Abra o arquivo *.csproj*. Adicione um elemento `UserSecretsId`, conforme mostrado aqui. Use o mesmo GUID ou substitua esse valor pelo seu próprio. Salve o arquivo.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

A ferramenta Gerenciador de Segredos armazena dados confidenciais para o trabalho de desenvolvimento fora da árvore do seu projeto. Essa abordagem ajuda a impedir o compartilhamento acidental de segredos do aplicativo no código-fonte. Para obter mais informações sobre o Gerenciador de Segredos, confira [Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Adicione uma referência ao pacote NuGet `Microsoft.Azure.AppConfiguration.AspNetCore`, executando o seguinte comando:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Execute o seguinte comando para restaurar pacotes do projeto:

    ```CLI
        dotnet restore
    ```
3. Adicione um segredo chamado *ConnectionStrings:AppConfig* ao Gerenciador de Segredos.

    Esse segredo contém a cadeia de conexão para acessar o repositório de Configuração de Aplicativos. Substitua o valor no seguinte comando pela cadeia de conexão do repositório de Configuração de Aplicativos.

    Este comando deve ser executado no mesmo diretório que o arquivo *.csproj*.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Alguns shells truncarão a cadeia de conexão, a menos que ela seja colocada entre aspas. Verifique se a saída do comando `dotnet user-secrets` mostra toda a cadeia de conexão. Se não, execute novamente o comando, colocando a cadeia de conexão entre aspas.

    O Gerenciador de Segredos é usado apenas para testar o aplicativo Web localmente. Quando o aplicativo for implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/web), por exemplo, você usará uma configuração de aplicativo **Cadeias de Conexão** no Serviço de Aplicativo, em vez de armazenar a cadeia de conexão no Gerenciador de Segredos.

    Esse segredo é acessado com a API de configuração. Um sinal de dois pontos (:) funciona no nome da configuração com a API de configuração em todas as plataformas com suporte. Consulte [Configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Abra *Program.cs* e adicione uma referência ao provedor da Configuração de Aplicativos do .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Atualize o método `CreateWebHostBuilder` para usar a Configuração de Aplicativos chamando o método `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` no .NET Core 3.0.  Selecione a sintaxe correta com base em seu ambiente.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Atualizar `CreateWebHostBuilder` para .NET Core 2.x

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Atualizar `CreateHostBuilder` para .NET Core 3.x

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Abra *Index.cshtml* no diretório Views > Página Inicial e substitua o conteúdo pelo seguinte código:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Abra *_Layout.cshtml* no diretório Views > Compartilhado e substitua o conteúdo pelo seguinte código:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Para criar o aplicativo usando a CLI do .NET Core, execute o seguinte comando no shell de comando:

    ```CLI
       dotnet build
    ```

2. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

    ```CLI
        dotnet run
    ```

3. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão do aplicativo Web hospedado localmente.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o usou com um aplicativo Web ASP.NET Core por meio do [Provedor da Configuração de Aplicativos](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber como configurar seu aplicativo ASP.NET Core para atualizar dinamicamente as configurações, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-aspnet-core.md)
