---
title: Configurar a recuperação de desastre de VMs VMware locais para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre do Azure para de VMs VMware locais com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2a96655c26e2df2534f420239b56ef0c3959319a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212867"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurar a recuperação de desastre de VMs VMware locais para o Azure

Este tutorial mostra como configurar a recuperação de desastre para o Azure para VMs de VMware locais em execução no Windows. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Inserir a origem e o destino da replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes locais do Azure Site Recovery e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Habilitar a replicação para uma VM.

Este tutorial é o terceiro de uma série. Este tutorial presume que você já concluiu as tarefas dos tutoriais anteriores:

* [Preparar o Azure](tutorial-prepare-azure.md). Este tutorial descreve como configurar uma conta de armazenamento do Azure e rede, verificar se a sua conta do Azure tem as permissões corretas e criar um cofre dos Serviços de Recuperação.
* [Preparar o VMware local](vmware-azure-tutorial-prepare-on-premises.md). Neste tutorial você prepara contas para que o Site Recovery possa acessar servidores VMware para descobrir VMs e, opcionalmente, fazer uma instalação por push do componente de serviço Site Recovery Mobility quando você habilitar a replicação de uma VM. Você também garante que seus servidores VMware e VMs estejam em conformidade com os requisitos do Site Recovery.

Antes de começar, é aconselhável [examinar a arquitetura](vmware-azure-architecture.md) dos cenários de recuperação de desastre.


## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Estamos usando **ContosoVMVault** para este cenário.
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hipervisor do VMware vSphere**. Depois, selecione **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem


Para configurar o ambiente de origem, é necessário um computador local único e altamente disponível para hospedar os componentes locais do Site Recovery. Os componentes incluem o servidor de configuração, o servidor de processo e o servidor de destino mestre:

- O servidor de configuração coordena a comunicação entre o ambiente local e o Azure, além de gerenciar a replicação de dados.
- O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure. O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.
- O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.

Para configurar o servidor de configuração como uma VM VMware altamente disponível, baixe um modelo OVF preparado e importe-o na VMware para criar a VM. Depois de configurar o servidor de configuração, registre-o no cofre. Após o registro, o Site Discovery descobre VMs locais da VMware.

> [!TIP]
> Este tutorial usa um modelo OVF para criar a VM do VMware do servidor de configuração. Se você não conseguir fazer isso, execute a [Configuração manual](physical-manage-configuration-server.md). 


### <a name="download-the-vm-template"></a>Baixe o modelo de VM

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo de OVF para o servidor de configuração.

  > [!TIP]
  É possível baixar a versão mais recente do modelo de servidor de configuração diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Entre no VMware vCenter Server ou no host vSphere ESXi, usando o VMware vSphere Client.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF** para iniciar o assistente de Implantação do modelo de OVF. 

     ![Modelo de OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

    * Caso deseje incluir uma interface de rede adicional, desmarque **Ligar após a implantação**. Em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.

## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

Para incluir uma NIC adicional ao servidor de configuração, faça-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Avançar**.
3. Selecione um tipo de adaptador e uma rede. 
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Selecione **Avançar** > **Concluir**. Depois, selecione **OK**.


## <a name="register-the-configuration-server"></a>Registrar o servidor de configuração 

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. Na primeira vez que entrar, a Ferramenta de Configuração do Azure Site Recovery é iniciada.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Avançar**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração é iniciado automaticamente.

### <a name="configure-settings-and-add-the-vmware-server"></a>Definir as configurações e adicionar o servidor do VMware

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade** e, em seguida, selecione a NIC que será usada pelo servidor de processo para receber o tráfego de replicação das VMs. Em seguida, selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida.
2. Em **Selecionar cofre de Serviços de Recuperação**, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.
3. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Baixar e Instalar** para instalar o MySQL Server.
4. Selecione **Instalar VMware PowerCLI**. Verifique se todas as janelas de navegador estão fechadas antes de fazer isso. Depois selecione **Continuar**.
5. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
6. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
7. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** e depois **Continuar**.
8. Em **Configurar credenciais de máquina virtual**, insira o nome de usuário e a senha a serem usados para instalar automaticamente o Serviço de Mobilidade em computadores, quando a replicação está habilitada. Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. Para o Linux, forneça detalhes para a conta raiz.
9. Selecione **Finalizar configuração** para concluir o registro. 
10. Após a conclusão do registro, no portal do Azure, verifique se o servidor de configuração e o VMware Server estão listados na página **Fonte** no cofre. Em seguida, selecione **OK** para definir as configurações de destino.


O Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

> [!NOTE]
> Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Selecione **Preparar infraestrutura** > **Destino**. Selecione a assinatura do Azure que deseja usar.
2. Especifique se seu modelo de implantação de destino é baseado no Azure Resource Manager ou é o clássico.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Guia Destino](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [Portal do Azure](https://portal.azure.com) e selecione **Todos os recursos**.
2. Selecione o cofre do Serviço de Recuperação chamado **ContosoVMVault**.
3. Para criar uma política de replicação, selecione **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
4. Em **Criar política de replicação**, insira o nome de política. Estamos usando **VMwareRepPolicy** para este cenário.
5. Em **Limite de RPO**, use o padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
6. Em **Retenção de ponto de recuperação**, use o padrão de 24 horas para a duração do período de retenção de cada ponto de recuperação. Neste tutorial, use 72 horas. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
7. Em **Frequência do instantâneo consistente com aplicativo**, use o padrão de 60 minutos para a frequência em que os instantâneos consistentes com o aplicativo são criados. Selecione **OK** para criar a política.

   ![Criar política de replicação](./media/vmware-azure-tutorial/replication-policy.png)

A política é associada automaticamente ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.

## <a name="enable-replication"></a>Habilitar a replicação

O Site Recovery instala o Serviço de Mobilidade quando a replicação é habilitada para uma VM. Pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

Habilite a replicação da seguinte maneira:

1. Selecione **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host.
5. Selecione o servidor de processo (servidor de configuração). Depois, selecione **OK**.
6. Em **Destino**, selecione a assinatura e o grupo de recursos em que deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou Resource Manager) para as VMs do failover.
7. Selecione a conta de armazenamento do Azure que deseja usar para replicar dados.
8. Selecione a rede e a sub-rede do Azure às quais conectar as VMs do Azure quando elas forem criadas após o failover.
9. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
10. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Depois, selecione **OK**.
11. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o Serviço de Mobilidade no computador.
12. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
13. Selecione **Habilitar Replicação**.

Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

Para monitorar as VMs adicionadas, verifique o horário da última descoberta de VMs em **Servidores de Configuração** > **Último Contato Às**. Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](site-recovery-test-failover-to-azure.md)
