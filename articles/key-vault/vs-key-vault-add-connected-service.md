---
title: Adicionar suporte do Key Vault ao projeto do ASP.NET usando o Visual Studio - Azure Key Vault | Microsoft Docs
description: Use este tutorial para aprender a adicionar suporte do Key Vault para um aplicativo Web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 27c21171c2a53cb739215dcae070b94c8610a490
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880878"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Key Vault ao seu aplicativo Web usando os Serviços Conectados do Visual Studio

Neste tutorial, você aprenderá como adicionar facilmente tudo o que você precisa para começar a usar o Azure Key Vault para gerenciar seus segredos para projetos Web no Visual Studio, quer esteja usando o ASP.NET Core ou qualquer outro tipo de projeto do ASP.NET. Usando o recurso serviços conectados no Visual Studio, você pode fazer com que o Visual Studio adicione automaticamente todos os pacotes NuGet e definições de configuração que você precisa para se conectar ao Key Vault no Azure.

Para obter detalhes sobre as alterações que os Serviços Conectados realizam em seu projeto para habilitar o Key Vault, confira [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) ou [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma assinatura do Azure**. Se você não tiver uma assinatura, Inscreva-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 versão 16,3 Preview 1** ou posterior ou **visual studio 2017 versão 15,7** com a carga de trabalho de **desenvolvimento da Web** instalada. [Baixe agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Para ASP.NET (não núcleo) com o Visual Studio 2017, você precisa das ferramentas de desenvolvimento .NET Framework 4.7.1 ou posterior, que não são instaladas por padrão. Para instalá-las, inicie o Instalador do Visual Studio, escolha **Modificar** e, em seguida, selecione **Componentes Individuais**. Em seguida, do lado direito, expanda **Desenvolvimento Web e do ASP.NET** e selecione **Ferramentas de Desenvolvimento do .NET Framework 4.7.1**.
- Um ASP.NET 4.7.1 ou posterior, ou ASP.NET Core projeto da Web 2,0 aberto.

## <a name="add-key-vault-support-to-your-project"></a>Como adicionar suporte do Key Vault ao seu projeto

Antes de começar, verifique se você está conectado ao Visual Studio. Entre com a mesma conta que você usa para sua assinatura do Azure. Em seguida, abra um ASP.NET 4.7.1 ou posterior, ou ASP.NET Core projeto Web 2,0, e execute as seguintes etapas:

1. No **Gerenciador de Soluções**, selecione **Adicionar** > **Serviço Conectado**.
   A página do Serviço Conectado aparece com os serviços que você pode adicionar ao seu projeto.
1. No menu de serviços disponíveis, selecione **Proteger Segredos com o Azure Key Vault**.

   ![Selecione "Proteger Segredos com o Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecione a assinatura que você deseja usar e, em seguida, escolha uma Key Vault nova ou existente. Se você escolher o novo Key Vault, um link de **edição** será exibido. Selecione-o para configurar o novo Key Vault.

   ![Selecione sua assinatura](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Em **editar Azure Key Vault**, insira o nome que você deseja usar para o Key Vault.

1. Selecione um **grupo de recursos**existente ou escolha criar um novo com um nome exclusivo gerado automaticamente.  Se você quiser criar um novo grupo com um nome diferente, poderá usar o [portal do Azure](https://portal.azure.com)e, em seguida, fechar a página e reiniciar para recarregar a lista de grupos de recursos.
1. Escolha o **local** no qual criar o Key Vault. Se seu aplicativo Web estiver hospedado no Azure, escolha a região que hospeda o aplicativo Web para um desempenho ideal.
1. Escolha um **tipo de preço**. Para obter mais detalhes, confira [Preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Escolha **OK** para aceitar as opções de configuração.
1. Depois de selecionar um Key Vault existente ou ter configurado um novo Key Vault, na guia **Azure Key Vault** do Visual Studio, selecione **Adicionar** para adicionar o serviço conectado.
1. Selecione o link **gerenciar segredos armazenados neste Key Vault** para abrir a página **segredos** de seu Key Vault. Se você fechou a página ou o projeto, pode navegar para ele na [portal do Azure](https://portal.azure.com) escolhendo **todos os serviços** e, em **segurança**, escolhendo **Key Vault**e, em seguida, escolher o Key Vault.
1. Na seção do Key Vault para o cofre de chaves que você criou, selecione **Segredos** e, em seguida, **Gerar/importar**.

   ![Gerar/Importar um segredo](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Insira um segredo, como *MySecret* e dê a ele qualquer valor de cadeia de caracteres como um teste e, em seguida, selecione o botão **criar** .

   ![Criar um segredo](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (opcional) Insira outro segredo, mas, desta vez, coloque-o em uma categoria nomeando-o como *Segredos--MySecret*. Essa sintaxe especifica uma categoria "segredos" que contém um segredo "MySecret".

Agora, você pode acessar seus segredos no código. As próximas etapas são diferentes dependendo se você estiver usando o ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Acesse seus segredos em código

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , localize e instale esses dois pacotes NuGet: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft. Azure. keyvault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. Selecione a `Program.cs` guia e substitua a classe programa pelo seguinte código:

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. Em seguida `About.cshtml.cs` , abra o arquivo e escreva o seguinte código:
   1. Inclua uma referência para `Microsoft.Extensions.Configuration` isso usando a instrução:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Adicione este construtor:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Atualize o `OnGet` método. Atualize o valor do espaço reservado mostrado aqui com o nome do segredo que você criou nos comandos acima.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Execute o aplicativo localmente navegando até a página sobre. Você deve ver seu valor secreto recuperado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **digite o nome do grupo de recursos:** , insira o nome do grupo de recursos e selecione **excluir**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como seu projeto de ASP.NET Core é modificado

Esta seção identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o serviço conectado do Key Vault usando o Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referências adicionadas para ASP.NET Core

Afeta as referências de .NET do arquivo de projeto e as referências de pacote NuGet.

| Tipo | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Arquivos adicionados para ASP.NET Core

- `ConnectedService.json`adicionado, que registra algumas informações sobre o provedor de serviços conectado, a versão e um link da documentação.

### <a name="project-file-changes-for-aspnet-core"></a>Alterações de arquivo de projeto para ASP.NET Core

- Adicionado o grupo e `ConnectedServices.json` o arquivo dos serviços conectados.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>alterações de launchsettings. JSON para ASP.NET Core

- Adição das seguintes entradas de variável de ambiente ao perfil do IIS Express e ao perfil que coincide com o nome do seu projeto da Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Alterações no Azure para ASP.NET Core

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Como seu projeto do ASP.NET Framework é modificado

Esta seção identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o serviço conectado do Key Vault usando o Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Referências adicionadas para a estrutura ASP.NET

Afeta o arquivo de projeto referências do `packages.config` .net e (referências do NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Arquivos adicionados para a estrutura ASP.NET

- `ConnectedService.json`adicionado, que registra algumas informações sobre o provedor de serviços conectado, a versão e um link para a documentação.

### <a name="project-file-changes-for-aspnet-framework"></a>Alterações de arquivo de projeto para a estrutura ASP.NET

- Adicione o ItemGroup dos Serviços Conectados e o arquivo ConnectedServices.json.
- Referências aos assemblies .NET descritos na seção [Referências adicionadas](#added-references-for-aspnet-framework).

### <a name="webconfig-or-appconfig-changes"></a>alterações de web.config ou app.config

- Adicionadas as seguintes entradas de configuração:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Alterações no Azure para ASP.NET Framework

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o desenvolvimento de Key Vault lendo o [Guia do desenvolvedor do Key Vault](key-vault-developers-guide.md).
