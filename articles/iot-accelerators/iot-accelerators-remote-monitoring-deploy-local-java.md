---
title: Implantar a solução de monitoramento remoto localmente-IntelliJ IDE-Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto em seu computador local usando o IntelliJ para teste e desenvolvimento.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888819"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Implantar o acelerador de solução de Monitoramento Remoto localmente – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de Monitoramento Remoto no computador local para teste e desenvolvimento. Você aprenderá a executar os microserviços no IntelliJ. Uma implantação de microserviços locais usará os seguintes serviços de nuvem: Hub IoT, Azure Cosmos DB, análise de streaming do Azure e Azure Time Series Insights.

Se você quiser executar o acelerador de solução de Monitoramento Remoto no Docker em seu computador local, confira [Implantar o acelerador de solução de Monitoramento Remoto localmente – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de Monitoramento Remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisa ter as seguintes ferramentas instaladas no computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Plug-in escalar IntelliJ](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plug-in IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plug-in executor do IntelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [V8 node. js](https://nodejs.org/)

Node. js V8 é um pré-requisito para a CLI de PCS que os scripts usam para criar recursos do Azure. Não use o Node.js v10.

> [!NOTE]
> O IntelliJ IDE está disponível para Windows e Mac.

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Os repositórios de código-fonte de Monitoramento Remoto incluem o código-fonte e os arquivos de configuração do Docker necessários para executar as imagens do Docker dos microsserviços.

Para clonar e criar uma versão local do repositório, use seu ambiente de linha de comando para ir para uma pasta adequada no computador local. Em seguida, execute um dos seguintes conjuntos de comandos para clonar o repositório Java:

* Para baixar a versão mais recente das implementações do microserviço Java, execute o seguinte comando:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Para recuperar os submódulos mais recentes, execute os seguintes comandos:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Esses comandos baixam o código-fonte para todos os microsserviços, bem como os scripts usados para executar os microsserviços localmente. Você não precisa do código-fonte para executar os microserviços no Docker. Mas o código-fonte será útil se você planejar posteriormente modificar o Solution Accelerator e testar as alterações localmente.

## <a name="deploy-the-azure-services"></a>Implantar os serviços do Azure

Embora este artigo mostre como executar os microsserviços localmente, eles dependem de serviços do Azure em execução na nuvem. Use o seguinte script a seguir para implantar os serviços do Azure. Os exemplos de script pressupõem que você esteja usando o repositório Java em um computador Windows. Se estiver trabalhando em outro ambiente, ajuste os caminhos, extensões de arquivo e separadores de caminho adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos do Azure

Se você ainda não criou os recursos do Azure necessários, siga estas etapas:

1. Em seu ambiente de linha de comando, vá para a pasta **\services\scripts\local\launch** na cópia clonada do repositório.

1. Execute os seguintes comandos para instalar as ferramentas da CLI **pcs** e entrar em sua conta do Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Execute o script **start.cmd**. O script solicitará as seguintes informações:

   * Um nome de solução.
   * A assinatura do Azure a utilizar.
   * O local do datacenter do Azure para usar.

   O script cria um grupo de recursos no Azure que tem o nome da solução. Esse grupo de recursos contém os recursos do Azure que o Solution Accelerator usa. Você pode excluir esse grupo de recursos depois de não precisar mais dos recursos correspondentes.

   O script também adiciona um conjunto de variáveis de ambiente ao computador local. Cada nome de variável tem o prefixo **PCs**. Essas variáveis de ambiente fornecem detalhes que permitem que o monitoramento remoto leia seus valores de configuração de um recurso de Azure Key Vault.

   > [!TIP]
   > Quando o script for concluído, ele salvará as variáveis de ambiente em um arquivo chamado **\<pasta base\>\\. pcs\\\<nome da solução\>. env**. Você pode usá-los para implantações futuras do Solution-Accelerator. Observe que todas as variáveis de ambiente definidas no computador local substituem os valores nos **\\scripts de serviços\\arquivo local\\. env** quando você executa o **Docker-Compose**.

1. Feche seu ambiente de linha de comando.

### <a name="use-existing-azure-resources"></a>Usar recursos existentes do Azure

Se você já tiver criado os recursos do Azure necessários, defina as variáveis de ambiente correspondentes em seu computador local:
* **PCS_KEYVAULT_NAME**: o nome do recurso de Key Vault.
* **PCS_AAD_APPID**: a ID do aplicativo Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET**: o segredo do aplicativo do Azure AD.

Os valores de configuração serão lidos deste Key Vault recurso. Essas variáveis de ambiente podem ser salvas no **\<sua pasta base\>\\. pcs\\\<nome da solução\>arquivo. env** da implantação. Observe que quaisquer variáveis de ambiente definidas em seu computador local substituem os valores no arquivo **serviços\\scripts\\local\\.env** ao executar **docker-compose**.

Algumas das configurações necessárias para o microserviço são armazenadas em uma instância do Key Vault que foi criada na implantação inicial. As variáveis correspondentes no cofre de chaves devem ser modificadas conforme necessário.

## <a name="run-the-microservices"></a>Executar os microsserviços

Nesta seção, você executa os microsserviços do Monitoramento Remoto. Você executa:

* A interface do usuário da Web nativamente.
* Os serviços de simulação de dispositivo IoT do Azure, autenticação e Gerenciador de Azure Stream Analytics no Docker.
* Os microserviços no IntelliJ.

### <a name="run-the-device-simulation-service"></a>Executar o serviço de simulação de dispositivo

Abra uma nova janela de prompt de comando. Verifique se você tem acesso às variáveis de ambiente definidas pelo script **Start. cmd** na seção anterior.

Execute o comando a seguir para abrir o contêiner do Docker para o serviço de simulação de dispositivo. O serviço simula dispositivos para a solução de monitoramento remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Executar o serviço de Autenticação

Abra uma nova janela de prompt de comando e, em seguida, execute o comando a seguir para abrir o contêiner do Docker para o serviço de autenticação. Ao usar esse serviço, você pode gerenciar os usuários que estão autorizados a acessar as soluções de IoT do Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Executar o serviço do Gerenciador de Stream Analytics

Abra uma nova janela de prompt de comando e, em seguida, execute o comando a seguir para abrir o contêiner do Docker para o serviço do Gerenciador de Stream Analytics. Com esse serviço, você pode gerenciar trabalhos de Stream Analytics. Essa gestão inclui configuração de configuração de trabalho e início, interrupção e monitoramento do status do trabalho.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Implantar todos os outros microserviços em seu computador local

As etapas a seguir mostram como executar os microserviços de monitoramento remoto no IntelliJ.

#### <a name="import-a-project"></a>Importar um projeto

1. Abra o IDE do IntelliJ.
1. Selecione **Importar projeto**.
1. Escolha **Azure-IOT-PCs-Remote-Monitoring-java\services\build.SBT**.

#### <a name="create-run-configurations"></a>Criar configurações de execução

1. Selecione **executar** > **Editar configurações**.
1. Selecione **Adicionar nova configuração** > **tarefa SBT**.
1. Insira **nome**e, em seguida, insira **tarefas** como **executar**.
1. Selecione o **diretório de trabalho** com base no serviço que você deseja executar.
1. Selecione **aplicar** > **OK** para salvar suas escolhas.
1. Crie configurações de execução para os seguintes serviços Web:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Por exemplo, a imagem a seguir mostra como adicionar uma configuração para um serviço:

[![captura de tela da janela configurações de execução/depuração do IDE do IntelliJ, mostrando a opção storageAdapter realçada na lista tarefas do SBT no painel esquerdo e entradas nas caixas nome, tarefas, diretório de trabalho e parâmetros de VM no painel direito.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Criar uma configuração composta

1. Para executar todos os serviços juntos, selecione **Adicionar nova configuração** > **composto**.
1. Insira **nome**e, em seguida, selecione **adicionar tarefas de SBT**.
1. Selecione **aplicar** > **OK** para salvar suas escolhas.

Por exemplo, a imagem a seguir mostra como adicionar todas as tarefas SBT a uma única configuração:

[![captura de tela da janela de configurações de execução/depuração do IDE do IntelliJ, mostrando a opção de todos os serviços realçada na lista composta no painel esquerdo e a opção SBT tarefa ' deviceTelemetry ' realçada no painel direito.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selecione **executar** para compilar e executar os serviços Web no computador local.

Cada serviço Web abre uma janela de prompt de comando e uma janela do navegador da Web. No prompt de comando, você vê a saída do serviço em execução. A janela do navegador permite que você monitore o status. Não feche as janelas de prompt de comando ou as páginas da Web, uma vez que essas ações interrompem o serviço.

Para acessar o status dos serviços, acesse as seguintes URLs:

* Gerenciador de Hub IoT: [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria do dispositivo: [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* configuração: [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adaptador de armazenamento: [http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho do Stream Analytics

Siga estas etapas para iniciar o trabalho de Stream Analytics:

1. Vá para o [Portal do Azure](https://portal.azure.com).
1. Vá para o **grupo de recursos** criado para sua solução. O nome do grupo de recursos é o nome da solução que você escolheu quando executou o script **start.cmd**.
1. Selecione o **trabalho de Stream Analytics** na lista de recursos.
1. Na página **visão geral** do trabalho de Stream Analytics, selecione o botão **Iniciar** e, em seguida, selecione **Iniciar** para iniciar o trabalho.

### <a name="run-the-web-ui"></a>Executar a interface do usuário da Web

Nesta etapa, você inicia a interface do usuário da Web. Abra uma nova janela de prompt de comando. Verifique se você tem acesso às variáveis de ambiente definidas pelo script **Start. cmd** . Vá para a pasta **WebUI** em sua cópia local do repositório e, em seguida, execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o comando **Iniciar** for concluído, o navegador exibirá a página no endereço [http://localhost:3000/dashboard](http://localhost:3000/dashboard). São esperados erros nessa página. Para exibir o aplicativo sem erros, conclua as etapas a seguir.

### <a name="configure-and-run-nginx"></a>Configurar e executar o Nginx

Configure um servidor proxy reverso que vincule o aplicativo Web aos microserviços em execução no computador local:

1. Copie o arquivo **nginx. conf** da pasta **webui\scripts\localhost** em sua cópia local do repositório para o diretório de instalação do **nginx\conf** .
1. Execute nginx.

Para obter mais informações sobre como executar o Nginx, consulte [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Conecte-se ao painel

Para acessar o painel da solução de monitoramento remoto, vá para http://localhost:9000 em seu navegador.

## <a name="clean-up"></a>Limpar

Para evitar encargos desnecessários, remova os serviços de nuvem da sua assinatura do Azure depois de concluir o teste. Para remover os serviços, vá para o [portal do Azure](https://ms.portal.azure.com)e exclua o grupo de recursos criado pelo script **Start. cmd** .

Você também pode excluir a cópia local do repositório de monitoramento remoto que foi criado quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel de solução](quickstart-remote-monitoring-deploy.md).
