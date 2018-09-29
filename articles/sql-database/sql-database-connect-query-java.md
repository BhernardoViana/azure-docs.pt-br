---
title: Como usar o Java para consultar o banco de dados SQL do Azure| Microsoft Docs
description: Este tópico mostra como usar o Java para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-los usando instruções do Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 89afeb53c6928f2907e22ff740a5d83fddb7781c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062802"
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Como usar o Java para consultar um banco de dados SQL do Azure

Este guia de início rápido demonstra como usar o [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) para conectar um banco de dados SQL do Azure e, em seguida, usar instruções Transact-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa atender aos seguinte pré-requisitos:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md) para o endereço IP público do computador que usou para este início rápido.

- O Java e o software relacionado para seu sistema operacional instalados:

    - **MacOS**: instale o Homebrew e o Java e, em seguida, instale o Maven. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **Ubuntu**: instale o Kit de desenvolvimento Java e instale o Maven. Confira as [etapas 1.2, 1.3 e 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **Windows**: instale o Kit de desenvolvimento Java e o Maven. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Informações de conexão do servidor SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-maven-project-and-dependencies"></a>**Como criar dependências e projeto Maven**
1. A partir do terminal, crie um novo projeto Maven chamado **sqltest**. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Digite **Y** quando solicitado.
3. Altere o diretório para **sqltest** e abra ***pom.xml*** com seu editor de texto preferido.  Adicione o **Microsoft JDBC Driver para SQL Server** às suas dependências do projeto usando o seguinte código:

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.4.0.jre8</version>
   </dependency>
   ```

4. Também no ***pom.xml***, adicione as seguintes propriedades ao seu projeto.  Se não tiver uma seção de propriedades, você pode adicioná-lo depois das dependências.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Salve e feche o ***pom.xml***.

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar o banco de dados SQL

1. Você já deve ter um arquivo chamado ***App.java*** no seu projeto Maven localizado em:  ..\sqltest\src\main\java\com\sqlsamples\App.java

2. Abra o arquivo e substitua o conteúdo dele pelo código a seguir e adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.DriverManager;

   public class App {

    public static void main(String[] args) {
    
        // Connect to database
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
           Connection connection = null;

           try {
                   connection = DriverManager.getConnection(url);
                   String schema = connection.getSchema();
                   System.out.println("Successful connection - Schema: " + schema);

                   System.out.println("Query data example:");
                   System.out.println("=========================================");

                   // Create and execute a SELECT SQL statement.
                   String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                       + "FROM [SalesLT].[ProductCategory] pc "  
                       + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                   try (Statement statement = connection.createStatement();
                       ResultSet resultSet = statement.executeQuery(selectSql)) {

                           // Print results from select statement
                           System.out.println("Top 20 categories:");
                           while (resultSet.next())
                           {
                               System.out.println(resultSet.getString(1) + " "
                                   + resultSet.getString(2));
                           }
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute estes comandos:

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Verifique se as 20 linhas superiores são retornadas e, em seguida, feche a janela do aplicativo.


## <a name="next-steps"></a>Próximas etapas
- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Microsoft JDBC Driver para SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Relatar problemas/fazer perguntas](https://github.com/microsoft/mssql-jdbc/issues)

