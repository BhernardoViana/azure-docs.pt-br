---
title: Diagnósticos de inicialização para máquinas virtuais Linux no Azure | Microsoft Docs
description: Visão geral dos dois recursos de depuração para máquinas virtuais Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 0183da348a515787d9382df6db3df8524d584d93
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Como usar o diagnóstico de inicialização para solucionar problemas de máquinas virtuais Linux no Azure

O suporte a dois recursos de depuração agora está disponível no Azure: Saída do Console e a Captura de Tela para o modelo de implantação do Resource Manager de Máquinas Virtuais do Azure. 

Ao colocar sua própria imagem no Azure ou iniciar uma das imagens da plataforma, muitas razões podem contribuir para que uma máquina virtual fique em um estado não inicializável. Esses recursos permitem que você diagnostique e recupere as máquinas virtuais de falhas de inicialização facilmente.

No caso de máquinas virtuais Linux, você pode exibir a saída do seu log de console no Portal:

![Portal do Azure](./media/boot-diagnostics/screenshot1.png)
 
No entanto, no caso dde máquinas virtuais Linux e Windows, o Azure também permite que você veja uma captura de tela da VM no hipervisor:

![Erro](./media/boot-diagnostics/screenshot2.png)

Esses dois recursos têm suporte em máquinas virtuais do Azure em todas as regiões. Anotações, capturas de tela e saídas podem levar até 10 minutos para aparecer na sua conta de armazenamento.

## <a name="common-boot-errors"></a>Erros comuns de inicialização

- [Problemas com o sistema de arquivos](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Problemas de kernel](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [Erros de FSTAB](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Habilitar o diagnóstico em uma nova máquina virtual
1. Ao criar uma nova máquina virtual no Portal de Versão Prévia, selecione **Azure Resource Manager** no menu suspenso do modelo de implantação:
 
    ![Gerenciador de Recursos](./media/boot-diagnostics/screenshot3.jpg)

2. Configure a opção de monitoramento para selecionar a conta de armazenamento em que você deseja colocar esses arquivos de diagnóstico.
 
    ![Criar VM](./media/boot-diagnostics/screenshot4.jpg)

3. Se você estiver implantando um modelo do Azure Resource Manager, navegue até seu recurso de máquina virtual e acrescente a seção de diagnóstico do perfil. Lembre-se de usar o cabeçalho de versão de API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. O perfil de diagnóstico permite que você selecione a conta de armazenamento em que deseja colocar esses logs.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

## <a name="update-an-existing-virtual-machine"></a>Atualizar uma máquina virtual existente

Para habilitar o diagnóstico de inicialização no portal, você também pode atualizar uma máquina virtual existente pelo portal. Selecione a opção Diagnóstico de inicialização e salve. Reinicie a VM para que entre em vigor.

![Atualizar VM existente](./media/boot-diagnostics/screenshot5.png)

## <a name="next-steps"></a>Próximas etapas

Se você vir um erro "Falha ao obter o conteúdo do log" ao usar o diagnóstico de inicialização de VM, consulte [Falha ao obter o conteúdo do erro de log no diagnóstico de inicialização da VM](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).