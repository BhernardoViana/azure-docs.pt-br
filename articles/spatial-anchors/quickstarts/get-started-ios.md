---
title: 'Início Rápido: Criar um aplicativo do iOS'
description: Neste Início Rápido, você aprenderá a criar um aplicativo para iOS usando as Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 39732ef785fc610572f917be77dec3f560698fb7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277200"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Início Rápido: Criar um aplicativo para iOS com Âncoras Espaciais do Azure em Objective-C ou Swift

Este início rápido aborda como criar um aplicativo iOS usando [Âncoras Espaciais do Azure](../overview.md) em Swift ou Objective-C. As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo do ARKit iOS que pode salvar e lembrar de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Implantar e executar em um dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

- Um desenvolvedor habilitou o computador macOS com a versão mais recente do <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods</a> instalada.
- Git instalado via Homebrew. Insira o seguinte comando em uma única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
- Um dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com ARKit</a> habilitado para desenvolvedor.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

Use o Terminal para realizar as ações a seguir.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Instale os pods necessários usando o CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Navegue até `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Navegue até `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Execute `pod install --repo-update` para instalar os CocoaPods para o projeto.

Agora, abra o `.xcworkspace` no Xcode.

> [!NOTE]
> Confira as etapas de solução de problemas [aqui](#cocoapods-issues-on-macos-catalina-1015) se estiver enfrentado problemas com o CocoaPod após atualizar para o macOS Catalina (10.15).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

A próxima etapa é configurar o aplicativo para usar o identificador e a chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Abra `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Localize o campo `spatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `spatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Abra `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** como o dispositivo iOS.

![Selecione o dispositivo](./media/get-started-ios/select-device.png)

Selecione **Compilar e, em seguida, executar o esquema atual**.

![Implantar e executar](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se um erro `library not found for -lPods-SampleObjC` é exibido, provavelmente, você abriu o arquivo `.xcodeproj` em vez do `.xcworkspace`. Abra o `.xcworkspace` e tente novamente.

No Xcode, interrompa o aplicativo pressionando **Parar**.

## <a name="troubleshooting"></a>solução de problemas

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemas do CocoaPods no macOS Catalina (10.15)

Se você atualizou recentemente para o macOS Catalina (10.15) e tinha o CocoaPods instalado anteriormente, o CocoaPods pode estar em um estado incompleto e falhar ao configurar corretamente seus arquivos de projeto `.xcworkspace` e pods. Para resolver esse problema, será preciso reinstalar o CocoaPods executando os seguintes comandos:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
