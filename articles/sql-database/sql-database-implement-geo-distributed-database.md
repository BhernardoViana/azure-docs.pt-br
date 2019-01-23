---
title: Implementar uma solução de Banco de Dados SQL do Azure distribuída geograficamente | Microsoft Docs
description: Saiba como configurar o Banco de Dados SQL do Azure e o aplicativo para o failover para um banco de dados replicado e failover de teste.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/03/2018
ms.openlocfilehash: 679a02c760d8b37d94a734bc9b023ed8fe59acad
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198177"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Tutorial: Implementar um banco de dados distribuído geograficamente

Configurar um Banco de Dados SQL do Azure e o aplicativo para o failover para uma região remota e testar um plano de failover. Você aprenderá como:

> [!div class="checklist"]
> - Criar um [grupo de failover](sql-database-auto-failover-group.md)
> - Executar um aplicativo Java para consultar um Banco de Dados SQL do Azure
> - Failover de Teste

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você instalou os seguintes itens:

- [PowerShell do Azure](/powershell/azureps-cmdlets-docs)
- Um banco de dados SQL do Azure. Para criar um, use
  - [Portal](sql-database-get-started-portal.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > O tutorial usa o banco de dados de exemplo *AdventureWorksLT*.

- Java e Maven, confira [Compilar um aplicativo usando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), realce **Java** e selecione seu ambiente; em seguida, siga as etapas.

> [!IMPORTANT]
> Não se esqueça de configurar regras de firewall para usar o endereço IP público do computador em que você está executando as etapas nesse tutorial. As regras de firewall no nível do banco de dados serão replicadas automaticamente para o servidor secundário.
>
> Para obter informações, confira [Criar uma regra de firewall de nível de banco de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou, para determinar o endereço IP usado para a regra de firewall de nível de servidor para seu computador, confira [Criar um firewall de nível de servidor](sql-database-get-started-portal-firewall.md).  

## <a name="create-a-failover-group"></a>Criar um grupo de failover

Usando o Azure PowerShell, crie [grupos de failover](sql-database-auto-failover-group.md) entre um SQL Server do Azure existente e um SQL Server do Azure em outra região. Em seguida, adicione o banco de dados ao grupo de failover.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Para criar um grupo de failover, execute o seguinte script:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzureRMSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzureRmSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzureRmSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

As configurações de replicação geográfica também podem ser alteradas no portal do Azure selecionando seu banco de dados, em seguida **Configurações** > **Replicação geográfica**.

![Configurações da replicação geográfica](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Executar o projeto de exemplo

1. No console, crie um projeto do Maven com o seguinte comando:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Digite **Y** e pressione **Enter**.

1. Altere os diretórios para o novo projeto.

   ```bash
   cd SqlDbSample
   ```

1. Usando seu editor favorito, abra o arquivo *pom.xml* na pasta do projeto.

1. Adicione a dependência do Microsoft JDBC Driver para SQL Server adicionando a seção `dependency` a seguir. A dependência deve ser colada dentro da seção `dependencies` maior.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Especifique a versão de Java adicionando a seção `properties` após a seção `dependencies`:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Dê suporte a arquivos de manifesto adicionando a seção `build` após a seção `properties`:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Salve e feche o arquivo *pom.xml*.

1. Abra o arquivo *App.java* localizado em ..\SqlDbSample\src\main\java\com\sqldbsamples e substitua o conteúdo pelo código a seguir:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Salve e feche o arquivo *App.java*.

1. No console de comando, execute o seguinte comando:

   ```bash
   mvn package
   ```

1. Inicie o aplicativo que será executado por cerca de 1 hora até ser interrompido manualmente, permitindo que você tenha tempo para executar o teste de failover.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Failover de Teste

Execute os scripts a seguir para simular um failover e observe os resultados do aplicativo. Observe como algumas inserções e seleções falharão durante a migração do banco de dados.

Também é possível verificar a função do servidor de recuperação de desastre durante o teste com o seguinte comando:

   ```powershell
   (Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Para testar um failover:

1. Inicie um failover manual do grupo de failover:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Reverta o grupo de failover de volta para o servidor primário:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um Banco de Dados SQL do Azure e o aplicativo para o failover para uma região remota e testou um plano de failover. Você aprendeu como:

> [!div class="checklist"]
> - Criar um grupo de failover de replicação geográfica
> - Executar um aplicativo Java para consultar um Banco de Dados SQL do Azure
> - Failover de Teste

Avance para o próximo tutorial sobre como migrar usando DMS.

> [!div class="nextstepaction"]
> [Migrar o SQL Server para a instância gerenciada do Banco de Dados SQL do Azure usando DMS](../dms/tutorial-sql-server-to-managed-instance.md)
