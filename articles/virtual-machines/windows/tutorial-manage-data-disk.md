---
title: Tutorial – Gerenciar discos do Azure com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para criar e gerenciar discos do Azure para máquinas virtuais
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ea9d89b7dd94c38b326b83ff1fbf51595d67599a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Tutorial – Gerenciar discos do Azure com o Azure PowerShell

Máquinas virtuais do Azure usam discos para armazenar o sistema operacional de VMs, aplicativos e dados. Ao criar uma VM, é importante escolher um tamanho de disco e a configuração apropriada para a carga de trabalho esperada. Este tutorial aborda a implantação e gerenciamento de discos de VM. Você saberá mais sobre:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="default-azure-disks"></a>Discos padrão do Azure

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente anexados à máquina virtual. 

**Disco do sistema operacional** - Os discos do sistema operacional podem ser dimensionados para até 4 terabyte e hospedar o sistema operacional das máquinas virtuais.  O disco do SO é atribuído à letra de unidade *c:* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. O disco do SO **não deve** hospedar aplicativos nem dados. Para aplicativos e dados, use um disco de dados, que é detalhado posteriormente neste artigo.

**Disco temporário**: discos temporários usam uma unidade de estado sólido localizada no mesmo host do Azure que a VM. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da máquina virtual. Os discos temporários são atribuídos à letra de unidade *d:* por padrão.

### <a name="temporary-disk-sizes"></a>Tamanhos do disco temporário

| type | Tamanhos comuns | Tamanho máximo do disco temporário (GiB) |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A, B e D | 1600 |
| [Computação otimizada](sizes-compute.md) | Série F | 576 |
| [Memória otimizada](sizes-memory.md) | Série D, E, G e M | 6144 |
| [Armazenamento otimizado](sizes-storage.md) | Série L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1440 |
| [Alto desempenho](sizes-hpc.md) | Séries A e H | 2000 |

## <a name="azure-data-disks"></a>Discos de dados do Azure

Os discos de dados extras podem ser adicionados para instalação de aplicativos e armazenamento de dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. Cada disco de dados tem uma capacidade máxima de 4 terabytes. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada vCPU da VM, podem ser anexados dois discos de dados. 

### <a name="max-data-disks-per-vm"></a>Máximo de discos de dados por VM

| type | Tamanhos comuns | Máximo de discos de dados por VM |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A, B e D | 64 |
| [Computação otimizada](sizes-compute.md) | Série F | 64 |
| [Memória otimizada](sizes-memory.md) | Série D, E, G e M | 64 |
| [Armazenamento otimizado](sizes-storage.md) | Série L | 64 |
| [GPU](sizes-gpu.md) | Série N | 64 |
| [Alto desempenho](sizes-hpc.md) | Série A e H | 64 |

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard

Armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Os discos Premium são apresentados em cinco tipos (P10, P20, P30, P40, P50), o tamanho do disco determina o tipo de disco. Na escolha do tamanho de um disco, o valor é arredondado para o próximo tipo. Por exemplo, se o tamanho estiver abaixo de 128 GB o tipo de disco é P10 ou entre 129 GB e 512 GB de disco é P20.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GB | 64 GB | 128 GB | 512 GB | 1.024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| IOPS máxima por disco | 120 | 240 | 500 | 2.300 | 5.000 | 7.500 | 7.500 |
Taxa de transferência por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, 64 discos de dados podem ser anexados à VM Standard_GS5. Se cada um desses discos for dimensionado como um P30, será possível chegar a um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tipos e tamanhos de VM](./sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, crie uma máquina virtual com os seguintes comandos.

Defina o nome de usuário e a senha necessários para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Crie a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -AsJob
```

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os prompts do PowerShell sejam exibidos de volta para você. Você pode exibir os detalhes de trabalhos em segundo plano com o cmdelt `Job`.

Crie a configuração inicial com [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). O exemplo a seguir configura um disco com tamanho de 128 gigabytes.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Crie o disco de dados com o comando [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenha a máquina virtual à qual deseja adicionar o disco de dados com o comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adicione o disco de dados à configuração da máquina virtual com o comando [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Atualize a máquina virtual com o comando [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois que um disco é anexado à máquina virtual, o sistema operacional precisa ser configurado para usar o disco. O exemplo a seguir mostra como configurar manualmente o primeiro disco adicionado à VM. Esse processo também pode ser automatizado usando a [extensão de script personalizado](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma conexão RDP com a máquina virtual. Abra o PowerShell e execute este script.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos de discos da VM como:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados

Vá para o próximo tutorial para saber como automatizar a configuração da máquina virtual.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
