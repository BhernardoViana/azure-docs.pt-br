---
title: Matriz de suporte para o servidor de Backup do Microsoft Azure e System Center DPM
description: Este artigo resume o suporte de Backup do Azure quando você usa o Servidor de Backup do Microsoft Azure ou o System Center DPM para fazer backup de recursos de VM do Azure e no local.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 0f99042d91738c88a8b673444bb3d4a40602b012
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204180"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com o servidor de Backup do Microsoft Azure ou o System Center DPM

Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e máquinas virtuais (VMs). Este artigo resume as configurações de suporte e limitações para fazer backup de máquinas usando o servidor de Backup do Microsoft Azure (MABS) ou o System Center Data Protection Manager (DPM) e o Backup do Azure.

## <a name="about-dpmmabs"></a>Sobre o MABS/DPM

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gerencia o backup e recuperação de máquinas de empresa e dados. Ele faz parte do conjunto [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) de produtos.

O MABS é um produto de servidor que pode ser usado para fazer backup de servidores físicos locais, VMs e aplicativos executados nelas.

O MABS se baseia no System Center DPM e fornece uma funcionalidade semelhante com algumas diferenças:
- Não é necessária a licença do System Center para executar o MABS.
- Para o MABS e no DPM, o Azure fornece armazenamento de backup de longo prazo. Além disso, o DPM permite fazer backup de dados para armazenamento de longo prazo em fita. O MABS não possui essa funcionalidade.

Baixe o MABS do [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Ele pode ser executado no local ou em uma VM do Azure.

O DPM e o MABS são compatíveis com backup de uma ampla variedade de aplicativos e sistemas operacionais de clientes e servidores. Eles fornecem vários cenários de backup:

- É possível fazer backup no nível do computador com o backup de estado do sistema ou bare-metal.
- É possível fazer backup de arquivos, compartilhamentos, pastas e volumes específicos.
- Você pode fazer backup de aplicativos específicos usando as configurações de reconhecimento de aplicativo otimizadas.

## <a name="dpmmabs-backup"></a>Backup do MABS/DPM

Backup usando o MABS/DPM e o Backup do Azure funciona da seguinte maneira:

1. Agente de proteção do MABS/DPM é instalado em cada máquina que será submetida a backup.
1. As máquinas e aplicativos são feitos backup no armazenamento local no MABS/DPM.
1. O Agente do MARS (Serviços de Recuperação do Microsoft Azure) é instalado no servidor MARS/DPM.
1. O agente do MARS faz backup os discos do MABS/DPM em um cofre de serviços de recuperação de backup no Azure usando o Backup do Azure.

Para mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Examine o que há suporte para](backup-support-matrix-mars-agent.md) para o agente do MARS.

## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Agente** | **Localidade**
--- | --- | ---
**Fazer backup das cargas de trabalho/computadores locais** | Agente de proteção do MABS/DPM é executado nas máquinas que você deseja fazer backup.<br/><br/> O agente do MARS no servidor MABS/DPM. | MABS/DPM deve estar em execução no local.
**Fazer backup de VMs/cargas de trabalho do Azure** | Agente de proteção do MABS/DPM no computador protegido.<br/><br/> O agente do MARS no servidor MABS/DPM. | O MABS/DPM deve ser executado na VM do Azure.

## <a name="supported-deployments"></a>Implantações com suporte

MABS/DPM podem ser implantado como resumido na tabela a seguir.

**Implantação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado localmente** | Servidor físico<br/><br/>VM do Hyper-V<br/><br/> VM do VMware | Se o MABS/DPM estiver instalado como uma VM VMware, ele faz backup apenas VMs VMware e cargas de trabalho que estão em execução nessas VMs.
**Implantado como uma VM do Azure Stack** | Somente no MABS | O DPM não pode ser usado para fazer backup das VMs do Azure Stack.
**Implantado como uma VM do Azure** | Protege as VMs do Azure e cargas de trabalho que estão em execução nessas VMs | MABS/DPM em execução no Azure não pode fazer backup de máquinas locais.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operacionais compatíveis com MABS e DPM

O Backup do Azure pode fazer backup de instâncias MABS/DPM que estão executando qualquer um dos seguintes sistemas operacionais. Os sistemas operacionais devem estar executando os pacotes e as atualizações de serviços mais recentes.

**Cenário** | **DPM/MABS**
--- | ---
**MABS na VM do Azure** | Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Datacenter do Windows de 2019.<br/><br/> É recomendável que você comece com uma imagem do marketplace.<br/><br/> Mínimo A2 Standard com dois núcleos e 3,5 GB de RAM.
**DPM na VM do Azure** | System Center 2012 R2 com atualização 3 ou posterior.<br/><br/> Sistema de operacional Windows conforme [exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> É recomendável que você comece com uma imagem do marketplace.<br/><br/> Mínimo A2 Standard com dois núcleos e 3,5 GB de RAM.
**O MABS no local** | Sistemas operacionais de 64 bits compatíveis:<br/><br/> O MABS v3 e posterior: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> O MABS v2 e posterior: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Todas as versões do MABS:  Windows Server 2012 R2.<br/><br/>Todas as versões do MABS: Windows Storage Server 2012 R2.
**O DPM no local** | Servidor físico/VM do Hyper-V: System Center 2012 SP1 ou posterior.<br/><br/> VM do VMware: System Center 2012 R2 com atualização 5 ou posterior.



## <a name="management-support"></a>Suporte de gerenciamento

**Problema** | **Detalhes**
--- | ---
**Instalação** | Instale o MABS/DPM em um computador de finalidade única.<br/><br/> Não instale o MABS/DPM em um controlador de domínio, em um computador com a instalação da função de servidor de aplicativos, em um computador que está executando o Microsoft Exchange Server ou System Center Operations Manager ou em um nó de cluster.<br/><br/> [Examine todos os requisitos de sistema do DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domínio** | MABS/DPM deve ser associado a um domínio. Instale primeiro e, em seguida, associe o MABS/DPM em um domínio. Não é possível mover o MABS/DPM para um novo domínio após a implantação.
**Armazenamento** | Armazenamento de backup moderno (MBS) tem suporte do DPM 2016 para o MABS v2 e posterior. Ele não está disponível para o MABS v1.
**Atualização do MABS** | Você pode instalar diretamente o MABS v3 ou atualizar o MABS v2 para o MABS v3. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mover o MABS** | Se você estiver usando o MBS, é possível mover o MABS para um novo servidor mantendo o armazenamento.<br/><br/> O servidor deve ter o mesmo nome que o original. Você não pode alterar o nome se quiser manter o mesmo pool de armazenamento e usar o mesmo banco de dados do MABS para armazenar os pontos de recuperação de dados.<br/><br/> Você precisará de um backup do banco de dados MABS pois precisará restaurá-lo.


## <a name="mabs-support-on-azure-stack"></a>Compatibilidade do MABS no Azure Stack

Você pode implantar o MABS em uma VM do Azure Stack para poder gerenciar o backup de VMs do Azure Stack e as cargas de trabalho em um único local.

**Componente** | **Detalhes**
--- | ---
**MABS na VM do Azure Stack** | Pelo menos o tamanho A2. É recomendável que você comece com uma imagem do Windows Server 2012 R2 ou Windows Server 2016 no Azure Marketplace.<br/><br/> Não instale qualquer outra coisa na VM do MABS.
**Armazenamento do MABS** | Use uma conta de armazenamento separada para a VM do MABS. O agente de MARS em execução no MABS precisa de armazenamento temporário para um local de cache e para manter os dados restaurados da nuvem.
**Pool de armazenamento do MABS** | O tamanho do pool de armazenamento do MABS é determinado pelo número e tamanho dos discos anexados à VM do MABS. Cada tamanho de VM do Azure Stack tem um número máximo de discos. Por exemplo, A2 é para quatro discos.
**Retenção do MABS** | Não manter os dados submetidos a backup nos discos locais MABS por mais de cinco dias.
**Escalar verticalmente o MABS** | Para escalar verticalmente sua implantação, você pode aumentar o tamanho da VM do MABS. Por exemplo, você pode alterar de uma série D.<br/><br/> Você também pode garantir que você estiver transferindo dados com backups no Azure regularmente. Se necessário, você pode implantar servidores adicionais do MABS.
**.NET framework no MABS** | A VM MABS precisa 3.3 do .NET Framework SP1 ou posterior instalado nele.
**Domínio do MABS** | A VM do MABS deve ser associada a um domínio. Um usuário de domínio com privilégios de administrador deve instalar o MABS na VM.
**Backup de dados da VM do Azure Stack** | Você pode fazer backup de arquivos, pastas e aplicativos.
**Compatibilidade de backup** | Esses sistemas operacionais têm suporte para VMs que você deseja fazer backup:<br/><br/> Windows Server canal semestral (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte do SQL Server para VMs do Azure Stack** | Fazer backup do SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Fazer backup e recuperar um banco de dados.
**Compatibilidade do SharePoint para VMs do Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Fazer backup e recuperar um farm, o banco de dados, o front-end e o servidor web.
**Requisitos de rede para backups de VMs** | Todas as máquinas virtuais na carga de trabalho do Azure Stack devem pertencer à mesma rede virtual e pertencem à mesma assinatura.

## <a name="dpmmabs-networking-support"></a>Suporte às redes MABS/DPM

### <a name="url-access"></a>Acesso à URL

O servidor MABS/DPM precisa de acesso a estas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade do MABS/DPM no Backup do Azure

É necessária conectividade com o serviço de Backup do Azure para os backups funcionarem corretamente e a assinatura do Azure deve estar ativa. A tabela a seguir mostra o comportamento se essas duas coisas não ocorrerem.

**MABS no Azure** | **Assinatura** | **Backup/restauração**
--- | --- | ---
Conectado | Ativo | Fazer backup em disco do MABS/DPM.<br/><br/> Fazer backup do Azure.<br/><br/> Restaure do disco.<br/><br/> Restaure do Azure.
Conectado | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura tiver expirada, você pode restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não pode restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.
Sem conectividade por mais de 15 dias | Ativo | Nenhum backup em disco ou no Azure.<br/><br/> Você pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura tiver expirada, você pode restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não pode restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.

## <a name="dpmmabs-storage-support"></a>Compatível com armazenamento do MABS/DPM

Os dados cujo backup são feitos para MABS/DPM são armazenados no armazenamento de disco local.

**Armazenamento** | **Detalhes**
--- | ---
**MBS** | Armazenamento de backup moderno (MBS) tem suporte do DPM 2016 para o MABS v2 e posterior. Ele não está disponível para o MABS v1.
**Armazenamento MABS na VM do Azure** | Dados são armazenados em discos do Azure que estão anexados à VM do MABS/DPM e que são gerenciados no MABS/DPM. O número de discos que podem ser usados para o pool de armazenamento do MABS/DPM é limitado pelo tamanho da VM.<br/><br/> VM A2: 4 discos; VM A3: 8 discos; VM A4: 16 discos, com um tamanho máximo de 1 TB para cada disco. Isso determina o pool de armazenamento total de backup que está disponível.<br/><br/> A quantidade de dados que você pode fazer backup depende do número e do tamanho dos discos anexados.
**Retenção de dados do MABS em VM do Azure** | É recomendável que você reter dados de um dia no disco do Azure do MABS/DPM e fazer backup do MABS/DPM no cofre de retenção mais longa. Assim, você pode proteger uma quantidade maior de dados descarregando-os no Backup do Azure.


### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)
Do DPM 2016 para o MABS v2 (em execução no Windows Server 2016) e posterior, você pode tirar proveito do armazenamento de backup moderno (MBS).

- Os backups do MBS são armazenados no disco ReFS (Sistema de Arquivos Resiliente).
- MBS usa a clonagem de backup mais rápido e um uso mais eficiente do espaço de armazenamento de bloco do ReFS.
- Quando você adiciona volumes ao pool de armazenamento local do MABS/DPM, você pode configurá-los com letras de unidade. Em seguida, é possível configurar o armazenamento de carga de trabalho em diferentes volumes.
- Ao criar grupos de proteção para fazer backup dos dados do MABS/DPM, é possível selecionar a unidade que você deseja usar. Por exemplo, você pode armazenar os backups para outra unidade de cargas de trabalho de alto desempenho de IOPS alta ou de SQL e armazenar cargas de trabalho de backup com menos frequência em uma unidade de desempenho inferior.


## <a name="supported-backups-to-mabs"></a>Backups compatíveis com o MABS

A tabela a seguir resume o que pode ser feito backup no MABS das máquinas locais e VMs do Azure.


**Backup** | **Versões** | **MABS** | **Detalhes** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bits) | MABS v3, v2 | No local. | Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação.<br/><br/> Os volumes devem ter pelo menos 1 GB e NTFS. |
**Windows Server 2016 (Datacenter, Standard, não Nano)**<br/><br/> 64/32 bits | MABS v3, v2 | VM local/Azure.| Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2 | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | MABS v3, v2 <br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | Suporte para MABS v3, v2.<br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. | VM local/Azure. |   **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | No MABS v2/v3, o sistema operacional deve estar executando o SP1. | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação. |
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | MABS v2, v3 | O MABS v2, v3 tem suporte quando o MABS é implantado como uma VM VMware.<br/><br/> Não é compatível com o MABS em execução na VM do Azure. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal. |
**Windows Storage Server 2008** | MABS v2, v3 | MABS como local físico/Hyper-V server VM. <br/><br/> Não é compatível com o MABS em execução na VM do Azure. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**SQL Server 2017** | MABS v3 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs. |
**SQL Server 2016/2016 com SP1** | MABS v3, v2 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2 | VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2 | No local. | Fazer backup de servidor do Exchange autônomo, banco de dados em um DAG.<br/><br/> Recupere a caixa de correio, o banco de dados da caixa de correio no DAG.<br/><br/> Não é compatível com ReFS.<br/><br/> Fazer backup de clusters de discos não compartilhados.<br/><br/> Fazer backup do Exchange Server configurado para replicação contínua. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2 | VM local/Azure. | Fazer backup de farm, servidor web front-end.<br/><br/> Recupere farm, banco de dados, aplicativo web, arquivo ou item de lista, pesquisa do SharePoint, servidor web front-end.<br/><br/> Você não pode fazer backup de um farm usando o SQL Server AlwaysOn para os bancos de dados de conteúdo. |
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2008 R2 (com SP1)** | MABS v3, v2 | No local. | **Agente do MABS no host Hyper-V**: Fazer backup de VMs inteiras e de arquivos de dados do host. Fazer backup das VMs com armazenamento local, das VMs em cluster com armazenamento CSV, das VMs com armazenamento de servidor de arquivos SMB.<br/><br/> **Agente do MABS na VM convidada**: Fazer backup de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação em nível de item de VHD/volume/pastas/arquivos.<br/><br/> **VMs Linux**: Fazer backup ao Hyper-V está em execução no Windows Server 2012 R2 e versões posteriores. A recuperação de VMs do Linux é para o computador inteiro. |
**VMs do VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 | No local. | Fazer backup de VMs de VMware no armazenamento CSVs, NFS e SAN.<br/><br/> Recuperar toda a VM.<br/><br/> Backup do Windows/Linux.<br/><br/> Recuperação em nível de item de pasta/arquivos somente para VMs do Windows.<br/><br/> Não é compatível com vApps de VMware.<br/><br/> A recuperação de VMs do Linux é para o computador inteiro. |



## <a name="supported-backups-to-dpm"></a>Backups compatíveis com o DPM

A tabela a seguir resume o que pode ser feito backup no DPM das máquinas locais e VMs do Azure.



**Backup** | **DPM** | **Detalhes**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bits) | Somente no local.<br/><br/> Para fazer backup do Windows 10 com o DPM 2012 R2, é recomendável instalar [atualização 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/compartilhamento/pasta/arquivo.<br/><br/> Compatíveis com volumes com eliminação de duplicação.<br/><br/> Os volumes devem ter pelo menos 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, não Nano)**<br/><br/> 64/32 bits | VM local/Azure.<br/><br/> Somente o DPM 2016.| Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> Compatíveis com volumes com eliminação de duplicação.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Volumes com eliminação de duplicação com suporte com o DPM 2012 R2 e posterior.
**Windows Server 2012 com SP1 (Datacenter e Standard)**<br/><br/> 64/32 bits | VM local/Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.<br/><br/> Volumes com eliminação de duplicação com suporte com o DPM 2012 R2 e posterior.
**Windows 2008 R2 com SP1 (Standard e Enterprise)**<br/><br/> 64/32 bits | VM local/Azure.<br/><br/> O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve ser instalado. |   **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.<br/><br/> **Proteção da VM do Azure**: Volume/compartilhamento/pasta/arquivo.
**Windows 2008 R2 (Standard e Enterprise)**<br/><br/> 64/32 bits | No local.<br/><br/> O DPM não pode ser instalado como uma VM do VMware.<br/><br/> Não é compatível com o DPM em execução na VM do Azure. | **Proteção local**: Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**Windows Server 2008 com SP2**<br/><br/> 64/32 bits | Somente no local.<br/><br/> O DPM é compatível ao executar como uma VM do VMware. Não é compatível ao executar como um servidor físico ou VM do Hyper-V. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**Windows Storage Server 2008** | O DPM no local em execução como um servidor físico ou VM do Hyper-V. | Volume/compartilhamento/pasta/arquivo; estado do sistema/bare metal.
**SQL Server 2017** | O DPM SAC; O DPM 2016 em execução cumulativo até 5 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016 com SP1** | Não é compatível com o DPM 2012 R2; Compatível com o DPM SAC, o DPM 2016 executando o pacote cumulativo de atualizações 4 ou posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2016** | Não é compatível com o DPM 2012 R2. Suporte para o DPM SAC, o DPM 2016 do Update Rollup 2 e posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 com o DPM 2012 R2 com pacote cumulativo de atualizações 4 e posterior.<br/><br/> VM local/Azure.| Fazer backup do banco de dados do SQL Server.<br/><br/> Compatível com o backup de cluster do SQL Server.<br/><br/>Não compatível com bancos de dados armazenados no CSVs.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Para o Exchange 2016, o DPM 2012 R2 precisa do pacote cumulativo de atualizações 9 ou posterior.<br/><br/> Configuração local | Fazer backup de servidor do Exchange autônomo, banco de dados em um DAG.<br/><br/> Recupere a caixa de correio, o banco de dados da caixa de correio no DAG.<br/><br/> Não é compatível com ReFS.<br/><br/> Fazer backup de clusters de discos não compartilhados.<br/><br/> Fazer backup do Exchange Server configurado para replicação contínua.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 no DPM 2016 e posterior.<br/><br/>VM local/Azure. | Fazer backup de farm, servidor web front-end.<br/><br/> Recupere farm, banco de dados, aplicativo web, arquivo ou item de lista, pesquisa do SharePoint, servidor web front-end.<br/><br/> Você não pode fazer backup de um farm usando o SQL Server AlwaysOn para os bancos de dados de conteúdo.
**Hyper-V no Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (com SP1)** | Hyper-V em 2016 tem suportada para o DPM 2016 e posterior.<br/><br/> No local. | **Agente do MABS no host Hyper-V**: Fazer backup de VMs inteiras e de arquivos de dados do host. Fazer backup das VMs com armazenamento local, das VMs em cluster com armazenamento CSV, das VMs com armazenamento de servidor de arquivos SMB.<br/><br/> **Agente do MABS na VM convidada**: Fazer backup de cargas de trabalho em execução na VM. CSVs.<br/><br/> **Recuperação**: VM, recuperação em nível de item de VHD/volume/pastas/arquivos.<br/><br/> **VMs Linux**: Fazer backup ao Hyper-V está em execução no Windows Server 2012 R2 e versões posteriores. A recuperação de VMs do Linux é para o computador inteiro.
**VMs do VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 <br/><br/> O DPM 2012 R2 precisa do System Center com pacote cumulativo de atualizações 1) <br/><br/>No local. | Fazer backup de VMs de VMware no armazenamento CSVs, NFS e SAN.<br/><br/> Recuperar toda a VM.<br/><br/> Backup do Windows/Linux.<br/><br/> Recuperação em nível de item de pasta/arquivos somente para VMs do Windows.<br/><br/> Não é compatível com vApps de VMware.<br/><br/> A recuperação de VMs do Linux é para o computador inteiro.


- Observe que cargas de trabalho clusterizadas backup pelo MABS/DPM devem estar no mesmo domínio que o MABS/DPM ou em um domínio filho/confiável.
- Você pode usar a autenticação NTLM/certificado para fazer backup de dados em grupos de trabalho ou domínios não confiáveis.



## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise](backup-support-matrix-mars-agent.md) o que é compatível com o agente do MARS.
- [Configure](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configurar o DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
