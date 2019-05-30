---
title: Configurar recuperação de desastre para VMs do Hyper-V locais em nuvens do VMM para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de VMs do Hyper-V locais em nuvens do System Center VMM no Azure usando o Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 653db1497fcce5981bba7416f073b0330ca2861f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398146"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurar a recuperação de desastre de VMs locais do Hyper-V em nuvens do VMM para o Azure

Este artigo descreve como habilitar a replicação para VMs do Hyper-V locais gerenciadas pelo System Center Virtual Machine Manager (VMM), para recuperação de desastres para o Azure usando o [Azure Site Recovery](site-recovery-overview.md) service. Se você não estiver usando o VMM, [siga este tutorial](hyper-v-azure-tutorial.md) em vez disso.

Este é o terceiro tutorial em uma série que mostra como configurar a recuperação de desastre para o Azure para VMs VMware locais. No tutorial anterior, podemos [preparou o ambiente do Hyper-V local](hyper-v-prepare-on-premises-tutorial.md) para recuperação de desastres no Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecionar a origem e o destino de replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Configure mapeamento de rede para mapear entre redes de VM do VMM e redes virtuais do Azure.
> * Criar uma política de replicação.
> * Habilitar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção **Guias de Instruções** da [documentação do Site Recovery](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Presume-se que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. No portal do Azure, vá até a folha de cofres dos **Serviços de Recuperação** e selecione o cofre. Preparamos o cofre **ContosoVMVault** no tutorial anterior.
2. No **Guia de Introdução**, selecione **Site Recovery** e, em seguida, selecione **Preparar a Infraestrutura**.
3. Em **Meta de proteção** > **Em que local os seus computadores estão?** , selecione **Local**.
4. Em **Para qual deseja replicar os seus computadores?** , selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
6. Na **você estiver usando System Center VMM para gerenciar seus hosts Hyper-V?** , selecione **Sim**.
7.  Selecione **OK**.

    ![Meta de replicação](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

1. Em **Planejamento de implantação**, se você estiver planejando uma implantação grande, baixe o Planejador de Implantações do Hyper-V usando o link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planejamento de implantação do Hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantações. Em **Você concluiu o planejamento de implantação?** , selecione **Farei isso mais tarde** e, em seguida, selecione **OK**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Quando você configura o ambiente de origem, você pode instala o provedor Azure Site Recovery no servidor do VMM e registrar o servidor no cofre. Você pode instalar o agente de serviços de recuperação do Azure em cada host Hyper-V.

1. Na **preparar infraestrutura**, selecione **origem**.
2. Na **preparar origem**, selecione **+ VMM** para adicionar um servidor do VMM. Em **Adicionar Servidor**, verifique se o **Servidor do System Center VMM** aparece em **Tipo de servidor**.
3. Baixe o instalador do Provedor do Microsoft Azure Site Recovery.
4. Baixar a chave de registro do cofre. Você precisa dessa chave quando você executa a instalação do provedor. A chave é válida por cinco dias após ser gerada.
5. Baixe o instalador para o agente de serviços de recuperação do Microsoft Azure.

    ![Baixe o provedor e chave de registro do agente](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalar o Provedor no servidor VMM

1. No assistente de Instalação do Provedor do Azure Site Recovery > **Microsoft Update**, opte por usar o Microsoft Update para verificar se há atualizações do provedor.
2. Na **instalação**, aceite o local de instalação padrão para o provedor e selecione **instalar**.
3. Após a instalação, no Assistente de registro do Microsoft Azure Site Recovery > **as configurações do cofre**, selecione **procurar**e, na **arquivo de chave**, selecione a chave de cofre do arquivo que você baixou.
4. Especifique a assinatura do Azure Site Recovery e o nome do cofre (**ContosoVMVault**). Especifique um nome amigável para o servidor de VMM para identificá-lo no cofre.
5. Em **Configurações de Proxy**, selecione **Conectar diretamente ao Azure Site Recovery sem um proxy**.
6. Aceite o local padrão para o certificado que é usado para criptografar dados. Dados criptografados serão descriptografados quando você executar o failover.
7. Em **Sincronizar metadados de nuvem**, selecione **Sincronizar metadados da nuvem com o portal de recuperação de site**. Essa ação precisa ocorrer apenas uma vez em cada servidor. Em seguida, selecione **Registrar**.
8. Depois que o servidor está registrado no cofre, selecione **concluir**.

Após a conclusão do registro, os metadados do servidor são recuperados pelo Azure Site Recovery e o servidor VMM é exibido em **Infraestrutura do Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente de serviços de recuperação em hosts Hyper-V

Instale o agente em cada host Hyper-V que contém VMs que você deseja replicar.

1. Em que o Assistente de instalação do Microsoft Azure Recovery Services Agent > **verificação de pré-requisitos**, selecione **próxima**. Todos os pré-requisitos ausentes serão instalados automaticamente.
2. Na **configurações de instalação**, aceite o local de instalação e o local do cache. A unidade de cache precisa de pelo menos 5 GB de armazenamento. É recomendável uma unidade com 600 GB ou mais de espaço livre. Em seguida, selecione **Instalar**.
3. Na **instalação**, quando a instalação for concluída, selecione **fechar** para concluir o assistente.

    ![Instalar o agente](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos **ContosoRG**, em que as VMs do Azure serão criadas após o failover.
3. Selecione o **Resource Manager** modelo de implantação.

A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="configure-network-mapping"></a>Configurar o mapeamento de rede

1. Na **infraestrutura do Site Recovery** > **mapeamentos de rede** > **mapeamento de rede**, selecione o **+ mapeamento de rede** ícone.
2. Em **Adicionar mapeamento de rede**, selecione o servidor do VMM de origem. Selecione **Azure** como o destino.
3. Verifique a assinatura e o modelo de implantação após o failover.
4. Em **Rede de origem**, selecione a rede de origem da VM local.
5. Em **Rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizadas quando criadas após o failover. Depois, selecione **OK**.

    ![Mapeamento de rede](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **Preparar a Infraestrutura** > **Configurações de Replicação** >  **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos usando **ContosoReplicationPolicy**.
3. Deixe as configurações padrão e selecione **Okey**.
    - **Frequência de cópia** indica que, após a replicação inicial, os dados delta serão replicadas a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica que cada ponto de recuperação será mantido por duas horas.
    - **Frequência do instantâneo consistente com o aplicativo** indica que os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados a cada hora.
    - **Hora de início para replicação inicial** indica que a replicação inicial deve começar imediatamente.
    - **Criptografar dados armazenados no Azure** é definido como o padrão (**desativar**) e indica que os dados em repouso no Azure não estão criptografados.
4. Depois que a política for criada, selecione **OK**. Quando você cria uma nova política, ela é associada automaticamente a nuvem do VMM.

## <a name="enable-replication"></a>Habilitar a replicação

1. Em **Replicar aplicativo**, selecione **Origem**.
2. Em **Origem**, selecione a nuvem do VMM. Depois, selecione **OK**.
3. Na **alvo**, verifique se o destino (Azure), a assinatura do cofre e selecione o **Gerenciador de recursos** modelo.
4. Selecione o **contosovmsacct1910171607** conta de armazenamento e o **ContosoASRnet** rede do Azure.
5. Em **Máquinas Virtuais** > **Selecionar**, selecione a VM que deseja replicar. Depois, selecione **OK**.

   É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Depois que o trabalho **Finalizar Proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)
