---
title: Como procurar um endereço usando o serviço de pesquisa de Mapas do Azure | Microsoft Docs
description: Saiba como procurar um endereço usando o serviço de pesquisa de Mapas do Azure
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 8b7d2119e1eef8532c30b0a45ae2684493462277
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990006"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Como encontrar um endereço usando o serviço de pesquisa de Mapas do Azure

O serviço de pesquisa de mapas é um conjunto RESTful de APIs projetado para desenvolvedores procurarem endereços, locais, pontos de interesse, anúncios de empresas e outras informações geográficas. O serviço atribui um valor de latitude/longitude para um endereço específico, cruzamento, recurso geográfico ou POI (ponto de interesse). Os valores de latitude e longitude retornados pela pesquisa podem ser usados como parâmetros em outros serviços de mapas, como rota e fluxo de tráfego.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer todas as chamadas para as APIs do serviço de mapas, você precisa de uma conta de mapas e chave. Para obter mais informações sobre como criar uma conta e recuperar uma chave, consulte [Como gerenciar as chaves e a conta dos Mapas do Azure](how-to-manage-account-keys.md).

Este artigo usa o [aplicativo Postman](https://www.getpostman.com/apps) para criar chamadas REST. Você pode usar qualquer ambiente de desenvolvimento de API que você preferir. 


## <a name="using-fuzzy-search"></a>Usando a Pesquisa Difusa

A API padrão para o serviço de pesquisa é a [pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), que lida com as entradas de qualquer combinação de tokens de endereço ou POI. Essa API de pesquisa é uma 'pesquisa de linha única' canônica e é útil quando você não sabe o que seus usuários inserem como consulta de pesquisa. A API de pesquisa difusa é uma combinação de pesquisa de POI e geocodificação. A API também pode ser ponderada com uma posição contextual (coordenadas lat./long.), totalmente restrito por uma coordenada ou raio, ou pode ser executada de maneira mais geral sem um ponto de ancoragem com base geográfica.

A maioria das consultas de pesquisa usa o padrão 'maxFuzzyLevel=1' para ter melhor desempenho e reduzir os resultados incomuns. Esse padrão pode ser substituído conforme necessário pela solicitação com a transmissão do parâmetro de consulta 'maxFuzzyLevel=2' ou '3'.

### <a name="search-for-an-address-using-fuzzy-search"></a>Procurar um endereço usando a Pesquisa Difusa

1. Abra o aplicativo Postman, clique em Novo | Criar Novo e selecione **Solicitação GET**. Insira um nome de solicitação de **Pesquisa difusa**, selecione uma coleção ou uma pasta onde salvá-la e clique em **Salvar**.

2. Na guia Construtor, selecione o método HTTP **GET** e insira a URL de solicitação para o ponto de extremidade de API.

    ![Pesquisa Difusa ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | https://atlas.microsoft.com/search/fuzzy/json? |
    | Autorização | Sem autenticação |

    O atributo **json** no caminho da URL determina o formato da resposta. Você está usando json neste artigo pela facilidade de uso e leitura. É possível localizar os formatos de resposta disponíveis na definição **Obter Pesquisa Difusa** da [Referência de API Funcional Mapas] (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:

    ![Pesquisa Difusa ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | query | pizza |

4. Clique em **Enviar** e analise o corpo da resposta. 

    A cadeia de caracteres de consulta ambígua "pizza" retornou resultados de 10 POI (pontos de interesse) com categorias em "pizza" e "restaurante". Cada resultado retorna um endereço de rua, valores de latitude/longitude, exibições de porta e pontos de entrada para o local.
    
    Os resultados variam para essa consulta, não associados a nenhum local de referência específico. Você pode usar o parâmetro **countrySet** para especificar apenas os países nos quais seu aplicativo precisa de cobertura, já que o comportamento padrão é pesquisar no mundo inteiro, retornando possivelmente resultados desnecessários.

5. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |------------------|-------------------------|
    | countrySet | EUA |
    
    Os resultados agora são limitados pelo código do país e a consulta retorna as pizzarias no Brasil.
    
    Para fornecer resultados com base em um local específico, você pode consultar um ponto de interesse e usar os valores de longitude e latitude retornados em sua chamada no serviço de Pesquisa Difusa. Nesse caso, você usou o Serviço de pesquisa para retornar a localização do Corcovado e usou os valores de lat. /long. para orientar a pesquisa.
    
4. Em Params, insira os seguintes pares Chave/Valor e clique em **Enviar**:

    ![Pesquisa Difusa ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Chave | Valor |
    |-----|------------|
    | lat | 47.62039 |
    | long | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Pesquisar propriedades e coordenadas de endereço 

Você pode transmitir um endereço de rua completo ou parcial para a API de pesquisa de endereço e receber uma resposta que inclui as propriedades detalhadas do endereço, como o município ou o bairro, bem como valores de posição de latitude e longitude.

1. No Postman, clique em **Nova Solicitação** | **Solicitação GET** e nomeie-a **Pesquisa de Endereço**.
2. Na guia Criador, selecione o método HTTP **GET**, insira a URL de solicitação para o ponto de extremidade de API e selecione um protocolo de autorização, se houver.

    ![Pesquisa de Endereço ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | https://atlas.microsoft.com/search/address/json? |
    | Autorização | Sem autenticação |

2. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:
    
    ![Pesquisa de Endereço ](./media/how-to-search-for-address/address_search_params.png)
    
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | query | Rua Cosme Velho, 513, Rio de Janeiro, 22241-090 |
    
3. Clique em **Enviar** e analise o corpo da resposta. 
    
    Nesse caso, você especificou uma consulta de endereço completo e recebe um único resultado no corpo da resposta. 
    
4. Em Params, edite a cadeia de consulta com o seguinte valor:
    ```
        400 Broad, Seattle
    ```

5. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | typeahead | verdadeiro |

    O sinalizador **typeahead** informa à API de Pesquisa de Endereço para tratar a consulta como uma entrada parcial e retornar uma matriz de valores de previsão.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procurar um endereço usando a Pesquisa Invertida de Endereço
1. No Postman, clique em **Nova Solicitação** | **Solicitação GET** e nomeie-a **Pesquisa Invertida de Endereço**.

2. Na guia Construtor, selecione o método HTTP **GET** e insira a URL de solicitação para o ponto de extremidade de API.
    
    ![URL de Pesquisa Invertida de Endereço ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | https://atlas.microsoft.com/search/address/reverse/json? |
    | Autorização | Sem autenticação |
    
2. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:
    
    ![Parâmetros de Pesquisa Invertida de Endereço ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | query | 47.59093,-122.33263 |
    
3. Clique em **Enviar** e analise o corpo da resposta. 
    
    A resposta inclui a entrada do POI Pão de Açúcar com uma categoria de POI de "Montanha". 
    
4. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | número | verdadeiro |

    Se o parâmetro de consulta [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) é enviado com a solicitação, a resposta pode incluir o lado da rua (esquerda/direita) e também uma posição ajustada para o número.
    
5. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | spatialKeys | verdadeiro |

    Quando o parâmetro de consulta [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) é definido, a resposta contém a informação geoespacial principal proprietária de um local especificado.

6. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnSpeedLimit | verdadeiro |
    
    Quando o parâmetro de consulta [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) é definido, a resposta retorna o limite de velocidade indicado.

7. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | returnRoadUse | verdadeiro |

    Quando o parâmetro de consulta [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) é definido, a resposta retorna a matriz de uso de estrada para reversegeocodes no nível da rua.

8. Adicione a seguinte par de Chave/Valor para a seção **Params** e clique em **Enviar**:

    | Chave | Valor |
    |-----|------------|
    | roadUse | verdadeiro |

    Você pode restringir a consulta invertida de geocodificação para um tipo específico de estrada usando o parâmetro de consulta [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters).
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Pesquise o cruzamento usando Pesquisa Invertida de Endereço de Cruzamento

1. No Postman, clique em **Nova Solicitação** | **Solicitação GET** e nomeie-a **Pesquisa Invertida de Endereço de Cruzamento**.

2. Na guia Construtor, selecione o método HTTP **GET** e insira a URL de solicitação para o ponto de extremidade de API.
    
    ![Pesquisa Invertida de Endereço de Cruzamento ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL de Solicitação | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Autorização | Sem autenticação |
    
3. Clique em **Params** e digite os seguinte pares Chave/Valor para uso como parâmetros de consulta ou de caminho na URL da solicitação:
    
    | Chave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<sua chave de mapas do Azure\> |
    | query | 47.59093,-122.33263 |
    
4. Clique em **Enviar** e analise o corpo da resposta. 

## <a name="next-steps"></a>Próximas etapas
- Explorar a documentação da API [serviço de pesquisa de Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search) 
