---
title: Regiões – Serviços de Fala
titlesuffix: Azure Cognitive Services
description: Referência para regiões do serviço de Fala.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 1ad618e9eb70fd75f433030584c0f6538532928f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466983"
---
# <a name="speech-service-supported-regions"></a>Regiões com suporte do Serviço de Fala

O Serviço de Fala permite que seu aplicativo converta áudio em texto, realize a tradução de fala e converta texto em Fala. O serviço está disponível em várias regiões com pontos de extremidade exclusivos para o SDK de Fala e APIs de REST.

Certifique-se de que você use o ponto de extremidade que corresponde à região de sua assinatura.

## <a name="speech-sdk"></a>SDK de fala

No [SDK de Fala](speech-sdk.md), regiões são especificadas como uma cadeia de caracteres (por exemplo, como um parâmetro para `SpeechConfig.FromSubscription` no SDK de Fala para C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Conversão de fala em texto, texto em fala e tradução

O Speech SDK está disponível nessas regiões para **reconhecimento de fala**, **texto em fala**, e **tradução**:

  Região | Parâmetro do SDK de Fala | Portal de personalização de Fala
 ------|-------|--------
 Oeste dos EUA | `westus` | https://westus.cris.ai
 Oeste dos EUA 2 | `westus2` | https://westus2.cris.ai
 Leste dos EUA | `eastus` | https://eastus.cris.ai
 Leste dos EUA 2 | `eastus2` | https://eastus2.cris.ai
 Centro dos EUA | `centralus` | https://centralus.cris.ai
 Centro-Norte dos EUA | `northcentralus` | https://northcentralus.cris.ai
 Centro-Sul dos Estados Unidos | `southcentralus` | https://southcentralus.cris.ai
 Índia Central | `centralindia` | https://centralindia.cris.ai
 Ásia Oriental | `eastasia` | https://eastasia.cris.ai
 Sudeste Asiático | `southeastasia` | https://southeastasia.cris.ai
 Leste do Japão | `japaneast` | https://japaneast.cris.ai
 Coreia Central | `koreacentral` | https://koreacentral.cris.ai
 Leste da Austrália | `australiaeast` | https://australiaeast.cris.ai
 Canadá Central | `canadacentral` | https://canadacentral.cris.ai
 Norte da Europa | `northeurope` | https://northeurope.cris.ai
 Europa Ocidental | `westeurope` | https://westeurope.cris.ai
 Sul do Reino Unido | `uksouth` | https://uksouth.cris.ai
 França Central | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Reconhecimento de intenção

As regiões disponíveis para **reconhecimento de intenção** por meio do SDK de Fala são os seguintes:

 Região global | Região | Parâmetro do SDK de Fala
 ------|-------|--------
 Ásia | Ásia Oriental | `eastasia`
 Ásia | Sudeste Asiático | `southeastasia`
 Austrália | Leste da Austrália | `australiaeast`
 Europa | Norte da Europa | `northeurope`
 Europa | Europa Ocidental | `westeurope`
 América do Norte | Leste dos EUA | `eastus`
 América do Norte | Leste dos EUA 2 | `eastus2`
 América do Norte | Centro-Sul dos Estados Unidos | `southcentralus`
 América do Norte | Centro-Oeste dos EUA | `westcentralus`
 América do Norte | Oeste dos EUA | `westus`
 América do Norte | Oeste dos EUA 2 | `westus2`
 América do Sul | Sul do Brasil | `brazilsouth`

Este é um subconjunto das regiões de publicação compatíveis com o [LUIS (Serviço Inteligente de Reconhecimento Vocal)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Assistentes virtuais que têm como prioridade o uso de voz

O [SDK de fala](speech-sdk.md) dá suporte a **Assistente de voz-first virtual** recursos nessas regiões:

Região | Parâmetro do SDK de Fala
-------|---------------------
Oeste dos EUA | `westus`
Oeste dos EUA 2 | `westus2`
Leste dos EUA | `eastus`
Leste dos EUA 2 | `eastus2`
Europa Ocidental | `westeurope`
Norte da Europa | `northeurope`
Sudeste Asiático | `southeastasia`

## <a name="rest-apis"></a>APIs REST

O serviço de fala também expõe pontos de extremidade REST para solicitações de Fala em texto e texto em Fala.

### <a name="speech-to-text"></a>Conversão de fala em texto

Para obter a documentação de referência de fala em texto, consulte [API REST de fala em texto](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Conversão de texto em fala

Para obter a documentação de referência de texto em fala, consulte [API REST do texto em fala](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]