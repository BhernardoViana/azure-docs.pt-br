---
title: Desenvolver aplicativos com o SDK de Fala – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como criar aplicativos usando o SDK de Fala.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 9bb721221f8f90a39b1c84bcdd66429bbd628494
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912654"
---
# <a name="ship-an-application"></a>Enviar um aplicativo

Observe a [Licença do SDK de fala](https://aka.ms/csspeech/license201809), bem como as [notificações de software de terceiros](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) ao distribuir o SDK de Fala de Serviços Cognitivos do Azure. Além disso, leia a [Política de privacidade da Microsoft](https://aka.ms/csspeech/privacy).

Dependendo da plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a> Windows

O SDK dos Serviços Cognitivos de Fala é testado no Windows 10 e no Windows Server 2016.

O SDK de fala dos serviços Cognitivos requer o [Microsoft Visual C++ Redistributable para Visual Studio de 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Você pode baixar instaladores para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2019`:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Se seu aplicativo usar código gerenciado, o `.NET Framework 4.6.1` ou posterior será necessário no computador de destino.

Para a entrada do microfone, as bibliotecas do Media Foundation precisam ser instaladas. Essas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível usar o SDK de Fala sem essas bibliotecas, contanto que o microfone não seja usado como o dispositivo de entrada de áudio.

Os arquivos necessários do SDK de Fala podem ser implantados no mesmo diretório do seu aplicativo. Dessa forma, seu aplicativo pode acessar diretamente as bibliotecas. Selecione a versão correta (Win32/x64) que corresponda ao seu aplicativo.

| NOME | Função
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | SDK principal, necessário para implantação nativa e gerenciada
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para implantação gerenciada

>[!NOTE]
> Começando com a versão 1.3.0 o arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada no núcleo do SDK.

## <a name="linux"></a>Linux

O Speech SDK atualmente oferece suporte as distribuições Ubuntu 16.04, 18.04 Ubuntu e Debian 9.
Para um aplicativo nativo, você precisa enviar a biblioteca do SDK de Fala, `libMicrosoft.CognitiveServices.Speech.core.so`.
Selecione a versão (x86/x64) que corresponde ao seu aplicativo. Dependendo da versão do Linux, talvez você também precise incluir as seguintes dependências:

* As bibliotecas compartilhadas da biblioteca GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
* A biblioteca OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
* A biblioteca compartilhada para aplicativos ALSA (`libasound.so.2`)

No Ubuntu, as bibliotecas de GNU C já devem estar instaladas por padrão. Os três últimos podem ser instalados usando estes comandos:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9 instale esses pacotes:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
