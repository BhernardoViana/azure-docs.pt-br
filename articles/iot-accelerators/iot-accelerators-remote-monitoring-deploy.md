---
title: Implantar a solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como provisionar a solução de monitoramento remoto do azureiotsuite.com.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8738fbfc29f6776a091cdc2b872eab88abd195cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Implantar o acelerador de solução de monitoramento remoto

Este tutorial mostra como provisionar o acelerador de solução de monitoramento remoto. Você pode implantar a solução do azureiotsuite.com. Você também pode implantar a solução usando a CLI. Para saber mais sobre essa opção, consulte [Implantar um acelerador de solução da linha de comando](iot-accelerators-remote-monitoring-deploy-cli.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o acelerador de solução
> * Implantar o acelerador de solução
> * Entrar no acelerador de solução

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Implantar o acelerador de solução

Antes de implantar o acelerador de solução em sua assinatura do Azure, escolha algumas opções de configuração:

1. Faça logon no [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) usando suas credenciais de conta do Azure.

1. Clique em **Experimentar Agora** no bloco **Monitoramento Remoto**.

    ![Escolher o monitoramento remoto](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. Na página **Criar solução de Monitoramento Remoto**, insira um **Nome da solução** para seu acelerador de solução de monitoramento remoto.

1. Selecione uma implantação do tipo **Básico** ou **Standard**. Se você estiver implantando a solução para saber como ela funciona ou para executar uma demonstração, escolha a opção **Básico** para minimizar os custos.

1. Escolha **Java** ou **.NET** como a linguagem. Todos os microservices estão disponíveis como implementações Java ou .NET.

1. Examine o painel **Detalhes da solução** para obter mais informações sobre suas opções de configuração.

1. Selecione a **Assinatura** e a **Região** que você deseja usar para provisionar a solução.

1. Clique em **Criar Solução** para iniciar o processo de provisionamento. Este processo normalmente leva vários minutos para ser executado:

    ![Detalhes da solução de monitoramento remoto](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Para obter informações de solução de problemas, consulte [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (O que fazer quando uma implantação falhar) no repositório do GitHub.

## <a name="sign-in-to-the-solution-accelerator"></a>Entrar no acelerador de solução

Após a conclusão do processo de provisionamento, você poderá entrar em seu acelerador de solução de monitoramento remoto.

1. Na página **Soluções provisionadas**, escolha sua nova solução de monitoramento remoto:

    ![Escolher a nova solução](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. Você pode exibir informações sobre sua solução de monitoramento remoto no painel que é exibido. Escolha **Painel da solução** para conectar-se à solução de monitoramento remoto.

    > [!NOTE]
    > Você poderá excluir sua solução de monitoramento remoto usando este painel quando não precisar mais dela.

    ![Painel de solução](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. O painel da solução de monitoramento remoto será exibido no seu navegador.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar o acelerador de solução
> * Implantar o acelerador de solução
> * Entrar no acelerador de solução

Agora que você implantou a solução de monitoramento remoto, a próxima etapa será [explorar os recursos do painel da solução](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->