---
title: Usar o módulo de Serviço de Migração de Banco de Dados do Azure no Microsoft Azure PowerShell para migrar o SQL Server local para o BD SQL do Azure | Microsoft Docs
description: Saiba como migrar do SQL Server local para o SQL do Azure usando o Azure PowerShell.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: ffa4d5f87a722ed3cb95d873d02707ed1c797dc6
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886601"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrar o SQL Server local para o BD SQL do Azure usando o Azure PowerShell
Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado em uma instância local do SQL Server 2016 ou posterior para um Banco de Dados SQL do Azure usando o Microsoft Azure PowerShell. Migre bancos de dados de uma instância local do SQL Server para o Banco de Dados SQL do Azure usando o módulo `AzureRM.DataMigration` no Microsoft Azure PowerShell.

Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Crie um grupos de recursos.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração em uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir essas etapas, você precisa:

- [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
- Para habilitar o protocolo TCP/IP, que está desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Uma instância do Banco de Dados SQL do Azure. Crie uma instância do Banco de Dados SQL do Azure seguindo os detalhes no artigo [Criar um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Para criar uma VNET usando o modelo de implantação do Azure Resource Manager, que fornece o Serviço de Migração de Banco de Dados do Azure com conectividade site a site para os servidores de origem locais usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Para concluir a avaliação do banco de dados local e migração de esquema usando o Assistente de Migração de Dados, conforme descrito no artigo [ Executar uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Para baixar e instalar o módulo AzureRM.DataMigration da Galeria do PowerShell usando o [cmdlet Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); não se esqueça de abrir a janela de comando do PowerShell usando Executar como administrador.
- Para garantir que as credenciais usadas para conectar a instância do SQL Server de origem tenham a permissão [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Para garantir que as credenciais usadas para conectar A instância do Azure SQL DB de destino tenham a permissão CONTROL DATABASE nos bancos de dados do Banco de Dados SQL do Azure de destino.
- Uma assinatura do Azure. Se não tiver uma, crie uma conta[gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Fazer logon em sua assinatura do Microsoft Azure
Use as instruções do artigo [Fazer logon com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) para fazer logon em sua assinatura do Azure usando o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos antes de criar uma máquina virtual.

Crie um grupo de recursos usando o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1). 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *EastUS*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure 
Crie uma nova instância do Serviço de Migração de Banco de Dados do Azure usando o cmdlet `New-AzureRmDataMigrationService`. Esse cmdlet espera os seguintes parâmetros obrigatórios:
- *Nome do Grupo de Recursos do Azure*. Use o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) para criar o Grupo de recursos do Azure como mostrado anteriormente e forneça seu nome como um parâmetro.
- *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o Serviço de Migração de Banco de Dados do Azure 
- *Local*. Especifica o local do serviço. Especifique um local de data center do Azure, como Oeste dos EUA ou Sudeste Asiático
- *Sku*. Esse parâmetro corresponde ao nome do SKU do DMS. Atualmente, os nomes de SKU com suporte são *Basic_1vCore*, *Basic_2vCores* e *GeneralPurpose_4vCores*
- *Identificador de Sub-rede Virtual*. Use o cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) para criar uma sub-rede. 

O exemplo a seguir cria um serviço nomeado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *Leste dos EUA* com uma rede virtual nomeada *MyVNET* e sub-rede virtual chamada *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois de criar uma instância do Serviço de Migração de Banco de Dados do Azure, crie um projeto de migração. Um projeto do Serviço de Migração de Banco de Dados do Azure exige informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de Informações de Conexão de Banco de Dados para as conexões de origem e destino
Crie um objeto de Informações de Conexão de Banco de Dados usando o cmdlet `New-AzureRmDmsConnInfo`. Esse cmdlet espera os seguintes parâmetros:
- *ServerType*. O tipo de conexão de banco de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e SQL do Azure.
- *DataSource*. O nome ou IP de uma instância do SQL Server ou banco de dados SQL do Azure.
- *AuthType*. O tipo de autenticação da conexão, que pode ser SqlAuthentication ou WindowsAuthentication.
- O parâmetro *TrustServerCertificate* define um valor que indica se o canal é criptografado durante o bypass percorrendo a cadeia de certificados para validar a relação de confiança. O valor pode ser verdadeiro ou falso.

O exemplo a seguir cria o objeto Connection Info para SQL Server de origem chamado MySourceSQLServer usando autenticação sql: 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O próximo exemplo mostra a criação de Informações de Conexão para o servidor de banco de dados SQL do Azure chamado SQLAzureTarget usando a autenticação SQL:

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração
Crie uma lista de objetos `AzureRmDataMigrationDatabaseInfo` que especifica os bancos de dados como parte do projeto da Migração de Banco de Dados do Azure que podem ser fornecidos como parâmetro para a criação do projeto. O cmdlet `New-AzureRmDataMigrationDatabaseInfo` pode ser usado para criar AzureRmDataMigrationDatabaseInfo. 

O exemplo a seguir cria o projeto `AzureRmDataMigrationDatabaseInfo` para o banco de dados **AdventureWorks2016** e adiciona-o à lista a ser fornecida como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto
Por fim, crie o projeto de Migração de Banco de Dados do Azure chamado *MyDMSProject* localizado no *Leste dos EUA* usando `New-AzureRmDataMigrationProject` e adicionando as conexões de origem e destino criadas anteriormente e a lista de bancos de dados a serem migrados.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração
Por fim, crie e inicie a tarefa de Migração de Banco de Dados do Azure. A tarefa de Migração de Banco de Dados do Azure exige informações de credenciais da conexão para a origem e o destino e a lista de tabelas de banco de dados a serem migrados, além das informações já fornecidas com o projeto criado como um pré-requisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credenciais para a origem e o destino
As credenciais de segurança da conexão podem ser criadas como o objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

O exemplo a seguir mostra a criação de objetos *PSCredential* para fornecer para as conexões de origem e destino fornecendo senhas como variáveis de cadeia de caracteres *$sourcePassword* e *$ targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Criar um mapa de tabela e selecionar os parâmetros de origem e destino para migração
Outro parâmetro necessário para a migração é o mapeamento de tabelas da origem para o destino a serem migradas. Crie um dicionário de tabelas que fornece um mapeamento entre as tabelas de origem e destino para migração. O exemplo a seguir ilustra o mapeamento entre o esquema de Recursos Humanos das tabelas de origem e destino do banco de dados AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

A próxima etapa é selecionar os bancos de dados de origem e destino e fornecer o mapeamento de tabela a ser migrado como um parâmetro usando o cmdlet `New-AzureRmDmsSelectedDB`, conforme mostrado no seguinte exemplo:

```powershell
$selectedDbs = New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Use o cmdlet `New-AzureRmDataMigrationTask` para criar e iniciar uma tarefa de migração. Esse cmdlet espera os seguintes parâmetros:
- *TaskType*. O tipo de tarefa de migração a ser criado para o SQL Server para o tipo de migração de Banco de Dados SQL do Azure *MigrateSqlServerSqlDb* é esperado. 
- *Nome do Grupo de Recursos*. Nome do grupo de recursos do Azure no qual a tarefa será criada.
- *ServiceName*. Instância do Serviço de Migração de Banco de Dados do Azure na qual a tarefa será criada.
- *ProjectName*. Nome do projeto do Serviço de Migração de Banco de Dados do Azure no qual criar a tarefa. 
- *TaskName*. Nome da tarefa a ser criada. 
- *SourceConnection*. Objeto AzureRmDmsConnInfo que representa a conexão do SQL Server de origem.
- *TargetConnection*. Objeto AzureRmDmsConnInfo que representa a conexão de Banco de Dados SQL do Azure de destino.
- *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de origem.
- *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de destino.
- *SelectedDatabase*. Objeto AzureRmDataMigrationSelectedDB que representa o mapeamento do banco de dados de origem e destino.

O seguinte exemplo cria e inicia uma tarefa de migração chamada myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Monitorar a migração
Monitore a tarefa de migração em execução consultando a propriedade de estado da tarefa, conforme mostrado no seguinte exemplo:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Próximas etapas
- Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).