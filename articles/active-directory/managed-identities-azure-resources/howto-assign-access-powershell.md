---
title: Como atribuir um acesso de identidade gerenciada a um recurso do Azure usando o PowerShell
description: Instruções passo a passo para atribuir uma identidade gerenciada em um recurso, acessar outro recurso, usando o PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a001cdafbd608aa2ad06976ec345c612197036d8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189949"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerenciada a um recurso usando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de uma identidade gerenciada da máquina virtual do Azure para uma conta de armazenamento do Azure usando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC para atribuir um acesso de identidade gerenciada a outro recurso

Após habilitar a identidade gerenciada em um recurso do Azure, [como uma VM do Azure](qs-configure-powershell-windows-vm.md):

1. Entrar no Azure usando o cmdlet `Connect-AzAccount`. Use uma conta que está associada à assinatura do Azure na qual você configurou a identidade gerenciada:

   ```powershell
   Connect-AzAccount
   ```
2. Neste exemplo, fornecemos um acesso da VM do Azure para uma conta de armazenamento. Primeiro, usamos [Get-AzVM](/powershell/module/az.compute/get-azvm) para obter a entidade de serviço da VM chamada `myVM`, que foi criada quando habilitamos a identidade gerenciada. Em seguida, usamos [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para fornecer à VM o acesso de **Leitor** em uma conta de armazenamento chamada `myStorageAcct`:

    ```powershell
    $spID = (Get-Az -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Próximas etapas

- [Identidade gerenciada para visão geral dos recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md).
