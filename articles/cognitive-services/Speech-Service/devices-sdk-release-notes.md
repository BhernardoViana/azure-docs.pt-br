---
title: Documentação do SDK dos Dispositivos de Fala
titleSuffix: Azure Cognitive Services
description: Notas de versão – o que mudou nas versões mais recentes
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: 1c91cde45a6a420376af36f70487adf7fe0ee83a
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751818"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Notas de versão do SDK dos Dispositivos de Fala dos Serviços Cognitivos
As seções a seguir listam as alterações nas versões mais recentes.

## <a name="speech-devices-sdk-151"></a>Dispositivos de fala 1.5.1 do SDK:

*   Incluir [conversa transcrição](conversation-transcription-service.md) no aplicativo de exemplo.
*   Atualizado o [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.5.1. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Serviços cognitivos de fala dispositivos SDK 1.5.0: Versão de maio de 2019

*   SDK de dispositivos de fala agora está GA e não mais de uma versão prévia restrita.
*   Atualizado o [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.5.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew).
*   Nova tecnologia ativação de word traz melhorias significativas de qualidade, consulte alterações significativas.
*   Novo pipeline de processamento de áudio para reconhecimento de distante campo aprimorado.

**Alterações da falha**

*   Devido a nova tecnologia de palavra de ativação todas as palavras de ativação devem ser recriadas em nosso portal de word wake aprimorado. Para remover completamente o antigas palavras-chave do dispositivo desinstalar o aplicativo antigo.
    - desinstalar o adb com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Serviços cognitivos de fala dispositivos SDK 1.4.0: Versão de abril de 2019 

* Atualizado o [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.4.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Serviços cognitivos de fala dispositivos SDK 1.3.1: Versão de março de 2019 

* Atualizado o [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente versão 1.3.1. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew). 
*   Palavra de ativação atualizado tratamento, consulte alterações significativas.
*   Aplicativo de exemplo adiciona a opção de linguagem para o reconhecimento de fala e tradução.

**Alterações da falha** 

*   [Instalando um ativação word](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) foi simplificado, agora é parte do aplicativo e não precisa de uma instalação separada no dispositivo.
*   O reconhecimento da palavra wake foi alterado, e há suporte para dois eventos.
    - RecognizingKeyword, indica o resultado de fala contém o texto da palavra-chave (não verificado).
    - RecognizedKeyword, indica que esse reconhecimento de palavra-chave concluído reconhecer a palavra-chave determinada.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>SDK de Dispositivos de Fala dos Serviços Cognitivos 1.1.0: versão de novembro de 2018 

* Atualizado o [SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente para a versão 1.1.0. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew). 
* A precisão de reconhecimento de fala de campo distante foi aprimorada com nosso algoritmo de processamento de áudio aprimorado.
* O aplicativo de exemplo adicionou suporte ao reconhecimento de fala chinês.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>SDK de Dispositivos de Fala dos Serviços Cognitivos 1.0.1: versão de outubro de 2018 

* O componente do [SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) foi atualizado para a versão 1.0.1. Para obter mais informações, consulte [notas de versão](https://aka.ms/csspeech/whatsnew). 
* A precisão do reconhecimento de fala será aprimorada com nosso algoritmo de processamento de áudio aprimorada  
* Um bug da sessão de áudio de reconhecimento contínuo é corrigido.

**Alterações da falha** 

* Com essa versão, várias alterações significativas foram introduzidas. Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes das APIs. 
* Os arquivos de modelo KWS não são compatíveis com Dispositivos de Fala SDK 1.0.1. Os arquivos de ativação de Word existentes serão excluídos depois que os novos arquivos de ativação de Word são gravados no dispositivo. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>SDK de Dispositivos de Fala dos Serviços Cognitivos 0.5.0: versão de agosto de 2018

* Melhoria da precisão do reconhecimento de fala pela correção de um bug no código de processamento de áudio.
* O componente do [SDK de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) foi atualizado para a versão 0.5.0. Para obter mais informações, consulte [notas de versão](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>SDK de Dispositivos de Fala dos Serviços Cognitivos 0.2.12733: versão de maio de 2018

A primeira visualização pública do SDK dos Dispositivos de Fala dos Serviços Cognitivos.
