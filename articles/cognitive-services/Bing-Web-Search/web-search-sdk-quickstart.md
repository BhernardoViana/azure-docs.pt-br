---
title: 'Início Rápido: Usar o SDK de Pesquisa na Web do Bing para C#'
titleSuffix: Azure Cognitive Services
description: O SDK de Pesquisa na Web do Bing torna fácil integrar a Pesquisa na Web do Bing ao seu aplicativo C#. Neste início rápido, você aprenderá a criar uma instância de um cliente, enviar uma solicitação e imprimir a resposta.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: aahi
ms.openlocfilehash: ae536166d75af48ea653b256924b5432cfd1f5ef
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309973"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Início Rápido: Usar o SDK de Pesquisa na Web do Bing para C#

O SDK de Pesquisa na Web do Bing torna fácil integrar a Pesquisa na Web do Bing ao seu aplicativo C#. Neste início rápido, você aprenderá a criar uma instância de um cliente, enviar uma solicitação e imprimir a resposta.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Veja também [Preços dos Serviços Cognitivos - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão alguns requisitos que serão necessários antes de executar este início rápido:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) ou
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# para Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [Gerenciador de Pacotes do NuGet](https://github.com/jmrog/vscode-nuget-package-manager)
* [SDK do .Net Core](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Criar um projeto e instalar dependências

> [!TIP]
> Obtenha o código mais recente como uma solução do Visual Studio a partir do [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

A primeira etapa é criar um novo projeto de console. Se você precisar de ajuda para configurar um projeto de console, veja [Olá, Mundo – seu primeiro programa (guia de programação em C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Para usar o SDK de Pesquisa na Web do Bing no seu aplicativo, você precisará instalar `Microsoft.Azure.CognitiveServices.Search.WebSearch` usando o Gerenciador de Pacotes do NuGet.

O [pacote do SDK de Pesquisa na Web](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) também instala:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Declarar dependências

Abra o projeto no Visual Studio ou Visual Studio Code e importe estas dependências:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Criar scaffolding do projeto

Quando você criou seu novo projeto de console, um namespace e uma classe para o seu aplicativo devem ter sido criados. O programa deve ter esta aparência:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

Nas seções a seguir, vamos criar nosso aplicativo de exemplo nessa classe.

## <a name="construct-a-request"></a>Construir uma solicitação

Esse código cria a consulta de pesquisa.

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Tratar da resposta

Em seguida, vamos adicionar algum código para analisar a resposta e imprimir os resultados. O `name` e a `url` para a primeira página da Web, imagem, artigo de notícias e vídeo serão impressos se estiverem presentes no objeto de resposta.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Declarar o método Main

Neste aplicativo, o método principal inclui código que instancia o cliente, valida a `subscriptionKey` e chama `WebResults`. Insira uma chave de assinatura válida para sua conta do Azure antes de continuar.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Executar o aplicativo

Vamos executar o aplicativo!

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Definir funções e filtrar resultados

Agora que você criou sua primeira chamada à API de Pesquisa na Web do Bing, vamos examinar algumas funções que realçam a funcionalidade do SDK para refinar consultas e filtrar resultados. Cada função pode ser adicionada ao seu aplicativo C# criado na seção anterior.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitar o número de resultados retornados pelo Bing

Este exemplo usa os parâmetros `count` e `offset` para limitar o número de resultados retornados para "Melhores restaurantes em Seattle". São impressos o `name` e a `URL` para o primeiro resultado.

1. Adicione este código ao seu projeto de console:
    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Adicione `WebResultsWithCountAndOffset` à `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Execute o aplicativo.

### <a name="filter-for-news"></a>Filtro de notícias

Este exemplo usa o parâmetro `response_filter` para filtrar os resultados da pesquisa. Os resultados da pesquisa retornados são limitados a artigos de notícias para "Microsoft". São impressos o `name` e a `URL` para o primeiro resultado.

1. Adicione este código ao seu projeto de console:
    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Adicione `WebResultsWithCountAndOffset` à `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Execute o aplicativo.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Use a pesquisa segura, a contagem de respostas e o filtro de promoção

Este exemplo usa os parâmetros `answer_count`, `promote` e `safe_search` para filtrar os resultados da pesquisa para "Vídeos de Música". São exibidos o `name` e a `URL` para o primeiro resultado.

1. Adicione este código ao seu projeto de console:
    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Adicione `WebResultsWithCountAndOffset` à `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Execute o aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com este projeto, remova sua chave de assinatura do código do aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplos de SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
