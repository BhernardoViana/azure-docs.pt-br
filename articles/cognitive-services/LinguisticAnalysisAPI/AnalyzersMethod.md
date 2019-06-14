---
title: O método Analisadores ‒ API de Análise Linguística
titlesuffix: Azure Cognitive Services
description: A API REST de analisadores fornece uma lista de analisadores que atualmente têm suporte na API de Análise Linguística.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60404692"
---
# <a name="analyzers-method"></a>Método de analisadores

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

A API REST de **analisadores** fornece uma lista de analisadores atualmente com suporte pelo serviço.
A resposta inclui os [nomes](Analyzer-Names.md) e os idiomas com suporte em cada um (como "en" para inglês).

## <a name="request-parameters"></a>Parâmetros da solicitação
Nenhum

<br>

## <a name="response-parameters"></a>Parâmetros de resposta

NOME | Type | DESCRIÇÃO
-----|------|--------------
idiomas | lista de cadeias de caracteres | lista de códigos de idioma ISO de duas letras para os quais esse analisador pode ser usado.
id   | cadeia de caracteres | ID exclusiva desse analisador
kind | cadeia de caracteres | aqui, o tipo de analisador amplo
especificação | cadeia de caracteres | o nome da especificação usada para esse analisador
implementação | cadeia de caracteres | descrição do modelo e/ou algoritmo desse analisador

<br>

## <a name="example"></a>Exemplo
GET /analisadores

Resposta: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
