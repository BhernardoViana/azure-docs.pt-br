---
title: Expandir discos rígidos virtuais em uma VM Linux no Azure | Microsoft Docs
description: Saiba como expandir discos rígidos virtuais em uma VM Linux com a CLI do Azure 2.0
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: 8b3a4d7feccc3af55415f54473ae1a2588ad5672
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936880"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Como expandir discos rígidos virtuais em uma VM Linux com a CLI do Azure
Normalmente, o tamanho do disco rígido virtual padrão do sistema operacional é de 30 GB em uma VM (máquina virtual) do Linux no Azure. É possível [adicionar discos de dados](add-disk.md) para fornecer espaço de armazenamento adicional, mas você também pode desejar expandir um disco de dados existente. Este artigo fornece detalhes de como expandir discos gerenciados de uma VM Linux com a CLI do Azure 2.0. 

> [!WARNING]
> Certifique-se sempre de fazer backup dos dados antes de realizar operações de redimensionamento do disco. Para saber mais, confira [Fazer backup de máquinas virtuais do Linux no Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Expandir disco gerenciado do Azure
Certifique-se de que você tenha instalado a versão mais recente da [CLI 2.0 do Azure](/cli/azure/install-az-cli2) e entrado em uma conta do Azure usando [az login](/cli/azure/reference-index#az_login).

Este artigo requer uma VM existente no Azure com, pelo menos, um disco de dados anexado e preparado. Caso ainda não tenha uma VM que possa ser usada, confira [Criar e preparar uma VM com discos de dados](tutorial-manage-disks.md#create-and-attach-disks).

Nas amostras a seguir, substitua os nomes de parâmetro de exemplo por seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup* e *myVM*.

1. As operações em discos rígidos virtuais não podem ser executadas com a VM em execução. Desaloque a VM com [az vm deallocate](/cli/azure/vm#az_vm_deallocate). O exemplo a seguir desaloca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A VM deve ser desalocada para que o disco rígido virtual seja expandido. `az vm stop` não libera os recursos de computação. Para liberar os recursos de computação, use `az vm deallocate`.

2. Veja uma lista de discos gerenciados em um grupo de recursos com [az disk list](/cli/azure/disk#az_disk_list). O exemplo a seguir exibe uma lista de discos gerenciados no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda o disco necessário com [az disk update](/cli/azure/disk#az_disk_update). O exemplo a seguir expande o disco gerenciado chamado *myDataDisk* para ter *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Ao expandir um disco gerenciado, o tamanho atualizado é mapeado para o tamanho de disco gerenciado mais próximo. Para obter uma tabela dos tamanhos de disco gerenciado e as camadas disponíveis, consulte [Visão geral do Azure Managed Disks – Preço e cobrança](../windows/managed-disks-overview.md#pricing-and-billing).

3. Inicie a VM com [az vm start](/cli/azure/vm#az_vm_start). O exemplo a seguir inicia a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Expanda a partição de disco e de sistema de arquivos
Para usar o disco expandido, é preciso expandir a partição subjacente e o sistema de arquivos.

1. SSH da VM com as credenciais apropriadas. Você pode obter o endereço IP público da sua VM com [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. Para usar o disco expandido, é preciso expandir a partição subjacente e o sistema de arquivos.

    a. Se já estiver montado, desmonte o disco:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Use `parted` para exibir informações de disco e redimensionar a partição:

    ```bash
    sudo parted /dev/sdc
    ```

    Exiba informações sobre o layout da partição existente com `print`. A saída é semelhante ao exemplo a seguir, que mostra que o disco subjacente tem um tamanho de 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expanda a partição com `resizepart`. Insira o número de partição, *1*, e um tamanho para a nova partição:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para sair, digite `quit`

3. Com a partição redimensionada, verifique a consistência da partição com `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. Agora redimensione o sistema de arquivos com `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. Monte a partição no local desejado, como `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. Para verificar se o disco do sistema operacional foi redimensionado, use `df -h`. A seguinte saída de exemplo mostra que a unidade de dados, */dev/sdc1*, agora tem 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Próximas etapas
Se precisar de armazenamento adicional, também [adicione discos de dados a uma VM do Linux](add-disk.md). Para obter mais informações sobre a criptografia de disco, consulte [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md).
