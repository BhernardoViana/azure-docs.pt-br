---
title: Instalar o Servidor de Backup do Azure v2
description: O Servidor de Backup do Azure v2 oferece recursos aprimorados de backup para proteção de VMs, arquivos e pastas, cargas de trabalho e muito mais. Saiba como instalar ou atualizar para o Servidor de Backup do Azure v2.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: adigan
ms.openlocfilehash: 4f1e0c14d3a835b9f6d739511186bdcc19917a7a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230964"
---
# <a name="install-azure-backup-server-v2"></a>Instalar o Servidor de Backup do Azure v2

Servidor de Backup do Azure ajuda a proteger suas máquinas virtuais (VMs), cargas de trabalho, arquivos e pastas e muito mais. O Servidor de Backup do Azure v2 se baseia no Servidor de Backup do Azure v1 e oferece novos recursos que não estão disponíveis em v1. Para obter uma comparação dos recursos da v1 e v2, consulte o [matriz de proteção do servidor de Backup do Azure](backup-mabs-protection-matrix.md). 

Os recursos adicionais no Servidor de Backup v2 são uma atualização do Servidor de Backup v1. No entanto, o Servidor de Backup v1 não é um pré-requisito para instalar o Servidor de Backup v2. Se você quiser atualizar do Servidor de Backup v1 para o Servidor de Backup v2, instale o Servidor de Backup v2 no servidor de proteção do Servidor de Backup. As suas configurações do Servidor de Backup existentes permanecem intactas.

Você pode instalar o Backup Server v2 no Windows Server 2016 ou no Windows Server 2012 R2. Para tirar proveito dos novos recursos como o moderno armazenamento de backup do System Center 2016 Data Protection Manager, você deve instalar o Servidor de Backup v2 no Windows Server 2016. Antes de atualizar ou instalar o Servidor de Backup v2, leia sobre [os pré-requisitos de instalação](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Servidor de Backup do Azure tem o mesmo código de base do System Center Data Protection Manager. O Backup Server v1 é equivalente ao Data Protection Manager 2012 R2. O Backup Server v2 é equivalente ao Data Protection Manager 2016. Este artigo ocasionalmente faz referência à documentação do Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Atualizar Servidor de Backup para v2
Para atualizar do Servidor de Backup v1 para o Servidor de Backup v2, verifique se a instalação tem as atualizações necessárias:

- [Atualização dos agentes de proteção](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) nos servidores protegidos.
- Atualização do Windows Server 2012 R2 para o Windows Server 2016.
- Atualização do administrador remoto do Servidor de Backup do Azure em todos os servidores de produção.
- Certifique-se de que os backups estão definidos para continuar sem reiniciar o servidor de produção.


### <a name="upgrade-steps-for-backup-server-v2"></a>Etapas de atualização para o Servidor de Backup v2

1. No Centro de Download, [baixar o instalador de atualização](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Depois de extrair o assistente de instalação, certifique-se de que **Executar setup.exe** está selecionado e, em seguida, selecione **Concluir**.

  ![Instalador de instalação - Execute a instalação](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. No assistente do Servidor de Backup do Microsoft Azure, em **Instalar**, selecione **Microsoft Azure Backup Server**.

  ![Instalador de instalação - Selecione instalação](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Na página **Inicial**, examine os avisos, e em seguida selecione **Avançar**.

  ![Instalador de instalação - Página Inicial](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. O assistente de instalação executa as verificações de pré-requisitos para certificar de que seu ambiente pode ser atualizado. Na página **Verificações de pré-requisitos**, selecione **Verificar**.

  ![Instalador de instalação - página Verificações de Pré-requisitos](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Seu ambiente deve passar pelas verificações de pré-requisito. Se seu ambiente não atender as verificações, observe os problemas e corrija-os. Em seguida, selecione **Verificar Novamente**. Depois de passar as verificações de pré-requisitos, selecione **Avançar**.

  ![Instalador de instalação - Botão Verificar Novamente](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Na página **Configurações do SQL**, selecione a opção relevante para sua instalação do SQL e, em seguida, selecione **Verificar e Instalar**.

  ![Instalador de instalação - Página Configurações do SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  As verificações podem levar alguns minutos. Quando as verificações tiverem terminado, selecione **Avançar**.

  ![Instalador de instalação - botão de Instalação e Verificação de Configurações do SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and-fix-settings.png)

8. Na página **Configurações de instalação**, faça as alterações para o local onde o Servidor de Backup está instalado, ou para o Local de Rascunho. Selecione **Avançar**.

  ![Instalador de instalação - página de Configurações de Instalação](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Para concluir o assistente de instalação, selecione **Concluir**.

  ![Instalador de instalação - Concluir](./media/backup-mabs-upgrade-to-v2/run-setup.png)

## <a name="add-storage-for-modern-backup-storage"></a>Adicionar armazenamento para Armazenamento de Backup moderno

Para melhorar a eficiência do armazenamento de backup, o Servidor de Backup v2 adiciona suporte para volumes. Servidor de backup v1 e v2 do servidor de Backup oferecer suporte a discos.

### <a name="add-volumes-and-disks"></a>Adicionar discos e volumes

Se você executar o Servidor de Backup v2 no Windows Server 2016, você pode usar volumes para armazenar dados de backup. Os volumes oferecem economia de armazenamento e backups mais rápidos. Como os volumes são novos para o Servidor de Backup, você deve adicioná-los. 

Quando você adicionar um volume para o Servidor de Backup, você pode dar um nome amigável ao volume. Selecione o **nome amigável** coluna do volume que você deseja nomear. Você pode alterar o nome posteriormente, se necessário. Você também pode usar o PowerShell para adicionar ou alterar os nomes amigáveis dos volumes.

Para adicionar um volume no Console do Administrador:

1. No Console do Administrador do Servidor de Backup do Azure, selecione **Gerenciamento** > **Armazenamento em Disco** > **Adicionar**.

  ![Abra o assistente para Adicionar Armazenamento em Disco](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

  O assistente Adicionar disco rígido é aberto.

2. Na página **Adicionar Armazenamento em Disco**, na caixa **Volumes disponíveis**, selecione um volume e, em seguida, selecione **Adicionar**.

3. Na caixa **Volumes selecionados**, digite um nome amigável para o volume e, em seguida, selecione **OK**.

  ![Assistente para Adicionar Armazenamento em Disco - Adicionar volume](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Se você quiser adicionar um disco, o disco deve pertencer a um grupo de proteção que tem armazenamento herdado. Você pode usar esses discos apenas para esses grupos de proteção. Se o Servidor de Backup não tiver fontes que têm proteção herdada, o disco não está listado.

  Para obter mais informações sobre como adicionar discos, consulte [Adicionar discos para aumentar o armazenamento herdado](https://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Você não pode dar um nome amigável para um disco.


### <a name="assign-workloads-to-volumes"></a>Atribuir as cargas de trabalho para volumes

No Servidor de Backup, você especificar as cargas de trabalho que estão atribuídas aos volumes. Por exemplo, você pode definir volumes caros que dão suporte a um grande número de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que exigem backups frequentes de alto volume. Um exemplo é o SQL Server com logs de transação.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Para atualizar as propriedades de um volume no pool de armazenamento no servidor de backup, use o cmdlet **Update-DPMDiskStorage** do PowerShell.

Sintaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [<CommonParameters>]
```

Todas as alterações feitas por meio do PowerShell são refletidas na interface do usuário.


## <a name="protect-data-sources"></a>Proteger fontes de dados
Para começar a proteger as fontes de dados, crie um grupo de proteção. As etapas a seguir destacam as alterações ou adições ao Assistente de Novo Grupo de Proteção.

Para criar um grupo de proteção:

1. No Console do Administrador do Servidor de Backup, selecione **Proteção**.

2. Na faixa de opções da ferramenta, selecione **Novo**.

  O Assistente de Novo Grupo de Proteção é aberto.

  ![Assistente para Criar Novo Grupo de Proteção](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Sobre o **boas-vindas** página, selecione **próxima**.

4. Na página **Selecionar Tipo de Grupo de Proteção**, selecione o tipo de grupo de proteção que você deseja criar e, em seguida, selecione **Avançar**.

  ![Selecionar a página do Tipo de Grupo de Proteção](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Na página **Selecionar membros do grupo**, na caixa **Membros disponíveis**, os membros com agentes de proteção são listados. Para este exemplo, selecione o volume D:\ e E:\, e, em seguida, adicione-os a **Membros selecionados**. Selecione **Avançar**.

  ![Selecionar a página Membros do Grupo](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Na página **Selecionar Método de Proteção de Dados**, insira um **nome do Grupo de Proteção** e selecione o método de proteção e, em seguida, selecione **Avançar**. Se você quiser proteção de curto prazo, selecione o método de backup **Disk**.

  ![Selecionar a página Método de Proteção de Dados](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Na página **Especificar Objetivos de Curto Prazo**, selecione os detalhes de **Período de retenção** e **Frequência de sincronização**. Em seguida, selecione **Avançar**. Opcionalmente, para alterar o agendamento para quando os pontos de recuperação são obtidos, selecione **Modificar**.

  ![Especificar a página Objetivos de Curto Prazo](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Na página **Revisar Propriedade de Armazenamento em Disco**, revise os detalhes sobre as fontes de dados selecionadas, incluindo o tamanho da fonte de dados, valores para o espaço a ser provisionado e o volume de armazenamento de destino.

  ![Página Examinar Alocação de Armazenamento de Disco](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Volumes de armazenamento são baseados na alocação de volume de carga de trabalho (definido usando o PowerShell) e o armazenamento disponível. Você pode alterar os volumes de armazenamento selecionando outros volumes no menu suspenso. Se você alterar o valor de **Armazenamento de Destino**, o valor de **Armazenamento em Disco Disponível** dinamicamente altera para refletir os valores em **Espaço Livre** e **Espaço Aprovisionado**.

  Se as fontes de dados crescem conforme planejado, o valor para a coluna **Espaço Aprovisionado** no **Armazenamento em Disco Disponível** reflete a quantidade de armazenamento adicional é necessário. Use esse valor para ajudar a planejar suas necessidades de armazenamento de backups estáveis. Se o valor for zero, não há nenhum problema potencial com o armazenamento no futuro próximo. Se o valor for um número diferente de zero, você não terá armazenamento suficiente alocado (com base na sua política de proteção e no tamanho dos dados de seus membros protegidos).

  ![Armazenamento em disco subalocado](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

  Para terminar de criar seu grupo de proteção, conclua o assistente.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrar o armazenamento herdado para o Armazenamento de Backup Moderno
Depois de atualizar ou instalar o servidor de Backup v2 e, em seguida, atualize o sistema operacional para o Windows Server 2016, atualize seus grupos de proteção para usar o armazenamento de Backup moderno. Por padrão, os grupos de proteção não são alterados. Eles continuam a funcionar como foram configurados inicialmente. 

Atualizar grupos de proteção para usar o Armazenamento de Backup Moderno é opcional. Para atualizar o grupo de proteção, interrompa a proteção de todas as fontes de dados usando a opção manter dados. Em seguida, adicione as fontes de dados para um novo grupo de proteção.

1. No Console do Administrador do System Center 2016 DPM, selecione o recurso **Proteção**. Na lista **Membro do Grupo de Proteção**, clique com o botão direito no membro e, em seguida, selecione **Interromper proteção do membro**.

  ![Interromper a proteção de membro](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Na caixa de diálogo **Remover do grupo**, examine o espaço em disco usado e o espaço livre disponível no pool de armazenamento. O padrão é deixar os pontos de recuperação no disco e permitir a expiração por sua política de retenção associada. Selecione **OK**.

  Se você quiser retornar imediatamente o espaço em disco usado para o pool de armazenamento livre, selecione a caixa de seleção **Excluir réplica no disco** para excluir os dados de backup (e pontos de recuperação) associado a esse membro.

  ![Remover da caixa de diálogo Grupo](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Crie um grupo de proteção que usa o Armazenamento de Backup Moderno. Inclua as fontes de dados desprotegidos.


## <a name="add-disks-to-increase-legacy-storage"></a>Adicionar discos para aumentar o armazenamento herdado

Se você quiser usar armazenamento herdado com o Servidor de Backup, talvez seja preciso adicionar discos para aumentar o armazenamento legado. 

Para adicionar armazenamento em disco:

1. No System Center 2016 DPM Administrator Console, selecione **Management** > **armazenamento em disco** > **adicionar**.

  ![Adicionar caixa de diálogo de Armazenamento em Disco](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

2. Na caixa de diálogo **Add Disk Storage**, selecione **Add disks**.

3. Na lista de discos disponíveis, selecione os discos que você deseja adicionar. Selecione **Adicionar** e, em seguida, selecione **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Atualizar o agente de proteção do Data Protection Manager

O Servidor de Backup usa o agente de proteção do System Center Data Protection Manager para atualizações. Se você estiver atualizando um agente de proteção não está conectado à rede, você não pode usar o Console do administrador do Data Protection Manager para concluir uma atualização de agente conectado. Você deve atualizar o agente de proteção em um ambiente de domínio não ativo. Até que o computador cliente esteja conectado à rede, o Console do Administrador do Data Protection Manager mostrará que a atualização do agente de proteção está pendente.

As seções a seguir descrevem como atualizar os agentes de proteção para computadores cliente que estão conectados e para computadores cliente que não estão conectados.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Atualizar um agente de proteção de um computador cliente conectado

1. No Console do Administrador do Servidor de Backup, selecione **Gerenciamento** > **Agentes**.

2. No painel de exibição, selecione os computadores cliente para o qual você deseja atualizar o agente de proteção.

  > [!NOTE]
  > A coluna de **Atualizações de Agente** indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No painel **Ações**, a ação de **Atualização** está disponível somente quando um computador protegido está selecionado e as atualizações estão disponíveis.

3. Para instalar agentes de proteção atualizados nos computadores selecionados, no painel **Ações**, selecione **Atualização**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Atualizar um agente de proteção em um computador cliente que não está conectado

1. No Console do Administrador do Servidor de Backup, selecione **Gerenciamento** > **Agentes**.

2. No painel de exibição, selecione os computadores cliente para o qual você deseja atualizar o agente de proteção.

  > [!NOTE]
  > A coluna de **Atualizações de Agente** indica quando uma atualização do agente de proteção está disponível para cada computador protegido. No **ações** painel, o **atualização** ação não está disponível quando um computador protegido está selecionado, a menos que as atualizações estão disponíveis.

3. Para instalar agentes de proteção atualizados nos computadores selecionados, selecione **Atualização**.

4. Para um computador cliente que não esteja conectado à rede, até que o computador esteja conectado à rede, a coluna **Status do agente** mostrará um status de **Atualização pendente**.

  Depois que um computador cliente está conectado à rede, a coluna **Atualizações de Agente** para o computador cliente mostra um status de **Atualizando**.
  
### <a name="move-legacy-protection-groups-from-the-old-version-and-sync-the-new-version-with-azure"></a>Mover grupos de proteção herdados da versão antiga e sincronizar a nova versão com o Azure

Quando o Servidor de Backup do Azure e o SO são atualizados, você está pronto para proteger novas fontes de dados usando o Armazenamento de Backup Moderno. As fontes de dados que já estão protegidas continuam a ser protegidas como estavam no servidor de backup do Azure (herdado). Todos os novos grupos de proteção usam o Armazenamento de backup moderno.

Para migrar fontes de dados do modo legado de proteção para o Armazenamento de backup moderno:

1.  Adicione o novo volume ao pool de armazenamento do Data Protection Manager. Você também pode atribuir um nome amigável e selecionar marcas de fonte de dados.

2. Para cada fonte de dados que está no modo herdado, pare a proteção das fontes de dados. Em seguida, selecione **Retain Protected Data**.  Isso permite a recuperação de pontos de recuperação antigos após a migração.

3. Crie um novo grupo de proteção. Em seguida, selecione as fontes de dados que você deseja armazenar usando o novo formato.

  O Data Protection Manager armazena uma cópia de réplica do armazenamento de backup herdado no volume Modern Backup Storage localmente.
    > [!NOTE] 
    > A criação da cópia aparece como um trabalho de operação pós-recuperação.

  Todos os novos pontos de sincronização e recuperação são armazenados no Armazenamento de backup moderno.

  Os pontos de recuperação antigos são eliminados quando expiram. À medida que os pontos de recuperação antigos são excluídos, o espaço em disco é liberado.

  Quando todos os volumes herdados forem excluídos do armazenamento antigo, você poderá remover o disco do Backup do Azure. Você pode então remover o disco do sistema.

4. Faça um backup do banco de dados do Data Protection Manager.

  > [!IMPORTANT]
  > - O novo nome do servidor deve ter o mesmo nome da instância original do Servidor de Backup do Azure. Você não pode alterar o nome da nova instância do Servidor de Backup do Azure se quiser usar o pool de armazenamento anterior e o banco de dados do Data Protection Manager para reter os pontos de recuperação.
  > - Você deve ter um backup do banco de dados do Data Protection Manager. Você precisará restaurar o banco de dados.

5. Encerre a instância original do Servidor de Backup do Azure ou coloque o servidor offline.

6. Redefina a conta da máquina no Active Directory.

7. Instale o Windows Server 2016 em uma nova máquina. Para o nome do servidor, use o mesmo nome de máquina da instância original do Servidor de Backup do Azure.

8. Ingresse no domínio.

9. Instale o servidor de Backup do Azure v2. (Remova os discos do pool de armazenamento do Data Protection Manager do servidor antigo e importá-los para o novo servidor.)

10. Restaure o banco de dados do Data Protection Manager que você criou na etapa 4.

11. Anexe o armazenamento do servidor de backup original ao novo servidor.

12. No SQL Server, restaure o banco de dados do Data Protection Manager.

13. Na linha de comando do administrador no novo servidor, use `cd` para ir para o local de instalação e a pasta bin da instalação do Microsoft Azure Backup.  

  Exemplo:  
  C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\ to Azure Backup

14. Execute `DPMSYNC -SYNC`.
  
  > [!NOTE]
  > Se você adicionou *novos* discos ao pool de armazenamento do Data Protection Manager em vez de movê-los, execute `DPMSYNC -Reallocatereplica`.

## <a name="new-powershell-cmdlets-in-backup-server-v2"></a>Novos cmdlets do PowerShell no servidor de Backup v2

Quando você instala o Servidor de Backup do Azure v2, dois novos cmdlets estão disponíveis: 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Próximas etapas

Saiba como preparar seu servidor, ou começar a proteger uma carga de trabalho:
- [Preparar as cargas de trabalho do Servidor de Backup](backup-azure-microsoft-azure-backup.md)
- [Usar o Servidor de Backup para fazer backup de um Servidor do VMware](backup-azure-backup-server-vmware.md)
- [Usar o Servidor de Backup para fazer backup de SQL Server](backup-azure-sql-mabs.md)
- [Usar o Armazenamento de Backup Moderno com o Servidor de Backup](backup-mabs-add-storage.md)

