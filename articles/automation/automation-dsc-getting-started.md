---
title: Introdução ao DSC de Automação do Azure
description: Explicações e exemplos das tarefas mais comuns no DSC (Configuração de Estado Desejado) de Automação do Azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd26c4b070ed7b92a73e43c1568805a9ac679d12
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196059"
---
# <a name="getting-started-with-azure-automation-dsc"></a>Introdução ao DSC de Automação do Azure

Este artigo explica como realizar as tarefas mais comuns com o DSC (Configuração de Estado Desejado) de Automação do Azure, como criar, importar e compilar configurações, máquinas de integração para gerenciar e exibir relatórios. Para obter uma visão geral do que o DSC de Automação do Azure é, consulte [Visão geral do DSC da Automação do Azure](automation-dsc-overview.md). Para obter a documentação da DSC, consulte [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/dsc/overview).

Este artigo fornece um guia passo a passo para usar o DSC de Automação do Azure. Se você quiser um ambiente de exemplo que já esteja configurado sem seguir as etapas descritas neste artigo, você pode usar o modelo do Gerenciador de Recursos a seguir: Este modelo define um ambiente de DSC de Automação do Azure concluído, incluindo uma VM do Azure gerenciada pelo DSC de Automação do Azure.

## <a name="prerequisites"></a>pré-requisitos

Para concluir os exemplos neste artigo, são necessários:

* Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
* Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Criando uma configuração de DSC

Crie uma [configuração de DSC](/powershell/dsc/configurations) simples que garante a presença ou a ausência do WindowsFeature do **Servidor Web** (IIS), dependendo de como os nós são atribuídos.

1. Inicie o ISE do Windows PowerShell (ou qualquer editor de texto).
1. Digite o seguinte texto:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true

            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'

            }
        }
    }
    ```
1. Salve o arquivo como `TestConfig.ps1`.

Esta configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), que garante a presença ou a ausência da funcionalidade **Servidor Web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importando uma configuração na Automação do Azure

Em seguida, importe a configuração para a conta de Automação.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página **Conta de automação**, selecione **Configurações DSC** em **Gerenciamento de Configuração**.
1. Na página **Configurações DSC**, clique em **+ Adicionar uma configuração**.
1. Na página **Importar Configuração**, procure o arquivo `TestConfig.ps1` no computador.

    ![Captura de tela da folha **Importar Configuração**](./media/automation-dsc-getting-started/AddConfig.png)
1. Clique em **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Exibindo uma configuração na Automação do Azure

Depois de importar uma configuração, você pode vê-la no Portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página **Conta de automação**, selecione **Configurações DSC** em **Gerenciamento de Configuração**.
1. Na página **Configurações DSC**, clique em **TestConfig** (esse é o nome da configuração importada no procedimento anterior).
1. Na página **Configuração de TestConfig**, clique em **Exibir fonte da configuração**.

    ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig)](./media/automation-dsc-getting-started/ViewConfigSource.png)

    A folha **Fonte da Configuração do TestConfig** é aberta, exibindo o código do PowerShell para a configuração.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilando uma configuração na Automação do Azure

Antes de aplicar um estado desejado a um nó, uma configuração DSC definindo esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no servidor de pull do DSC de Automação. Para obter uma descrição mais detalhada da compilação das configurações na DSC de Automação do Azure, consulte [Compilando as configurações na DSC de Automação do Azure](automation-dsc-compile.md). Para obter mais informações sobre a compilação das configurações, consulte [Configurações da DSC](/powershell/dsc/configurations).

1. Entre no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página **Conta de automação**, clique em **Configurações DSC** em **Gerenciamento de Configuração**.
1. Na página **Configurações DSC**, clique em **TestConfig** (o nome da configuração importada anteriormente).
1. Na página **Configuração de TestConfig**, clique em **Compilar** e, em seguida, em **Sim**. Isso inicia um trabalho de compilação.

    ![Captura de tela da página de configuração de TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando você compila uma configuração na Automação do Azure, ela implanta automaticamente quaisquer MOFs de configuração de nó no servidor de pull.

## <a name="viewing-a-compilation-job"></a>Exibindo um trabalho de compilação

Depois de iniciar uma compilação, você pode vê-la no bloco **Trabalhos de compilação** na folha **Configuração**. O bloco **Trabalhos de compilação** mostra os trabalhos atualmente em execução, concluídos e com falha. Quando você abre uma folha de trabalho de compilação, ela mostra informações sobre esse trabalho, incluindo quaisquer erros ou avisos encontrados, parâmetros de entrada usados na configuração e logs de compilação.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página **Conta de automação**, clique em **Configurações DSC** em **Gerenciamento de Configuração**.
1. Na página **Configurações DSC**, clique em **TestConfig** (o nome da configuração importada anteriormente).
1. Em **Trabalhos de compilação**, selecione o trabalho de compilação que você deseja exibir. Uma página **Trabalho de Compilação** é aberta, rotulada com a data em que o trabalho de compilação foi iniciado.

    ![Captura de tela da página Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)
1. Clique em um bloco na página **Trabalho de Compilação** para ver mais detalhes sobre o trabalho.

## <a name="viewing-node-configurations"></a>Exibindo configurações de nó

A conclusão com êxito de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de pull e está pronto para ser submetido ao pull e aplicado por um ou mais nós. Você pode exibir as configurações do nó em sua conta de Automação na folha **Configurações do Nó DSC** . Uma configuração de nó tem um nome com o formato *NomeConfiguração*.*NomeNó*.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
1. Na folha **Conta de Automação**, clique em **Configurações do Nó DSC**.

    ![Captura de tela da folha DSC Node Configurations (Configurações de Nó DSC)](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Integrando uma VM do Azure para o gerenciamento com o DSC de Automação do Azure

Você pode usar o DSC de Automação do Azure para gerenciar VMs do Azure (Clássicas e Resource Manager), VMs locais, computadores Linux, VMs AWS e computadores físicos locais. Neste artigo, você aprenderá como integrar somente VMs do Azure Resource Manager. Para obter informações sobre a integração de outros tipos de computadores, consulte [Integrando computadores para o gerenciamento pela DSC de Automação do Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Para integrar uma VM do Azure Resource Manager para o gerenciamento pelo DSC de Automação do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página **Conta de automação**, clique em **Nós DSC** em **Gerenciamento de Configuração**.
1. Na página **Nós DSC**, clique em **Adicionar VM do Azure**.

    ![Captura de tela da página Nós DSC realçando o botão Adicionar VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)
1. Na página Máquinas Virtuais, selecione sua VM. Na página **Adicionar VMs do Azure**, clique em **Selecionar máquinas virtuais para integração**.
1. Clique em **Conectar**.

   > [!IMPORTANT]
   > Ela deve ser uma VM do Azure Resource Manager executando o Windows Server 2008 R2 ou posterior.

1. Na página **Registro**, insira o nome da configuração do nó que você deseja aplicar à VM na caixa **Nome de Configuração do Nó**. Isso deve corresponder exatamente ao nome de uma configuração de nó na conta de Automação. Fornecer um nome neste ponto é opcional. Você pode alterar a configuração de nó atribuída após integrar o nó.
   Marque **Reinicializar o Nó se Necessário** e clique em **OK**.

    ![Captura de tela da folha Registro](./media/automation-dsc-getting-started/RegisterVM.png)

    A configuração do nó especificada é aplicada à VM em intervalos especificados pela **Frequência do Modo de Configuração** e a VM verifica se há atualizações para a configuração do nó em intervalos especificados pela **Frequência de Atualização**. Para obter mais informações sobre como esses valores são usados, consulte [Configurando o Gerenciador de Configuração Local](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. Na folha **Adicionar VMs do Azure**, clique em **Criar**.

O Azure inicia o processo de integração da VM. Quando for concluída, a VM é exibida na folha **Nós DSC** na conta de Automação.

## <a name="viewing-the-list-of-dsc-nodes"></a>Exibindo a lista de nós DSC

Você pode exibir a lista de todos os computadores que foram integrados para gerenciamento em sua conta de Automação na folha **Nós DSC** .

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
3. Na página **Conta de automação**, clique em **Nós DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Exibindo relatórios para nós DSC

Cada vez que o DSC de Automação do Azure executa uma verificação de consistência em um nó gerenciado, o nó envia um relatório de status para o servidor de pull. Exiba esses relatórios na página do nó.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
3. Na página **Conta de automação**, clique em **Nós DSC**.
4. Na lista **Nós DSC**, selecione o nó que deseja exibir.
5. Na página **Nó**, clique no relatório que você deseja exibir em **Relatórios**.

    ![Captura de tela da folha Relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na folha de um relatório individual, você pode ver as seguintes informações de status para a verificação de consistência correspondente:

* Status do relatório — se o nó é "Compatível", a configuração está "Com Falha" ou o nó "Não é Compatível" (quando o nó está no modo **applyandmonitor** e o computador não está no estado desejado).
* A hora de início para a verificação de consistência.
* O tempo de execução total para a verificação de consistência.
* O tipo de verificação de consistência.
* Quaisquer erros, incluindo o código de erro e a mensagem de erro.
* Todos os recursos DSC usados na configuração e o estado de cada recurso (se o nó está no estado desejado para esse recurso) — você pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
* O nome, o endereço IP e o modo de configuração do nó.

Você também pode clicar em **Exibir relatório bruto** para ver os dados reais que o nó envia para o servidor. Para obter mais informações sobre como usar esses dados, consulte [Usando um servidor de relatório da DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Pode levar algum tempo depois de um nó ser integrado até que o primeiro relatório esteja disponível. Talvez seja necessário aguardar até 30 minutos para o primeiro relatório após você integrar um nó.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Reatribuindo um nó a uma configuração de nó diferente

Você pode atribuir um nó para usar uma configuração de nó diferente daquela que inicialmente atribuída.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
3. Na página **Conta de automação**, clique em **Nós DSC**.
4. Na página **Nós DSC**, clique no nome do nó que você deseja reatribuir.
5. Na página desse nó, clique em **Atribuir nó**.

    ![Captura de tela da folha Nó realçando o botão Assign Node (Atribuir Nó)](./media/automation-dsc-getting-started/AssignNode.png)
6. Na página **Atribuir Configuração de Nó**, selecione a configuração de nó à qual você deseja atribuir o nó e, em seguida, clique em **OK**.

    ![Captura de tela da folha Atribuir Configuração de Nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Cancelando o registro de um nó

Se você não desejar mais que um nó seja gerenciado pelo DSC de Automação do Azure, você poderá cancelar o registro dele.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
3. Na página **Conta de automação**, clique em **Nós DSC**.
4. Na página **Nós DSC**, clique no nome do nó cujo registro você deseja cancelar.
5. Na página desse nó, clique em **Cancelar registro**.

    ![Captura de tela da folha Nó realçando o botão Cancelar Registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Artigos relacionados

* [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md)
* [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)
* [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/overview)
* [cmdlets do DSC de Automação do Azure](/powershell/module/azurerm.automation/#automation)
* [preço da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
