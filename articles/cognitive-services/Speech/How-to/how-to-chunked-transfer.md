---
title: Como Realizar Transferência em Partes da Transmissão de Áudio| Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Como realizar transferência em partes para enviar transmissão de áudio para o serviço de Fala do Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d9796cf60e2695c21d781131c935d24891401efa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515000"
---
# <a name="chunked-transfer-encoding"></a>Codificação de transferência de conteúdo

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Para transcrever a fala em texto, API de Reconhecimento de Fala da Microsoft permite que você envie o áudio como um bloco inteiro ou recorte o áudio em pequenas partes. Para transmissão de áudio eficiente e reduzir a latência de transcrição, é recomendável que você use a [codificação de transferência em partes](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) ao fluxo de áudio para o serviço. Outras implementações podem resultar em maior latência percebida pelo usuário. Para saber mais, confira a página de [Transmissões de Áudio por Streaming](../concepts.md#audio-streams).

> [!NOTE]
> Você não pode carregar mais de 10 segundos de áudio em qualquer solicitação e a duração total de solicitação não pode exceder 14 segundos.
> [!NOTE]
> Você precisa especificar a codificação da transferência em partes somente se você usar o [APIs REST](../GetStarted/GetStartedREST.md) para chamar o serviço de fala. Aplicativos que usam [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) não são necessários para configurar a codificação de transferência em partes.

O código a seguir mostra como definir a codificação de transferência em partes e para enviar um arquivo de áudio sendo em bloco em partes de 1024 bytes.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
