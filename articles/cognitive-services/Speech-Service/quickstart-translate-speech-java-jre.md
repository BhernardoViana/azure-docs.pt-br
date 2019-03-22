---
title: 'Início rápido: Traduzir fala, Java (Windows, Linux) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você criará um aplicativo simples do Java para capturar a fala do usuário, traduzi-la para outro idioma e produzir o texto para a linha de comando. Este guia foi projetado para usuários do Windows e do Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 36eaaeabcf888aac10bcf9b8a27e3590d21079ec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897089"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Início Rápido: Tradução de fala com o Speech SDK para Java

Neste início rápido, você criará um aplicativo Java simples que captura a fala do usuário do microfone do seu computador, converte a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado no Windows de 64 bits ou no Ubuntu Linux 16.04/18.04 de 64 bits e é compilado com o pacote do Maven do Speech SDK e o do Java IDE do Eclipse.

Para obter uma lista completa dos idiomas disponíveis para tradução de fala, confira [suporte ao idioma](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operacional: Windows de 64 bits ou Ubuntu Linux 16.04/18.04 de 64 bits
* [Java IDE do Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

Se você estiver executando o Ubuntu 16.04/18.04, garanta que essas dependências estejam instaladas antes de iniciar o Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo snippet de código a seguir:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.

A entrada de fala do seu microfone será transcrita em alemão e registrada na janela do console. Pressione "Enter" para interromper a captura de fala.

![Captura de tela da saída do console após o reconhecimento com êxito](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, por exemplo, como ler fala de um arquivo de áudio e produzir texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Início Rápido: Reconhecer fala, Java (Windows, Linux)](quickstart-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
