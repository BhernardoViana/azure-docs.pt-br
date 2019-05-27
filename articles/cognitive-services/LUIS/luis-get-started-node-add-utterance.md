---
title: Alterar, treinar aplicativo, Node.js
titleSuffix: Azure Cognitive Services
description: Neste início rápido do Node.js, adicione enunciados de exemplo a um aplicativo Automação de Página Inicial e treine o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/17/2019
ms.author: diberry
ms.openlocfilehash: 6ee3ecc37cfb21630b671e70260b44f1532de2b6
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594116"
---
# <a name="quickstart-change-model-using-nodejs"></a>Início Rápido: Alterar o modelo usando Node.js

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [**Node.js**](https://nodejs.org/en/download/) mais recente com NPM.
* Dependências NPM para este artigo: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Criar código de início rápido 

Adicione as dependências NPM ao arquivo chamado `add-utterances.js`.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Adicione as constantes de LUIS ao arquivo. Copie o código a seguir e insira a sua chave de criação, a ID do aplicativo e a ID da versão.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Adicione o nome e o local do arquivo para upload que contém seus enunciados. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Adicionar método e objeto para a função `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Adicionar método e objeto para a função `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Adicione a função `sendUtteranceToApi` para enviar e receber chamadas HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Adicione o código **principal** que escolhe qual ação.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Instalar dependências

Crie o arquivo `package.json` com o seguinte texto:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

Na linha de comando, do diretório que tem o package.JSON, instale dependências com NPM: `npm install`.

## <a name="run-code"></a>Executar código

Execute o aplicativo a partir de uma linha de comando com Node.js.

Chamar `npm start` adiciona enunciados, treina e obtém o status de treinamento.

```console
> npm start 
```

A linha de comando exibe os resultados da chamada da API para adicionar enunciados. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar com o início rápido, remova todos os arquivos criados nesse início rápido. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar um aplicativo LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md)
