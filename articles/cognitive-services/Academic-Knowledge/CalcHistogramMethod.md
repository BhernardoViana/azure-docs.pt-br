---
title: Método CalcHistogram - API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método CalcHistogram para calcular a distribuição de valores de atributos para um conjunto de entidades de papel.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a228c5b90e47c9c24c5da70484a1a28f9a3054b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498815"
---
# <a name="calchistogram-method"></a>Método CalcHistogram

A API REST **calchistogram** é usada para calcular a distribuição de valores de atributo para um conjunto de entidades de papel.          


**Ponto de extremidade REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parâmetros de solicitação

NOME  |Value | Obrigatório?  |DESCRIÇÃO
-----------|----------|--------|----------
**expr**    |Cadeia de caracteres de texto | Sim  |Uma expressão de consulta que especifica as entidades sobre as quais calcular histogramas.
**modelo** |Cadeia de caracteres de texto | Não |Selecione o nome do modelo que você deseja consultar.  Atualmente, o valor padrão é o *mais recente*.
**atributos** | Cadeia de caracteres de texto | Não<br>padrão: | Uma lista delimitada por vírgulas que especifica os valores de atributo que são incluídos na resposta. Os nomes de atributo diferenciam maiúsculas de minúsculas.
**count** |Número | Não<br>Padrão: 10 |Número de resultados para retornar.
**offset**  |Número | Não<br>Padrão: 0 |Índice do primeiro resultado para retornar.
**timeout**  |Número | Não<br>Padrão: 1000 |Tempo limite em milissegundos. Somente interpretações localizadas antes que o tempo limite tenha decorrido serão retornadas.

## <a name="response-json"></a>Resposta (JSON)

NOME | DESCRIÇÃO
--------|---------
**expr**  |O parâmetro expr da solicitação.
**num_entities** | Número total de entidades correspondentes.
**histograms** |  Uma matriz de histogramas, uma para cada atributo especificado na solicitação.
**histograms[x].attribute** | Nome do atributo sobre o qual o histograma foi calculado.
**histograms[x].distinct_values** | Número de valores distintos entre a correspondência de entidades para este atributo.
**histograms[x].total_count** | Número total de instâncias de valores entre a correspondência de entidades para este atributo.
**histograms[x].histogram** | Dados de histograma para este atributo.
**histograms[x].histogram** |  Um valor para o atributo.
**histograms[x].histogram**  |Probabilidade de log natural total de entidades correspondentes com o valor de atributo.
**histograms[x].histogram[y].count**  |Número de entidades correspondentes com este valor do atributo.
**aulado** | Verdadeiro se a solicitação atingiu o tempo limite.


#### <a name="example"></a>Exemplo:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Neste exemplo, para gerar um histograma da contagem de publicações por ano para um determinado autor desde 2010, podemos primeiro gerar a expressão de consulta usando a API **interpret** com a cadeia de caracteres de consulta: *artigos por jaime teevan após 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>A expressão na primeira interpretação que é retornada da API de interpretação é *And(Composite(AA.AuN=='jaime teevan'),Y>2012)* .
<br>Esse valor de expressão, em seguida, é passado para a API **calchistogram**. O parâmetro *attributes=Y,F.FN* indica que as distribuições de contagens de artigos devem ser por ano e o campo de estudo, por exemplo:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>A resposta a essa solicitação primeiro indica que há 37 documentos que correspondem à expressão de consulta.  Para o atributo *ano*, há 3 valores distintos, um para cada ano após 2012 (ou seja, 2013, 2014 e 2015) conforme especificado na consulta.  A contagem total de artigos sobre os 3 valores distintos é 37.  Para cada *ano*, o histograma mostra o valor, a probabilidade de log natural total e a contagem de entidades correspondentes.     

O histograma para *campo de estudo* mostra que existem 34 campos de estudo distintos. Como um documento pode ser associado a vários campos de estudo, a contagem total (53) pode ser maior que o número de entidades correspondentes.  Embora haja 34 valores distintos, a resposta inclui somente os 4 primeiros devido ao parâmetro *contagem = 4*.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
