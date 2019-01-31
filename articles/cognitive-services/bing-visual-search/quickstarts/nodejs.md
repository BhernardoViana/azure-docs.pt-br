---
title: 'Início Rápido: Obtenha insights de imagem usando a API REST da Pesquisa Visual do Bing e o Node.js'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem para a API da Pesquisa Visual do Bing e obter insights sobre ela.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 23c3e167f2fd1544a42c98946f4ae95d3451dafe
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180612"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Início Rápido: Obtenha insights de imagem usando a API REST da Pesquisa Visual do Bing e o Node.js

Use este início rápido para fazer sua primeira chamada à API da Pesquisa Visual do Bing e exibir os resultados da pesquisa. Este aplicativo JavaScript simples carrega uma imagem para a API e exibe as informações retornadas sobre ela. Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Ao carregar uma imagem local, os dados de formulário devem incluir o cabeçalho Content-Disposition. Seu parâmetro `name` precisa ser definido como "image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário é o binário da imagem. O tamanho máximo de imagem que você pode fazer upload é 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* O módulo de Solicitação para JavaScript
    * Você pode instalar esse módulo usando `npm install request`
* O módulo de dados de formulário
    * Você pode instalar esse módulo usando `npm install form-data`


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e defina os seguintes requisitos:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Crie variáveis para seu ponto de extremidade de API, a chave de assinatura e o caminho para a imagem.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Crie uma função chamada `requestCallback()` para imprimir a resposta da API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construir e enviar a solicitação de pesquisa

1. Crie um novo dado de formulário usando `FormData()` e acrescente o caminho da imagem a ele usando `fs.createReadStream()`.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Use a biblioteca de solicitação para carregar a imagem, chamando `requestCallback()` para imprimir a resposta. Adicione sua chave de assinatura ao cabeçalho de solicitação. 

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de Pesquisa Personalizada](../tutorial-bing-visual-search-single-page-app.md)
