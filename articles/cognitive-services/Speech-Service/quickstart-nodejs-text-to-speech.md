---
title: 'Início Rápido: Converter texto em fala, Node.js - Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá como converter texto em fala usando Node.js e API REST de Conversão de Texto em Fala. O texto de exemplo incluído neste guia é estruturado como Linguagem de marcação de síntese de Fala (SSML). Isso permite que você escolha a voz e o idioma da resposta de Fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a7713576565ca2632d7d91857040ece4d02c411b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621940"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Início Rápido: Converter texto em fala usando Node.js

Neste início rápido, você aprenderá como converter texto em fala usando Node.js e API REST de conversão de texto em Fala. O corpo da solicitação neste guia é estruturado como [Linguagem de marcação de síntese de Fala (SSML)](speech-synthesis-markup.md), que permite escolher a voz e o idioma da resposta.

Este início rápido requer uma [conta de serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviços de fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](get-started.md) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou seu editor de texto favorito
* Uma chave de assinatura do Azure para os Serviços de Fala. [Obtenha uma gratuitamente!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node.js usando seu editor ou IDE favorito. Em seguida, copie esse trecho de código para seu projeto em um arquivo denominado `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST de conversão de texto em fala requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Essa função substitui a sua chave de assinatura de serviços de fala para obter um token de acesso usando o `issueToken` ponto de extremidade.

Este exemplo pressupõe que sua assinatura dos serviços de fala é na região Oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `uri`. Para uma lista completa, consulte [Regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código em seu projeto:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [Autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Na próxima seção, criaremos a função para chamar a API de conversão de texto em fala e salvar a resposta de fala sintetizada.

## <a name="make-a-request-and-save-the-response"></a>Faça uma solicitação e salve a resposta

Aqui, você criará a solicitação à API de conversão de texto em fala e salvará a resposta de fala. Este exemplo supõe que você esteja usando o endpoint do West US. Se o seu recurso estiver registrado em uma região diferente, atualize o `uri`. Para obter mais informações, consulte [regiões de serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, você precisa adicionar cabeçalhos obrigatórios para a solicitação. Certifique-se de atualizar `User-Agent` com o nome do seu recurso (localizado no portal do Azure) e defina `X-Microsoft-OutputFormat` como sua saída de áudio preferida. Para obter uma lista completa dos formatos de saída, consulte [Saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo da solicitação usando a Linguagem de marcação de síntese de Fala (SSML). Esta amostra define a estrutura e usa a entrada `text` criada anteriormente.

>[!NOTE]
> Este exemplo usa a fonte de voz `JessaRUS`. Para obter uma lista completa das vozes / idiomas fornecidos pela Microsoft, consulte [Suporte ao idioma](language-support.md).
> Se você estiver interessado em criar uma voz exclusiva e reconhecível para sua marca, consulte [Criando fontes de voz personalizadas](how-to-customize-voice-font.md).

Finalmente, você fará uma solicitação ao serviço. Se a solicitação for obtida com êxito e um código de status 200 for retornado, a resposta de fala será gravada como `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

Você está quase lá. A última etapa é criar uma função assíncrona. Essa função lerá a sua chave de assinatura de uma variável de ambiente, o prompt para texto, obter um token, aguarde até que a solicitação para concluir, em seguida, converter o texto em fala e salvar o áudio como um. wav.

Se você não estiver familiarizado com as variáveis de ambiente ou prefere testar com seu codificado de chave de assinatura como uma cadeia de caracteres, substitua `process.env.SPEECH_SERVICE_KEY` com sua chave de assinatura como uma cadeia de caracteres.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

É isso aí, você está pronto para executar seu aplicativo de amostra de texto para fala. Na linha de comando (ou sessão de terminal), navegue até o diretório do seu projeto e execute:

```console
node tts.js
```

Quando solicitado, digite o que você deseja converter de conversão de texto em fala. Se bem sucedido, o arquivo de fala está localizado na pasta do seu projeto. Reproduzi-lo usando o player de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Consulte também

* [Referência de API de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar fontes de voz personalizada](how-to-customize-voice-font.md)
* [Grave amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
