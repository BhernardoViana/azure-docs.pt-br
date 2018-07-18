---
title: Exemplo de conversão | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Aqui está um exemplo de conversão de fala.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028380"
---
# <a name="sample-for-translation"></a>Exemplo de conversão

> [!NOTE]
> Para obter instruções para fazer o download desta amostra e de outras, consulte [Samples for Speech SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Para todos os exemplos abaixo, as seguintes declarações de nível superior devem estar em vigor:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Usando o microfone de tradução

O trecho de código a seguir mostra como converter a entrada de fala do inglês para o alemão e também obter a saída de voz do texto traduzido. Ele usa o microfone.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Usando a entrada de arquivo de tradução

O trecho de código a seguir mostra como converter a entrada de fala do inglês, alemão e francês.
Ele usa o arquivo como entrada.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Exemplo de código fonte

A versão mais recente dos exemplos e amostras ainda mais avançadas estão em um repositório [dedicado do GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Próximas etapas

- [Reconhecimento de fala](./speech-to-text-sample.md)

- [Reconhecimento de intenção](./intent.md)
