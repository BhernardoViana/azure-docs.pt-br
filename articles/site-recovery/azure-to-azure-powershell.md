---
title: Azure Site Recovery – Configurar e testar a recuperação de desastre para máquinas virtuais do Azure usando o Azure PowerShell | Microsoft Docs
description: Saiba como configurar a recuperação de desastre para máquinas virtuais do Azure com o Azure Site Recovery usando o Azure PowerShell.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: bsiva
ms.openlocfilehash: e12d19612e10885f97d4a8023967fb9ba2d1f8cb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209858"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configurar a recuperação de desastre para máquinas virtuais do Azure usando o Azure PowerShell

Neste artigo, você verá como configurar e testar a recuperação de desastre para máquinas virtuais do Azure usando o Azure PowerShell. 

Você aprenderá como:

> [!div class="checklist"]
> - Crie um cofre dos Serviços de Recuperação.
> - Definir o contexto do cofre para a sessão do PowerShell.
> - Preparar o cofre para iniciar a replicação de máquinas virtuais do Azure.
> - Criar mapeamentos de rede.
> - Criar contas de armazenamento para a qual replicar máquinas virtuais.
> - Replicar máquinas virtuais do Azure para uma região de recuperação para recuperação de desastre.
> - Executar um failover de teste, validar e limpar o failover de teste.
> - Fazer failover para a região de recuperação.

> [!NOTE]
> No momento, o suporte ao PowerShell do Azure Site Recovery para o cenário "*Recuperação de desastre para máquinas virtuais do Azure*" está em versão prévia. Nem todas as funcionalidades de cenário disponíveis por meio do portal podem estar disponíveis por meio do Azure PowerShell. Algumas funcionalidades de cenário sem suporte no momento por meio do Azure PowerShell são:
> - A capacidade de replicar máquinas virtuais do Azure que usam discos gerenciados.
> - A capacidade de especificar que todos os discos em uma máquina virtual devem ser replicados sem a necessidade de especificar explicitamente cada disco da máquina virtual.  

## <a name="prerequisites"></a>pré-requisitos

Antes de começar:
- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Você tem a versão 5.7.0 ou posterior do módulo do AzureRm PowerShell. Se precisar instalar ou atualizar o Azure PowerShell, siga este [Guia para instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Fazer logon em sua assinatura do Microsoft Azure

Faça logon em sua assinatura do Azure usando o cmdlet Connect-AzureRmAccount

```azurepowershell
Connect-AzureRmAccount
```
Selecione sua assinatura do Azure. Use o cmdlet Get-AzureRmSubscription para obter a lista de assinaturas do Azure a que tem acesso. Selecione a assinatura do Azure com a qual deseja trabalhar usando o cmdlet Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obter detalhes das máquinas virtuais a serem replicadas

No exemplo deste artigo, uma máquina virtual na região Leste dos EUA será replicada e recuperada na região Oeste dos EUA 2. A máquina virtual que está sendo replicada é uma máquina virtual com um disco do sistema operacional e um único disco de dados. O nome da máquina virtual usada no exemplo é AzureDemoVM.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obtenha detalhes dos discos da máquina virtual. Os detalhes do disco serão usados posteriormente ao iniciar a replicação da máquina virtual.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Crie um grupo de recursos no qual você criará o cofre dos Serviços de Recuperação. 

> [!IMPORTANT]
> * Um cofre de Serviços de Recuperação e as máquinas virtuais que estão sendo protegidas devem estar em locais diferentes do Azure.
> * O grupo de recursos do cofre de Serviços de Recuperação e as máquinas virtuais que estão sendo protegidas devem estar em locais diferentes do Azure.
> * O cofre de Serviços de Recuperação, e o grupo de recursos ao qual ele pertence, pode estar no mesmo local do Azure.
 
No exemplo deste artigo, a máquina virtual que está sendo protegida está na região Leste dos EUA. A região de recuperação selecionada para recuperação de desastre é a região Oeste dos EUA 2. O cofre de serviços de recuperação, e o grupo de recursos do cofre, estão na região de recuperação (Oeste dos EUA 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```
   
Criar um cofre dos Serviços de Recuperação. No exemplo abaixo, um cofre de Serviços de Recuperação chamado a2aDemoRecoveryVault é criado na região Oeste dos EUA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 
## <a name="set-the-vault-context"></a>Definir o contexto do cofre

> [!TIP]
> O módulo do PowerShell do Azure Site Recovery (módulo AzureRm.RecoveryServices.SiteRecovery) vem com aliases fáceis de usar para a maioria dos cmdlets. Os cmdlets no módulo assumem a forma *\<Operação>-**AzureRmRecoveryServicesAsr**\<Objeto >* e têm aliases equivalentes que assumem a forma *\<Operação>-**ASR**\<Objeto>*. Este artigo usa os aliases de cmdlet para facilitar a leitura.

Defina o contexto do cofre para uso na sessão do PowerShell. Para fazer isso, baixe o arquivo de configurações do cofre e importe o arquivo baixado na sessão do PowerShell para definir o contexto do cofre. 

Após a definição, operações posteriores do Azure Site Recovery na sessão do PowerShell serão executadas no contexto do cofre selecionado. 

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Preparar o cofre para iniciar a replicação de máquinas virtuais do Azure

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Criar um objeto de malha do Site Recovery para representar a região primária (origem)

O objeto de malha no cofre representa uma região do Azure. O objeto de malha principal é o objeto de malha criado para representar a região do Azure à qual as máquinas virtuais que estão sendo protegidas no cofre pertencem. No exemplo deste artigo, a máquina virtual que está sendo protegida está na região Leste dos EUA.

> [!NOTE]
> As operações do Azure Site Recovery Azure são executadas de forma assíncrona. Quando você inicia uma operação, um trabalho do Azure Site Recovery é enviado e um objeto de acompanhamento do trabalho é retornado. Use o objeto de acompanhamento do trabalho para obter o status mais recente do trabalho (Get-ASRJob) e para monitorar o status da operação.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Se máquinas virtuais de várias regiões do Azure estiverem sendo protegidas no mesmo cofre, crie um objeto de malha para cada região do Azure de origem.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. Criar um objeto de malha do Site Recovery para representar a região de recuperação

O objeto de malha de recuperação representa o local do Azure de recuperação. As máquinas virtuais serão replicadas e recuperadas (no caso de um failover) para a região de recuperação representada pela malha de recuperação. A região do Azure de recuperação usada nesse exemplo é Oeste dos EUA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS" 

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Criar um contêiner de proteção do Site Recovery na malha principal

O contêiner de proteção é um contêiner usado para agrupar itens replicados dentro de uma malha.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. Criar um contêiner de proteção do Site Recovery na malha de recuperação

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

####<a name="5-create-a-replication-policy"></a>5. Criar uma política de replicação

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Criar um mapeamento de contêiner de proteção entre o contêiner de proteção principal e de recuperação

Um mapeamento de contêiner de proteção mapeia o contêiner de proteção principal com um contêiner de proteção de recuperação e uma política de replicação. Crie um mapeamento para cada política de replicação que você usará para replicar máquinas virtuais entre um par de contêiner de proteção.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. Criar um mapeamento de contêiner de proteção para failback (replicação inversa após um failover)

Após um failover, quando você estiver pronto para trazer a máquina virtual com failover de volta para a região do Azure original, você realizará o failback. Para realizar o failback, a máquina virtual com failover é replicada de forma inversa da região com failover para a região original. Para replicação inversa das funções da região original e a opção da região de recuperação. A região original agora se torna a nova região de recuperação e o que era originalmente a região de recuperação agora se torna a região principal. O mapeamento de contêiner de proteção para replicação inversa representa as funções comutadas das regiões original e de recuperação.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy 
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Criar contas de armazenamento de cache e contas de armazenamento de destino

Uma conta de armazenamento de cache é uma conta de armazenamento padrão na mesma região do Azure que a máquina virtual que está sendo replicada. A conta de armazenamento de cache é usada para manter as alterações de replicação temporariamente, antes que elas sejam movidas para a região do Azure de recuperação. É possível optar por (mas não é necessário) especificar contas de armazenamento de cache diferentes para diferentes discos de uma máquina virtual.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Para máquinas virtuais que não usam discos gerenciados, a conta de armazenamento de destino é a conta de armazenamento na região de recuperação para a qual os discos da máquina virtual são replicados. A conta de armazenamento de destino pode ser uma conta de armazenamento padrão ou uma conta de armazenamento premium. Selecione o tipo de conta de armazenamento necessário com base na taxa de alteração de dados (taxa de gravação de E/S) para os discos e os limites de variação compatíveis com o Azure Site Recovery para o tipo de armazenamento.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Criar mapeamentos de rede

Um mapeamento de rede mapeia as redes virtuais na região primária para redes virtuais na região de recuperação. O mapeamento de rede especifica a Rede Virtual do Azure na região de recuperação para a qual uma máquina virtual na rede virtual principal deve fazer failover. Uma Rede Virtual do Azure pode ser mapeada para apenas uma única Rede Virtual do Azure em uma região de recuperação.

- Criar uma Rede Virtual do Azure na região de recuperação para a qual fazer failover

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Recuperar a rede virtual primária (a vnet à qual a máquina virtual está conectada)
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to
    
    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Criar mapeamento de rede entre a rede virtual primária e a rede virtual de recuperação
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
    
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    
   ```
- Criar mapeamento de rede para a direção inversa (failback)
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork
    
    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
            sleep 10; 
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }
        
    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replicar a máquina virtual do Azure

Replique a máquina virtual do Azure.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){ 
        sleep 10; 
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Quando a operação de replicação inicial for bem-sucedida, os dados da máquina virtual serão replicados para a região de recuperação. 

O processo de replicação inicia-se propagando inicialmente uma cópia dos discos de replicação da máquina virtual na região de recuperação. Essa fase é chamada de fase de replicação inicial. 

Quando a replicação inicial for concluída, a replicação passará para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida e uma operação de failover de teste pode ser executada nela. O estado de replicação do item replicado que representa a máquina virtual vai para o estado "Protegido" após a conclusão da replicação inicial.

Monitore o estado e a integridade da replicação para a máquina virtual obtendo detalhes do item de replicação protegido correspondente a ele.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Executar um failover de teste, validar e limpar o failover de teste

Quando a replicação da máquina virtual tiver atingido um estado protegido, uma operação de failover de teste poderá ser realizada na máquina virtual (no item de replicação protegido da máquina virtual).

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Execute um failover de teste.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Aguarde a conclusão da operação de failover de teste.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```
Quando o trabalho de failover de teste for concluído com sucesso, será possível conectar-se à máquina virtual com failover de teste e validar o failover de teste.

Quando o teste for concluído na máquina virtual com failover de teste, limpe a cópia de teste iniciando a operação de failover de teste de limpeza. Essa operação exclui a cópia de teste da máquina virtual criada pelo failover de teste.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Failover para o Azure

Faça failover da máquina virtual para um ponto de recuperação específico.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Quando o failover for realizado com sucesso, será possível confirmar a operação de failover.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   : 
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

Após um failover, quando você estiver pronto para voltar para a região original, inicie a replicação inversa para o item de replicação protegido usando o cmdlet Update-AzureRmRecoveryServicesAsrProtectionDirection.

## <a name="next-steps"></a>Próximas etapas
Consulte a [Referência do Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) para saber como você pode executar outras tarefas, como criar planos de recuperação e testar o failover dos planos de recuperação por meio do PowerShell.
