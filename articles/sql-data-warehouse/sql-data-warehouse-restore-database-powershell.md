---
title: Restaurar um Azure SQL Data Warehouse (PowerShell) | Microsoft Docs
description: Tarefas do PowerShell para restaurar um Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9ba1137c3727182fec4e5e90b0d561b055ef756d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243616"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurar um Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Visão geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Neste artigo, você aprenderá como restaurar um Azure SQL Data Warehouse usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar
**Verifique sua capacidade de DTU.**  Cada SQL Data Warehouse é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão.  Antes de restaurar um SQL Data Warehouse, verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Instalar o PowerShell
Para usar o Azure PowerShell com o SQL Data Warehouse, você precisará instalar a versão 1.0 ou superior do Azure PowerShell.  Você pode verificar a versão executando **Get-Module -ListAvailable -Name AzureRM**.  A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Restaurar um banco de dados ativo ou pausado
Para restaurar um banco de dados por meio de um instantâneo, use o cmdlet do PowerShell [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Liste os pontos de restauração do banco de dados.
5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.
6. Restaure o banco de dados para o ponto de restauração desejado.
7. Verifique se o banco de dados restaurado está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo [Configurar o banco de dados após a recuperação][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído
Para restaurar um banco de dados excluído, use o cmdlet [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados excluído a ser restaurado.
4. Obtenha o banco de dados excluído em questão.
5. Restaure o banco de dados excluído.
6. Verifique se o banco de dados restaurado está online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo [Configurar o banco de dados após a recuperação][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Restaurar por meio de uma região geográfica do Azure
Para recuperar um banco de dados, use o cmdlet [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

> [!NOTE]
> Você pode realizar uma restauração geográfica ao nível de desempenho Otimizado para Computação! Para fazer isso, especifique um ServiceObjectiveName Otimizado para Computação como parâmetro opcional. 
>
> 

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados.
6. Verifique o status do banco de dados com restauração geográfica.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar o banco de dados após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação][Configure your database after recovery].
> 
> 

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade dos negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
