---
title: Configurar um ambiente de desenvolvimento do Windows para microsserviços do Azure | Microsoft Docs
description: Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.openlocfilehash: 993d5c804490540a9c71647f52692fb55b27c405
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113292"
---
# <a name="prepare-your-development-environment-on-windows"></a>Preparar seu ambiente de desenvolvimento no Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Para compilar e executar os [aplicativos do Azure Service Fabric][1] em seu computador de desenvolvimento do Windows, instale o tempo de execução do Service Fabric, o SDK e as ferramentas. Você também precisa [habilitar a execução dos scripts do Windows PowerShell](#enable-powershell-script-execution) incluídos no SDK.

## <a name="prerequisites"></a>pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operacional com suporte
Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Suporte do Windows 7:
> - O Windows 7 inclui, por padrão, apenas o Windows PowerShell 2.0. Cmdlets de PowerShell do Service Fabric exigem o PowerShell 3.0 ou superior. É possível [baixar o Windows PowerShell 5.0][powershell5-download] do Centro de Download da Microsoft.
> - O Proxy Reverso do Service Fabric não está disponível no Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Instalar o SDK e as ferramentas
### <a name="to-use-visual-studio-2017"></a>Para usar o Virtual Studio 2017
As ferramentas do Service Fabric fazem parte da carga de trabalho Desenvolvimento do Azure no Visual Studio 2017. Habilite essa carga de trabalho como parte da instalação do Visual Studio.
Além disso, você precisa instalar o SDK e o tempo de execução do Microsoft Azure Service Fabric usando o Web Platform Installer.

* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Para usar o Visual Studio 2015 (requer a atualização do Visual Studio 2015 2 ou posterior)
Para Visual Studio 2015, as ferramentas do Service Fabric são instaladas junto com o SDK e o tempo de execução, usando o Web Platform Installer:

* [Instalar as ferramentas e o SDK do Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Somente instalação do SDK
Se só precisar do SDK, você poderá instalar este pacote:
* [Instalar o SDK do Microsoft Azure Service Fabric][core-sdk]

As versões atuais são:
* Ferramentas e SDK do Service Fabric 3.1.301
* Tempo de execução do Service Fabric 6.2.301
* Ferramentas do Service Fabric para Visual Studio 2015, 2.1.20180510.2
* O Visual Studio 2017 15.7 inclui as Ferramentas do Service Fabric para Visual Studio 2.1.20180423.1 

Para obter uma lista das versões com suporte, consulte [suporte ao Service Fabric](service-fabric-support.md)

## <a name="enable-powershell-script-execution"></a>Habilitar a execução de script do PowerShell
A Malha do Serviço usa scripts do Windows PowerShell para criar um cluster de desenvolvimento local e implantar aplicativos do Visual Studio. Por padrão, o Windows bloqueia a execução desses scripts. Para habilitá-los, você deve modificar sua política de execução do PowerShell. Abra o PowerShell com um administrador e insira o seguinte comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Próximas etapas
Agora que você terminou de configurar seu ambiente de desenvolvimento, comece a compilar e executar aplicativos.

* [Criar seu primeiro aplicativo do Service Fabric no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Saiba como implantar e gerenciar aplicativos no seu cluster local](service-fabric-get-started-with-a-local-cluster.md)
* [Preparar um ambiente de desenvolvimento do Linux no Windows](service-fabric-local-linux-cluster-windows.md)
* [Saiba mais sobre os modelos de programação: Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Backup e restauração periódicos no Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
* [Confira os exemplos de código do Service Fabric no GitHub](https://aka.ms/servicefabricsamples)
* [Visualizar o cluster usando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Siga o roteiro de aprendizagem do Service Fabric para obter uma introdução abrangente à plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
* [Automatizar patches do sistema operacional no cluster](service-fabric-patch-orchestration-application.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Página da campanha do Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI link"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI link"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI link"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
