---
title: Habilidades cognitivas preteridas – Azure Search
description: Esta página contém uma lista de habilidades de pesquisa cognitiva que são consideradas obsoletas e não serão suportadas no futuro próximo.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fd49cdf1bd6a49e8f89f8b805cbaa50276527de3
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698886"
---
# <a name="deprecated-cognitive-search-skills"></a>Habilidades de pesquisa cognitiva preteridas

Este documento descreve as habilidades cognitivas que são consideradas obsoletas. Use o seguinte guia para o conteúdo:

* Nome da habilidade: O nome da habilidade que será preterida; mapeado para o atributo @odata.type.
* Última versão de API disponível: A última versão da API pública do Azure Search por meio da qual os conjuntos de habilidades contendo a habilidade preterida correspondente podem ser criados/atualizados.
* Fim do suporte: O último dia após o qual a habilidade correspondente é considerada sem suporte. Os conjuntos de habilidades criados anteriormente ainda devem continuar funcionando, mas recomenda-se que os usuários migrem de uma habilidade obsoleta.
* Recomendações: Encaminhamento de caminho de migração para usar uma habilidade com suporte. Os usuários são aconselhados a seguir as recomendações para continuar recebendo suporte.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Última versão disponível do api

2017-11-11-Versão prévia

### <a name="end-of-support"></a>Fim do suporte

15 de fevereiro de 2019

### <a name="recommendations"></a>Recomendações 

Use [ Microsoft.Skills.Text.EntityRecognitionSkill ](cognitive-search-skill-entity-recognition.md). Ele fornece a maior parte da funcionalidade do NamedEntityRecognitionSkill em uma qualidade superior. Também possui informações mais completas em seus campos de saída complexos.

Para migrar para a [Habilidade de Reconhecimento de Entidade](cognitive-search-skill-entity-recognition.md), você terá que executar uma ou mais das seguintes alterações na sua definição de habilidade. Você pode atualizar a definição de habilidade usando a [API Update Skillset](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Atualmente, não há suporte para a pontuação de confiança como um conceito. O `minimumPrecision` parâmetro existe no `EntityRecognitionSkill` para uso futuro e para compatibilidade com versões anteriores.

1. *(Obrigatório)* Altere o `@odata.type` de `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` para `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Opcional)*  Se você estiver fazendo uso do `entities` de saída, use o `namedEntities` saída de coleção complexa do `EntityRecognitionSkill` em vez disso. Você pode usar o `targetName` na definição de habilidade para mapeá-lo para uma anotação chamada `entities`.

3. *(Opcional)* Se você não especificar explicitamente o `categories`, o `EntityRecognitionSkill` poderá retornar diferentes tipos de categorias além daquelas que foram suportadas pelo `NamedEntityRecognitionSkill`. Se esse comportamento for indesejável, certifique-se de definir explicitamente o `categories` parâmetro para `["Person", "Location", "Organization"]`.

    _Exemplos de definições de migração_

    * Migração simples

        _(Before) NomeouEntityRecognition, definição de habilidade_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Depois) Definição de habilidades EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Um pouco complicado de migração

        _(Before) NomeouEntityRecognition, definição de habilidade_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Depois) Definição de habilidades EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)
