---
title: Amostra de Script do Azure PowerShell – fazer backup de uma máquina virtual do Azure | Microsoft Docs
description: Amostra de Script do Azure PowerShell – fazer backup de uma máquina virtual do Azure
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: eb22dc88c971e0ddc293fabd64bfd30145b2edd1
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651377"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Fazer backup de uma máquina virtual do Azure criptografada com o PowerShell

Esse script cria um cofre de Serviços de Recuperação com GRS (armazenamento com redundância geográfica) para uma máquina virtual do Azure criptografada. A política de proteção padrão é aplicada ao cofre. A política gera um backup diário para a máquina virtual e retém cada backup por 30 dias. O script também dispara o ponto de recuperação inicial para a máquina virtual e mantém esse ponto de recuperação por 365 dias. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.


| Comando | Observações | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. | 
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Cria um cofre dos Serviços de Recuperação para armazenar backups. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Define propriedades de armazenamento de backup no cofre dos Serviços de Recuperação. | 
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Cria a política de proteção usando a política de agendamento e a política de retenção no cofre dos Serviços de Recuperação. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder à entidade de serviço o acesso às chaves de criptografia. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Permite o backup de um item com uma política de proteção de Backup especificada. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de Backup existente. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Inicia um backup de um item de Backup do Azure protegido que não está vinculado ao agendamento de backup. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Aguarda até que um trabalho do Backup do Azure seja concluído. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. | 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

