---
title: 'Início Rápido: criar um projeto de classificação de imagem com o SDK de Visão Personalizada para C#'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcas, faça upload de imagens, treine seu projeto e faça uma previsão usando o SDK do .NET com C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: anroth
ms.openlocfilehash: 7faad2c432e15ed363bd1caf290e03dc75e9d298
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141057"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Início Rápido: criar um projeto de classificação de imagem com o SDK do .NET de Visão Personalizada

Este artigo fornece informações e código de exemplo para ajudar você a começar a usar o SDK da Visão Personalizada com C# para criar um modelo de classificação de imagem. Depois de criada, você poderá adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão do projeto padrão e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como um modelo para criar seu próprio aplicativo .NET. Se você quiser passar pelo processo de criar e usar um modelo de classificação _sem_ código, confira as [diretrizes baseadas em navegador](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e um exemplo de código

Para escrever um aplicativo .NET que usa a Visão Personalizada, você precisará dos pacotes NuGet da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo que você baixará, mas é possível acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou baixe o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue até a pasta **CustomVision/ImageClassification** e abra _ImageClassification.csproj_ no Visual Studio.

Este projeto do Visual Studio cria um novo projeto de Visão Personalizada denominado __My New Project__, que pode ser acessado pelo [site da Visão Personalizada](https://customvision.ai/). Ele então carrega as imagens para treinar e testar um classificador. Neste projeto, o classificador serve para determinar se uma árvore é uma __Cicuta__ ou uma __Cerejeira__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o arquivo _Program.cs_ e inspecione o código. Insira suas chaves de assinatura nas definições pertinentes no método **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

O parâmetro de ponto de extremidade deve apontar para a região em que o grupo de recursos do Azure que contém os recursos de Visão Personalizada foi criado. Para este exemplo, consideramos a região Centro-Sul dos EUA e usaremos:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

As linhas de código a seguir executam a funcionalidade principal do projeto.

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. Confira o método [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um classificador](getting-started-build-a-classifier.md)).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Criar marcas no projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Carregar e marcar imagens

As imagens para este projeto estão incluídas. Elas são referenciadas no método **LoadImagesFromDisk** em _Program.cs_. Você pode carregar até 64 imagens em um único lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Este código cria a primeira iteração no projeto e, em seguida, a publica no ponto de extremidade de previsão. O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

```csharp
var iteration = trainingApi.TrainProject(project.Id);
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Definir o ponto de extremidade de previsão

O ponto de extremidade de previsão é a referência que você pode usar para enviar uma imagem para o modelo atual e obter uma previsão de classificação.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Enviar uma imagem ao ponto de extremidade de previsão padrão

Nesse script, a imagem de teste é carregada no método **LoadImagesFromDisk** e a saída de previsão do modelo é exibida no console. O valor da variável publishedModelName deve corresponder ao valor "Publicado como" encontrado na guia **Desempenho** do portal de Visão Personalizada. 

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Executar o aplicativo

Enquanto o aplicativo é executado, ele deve abrir uma janela do console e gravar a seguinte saída:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Em seguida, você pode verificar se a imagem de teste (encontrada em **imagens/teste/** ) foi marcada apropriadamente. Pressione qualquer tecla para sair do aplicativo. Você pode também voltar para o [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto recém-criado.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como cada etapa do processo de classificação de imagem pode ser executada em código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)
