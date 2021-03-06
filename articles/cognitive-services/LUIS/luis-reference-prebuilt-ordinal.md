---
title: Entidade predefinida ordinal-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida de ordinal de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b2a2d9e78a0b152da14bb737079cf0dfdef0dc05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491236"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entidade ordinal predefinida para um aplicativo LUIS
Número ordinal é uma representação numérica de um objeto dentro de um conjunto: `first`, `second`, `third`. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo ordinal às intenções do aplicativo. A entidade ordinal é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Tipos de ordinal
A entidade ordinal é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Resolução para entidade ordinal predefinida

Os seguintes objetos de entidade são retornados para a consulta:

`Order the second option`

#### <a name="v3-responsetabv3"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o parâmetro `verbose` definido como `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com o parâmetro `verbose` definido como `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.ordinal**.

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [número de telefone](luis-reference-prebuilt-phonenumber.md)e [temperatura](luis-reference-prebuilt-temperature.md) . 
