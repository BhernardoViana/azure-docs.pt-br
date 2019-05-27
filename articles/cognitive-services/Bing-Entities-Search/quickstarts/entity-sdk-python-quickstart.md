---
title: 'Início Rápido: SDK da Pesquisa de Entidade do Bing, Python'
titlesuffix: Azure Cognitive Services
description: Configuração para aplicativo de console do SDK da Pesquisa de Entidade do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9a66df03ceb03885ba5f61b4adeb54cb1876338d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813607"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Início Rápido: SDK da Pesquisa de Entidade do Bing com Python

Use este Início Rápido para começar a pesquisar entidades com o SDK da Pesquisa de Entidade do Bing para o Python. Embora a Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://www.python.org/)

* O [SDK da Pesquisa de Entidade do Bing para o Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

É recomendável usar um ambiente virtual do Python. Instale e inicialize o ambiente virtual com o módulo venv. Você poderá instalar virtualenv para o Python 2.7 com:

```Console
python -m venv mytestenv
```

Instale o SDK da Pesquisa de Entidade do Bing com:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo arquivo Python em seu IDE ou o editor favorito e adicione as seguintes instruções de importação. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para a chave de assinatura e crie uma instância do cliente criando um objeto `CognitiveServicesCredentials` com ela.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Enviar uma solicitação de pesquisa e receber uma resposta

1. Envie uma solicitação de pesquisa para a Pesquisa de Entidade do Bing com `client.entities.search()` e uma consulta de pesquisa. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se alguma entidade for retornada, converta `entity_data.entities.value` em uma lista e imprima o primeiro resultado.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../overview.md )