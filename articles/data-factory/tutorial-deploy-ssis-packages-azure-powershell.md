---
title: Implantar pacotes do SSIS no Azure com o PowerShell | Microsoft Docs
description: Este artigo explica como implantar pacotes do SSIS no Azure e criar um tempo de execução de integração do Azure-SSIS usando o PowerShell.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: aab864696be7121be049ce4e907b10431a7b63cb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure-with-powershell"></a>Implantar pacotes do SQL Server Integration Services no Azure com o PowerShell
Este tutorial fornece etapas para o provisionamento de um Integration Runtime (IR) do Azure-SSIS no Azure Data Factory. Em seguida, você pode usar o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar pacotes do SSIS (SQL Server Integration Services) para esse tempo de execução no Azure. Neste tutorial, você executa as seguintes etapas:

> [!NOTE]
> Este artigo usa o Azure PowerShell para provisionar um IR do Azure SSIS. Para usar a interface do usuário (IU) do Data Factory para provisionar um IR do Azure SSIS, consulte [Tutorial: criar um tempo de execução de integração do Azure SSIS](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um Integration Runtime do Azure-SSIS
> * Iniciar o Integration Runtime do Azure-SSIS
> * Implantar pacotes do SSIS
> * Examinar o script completo

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).



## <a name="prerequisites"></a>pré-requisitos
- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter informações conceituais sobre o IR do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Servidor de banco de dados SQL do Azure**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. Este servidor hospeda o SSISDB (banco de dados do Catálogo do SSIS). É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução SSISDB sem cruzar regiões do Azure. 
    - Confirme se a configuração “**Permitir acesso aos serviços do Azure**” está **ATIVADA** para o servidor do banco de dados. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar essa configuração usando o PowerShell, veja [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md). 
    - Confirme que o servidor do Banco de Dados SQL não tem um Catálogo SSIS (Banco de dados SSIDB). O provisionamento do IR do Azure-SSIS não oferece suporte ao uso de um Catálogo do SSIS existente. 
- **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). Você usa o PowerShell para executar um script para provisionar um Integration Runtime do Azure-SSIS que executa pacotes do SSIS na nuvem. 

> [!NOTE]
> - Você pode criar um data factory da versão 2 nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Sudeste Asiático e Europa Ocidental. 
> - Você pode criar um Azure-SSIS IR nas regiões a seguir: Leste dos EUA, Leste dos EUA 2, EUA Central, Europa Setentrional, Europa Ocidental e Leste da Austrália.

## <a name="launch-windows-powershell-ise"></a>Inicialização do ISE do Windows PowerShell
Inicie o **ISE do Windows PowerShell** com privilégios administrativos. 

## <a name="create-variables"></a>Criar variáveis
Copie e cole o script a seguir: especificar valores para as variáveis. Para obter uma lista de **tipos de preços** com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"

# You can create Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, Australia East 
$AzureSSISLocation = "EastUS"
 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>Validar a conexão ao banco de dados
Adicione o seguinte script para validar seu servidor de banco de dados SQL do Azure server.database.windows.net. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

Para criar um banco de dados SQL do Azure como parte do script, veja o exemplo a seguir: 

Defina valores para as variáveis que ainda não foram definidas. Por exemplo: FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>Fazer logon e selecionar a assinatura
Adicione o código a seguir ao script para fazer logon e selecionar sua assinatura do Azure: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

Se seu grupo de recursos já existir, não copie este código ao seu script. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Criar uma data factory
Execute o comando a seguir para criar um data factory:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Criar um Integration Runtime
Execute o comando a seguir para criar um Integration Runtime do Azure-SSIS que executa os pacotes do SSIS no Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>Iniciar o Integration Runtime
Execute o comando a seguir para iniciar o Integration Runtime do Azure-SSIS: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Esse comando o levará de **20 a 30 minutos** para concluir. 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS
Agora, use o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar seus pacotes SSIS no Azure. Conecte-se ao Azure SQL Server que hospeda o SSISDB (catálogo do SSIS). O nome do SQL Server do Azure está no formato: `<servername>.database.windows.net` (para o banco de dados SQL do Azure). 

Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conecte-se ao catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Script completo
Nesta versão, você deve usar o PowerShell para provisionar uma instância do Integration Runtime do Azure-SSIS que execute pacotes do SSIS na nuvem. No momento, não é possível provisionar esse tempo de execução usando o Portal do Azure. 

O script do PowerShell nesta seção configura uma instância de Integration Runtime do Azure-SSIS na nuvem que executa os pacotes do SSIS. Depois de executar esse script com êxito, você pode implantar e executar pacotes do SSIS na nuvem do Microsoft Azure com o SSISDB hospedado no Banco de Dados SQL do Azure.

1. Inicie o ISE (ambiente de script integrado) do Windows PowerShell.
2. No ISE, execute o comando a seguir do prompt de comando.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Copie o script do PowerShell nesta seção e cole-o no ISE.
4. Forneça os valores apropriados para os parâmetros de script na seção "SSIS nas especificações do Azure" no início do script. Esses parâmetros são descritos na próxima seção.
5. Execute o script. O comando `Start-AzureRmDataFactoryV2IntegrationRuntime` próximo ao final do script é executado por **20 a 30 minutos**.

> [!NOTE]
> - O script se conecta ao Banco de Dados SQL do Azure para preparar o SSISDB (banco de dados do catálogo do SSIS). O script também configura permissões e configurações para sua VNet, se especificadas, e ingressa a nova instância de Integration Runtime do Azure-SSIS na VNet.
> - Quando você provisiona uma instância do IR do Azure SSIS, o Azure Feature Pack para SSIS e o Acesso Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel e do Access e para várias fontes de dados do Azure, além das fontes de dados com suporte dos componentes internos. Neste momento, não é possível instalar componentes de terceiros para SSIS (incluindo componentes de terceiros da Microsoft, como os componentes do Oracle e Teradata da Attunity e os componentes SAP BI).


Para obter uma lista de **tipos de preços** com suporte para o Banco de Dados SQL do Azure, veja [Limites de recursos de Banco de Dados SQL](../sql-database/sql-database-resource-limits.md). 

Para obter uma lista de regiões com suporte pelo Azure Data Factory V2 e pelo Integration Runtime do Azure-SSIS, consulte [Products available by region](https://azure.microsoft.com/regions/services/) (Produtos disponíveis por região). Expanda **Dados + Análise** para ver **Data Factory V2** e **Integration Runtime do SSIS**.

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>Unir um IR do Azure-SSIS a uma VNet
Se você usar uma Instância Gerenciada do SQL do Azure (versão prévia) para hospedar o catálogo do SQL Server Integration Services (SSIS) dentro de uma rede virtual (VNet), também deverá associar o tempo de execução de integração do Azure-SSIS à mesma rede virtual. O Azure Data Factory versão 2 (versão prévia) permite que você una o tempo de execução de integração do Azure-SSIS a uma VNet. Para obter mais informações, consulte [Ingressar o tempo de execução do Azure-SSIS em uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).

Veja um script completo para criar um tempo de execução do SSIS do Azure que uma VNet em [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorar e gerenciar o IR do Azure-SSIS
Consulte os artigos a seguir para obter detalhes sobre como monitorar e gerenciar um IR do Azure-SSIS. 

- [Monitorar um tempo de execução de integração do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Gerenciar um tempo de execução de integração do Azure-SSIS](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um Integration Runtime do Azure-SSIS
> * Iniciar o Integration Runtime do Azure-SSIS
> * Implantar pacotes do SSIS
> * Examinar o script completo

Avance para o tutorial a seguir para saber mais sobre a cópia de dados do local para a nuvem: 

> [!div class="nextstepaction"]
>[Copiar dados na nuvem](tutorial-copy-data-dot-net.md)
