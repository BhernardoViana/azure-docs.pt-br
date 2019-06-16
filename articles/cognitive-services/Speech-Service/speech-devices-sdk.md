---
title: Sobre o SDK dos Dispositivos de Fala – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: Introdução ao SDK de Dispositivos de Fala. Os Serviços de Fala funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5f3b0094834b1ca547ba5ddf9726068c9ea36079
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409097"
---
# <a name="about-the-speech-devices-sdk"></a>Sobre os dispositivos de fala do SDK

O [serviços de fala](overview.md) funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.

O SDK de dispositivos de fala pode ajudá-lo:

* Teste rapidamente novos cenários de voz.
* Integre mais facilmente os serviços de fala baseado em nuvem ao seu dispositivo.
* Crie uma experiência de usuário excepcional para seus clientes.

O SDK de dispositivos de fala consome os [Speech SDK](speech-sdk.md). Ele usa o SDK de fala para enviar áudio que é processado pelo nosso algoritmo de processamento de áudio avançada de matriz de microfone do dispositivo para o [serviços de fala](overview.md). Usa o áudio multicanal para fornecer um [reconhecimento de fala em campo distante](speech-to-text.md) mais preciso através da supressão de ruído, cancelamento de eco, beamforming e remoção da reverberação.

Você também pode usar o SDK de dispositivos de fala para criar o ambientes dispositivos que têm seus próprios [personalizado de ativação de word](speech-devices-sdk-create-kws.md) portanto, a indicação que inicia uma interação do usuário é exclusiva para sua marca.

O SDK de dispositivos de fala facilita uma variedade de cenários de habilitado para voz, como [personalizado primeiro voz Virtual assistentes](https://aka.ms/bots/speech/va), unidade-thru sistemas, de pedidos [transcrição de conversa](conversation-transcription-service.md), inteligente e falantes. Você pode responder aos usuários com texto, falar com eles com uma [voz padrão](how-to-customize-voice-font.md) ou personalizada, apresentar resultados de pesquisa, [traduzir](speech-translation.md) para outros idiomas e muito mais. Estamos ansiosos para ver suas criações.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha seu dispositivo de fala](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
