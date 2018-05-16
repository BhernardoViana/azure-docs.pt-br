---
title: Roteando mensagens com o Hub IoT do Azure (Java)| Microsoft Docs
description: Como processar mensagens do dispositivo para nuvem do Hub IoT do Azure usando regras de direcionamento e pontos de extremidade personalizados para enviar mensagens para outros serviços de back-end.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: ff45e9d717b93f89eb8f751294788f08a2fd4592
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Roteando mensagens com o Hub IoT (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Outros tutoriais ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d]) mostram como usar a funcionalidade básica de mensagem do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT.

Este tutorial foi criado com base no código mostrado no tutorial [Introdução ao Hub IoT] e mostra como usar o roteamento de mensagens para processar mensagens do dispositivo para a nuvem de maneira escalonável. O tutorial ilustra como processar mensagens que exigem ação imediata do back-end da solução. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM. Por outro lado, as mensagens de ponto de dados simplesmente são alimentadas no mecanismo de análise. Por exemplo, a telemetria de temperatura de um dispositivo que deve ser armazenado para análise posterior é uma mensagem de ponto de dados.

Ao fim deste tutorial, você executará três aplicativos de console do Java:

* **simulated-device**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT] , que envia mensagens de ponto de dados do dispositivo para a nuvem a cada segundo e mensagens interativas do dispositivo para a nuvem a cada 10 segundos. Este aplicativo usa o protocolo AMQP para se comunicar com o Hub IoT.
* **read-d2c-messages**, que exibe a telemetria enviada pelo aplicativo de dispositivo.
* **read-critical-queue** retira as mensagens críticas da fila do Barramento de Serviço conectada ao Hub IoT.

> [!NOTE]
> O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo neste tutorial por um dispositivo físico e como conectar dispositivos a um Hub IoT, confira o [Centro de Desenvolvedores do Azure IoT].

Para concluir este tutorial, você precisará do seguinte:

* Uma versão de trabalho completa do tutorial [Introdução ao Hub IoT] .
* A versão mais recente do [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Também é recomendável ler sobre o [Armazenamento do Azure] e o [Barramento de Serviço do Azure].

## <a name="send-interactive-messages-from-a-device-app"></a>Enviar mensagens interativas de um aplicativo de dispositivo
Nesta seção, você modificará o aplicativo de dispositivo criado no tutorial [Introdução ao Hub IoT] para enviar ocasionalmente mensagens que exigem processamento imediato.

1. Use um editor de texto para abrir o arquivo simulated-device\src\main\java\com\mycompany\app\App.java. Este arquivo contém o código do aplicativo **simulated-device** que você criou no tutorial [Introdução ao Hub IoT] .

2. Substitua a classe **MessageSender** pelo seguinte código:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Esse método adiciona aleatoriamente as propriedades `"level": "critical"` e `"level": "storage"` a mensagens enviadas pelo dispositivo, que simula uma mensagem que exige ação imediata do back-end do aplicativo ou de um que precise ser armazenado permanentemente. O aplicativo fornece suporte para mensagens de roteamento baseado no corpo da mensagem.
   
   > [!NOTE]
   > Você pode usar as propriedades a fim de direcionar as mensagens para vários cenários, incluindo processamento de ampliação, além do exemplo de afunilamento mostrado aqui.

2. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > É fortemente recomendado implementar uma política de repetição, como uma retirada exponencial, conforme sugerido no artigo [Tratamento de falhas transitórias] do MSDN.

3. Para compilar o aplicativo **simulated-device** usando o Maven, execute o comando a seguir no prompt de comando na pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adicionar uma fila ao seu Hub IoT e rotear mensagens para ela

Nesta seção, você cria uma fila do Barramento de Serviço, conecta-a ao Hub IoT e configura o Hub IoT para enviar mensagens à fila com base na presença de uma propriedade na mensagem. Para saber mais sobre como processar mensagens das filas do Barramento de Serviço, confira [Introdução às filas][lnk-sb-queues-java].

1. Crie uma fila do Barramento de Serviço conforme descrito em [Introdução às filas][lnk-sb-queues-java]. Anote o namespace e o nome da fila.

    > [!NOTE]
    > As filas do Barramento de Serviço e os tópicos utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.

    ![Pontos de extremidade no Hub IoT][30]

3. Na folha **Pontos de extremidade**, clique em **Adicionar** na parte superior para adicionar a fila ao Hub IoT. Chame o ponto de extremidade de **CriticalQueue** e use o menu suspenso para selecionar **Fila do Barramento de Serviço**, o namespace do Barramento de Serviço no qual suas filas estão e o nome da sua fila. Quando terminar, clique em **Salvar** na parte inferior.

    ![Adicionando um ponto de extremidade][31]

4. Agora clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **DeviceTelemetry** como a fonte dos dados. Insira `level="critical"` como a condição e escolha a fila que acabou de adicionar como um ponto de extremidade personalizado como o ponto de extremidade de regra do direcionamento. Quando terminar, clique em **Salvar** na parte inferior.

    ![Adicionando uma rota][32]

    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão de um Hub IoT.

    ![Rota de fallback][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Ler no ponto de extremidade da fila

Como opção, você pode ler as mensagens no ponto de extremidade da fila seguindo as instruções em [Introdução às filas][lnk-sb-queues-java]. Dê ao aplicativo o nome de **read-critical-queue**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora, você está pronto para executar os três aplicativos.

1. Para executar o aplicativo **read-d2c-messages**, em um prompt ou shell de comando, navegue até a pasta read-d2c e execute o seguinte comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Executar read-d2c-messages][readd2c]

2. Para executar o aplicativo **read-critical-queue**, em um prompt ou shell de comando, navegue até a pasta read-critical-queue e execute o seguinte comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar read-critical-messages][readqueue]

3. Para executar o aplicativo **simulated-device** , em um prompt de comando ou shell, navegue até a pasta simulated-device e execute o seguinte comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Executar simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcional) Adicionar contêiner de armazenamento ao Hub IoT e rotear mensagens para ele

Nesta seção, você cria uma conta de Armazenamento, conecta-a ao Hub IoT e configura o Hub IoT para enviar mensagens à conta baseada na presença de uma propriedade na mensagem. Para saber mais sobre como gerenciar o armazenamento, consulte [Introdução ao Armazenamento do Azure][Armazenamento do Azure].

 > [!NOTE]
   > Caso não haja limite de um **Ponto de extremidade**, é possível configurar o **StorageContainer**, além do **CriticalQueue**, e executar ambos simultaneamente.

1. Crie uma conta de armazenamento, conforme descrito em [documentação do Armazenamento do Azure] [lnk-storage]. Anote o nome da conta.

2. No Portal do Azure, abra o Hub IoT e clique em **Pontos de extremidade**.

3. Na folha **Pontos de Extremidade**, selecione o ponto de extremidade **CriticalQueue** e clique em **Excluir**. Clique em **Sim** e depois em **Adicionar**. Nomeie o ponto de extremidade **StorageContainer**, use os menus suspensos para selecionar o **Contêiner do Armazenamento do Azure** e crie uma **Conta de armazenamento** e um **Contêiner de armazenamento**.  Anote os nomes.  Quando terminar, clique em **OK** na parte inferior. 

 > [!NOTE]
   > Caso tenha mais de um **Ponto de Extremidade**, não é necessário excluir **CriticalQueue**.

4. Clique em **Rotas** no Hub IoT. Clique em **Adicionar** na parte superior da folha para criar uma regra que encaminhe mensagens para a fila que você acabou de adicionar. Selecione **Mensagens de Dispositivo** como a fonte de dados. Insira `level="storage"` como a condição e escolha **StorageContainer** como um ponto de extremidade personalizado para o ponto de extremidade da regra de roteamento. Clique em **Salvar** na parte inferior.  

    Verifique se a rota de fallback está definida como **ATIVADA**. Essa é a configuração padrão de um Hub IoT.

1. Verifique se os aplicativos anteriores ainda estão em execução. 

1. No Portal do Azure, acesse sua conta de armazenamento, em **Serviço de Blob**, e clique em **Procurar blobs...**.  Selecione o contêiner, navegue até o arquivo JSON e clique nele, depois clique em **Baixar** para exibir os dados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a expedir confiavelmente mensagens do dispositivo para nuvem usando a funcionalidade de roteamento de mensagens do Hub IoT.

O tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d] mostra como enviar mensagens da solução de back-end para seus dispositivos.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT][lnk-suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introdução ao Hub IoT]: iot-hub-java-java-getstarted.md
[Centro de Desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
