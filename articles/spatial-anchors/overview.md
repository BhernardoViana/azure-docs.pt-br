---
title: Visão geral de Âncoras Espaciais do Azure | Microsoft Docs
description: Saiba como as Âncoras Espaciais do Azure ajudam a desenvolver experiências multiplataforma de realidade misturada.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2451922f0eb49a5ccee036db72eb046760287dca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60240470"
---
# <a name="azure-spatial-anchors-overview"></a>Visão geral de Âncoras Espaciais do Azure

Bem-vindo a Âncoras Espaciais do Azure. Âncoras Espaciais do Azure capacita os desenvolvedores com recursos essenciais para criar aplicativos de realidade misturada com reconhecimento do ambiente. Esses aplicativos podem dar suporte a Microsoft HoloLens, dispositivos baseados em iOS compatíveis com ARKit e dispositivos baseados no Android que dão suporte a ARCore. Âncoras Espaciais do Azure permite aos desenvolvedores trabalhar com plataformas de realidade misturada para perceber espaços, designar pontos precisos de interesse e lembrar-se dos pontos de interesse de dispositivos com suporte.
Esses pontos de interesse precisos são chamados de Âncoras Espaciais.

![Multiplataforma](./media/cross-platform.png)

## <a name="examples"></a>Exemplos

Alguns casos de uso de exemplo habilitados por Âncoras Espaciais incluem:

- [Experiências multiusuários](tutorials/tutorial-share-anchors-across-devices.md). O recurso Âncoras Espaciais torna mais fácil para pessoas no mesmo lugar participarem de aplicativos de realidade misturada multiusuário. Por exemplo, duas pessoas podem iniciar um jogo de xadrez de realidade misturada colocando um tabuleiro de xadrez virtual em uma tabela. Em seguida, apontando o dispositivo para a tabela, elas podem exibir e interagir com o tabuleiro de xadrez virtual juntas.

- [Localização de caminho](concepts/anchor-relationships-way-finding.md). Os desenvolvedores também podem conectar Âncoras Espaciais criando relações entre elas. Por exemplo, um aplicativo pode incluir uma experiência com dois ou mais pontos de interesse com que um usuário deve interagir para concluir uma tarefa. Esses pontos de interesse podem ser criados de maneira conectada. Posteriormente, quando o usuário estiver concluindo a tarefa de várias etapas, o aplicativo poderá solicitar âncoras que estejam próximas da atual para direcionar o usuário para a próxima etapa na tarefa.

- [Manter o conteúdo virtual no mundo real](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Um aplicativo pode permitir que um usuário coloque um calendário virtual em uma parede de sala de conferência que as pessoas possam ver usando um aplicativo de telefone ou dispositivo HoloLens. Em uma configuração industrial, um usuário pode receber informações contextuais sobre um computador apontando uma câmera de dispositivos com suporte para ele.

O recurso Âncoras Espaciais do Azure é composto por um serviço gerenciado e os SDKs do cliente para plataformas de dispositivos com suporte. As seções a seguir fornecem informações sobre como começar a criar aplicativos usando Âncoras Espaciais do Azure.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo com Âncoras Espaciais.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
