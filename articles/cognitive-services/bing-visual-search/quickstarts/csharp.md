---
title: Início rápido de C# para API de Pesquisa Visual do Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Mostra como fazer upload de uma imagem para a API de Pesquisa Visual do Bing e obter insights sobre a imagem.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 930a89e3b1996c44f12bd3773565eda40e93ca9c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070919"
---
# <a name="your-first-bing-visual-search-query-in-c"></a>Sua primeira consulta de Pesquisa Visual do Bing em C#

API de Pesquisa Visual do Bing retorna informações sobre uma imagem que você fornece. Você pode fornecer a imagem usando a URL da imagem, um token de insights ou fazendo upload de uma imagem. Para obter informações sobre essas opções, consulte [O que é a API de Pesquisa Visual do Bing?](../overview.md) Este artigo demonstra como fazer upload de uma imagem. Fazer upload de uma imagem pode ser útil em cenários móveis em que você tira uma foto de um ponto de referência conhecido e obtém informações sobre ele. Por exemplo, os insights podem incluir fatos secundários sobre o ponto de referência. 

Se você fizer upload de uma imagem local, inclua no corpo do POST os dados de formulário mostrados a seguir. Os dados de formulário precisam incluir o cabeçalho Content-Disposition. Seu parâmetro `name` precisa ser definido como "image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário é o binário da imagem. O tamanho máximo de imagem que você pode fazer upload é 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Este artigo inclui um aplicativo de console simples que envia uma solicitação de API de Pesquisa Visual do Bing e exibe os resultados da pesquisa JSON. Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. 

O programa de exemplo usa somente classes .NET Core e é executado no Windows usando .NET CLR, ou no Linux ou no macOS usando [Mono](http://www.mono-project.com/).


## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Visual Studio 2017](https://www.visualstudio.com/downloads/) para obter esse código em execução no Windows. (O Community Edition gratuito funcionará.)

Para este guia de início rápido, você pode usar uma chave de assinatura de [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou uma chave de assinatura paga.

## <a name="running-the-application"></a>Executando o aplicativo

O exemplo a seguir mostra como enviar a mensagem usando HttpWebRequest. Para obter um exemplo que use HttpClient, HttpRequestMessage e MultipartFormDataContent, veja [Usando HttpClient](#using-httpclient).

Para executar o aplicativo, siga estas etapas:

1. Crie uma nova solução de Console no Visual Studio.
1. Substitua o conteúdo de `Program.cs` pelo código mostrado neste guia de início rápido.
2. Substitua o valor `accessKey` pela sua chave de assinatura.
2. Substitua o valor `imagePath` pelo caminho da imagem da qual o upload será feito.
3. Execute o programa.


```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;

namespace VisualSearchUpload
{

    class Program
    {
        // **********************************************
        // *** Update and verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Set the path to the image that you want to get insights of. 
        static string imagePath = @"<pathtoimagegoeshere>";

        // Boundary strings for form data in body of POST.
        const string CRLF = "\r\n";
        static string BoundaryTemplate = "batch_{0}";
        static string StartBoundaryTemplate = "--{0}";
        static string EndBoundaryTemplate = "--{0}--";

        const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
        const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;


        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        // Set up POST body.
                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var startFormData = BuildFormDataStart(boundary, filename);
                        var endFormData = BuildFormDataEnd(boundary);
                        var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);

                        var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }



        /// <summary>
        /// Verify that imagePath exists.
        /// </summary>
        static Boolean IsImagePathSet(string path)
        {
            if (string.IsNullOrEmpty(path))
                throw new ArgumentException("Image path is null or empty.");

            if (!File.Exists(path))
                throw new ArgumentException("Image path does not exist.");

            var size = new FileInfo(path).Length;

            if (size > 1000000)
                throw new ArgumentException("Image is greater than the 1 MB maximum size.");

            return true;
        }



        /// <summary>
        /// Get the binary characters of an image.
        /// </summary>
        static byte[] GetImageBinary(string path)
        {
            return File.ReadAllBytes(path);
        }


        /// <summary>
        /// Get the image's filename.
        /// </summary>
        static string GetImageFileName(string path)
        {
            return new FileInfo(path).Name;
        }


        /// <summary>
        /// Build the beginning part of the form data.
        /// </summary>
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }


        /// <summary>
        /// Build the ending part of the form data.
        /// </summary>
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }



        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }


            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```


## <a name="using-httpclient"></a>Usando HttpClient

Se você usar HttpClient, poderá usar MultipartFormDataContent para criar os dados do formulário. Use apenas as seções de código a seguir para substituir os mesmos métodos nomeados no exemplo anterior.

Substitua o método Main por este código:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Substitua o método BingImageSearch por este código:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```




## <a name="next-steps"></a>Próximas etapas

[Obter insights sobre uma imagem usando um token de insights](../use-insights-token.md)  
[Tutorial de upload de imagem da Pesquisa Visual do Bing](../tutorial-visual-search-image-upload.md)
[Tutorial de aplicativo de página única da Pesquisa Visual do Bing](../tutorial-bing-visual-search-single-page-app.md)
[Visão geral da Pesquisa Visual do Bing](../overview.md)  
[Experimente](https://aka.ms/bingvisualsearchtryforfree)  
[Obter uma chave de acesso de avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referência de API de Pesquisa Visual do Bing](https://aka.ms/bingvisualsearchreferencedoc)
