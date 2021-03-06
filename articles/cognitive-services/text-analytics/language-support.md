---
title: Idiomas compatíveis – API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista dos idiomas naturais compatíveis com a API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimento, extração de frases-chave, detecção de idioma e reconhecimento de entidade.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 529568cf4f667e72b0b4fb7ba83d0b85c4a90341
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499988"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Idiomas e regiões compatíveis com a API de Análise de Texto

Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimentos, extração de frases-chave, detecção de idioma e reconhecimento de entidade nomeada.

## <a name="language-detection"></a>Detecção de Idioma

O API de Análise de Texto pode detectar uma ampla gama de linguagens, variantes, dialetos e alguns idiomas regionais/culturais.  A detecção de idioma retorna o "script" de um idioma. Por exemplo, a frase "Eu tenho um cachorro" retornará `en`, em vez de `en-US`. O único especial é chinês, em que a funcionalidade de detecção de idioma retornará `zh_CHS` ou `zh_CHT` se ela puder determinar o script fornecido pelo texto fornecido. Em situações em que um script específico não pode ser identificado para um documento em chinês, ela retornará simplesmente `zh`.

Não publicamos a lista exata de idiomas para esse recurso, mas ele pode detectar uma ampla variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais. 

Se você tiver um conteúdo expresso em um idioma usado com menos frequência, experimente a Detecção de Idioma para ver se ela retorna um código. A resposta para idiomas que não pode ser detectada é `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Reconhecimento de entidade Análise de Sentimento, Extração de Frases-chave e nomeada

Para análise de sentimento, extração de frases-chave e reconhecimento de entidade, a lista de idiomas compatíveis é mais seletiva, uma vez que os analisadores são refinados para acomodar as regras linguísticas de idiomas adicionais. No reconhecimento de entidade nomeada v2, o suporte para o conjunto completo de [tipos de entidade](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition-v2) está atualmente limitado aos seguintes idiomas: 
* Inglês
* Chinês-simplificado
* Francês
* Alemão
* Espanhol

Somente as `Person`, `Location` e `Organization` entidades nomeadas são retornadas para os outros idiomas.

## <a name="language-list-and-status"></a>Status e lista de idiomas

O suporte a idiomas é implantado inicialmente em versão prévia, passando para o status GA (disponível ao público em geral), de modo independente um do outro e do serviço de Análise de Texto como um todo. É possível que idiomas permaneçam em versão prévia mesmo quando a API de Análise de Texto passar para disponível ao público em geral.

> [!NOTE]
> Para obter suporte de idioma detalhado para a visualização pública de reconhecimento de entidade nomeada (NER) v3, consulte [tipos de entidade nomeada](named-entity-types.md).

| idioma              | Código de idioma | Sentimento | Frases principais | Reconhecimento de Entidade Nomeada | Vinculação de entidade |       Observações        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Árabe                |     `ar`      |           |             |           ✔ \*           |                |                    |
| Tcheco                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| Chinês-simplificado    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                |                    |
| Chinês-tradicional   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| Dinamarquês                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Holandês                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Inglês               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| Finlandês               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Francês                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Alemão                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| Grego                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| Húngaro             |     `hu`      |           |             |           ✔ \*           |                |                    |
| Italiano               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Japonês              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| Coreano                |     `ko`      |           |      ✔      |           ✔ \*           |                |                    |
| Norueguês (Bokmål)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Polonês                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Português (Portugal) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` também é aceito |
| Português (Brasil)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| Russo               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Espanhol               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| Sueco               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| Turco               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

o suporte ao idioma \* está em versão prévia

\** também disponível no [análise de sentimento v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview) e/ou em visualizações públicas do [reconhecimento de entidade nomeada v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview) .

## <a name="see-also"></a>Consulte também

[Página Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)   
[Página do Produto de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
