---
title: Copiar um banco de dados SQL do Azure | Microsoft Docs
description: Crie uma cópia consistente transacionalmente de um banco de dados SQL do Azure existente no mesmo servidor ou em um servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sahsan
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: 4516d61c1bf8a32e807bcbc01ea1e5b283e31060
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762684"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Fazer uma cópia consistente transicionalmente de um banco de dados SQL do Azure

O Banco de Dados SQL do Azure fornece vários métodos para criar uma cópia transacionalmente consistente do Banco de Dados SQL do Azure existente no mesmo servidor ou em um servidor diferente. Você pode copiar um Banco de Dados SQL usando o Portal do Azure, o PowerShell ou o T-SQL. 

## <a name="overview"></a>Visão geral

Uma cópia do banco de dados é um instantâneo do banco de dados de origem no momento da solicitação de cópia. Você pode selecionar o mesmo servidor ou em um servidor diferente. Você pode optar por manter a sua camada de serviço de computação e tamanho ou use um tamanho de computação diferentes na mesma camada de serviço (edição). Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Neste ponto, é possível atualizar ou fazer o downgrade para qualquer edição. Os logons, os usuários e as permissões podem ser gerenciados independentemente.  

> [!NOTE]
> [Backups de banco de dados automatizados](sql-database-automated-backups.md) são usados quando você cria uma cópia de banco de dados.

## <a name="logins-in-the-database-copy"></a>Logons na cópia do banco de dados

Quando você copia um banco de dados no mesmo servidor do Banco de Dados SQL, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.  

Quando você copia um banco de dados para um servidor do Banco de Dados SQL diferente, a entidade de segurança no novo servidor torna-se a proprietária do banco de dados no novo banco de dados. Se você usar [usuários de banco de dados independente](sql-database-manage-logins.md) para acesso aos dados, garanta que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário. Portanto, depois que a cópia for concluída, será possível acessá-la imediatamente com as mesmas credenciais. 

Se você usar o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), será possível acabar completamente com a necessidade de gerenciar credenciais na cópia. No entanto, quando você copia o banco de dados para um novo servidor, o acesso baseado em logon geralmente não funciona, porque os logons não existirão no novo servidor. Para saber como gerenciar logons quando você copia um banco de dados para um servidor do Banco de Dados SQL diferente, confira [How to manage Azure SQL database security after disaster recovery (Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre)](sql-database-geo-replication-security-config.md). 

Depois que a cópia for bem-sucedida e antes que outros usuários sejam remapeados, somente o logon que tiver iniciado a cópia, o proprietário do banco de dados, poderá fazer logon no novo banco de dados. Para resolver logons após a conclusão da operação de cópia, confira [Resolver logons](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Cópia do banco de dados usando o Portal do Azure

Para copiar um banco de dados usando o Portal do Azure, abra a página do banco de dados e clique em **Copiar**. 

   ![Cópia do banco de dados](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Cópia de banco de dados usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para copiar um banco de dados usando o PowerShell, use o [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Para ver um script de exemplo completo, consulte [Copiar um banco de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Cópia de banco de dados usando o Transact-SQL

Faça logon no banco de dados mestre com o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados seja bem-sucedida, os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager. Para saber mais sobre logons e como se conectar ao servidor, confira [Gerenciar logons](sql-database-manage-logins.md).

Inicie a cópia do banco de dados de origem com a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . A execução dessa instrução inicia o processo de cópia do banco de dados. Como a cópia um banco de dados é um processo assíncrono, a instrução CREATE DATABASE retorna antes da conclusão da cópia do banco de dados.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados SQL para o mesmo servidor

Faça logon no banco de dados mestre com o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados seja bem-sucedida, os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager.

Esse comando copia o Banco de dados 1 para um novo banco de dados chamado Database2 (Banco de dados 2) no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente

Faça logon no banco de dados mestre do servidor de destino, o servidor do Banco de Dados SQL em que o novo banco de dados deve ser criado. Use um logon que tenha o mesmo nome e senha do proprietário do banco de dados de origem no servidor do Banco de Dados SQL de origem. O logon no servidor de destino também deve ser membro da função dbmanager ou ser o logon principal no nível de servidor.

Esse comando copia o Database1 no servidor 1- para um novo banco de dados chamado Database2 no servidor 2. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Firewalls de ambos os servidores devem ser configurados para permitir a conexão de entrada do IP do cliente emitir o comando de cópia de T-SQL.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copiar um banco de dados SQL para uma assinatura diferente

Você pode usar o descrbed etapas na seção anterior para copiar seu banco de dados para um servidor de banco de dados SQL em uma assinatura diferente. Verifique se você usar um logon que tem o mesmo nome e senha como o proprietário do banco de dados do banco de dados de origem e é um membro da função dbmanager ou é o logon principal no nível do servidor. 

> [!NOTE]
> O [portal do Azure](https://portal.azure.com) não oferece suporte de cópia para uma assinatura diferente, pois o Portal chama a API do ARM e usa os certificados de assinatura para ambos os servidores envolvidos na replicação geográfica de acesso.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorar o andamento da operação de cópia

Monitore o processo de cópia consultando as exibições sys.databases e sys.dm_database_copies. Enquanto a cópia estiver em andamento, a coluna **state_desc** da exibição sys.databases para o novo banco de dados é definida como **COPYING**.

* Se a cópia falhar, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **SUSPECT**. Execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
* Se a cópia for bem-sucedida, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **ONLINE**. A cópia foi concluída e o novo banco de dados é um banco de dados normal, capaz de ser alterado de forma independente do banco de dados de origem.

> [!NOTE]
> Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) no novo banco de dados. Como alternativa, a execução da instrução DROP DATABASE no banco de dados de origem também cancelará o processo de cópia.

## <a name="resolve-logins"></a>Resolver logons

Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para remapear os usuários do novo banco de dados para logons no servidor de destino. Para resolver usuários órfãos, confira [Solução de problemas de usuários órfãos](https://msdn.microsoft.com/library/ms175475.aspx). Veja também [Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Todos os usuários no novo banco de dados mantêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se tornará o proprietário do novo banco de dados e receberá um novo identificador de segurança (SID). Depois que a cópia for bem-sucedida e antes que outros usuários sejam remapeados, somente o logon que tiver iniciado a cópia, o proprietário do banco de dados, poderá fazer logon no novo banco de dados.

Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor do Banco de Dados SQL diferente, confira [How to manage Azure SQL database security after disaster recovery](sql-database-geo-replication-security-config.md) (Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre).

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre logons, consulte [Gerenciar logons](sql-database-manage-logins.md) e [Como gerenciar a segurança de Banco de Dados SQL do Azure após a recuperação de desastres](sql-database-geo-replication-security-config.md).
* Para exportar um banco de dados, consulte [Exportar o banco de dados para um BACPAC](sql-database-export.md).
