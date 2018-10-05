---
title: Como redefinir a senha do Linux local em VMs do Azure | Microsoft Docs
description: Apresenta as etapas para redefinir a senha do Linux local na VM do Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 37b02b657278782ea82f3eb129335793a0b5c98d
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47411033"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Como redefinir a senha local do Linux em VMs do Azure

Este artigo apresenta vários métodos para redefinir as senhas de VM (Máquina virtual) local do Linux. Se a conta de usuário expirou, ou se você quiser apenas criar uma nova conta, use os métodos a seguir para criar uma nova conta do administrador local e obter novamente o acesso à VM.

## <a name="symptoms"></a>Sintomas

Você não consegue fazer logon na VM, e recebe uma mensagem indicando que a senha usada está incorreta. Além disso, você não consegue usar o VMAgent para redefinir sua senha no portal do Azure.

## <a name="manual-password-reset-procedure"></a>Procedimento de redefinição de senha manual

1.  Exclua a VM e mantenha os discos conectados.

2.  Anexe a Unidade do sistema operacional como um disco de dados à outra VM temporal no mesmo local.

3.  Execute o seguinte comando SSH na VM temporal para se tornar um superusuário.

    ```bash
    sudo su
    ```

4.  Execute **fdisk -l** ou examine os logs do sistema para localizar o disco recém-anexado. Localize o nome da unidade para montagem. Em seguida, na VM temporal, examine o arquivo de log relevante.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Veja a seguir um exemplo de saída do comando grep :

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Crie um ponto de montagem chamado **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Monte o disco do sistema operacional no ponto de montagem. Normalmente, você precisa montar *sdc1* ou *sdc2*. Isso dependerá da partição de hospedagem no diretório */etc* do disco do computador quebrado.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Crie cópias dos principais arquivos de credencial antes de fazer alterações:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Redefina a senha do usuário necessária:

    ```bash
    passwd <<USER>> 
    ```

9.  Mova os arquivos modificados para o local correto no disco do computador quebrado.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Volte para a raiz e desmonte o disco.

    ```bash
    cd /
    umount /tempmount
    ```

11. Desanexe o disco do portal de gerenciamento.

12. Recrie a VM.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas com VM do Azure anexando o disco de SO para outra VM do Azure](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [CLI do Azure: Como excluir e reimplantar uma VM a partir do VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
