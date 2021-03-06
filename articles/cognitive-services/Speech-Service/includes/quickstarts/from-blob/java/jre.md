---
title: 'Início Rápido: Reconhecer a fala armazenada no Armazenamento de Blobs, Java – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d4a436540460240ebd97c39d3406b2886586a6df
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829179"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>Abra seu projeto no Eclipse

A primeira etapa é verificar se o projeto está aberto no Eclipse.

1. Iniciar o Eclipse
2. Carregue o projeto e abra o `Main.java`.

## <a name="add-a-reference-to-gson"></a>Adicionar uma referência a Gson
Usaremos um serializador/desserializador JSON externo neste início rápido. Para Java, escolhemos [Gson](https://github.com/google/gson).

Abra o pom.xml e adicione a seguinte referência: [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(Você precisará substituir os valores de `YourSubscriptionKey`, `YourServiceRegion` e `YourFileUrl` pelos seus próprios valores.)

## <a name="json-wrappers"></a>Wrappers JSON

Como as APIs REST usam solicitações no formato JSON e também retornam resultados em JSON, podemos interagir com elas usando apenas cadeias de caracteres, mas isso não é recomendado.
Para facilitar o gerenciamento das solicitações e das respostas, declararemos algumas classes a serem usadas para serializar/desserializar o JSON.

Vá em frente e coloque suas declarações antes de `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente Http
A primeira coisa que precisaremos é de um Cliente Http que tenha uma URL base correta e um conjunto de autenticação.
Insira este código em `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora postaremos a solicitação no serviço de Fala e verificaremos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma Url nos cabeçalhos de resposta, que é o local em que ele armazenará o status da transcrição.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de maneira assíncrona, precisamos investigar seu status periodicamente. Verificaremos a cada 5 segundos.

Podemos verificar o status recuperando o conteúdo na Url que obtivemos quando postamos a solicitação. Quando recebermos o conteúdo de volta, nós o desserializaremos em uma de nossa classe auxiliar para facilitar a interação com ele.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida. Faremos isso em seguida.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído a transcrição com êxito, os resultados serão armazenados em outra Url que podemos obter da resposta de status.

Baixaremos o conteúdo dessa URL, desserializaremos o JSON e faremos um loop pelos resultados imprimindo o texto de exibição conforme avançamos.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Verificar o código
Neste momento, seu código deverá ter a seguinte aparência: (Adicionamos alguns comentários a esta versão) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
