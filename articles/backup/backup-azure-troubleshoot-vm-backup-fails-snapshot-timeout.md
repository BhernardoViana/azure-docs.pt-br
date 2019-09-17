---
title: 'Solucionar problemas de falha de Backup do Azure: Status do agente convidado indisponível'
description: Sintomas, causas e resoluções para falhas do Backup do Azure relacionados a agente, extensão e discos.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Backup do Azure; agente da VM; conectividade de rede;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 85c0cbc1e516730018f80e1978ba565e311117fe
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018163"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Solucionar problemas de falha de Backup do Azure: Problemas com o agente ou extensão

Este artigo fornece etapas de solução de problemas que podem ajudar você a resolver erros de Backup do Azure relacionados à comunicação com a extensão e o agente da VM.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – o agente de VM não pode se comunicar com o Backup do Azure

**Erro de código**: UserErrorGuestAgentStatusUnavailable <br>
**Mensagem de erro**: Agente de VM não consegue se comunicar com o Backup do Azure<br>

O agente de VM do Azure pode ser interrompido, desatualizado, em um estado inconsistente ou não instalado e impedir que o serviço de backup do Azure dispare os instantâneos.  

- Se o agente de VM for interrompido ou estiver em um estado inconsistente, **reinicie o agente** e repita a operação de backup (experimente um backup ad hoc). Para obter as etapas para reiniciar o agente, consulte [VMs do Windows](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) ou VMS do [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).
- Se o agente de VM não estiver instalado ou estiver desatualizado, Instale/atualize o agente de VM e repita a operação de backup. Para obter as etapas para instalar/atualizar o agente, consulte [VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – não foi possível se comunicar com o agente de VM para o status do instantâneo

**Erro de código**: GuestAgentSnapshotTaskStatusError<br>
**Mensagem de erro**: Não foi possível se comunicar com o agente de VM para status do instantâneo <br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  

**Causa 1: [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Causa 3: [O status do instantâneo não pode ser recuperado, ou não é possível obter um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Causa 4: [A extensão de backup falha ao atualizar ou carregar](#the-backup-extension-fails-to-update-or-load)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – o limite máximo da coleção de pontos de restauração foi atingido

**Erro de código**: UserErrorRpCollectionLimitReached <br>
**Mensagem de erro**: O limite máximo da coleção do Ponto de Restauração foi atingido. <br>

- Esse problema pode ocorrer se houver um bloqueio no grupo de recursos do ponto de recuperação, impedindo a limpeza automática de pontos de recuperação.
- Esse problema também pode ocorrer se vários backups são disparados por dia. Atualmente, é recomendável apenas um backup por dia, pois os pontos de restauração instantâneas são mantidos por 1-5 dias de acordo com a retenção de instantâneo configurada e somente 18 RPs podem ser associados a uma VM em um determinado momento. <br>

Ação recomendada:<br>
Para resolver esse problema, remova o bloqueio do grupo de recursos da VM e repita a operação para disparar a limpeza.
> [!NOTE]
> O serviço de backup cria um grupo de recursos separado que o grupo de recursos da VM para armazenar a coleção de pontos de restauração. Os clientes são aconselhados a não bloquear o grupo de recursos criado para uso pelo serviço de Backup. O formato de nomenclatura do grupo de recursos criado pelo serviço de Backup é: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Etapa 1: [Remover bloqueio do grupo de recursos do ponto de restauração](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Etapa 2: [ Limpar a coleção do ponto de restauração](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured – o backup não tem permissões suficientes no cofre de chaves para o backup de VMs criptografadas

**Erro de código**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Mensagem de erro**: O backup não tem permissões suficientes para o cofre de chaves para fazer backup de VMs criptografadas. <br>

Para que uma operação de backup tenha sucesso em VMs criptografadas, ela deve ter permissões para acessar o cofre de chaves. Isso pode ser feito usando o [portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) ou por meio do [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – falha na operação de instantâneo por falta de conectividade à rede na máquina virtual

**Erro de código**: ExtensionSnapshotFailedNoNetwork<br>
**Mensagem de erro**: Houve falha na operação de instantâneo por falta de conectividade à rede na máquina virtual<br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:

**Causa 1: [O status do instantâneo não pode ser recuperado, ou não é possível obter um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [A extensão de backup falha ao atualizar ou carregar](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [A VM não tem acesso à internet](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>Falha na operação de extensão ExtensionOperationFailedForManagedDisks-VMSnapshot

**Erro de código**: ExtensionOperationFailedForManagedDisks <br>
**Mensagem de erro**: Falha na operação da extensão de VMSnapshot<br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [O status do instantâneo não pode ser recuperado, ou não é possível obter um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 2: [A extensão de backup falha ao atualizar ou carregar](#the-backup-extension-fails-to-update-or-load)**  
**Causa 3: [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 4: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – falha no backup, com um erro interno

**Erro de código**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Mensagem de erro**: Falha no backup com um erro interno – tente novamente a operação dentro de alguns minutos <br>

Depois de registrar e agendar uma máquina virtual para o serviço de Backup do Azure, o Backup inicia o trabalho comunicando-se com a extensão de backup de VM para obter um instantâneo point-in-time. Qualquer uma das condições a seguir pode impedir que o instantâneo seja disparado. Se o instantâneo não for disparado, poderá ocorrer uma falha de backup. Conclua as seguintes etapas de solução de problemas na ordem listada e, depois, repita a operação:  
**Causa 1: [O agente está instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [O status do instantâneo não pode ser recuperado, ou não é possível obter um instantâneo](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Causa 4: [A extensão de backup falha ao atualizar ou carregar](#the-backup-extension-fails-to-update-or-load)**  
**Causa 5: O serviço de backup não tem permissão para excluir os pontos de restauração antigos devido a um bloqueio de grupo de recursos** <br>
**Causa 6: [A VM não tem acesso à internet](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize-os tamanhos de disco configurados não têm suporte no momento pelo backup do Azure.

**Erro de código**: UserErrorUnsupportedDiskSize <br>
**Mensagem de erro**: No momento, não há suporte para os tamanhos de disco configurados pelo backup do Azure. <br>

Sua operação de backup pode falhar ao fazer backup de uma VM com um tamanho de disco maior que 30 TB. Além disso, o backup de discos criptografados com mais de 4 TB não tem suporte atualmente. Verifique se os tamanhos de disco são menores ou iguais ao limite com suporte, dividindo os discos.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – não é possível iniciar o backup porque outra operação de backup já está em andamento

**Erro de código**: UserErrorBackupOperationInProgress <br>
**Mensagem de erro**: Não é possível iniciar o backup porque outra operação de backup já está em andamento<br>

O seu trabalho de backup recente falhou porque há um trabalho de backup existente em andamento. Você não pode iniciar um novo trabalho de backup antes que o trabalho atual seja concluído. Verifique se a operação de backup em andamento foi concluída antes de iniciar ou agendar outras operações de backup. Para verificar o status de trabalhos de backup, execute as seguintes etapas:

1. Entre no portal do Azure, clique em **todos os serviços**. Digite Serviços de Recuperação e clique em **Cofres dos Serviços de Recuperação**. A lista de cofres de Serviços de Recuperação aparecerá.
2. Na lista de cofres dos serviços de recuperação, selecione um cofre no qual o backup esteja configurado.
3. No menu do dashboard do cofre, clique em **Trabalhos de Backup** para exibir todos os trabalhos de backup.
   - Se um trabalho de backup estiver em andamento, aguarde a sua conclusão ou cancele-o.
     - Para cancelar o trabalho de backup, clique com o botão direito do mouse no trabalho de backup e clique em **Cancelar** ou use o [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Se você reconfigurou o backup em um cofre diferente, verifique se não há trabalhos de backup em execução no antigo cofre. Se existir, cancele o trabalho de backup.
     - Para cancelar o trabalho de backup, clique com botão direito do mouse no trabalho de backup e clique em **Cancelar** ou use o [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. Tente novamente a operação de backup.

Se a operação de backup agendado estiver demorando mais, em conflito com a próxima configuração de backup, revise as [práticas recomendadas](backup-azure-vms-introduction.md#best-practices), [desempenho de backup](backup-azure-vms-introduction.md#backup-performance)e [consideração de restauração](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-vm-has-no-internet-access"></a>A VM não tem acesso à Internet

Devido ao requisito de implantação, a VM não tem acesso à Internet. Ou, talvez haja restrições que impedem o acesso à infraestrutura do Azure.

Para funcionar corretamente, a extensão de Backup exige conectividade com endereços IP públicos do Azure. A extensão envia comandos para um ponto de extremidade de armazenamento do Azure (URL de HTTP) para gerenciar os instantâneos da VM. Se a extensão não tiver acesso à Internet pública, o backup, eventualmente, falhará.

#### <a name="solution"></a>Solução

Para resolver o problema de rede, confira [Estabelecer conectividade de rede](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas sem resposta (para VMs do Windows)

#### <a name="solution"></a>Solução

O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente convidado do Microsoft Azure está executando nos serviços de VM (services.msc). Tente reiniciar o serviço de agente convidado do Microsoft Azure e inicie o backup.
2. Se o serviço de agente convidado do Microsoft Azures não estiver visível nos serviços, no Painel de Controle, acesse **Programas e Recursos** para determinar se o serviço de agente convidado do Microsoft Azure está instalado.
3. Se o serviço de agente convidado do Microsoft Azures aparecer em **Programas e Recursos**, desinstale o serviço de agente convidado do Microsoft Azure.
4. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.
5. Verifique se os serviços de agente convidado do Microsoft Azures aparecem nos serviços.
6. Execute um backup sob demanda:
   - No portal, selecione **Fazer Backup Agora**.

Verifique também se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4.5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução

A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

2. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   - Para o Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Execute um novo backup de teste. Se a falha persistir, colete os seguintes logs da VM:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Se você precisar de log detalhado para waagent, siga estas etapas:

1. No arquivo /etc/waagent.conf, localize a seguinte linha: **Habilitar log detalhado (y|n)**
2. Altere o valor **Logs.Verbose** de *n* para *s*.
3. Salve a alteração e, em seguida, reinicie o waagent, completando as etapas descritas anteriormente nesta seção.

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Não é possível recuperar o status do instantâneo ou não é possível obter o instantâneo

O backup da máquina virtual depende de emitir um comando de instantâneo para a conta de armazenamento subjacente. O backup pode falhar porque não tem acesso à conta de armazenamento ou porque a execução da tarefa de instantâneo está atrasada.

#### <a name="solution"></a>Solução

As seguintes condições podem causar falha na tarefa de instantâneo:

| Causa | Solução |
| --- | --- |
| O status da VM foi informado incorretamente porque a VM foi desligada no protocolo RDP. | Se você desligar a VM no RDP, verifique o portal para determinar se o status da VM está correto. Se não estiver correto, desligue a VM no portal usando a opção de **Desligar** no painel de VM. |
| A VM não pode obter o endereço do host ou da malha do DHCP. | O DHCP deve estar habilitado no convidado para que o backup da VM IaaS funcione. Se a VM não puder obter o endereço do host ou da malha por meio da resposta 245 do DHCP, ela não poderá baixar ou executar nenhuma extensão. Se você precisar de um IP privado estático, deverá configurá-lo por meio do **portal do Azure** ou do **PowerShell** e certificar-se de que a opção DHCP dentro da VM esteja habilitada. [Saiba mais](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) sobre como configurar um endereço IP estático com o PowerShell.

### <a name="the-backup-extension-fails-to-update-or-load"></a>A extensão de backup não pode ser atualizada ou carregada

Se as extensões não puderem ser carregadas, o backup falhará porque não é possível obter um instantâneo.

#### <a name="solution"></a>Solução

Desinstale a extensão para forçar o recarregamento da extensão VMSnapshot. A próxima tentativa de backup recarregará a extensão.

Para desinstalar a extensão:

1. No [Portal do Azure](https://portal.azure.com/), acesse a VM que está apresentando falha de backup.
2. Escolha a opção **Configurações**.
3. Selecione **Extensões**.
4. Selecione **Extensão Vmsnapshot**.
5. Selecione **Desinstalar**.

Para VM Linux, se a extensão VMSnapshot não for mostrada no Portal do Azure, [atualize o Agente Linux do Azure](../virtual-machines/linux/update-agent.md) e, em seguida, execute o backup.

A realização dessas etapas faz com que a extensão seja reinstalada durante o próximo backup.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Remover o bloqueio do grupo de recursos de ponto de recuperação

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Vá para a **opção Todos os Recursos** e selecione o grupo de recursos de coleção de ponto de restauração no seguinte formato: AzureBackupRG_`<Geo>`_`<number>`.
3. Na seção **Configurações**, selecione **Bloqueios** para exibir os bloqueios.
4. Para remover o bloqueio, selecione as reticências e clique em **Excluir**.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> Limpar a coleção de pontos de restauração

Depois de remover o bloqueio, os pontos de restauração precisam ser limpos. Para limpar os pontos de restauração, siga qualquer um dos seguintes métodos:<br>

- [Limpar a coleção de pontos de restauração executando backup ad hoc](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
- [Limpar a coleção de pontos de restauração do portal do Azure](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Limpar a coleção de pontos de restauração executando backup ad hoc

Depois de remover o bloqueio, dispare um backup ad hoc/manual. Isso garantirá que os pontos de restauração sejam limpos automaticamente. Esperar que essa operação ad hoc/manual falhe na primeira vez; no entanto, ele garantirá a limpeza automática em vez da exclusão manual de pontos de restauração. Após a limpeza, o próximo backup agendado deverá ser bem-sucedido.

> [!NOTE]
> A limpeza automática ocorrerá após algumas horas de acionamento do backup ad hoc/manual. Se o backup agendado ainda falhar, tente excluir manualmente a coleção de pontos de restauração usando as etapas listadas [aqui](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Limpar coleção de pontos de restauração do portal do Azure <br>

Para limpar manualmente a coleção de pontos de restauração, que não é limpa devido ao bloqueio no grupo de recursos, tente as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. No menu **Hub**, clique em **Todos os recursos**, selecione o grupo de recursos com o formato AzureBackupRG_`<Geo>`_`<number>` a seguir em que sua VM se encontra.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Clique em Grupo de recursos, a folha **Visão geral** é exibida.
4. Selecione a opção **Mostrar tipos ocultos** opção para exibir todos os recursos ocultos. Selecione as coleções de ponto de restauração com o seguinte formato: AzureBackupRG_`<VMName>`_`<number>`.

    ![Excluir bloqueio](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Clique em **excluir** para limpar a coleção de pontos de restauração.
6. Tente a operação de backup novamente.

> [!NOTE]
 >Se o recurso (coleção de RP) tiver um grande número de pontos de restauração, a exclusão deles do portal poderá expirar e falhar. Esse é um problema conhecido do CRP, em que todos os pontos de restauração não são excluídos no tempo determinado e a operação atinge o tempo limite; no entanto, a operação de exclusão geralmente terá êxito após 2 ou 3 tentativas.
