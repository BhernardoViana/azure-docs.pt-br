---
title: Solução de problemas de falhas da extensão de VM do Windows | Microsoft Docs
description: Saiba mais sobre como solucionar falhas da extensão de VM do Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 9973eaa7e930d38e78289219e726b5934d82ee86
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33945378"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Solucionando problemas de falhas da extensão da VM do Windows no Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Exibindo o status da extensão
Os modelos do Azure Resource Manager podem ser executados no Azure PowerShell. Depois que o modelo for executado, o status da extensão poderá ser exibido no Gerenciador de Recursos do Azure ou nas ferramentas de linha de comando.

Veja um exemplo:

PowerShell do Azure:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Veja o exemplo de saída:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Solução de problemas de falhas da extensão
### <a name="rerun-the-extension-on-the-vm"></a>Executar novamente a extensão na VM
Se estiver executando scripts na VM usando a Extensão de Script Personalizado, às vezes, você poderá se deparar com um erro em que a VM foi criada com êxito, mas o script falhou. Nessas condições, a maneira recomendada de se recuperar desse erro é remover a extensão e executar o modelo novamente.
Observação: no futuro, essa funcionalidade será aprimorada para eliminar a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-powershell"></a>Remover a extensão do Azure PowerShell
    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Depois que a extensão tiver sido removida, o modelo poderá ser executado novamente para executar os scripts na VM.

