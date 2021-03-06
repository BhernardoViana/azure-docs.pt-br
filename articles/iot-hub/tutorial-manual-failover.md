---
title: Failover manual de um hub IoT do Azure | Microsoft Docs
description: Saiba como executar um failover manual do hub IoT para outra região e confirmar que ele está funcionando e, em seguida, retorná-lo para a região original e verificá-lo novamente.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 42785e3ee636f24ca185f57a11d4ee1091db3e98
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890406"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Tutorial: Executar failover manual para um Hub IoT

Failover manual é um recurso do serviço Hub IoT que permite que os clientes façam [failover](https://en.wikipedia.org/wiki/Failover) das operações de seu hub de uma região primária para a região do Azure com emparelhamento geográfico correspondente. O failover manual pode ser feito em caso de um desastre regional ou uma interrupção de serviço estendida. Você também pode executar um failover planejado para testar recursos de recuperação de desastre, embora seja recomendável usar um hub IoT de teste em vez de um na produção. O recurso de failover manual é oferecido aos clientes sem custo adicional.

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
> * Criar um hub IoT usando o portal do Azure. 
> * Executar um failover. 
> * Ver o hub em execução na localização secundária.
> * Executar um failback para devolver as operações do hub IoT para a localização primária. 
> * Confirmar se o hub está sendo executado corretamente no local certo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

1. Faça logon no [Portal do Azure](https://portal.azure.com). 

2. Clique em **+ Criar um recurso**, selecione **Internet das Coisas** e **Hub IoT**.

   ![Captura de tela mostrando a criação de um hub IoT](./media/tutorial-manual-failover/create-hub-01.png)

3. Selecione a guia **Noções Básicas**. Preencha os campos a seguir.

    **Assinatura**: selecione a assinatura do Azure que você quer usar.

    **Grupo de Recursos**: clique em **Criar novo** e especifique **ManlFailRG** como o nome do grupo de recursos.

    Em **Região**, selecione uma região próxima de você. Este tutorial usa `West US 2`. Um failover só pode ser executado entre regiões do Azure emparelhadas geograficamente. A região geográfica emparelhada com Oeste dos EUA 2 é a Centro-oeste dos EUA.
    
   **Nome do Hub IoT**: especifique um nome para o hub IoT. O nome do hub deve ser globalmente exclusivo. 

   ![Captura de tela mostrando o painel Noções básicas para a criação de um hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Clique em **Revisar + Criar**. (Ele usa os padrões para tamanho e escala.) 

4. Examine as informações e clique em **Criar** para criar o hub IoT. 

   ![Captura de tela mostrando a última etapa da criação de um hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Realizar failover manual

Observe que há um limite de dois failovers e dois failbacks por dia para um hub IoT.

1. Clique em **Grupos de recursos** e selecione o grupo de recursos **ManlFailRG**. Clique no hub na lista de recursos. 

1. Em **Configurações** no painel de Hub IoT, clique em **Failover**.

   ![Captura de tela mostrando o painel de propriedades do Hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

1. No painel Failover manual, você vê o **Local atual** e o **Local de failover**. O local atual sempre indica o local no qual o hub está ativo no momento. A localização de failover é a [região do Azure emparelhada geograficamente](../best-practices-availability-paired-regions.md) padrão que está emparelhada com o local atual. Você não pode alterar os valores de localização. Para este tutorial, a localização atual é `West US 2` e a localização de failover é `West Central US`.

   ![Captura de tela mostrando o painel Failover Manual](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Na parte superior do painel Failover Manual, clique em **Iniciar failover**. 

1. No painel de confirmação, preencha o nome do Hub IoT para confirmar que ele será o objeto do failover. Em seguida, para iniciar o failover, clique em **Failover**.

   O tempo que leva para realizar o failover manual é proporcional ao número de dispositivos que estão registrados no hub. Por exemplo, se você tiver 100 mil dispositivos, pode levar 15 minutos, mas se tiver cinco milhões de dispositivos, pode levar uma hora ou mais.

   ![Captura de tela mostrando o painel Failover Manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Enquanto o processo de failover manual está em execução, uma faixa é exibida para informar que um failover manual está em andamento. 

   ![Captura de tela mostrando Failover Manual em andamento](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se você fechar o painel do Hub IoT e abri-lo novamente clicando no painel Grupo de Recursos, verá uma faixa informando que o hub está no meio de um failover manual. 

   ![Captura de tela mostrando o failover do Hub IoT em andamento](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Depois de concluído, as regiões atual e de failover na página Failover Manual são invertidas e o hub fica ativo novamente. Neste exemplo, a localização atual é agora `WestCentralUS` e a localização de failover, `West US 2`. 

   ![Captura de tela mostrando que o failover foi concluído](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   A página de visão geral também mostra uma faixa indicando que o failover foi concluído e que o Hub IoT está sendo executado em `West Central US`.

   ![Captura de tela mostrando que o failover foi concluído na página de visão geral](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Executar um failback 

Depois de realizar um failover manual, você pode alternar as operações do hub para a região primária original; isso recebe o nome de failback. Se você acabou de executar um failover, precisará esperar aproximadamente uma hora antes de poder solicitar um failback. Se você tentar realizar o failback em um curto período de tempo, uma mensagem de erro será exibida.

Um failback é executado da mesma forma que um failover manual. Estas são as etapas: 

1. Para executar um failback, volte para o painel Hub Iot Hub de seu hub Iot.

2. Em **Configurações** no painel de Hub IoT, clique em **Failover**. 

3. Na parte superior do painel Failover Manual, clique em **Iniciar failover**. 

4. No painel de confirmação, preencha o nome do Hub IoT para confirmar que ele será o objeto do failback. Em seguida, para iniciar o failback, clique em OK. 

   ![Captura de tela da solicitação de failback manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   As faixas são exibidas conforme explicado na seção Executar um failover. Após concluir o failback, ele mostra novamente `West US 2` como a localização atual e `West Central US` como a localização de failover, conforme definido originalmente.

## <a name="clean-up-resources"></a>Limpar recursos 

Para remover os recursos que você criou para este tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ele remove o hub IoT e o próprio grupo de recursos. 

1. Clique em **Grupos de Recursos**. 

2. Localize e selecione o grupo de recursos **ManlFailRG**. Clique nela para abri-la. 

3. Clique em **Excluir grupo de recursos**. Quando solicitado, insira o nome do grupo de recursos e clique em **Excluir** para confirmar. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar e executar um failover manual e a solicitar um failback executando as seguintes tarefas:

> [!div class="checklist"]
> * Criar um hub IoT usando o portal do Azure. 
> * Executar um failover. 
> * Ver o hub em execução na localização secundária.
> * Executar um failback para devolver as operações do hub IoT para a localização primária. 
> * Confirmar se o hub está sendo executado corretamente no local certo.

Avance para o próximo tutorial para aprender a gerenciar o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Gerenciar o estado de um dispositivo IoT](tutorial-device-twins.md)
