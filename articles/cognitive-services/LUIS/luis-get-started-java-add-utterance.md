---
title: 'Início Rápido: Alterar, treinar aplicativo, Java — LUIS'
titleSuffix: Azure Cognitive Services
description: Neste início rápido de Java, adicione exemplos de enunciados a um aplicativo Automação de Página Inicial e treine o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 934ec14356448fc03d501a407c45086035ab3e04
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307525"
---
# <a name="quickstart-change-model-using-java"></a>Início Rápido: Alterar o modelo usando Java 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Biblioteca de JSON de GSON do Google](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Criar código de início rápido

1. Adicione as dependências Java ao arquivo chamado `AddUtterances.java`.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Crie a classe `AddUtterances`. Essa classe contém todos os snippets de código seguintes.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Adicione as constantes de LUIS à classe. Copie o código a seguir e insira a sua chave de criação, a ID do aplicativo e a ID da versão.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Adicione o método para chamar a API do LUIS à classe AddUtterances. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Adicione o método para a resposta HTTP da API do LUIS para à AddUtterances.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Adicione tratamento de exceções à classe AddUtterances. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Adicione a função principal à classe AddUtterances.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Compilar o código

Compile AddUtterance com as dependências

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Executar código
Chamar `AddUtterance` sem argumentos adiciona os enunciados de LUIS ao aplicativo sem treiná-lo.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

A linha de comando exibe os resultados da chamada da API para adicionar enunciados. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Limpar recursos
Quando você terminar com o início rápido, remova todos os arquivos criados nesse início rápido. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar um aplicativo LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md) 
