---
title: Configurar a autenticação do Azure Active Directory
description: Saiba como se conectar ao banco de dados SQL, instância gerenciada e SQL Data Warehouse usando a autenticação Azure Active Directory-depois de configurar o Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 11/06/2019
ms.openlocfilehash: 76ca8a5d781c22279ccad633cc7c5bc98d645df8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901349"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerenciar autenticação do Azure Active Directory com SQL

Este artigo mostra como criar e popular o Azure AD e, em seguida, usar o Azure AD com o [banco de dados SQL](sql-database-technical-overview.md)do Azure, [instância gerenciada](sql-database-managed-instance.md)e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Autenticação do Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

> [!IMPORTANT]  
> Não há suporte para conectar ao SQL Server em execução em uma VM do Azure usando uma conta do Azure Active Directory. Use um conta de domínio do Active Directory.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD

Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado pelo Azure AD inicial. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, consulte [Integrando suas identidades locais no Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [O Microsoft Azure agora dá suporte à federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório do Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Gerenciar o Azure AD usando o Windows PowerShell](/powershell/azure/overview) e [Portas e protocolos necessários para a identidade híbrida](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para mais detalhes, consulte [Como as assinaturas do Azure são associadas ao Microsoft Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Use o seletor de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   > [!IMPORTANT]
   > Cada assinatura do Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais similares a recursos filho de uma assinatura. Se uma assinatura expira, o acesso a esses outros recursos associados à assinatura também pára. Mas o diretório permanece no Azure e você pode associar outra assinatura a ele, além de continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre essa relação de confiança, consulte relação [Como associar ou adicionar uma assinatura do Azure ao Microsoft Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o Azure SQL Server

Cada Servidor do Azure SQL Server (que hospeda um Banco de dados SQL ou SQL Data Warehouse) começa com uma única conta de administrador do servidor, que é o administrador de todo o Azure SQL Server. Um segundo administrador do SQL Server deve ser criado, que é uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db_owner** em todos os bancos de dados de usuários e inserem cada banco de dados de usuário como o usuário **dbo**. Para obter mais informações sobre as contas do administrador do servidor, veja [Gerenciando Bancos de Dados e Logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

Ao usar o Azure Active Directory com a Replicação Geográfica, o administrador do Azure Active Directory deverá ser configurado para os servidores primários e secundários. Se um servidor não tiver um administrador do Azure Active Directory, os usuários e logons do Azure Active Directory obterão um erro de servidor "Não é possível conectar-se".

> [!NOTE]
> Usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD porque eles não têm permissão para validar os usuários do banco de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisionar um administrador de Azure Active Directory para sua instância gerenciada

> [!IMPORTANT]
> Somente siga estas etapas se você estiver Provisionando uma instância gerenciada. Esta operação só pode ser executada pelo administrador global/da empresa ou por um administrador de função com privilégios no Azure AD. As etapas a seguir descrevem o processo de concessão de permissões para usuários com privilégios diferentes no diretório.

> [!NOTE]
> Para administradores do Azure AD para MI criado antes do GA, mas continue operando após a GA, não há nenhuma alteração funcional no comportamento existente. Para obter mais informações, consulte a seção [nova funcionalidade de administrador do Azure ad para mi](#new-azure-ad-admin-functionality-for-mi) para obter mais detalhes.

Sua instância gerenciada precisa de permissões para ler o Azure AD para realizar tarefas com êxito, como a autenticação de usuários por meio da Associação de grupo de segurança ou da criação de novos usuários. Para que isso funcione, você precisa conceder permissões à instância gerenciada para ler o Azure AD. Há duas maneiras de fazer isso: no Portal e no PowerShell. As etapas a seguir mostram os dois métodos.

1. No portal do Azure, no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis.

2. Escolha o Active Directory correto como o AD do Azure padrão.

   Esta etapa vincula a assinatura associada ao Active Directory à Instância Gerenciada, garantindo que a mesma assinatura é usada para o Azure AD e a Instância Gerenciada.

3. Navegue para a Instância Gerenciada e selecione uma que você deseja usar para a integração do Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página de administração do Active Directory e conceda permissão ao usuário atual. Se você estiver conectado como administrador global/da empresa no Azure AD, poderá fazer isso no portal do Azure ou usando o PowerShell com o script abaixo.

    ![grant permissions-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Depois que a operação for concluída com êxito, a seguinte notificação será exibida no canto superior direito:

    ![sucesso](./media/sql-database-aad-authentication/success.png)

6. Agora você pode escolher o administrador do Azure AD para sua instância gerenciada. Para isso, na página de administração do Active Directory, selecione o comando **Definir administrador**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Na página de administração do AAD, pesquise um usuário, selecione o usuário ou grupo para ser um administrador e, em seguida, escolha **Selecionar**.

   A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estejam esmaecidos não poderão ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [Limitações e recursos do Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). O RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para o SQL Server.

    ![Adicionar Azure Active Directory administrador](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Na parte superior da página Administrador do Active Directory, selecione **Salvar**.

    ![Salvar](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador é exibido na caixa de administração do Active Directory.

Depois de provisionar um administrador do Azure AD para sua instância gerenciada, você pode começar a criar entidades de segurança de servidor do Azure AD (logons) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login</a> . Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para remover um Administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nova funcionalidade de administrador do Azure AD para MI

A tabela a seguir resume a funcionalidade do administrador de logon do Azure AD de visualização pública para MI, em comparação com uma nova funcionalidade fornecida com o GA para logons do Azure AD.

| Administrador de logon do Azure AD para MI durante a visualização pública | Funcionalidade de GA para administrador do Azure AD para MI |
| --- | ---|
| Comporta-se de forma semelhante ao administrador do Azure AD para o banco de dados SQL, que habilita a autenticação do Azure AD, mas o administrador do Azure AD não pode criar logons do Azure AD ou SQL no BD mestre para MI. | O administrador do Azure AD tem a permissão sysadmin e pode criar logons do AAD e do SQL no BD mestre para MI. |
| Não está presente na exibição sys. server_principals | Está presente na exibição sys. server_principals |
| Permite que usuários convidados individuais do Azure AD sejam configurados como administrador do Azure AD para MI. Para obter mais informações, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../active-directory/b2b/add-users-administrator.md). | Requer a criação de um grupo do Azure AD com usuários convidados como membros para configurar esse grupo como um administrador do Azure AD para MI. Para obter mais informações, consulte [suporte do Azure ad Business to Business](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Como prática recomendada para os administradores existentes do Azure AD para MI criado antes do GA, e ainda operando após a GA, redefina o administrador do Azure AD usando a opção portal do Azure "remover administrador" e "definir administrador" para o mesmo usuário ou grupo do Azure AD.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Problemas conhecidos com o logon do Azure AD GA para MI

- Se um logon do Azure AD existir no banco de dados mestre para MI, criado usando o comando T-SQL `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, ele não poderá ser configurado como um administrador do Azure AD para MI. Você terá um erro ao definir o logon como um administrador do Azure AD usando os comandos portal do Azure, PowerShell ou CLI para criar o logon do Azure AD.
  - O logon deve ser descartado no banco de dados mestre usando o comando `DROP LOGIN [myaadaccount]`, antes que a conta possa ser criada como um administrador do Azure AD.
  - Configure a conta de administrador do Azure AD no portal do Azure depois que a `DROP LOGIN` for realizada com sucesso. 
  - Se você não puder configurar a conta de administrador do Azure AD, faça check-in do banco de dados mestre da instância gerenciada para o logon. Use o seguinte comando: `SELECT * FROM sys.server_principals`
  - Configurar um administrador do Azure AD para MI criará automaticamente um logon no banco de dados mestre para essa conta. Remover o administrador do Azure AD removerá automaticamente o logon do banco de dados mestre.

- Não há suporte para usuários convidados individuais do Azure AD como administradores do Azure AD para MI. Os usuários convidados devem fazer parte de um grupo do Azure AD para ser configurado como administrador do Azure AD. Atualmente, a folha portal do Azure não esmaece os usuários convidados para outro Azure AD, permitindo que os usuários continuem com a configuração do administrador. Salvar usuários convidados como um administrador do Azure AD fará com que a instalação falhe.
  - Se você quiser tornar um usuário convidado um administrador do Azure AD para MI, inclua o usuário convidado em um grupo do Azure AD e defina esse grupo como um administrador do Azure AD.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para instância gerenciada do SQL

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para instância gerenciada do SQL:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisiona um administrador do Azure AD para instância gerenciada do SQL na assinatura atual. (Deve ser da assinatura atual)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador do Azure AD para instância gerenciada do SQL na assinatura atual. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retorna informações sobre um administrador do Azure AD para instância gerenciada do SQL na assinatura atual.|

O comando a seguir obtém informações sobre um administrador do Azure AD para uma instância gerenciada denominada ManagedInstance01 que está associada a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

O comando a seguir provisiona um grupo de administradores do Azure AD chamado DBAs para a instância gerenciada chamada ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O comando a seguir remove o administrador do Azure AD da instância gerenciada denominada ManagedInstanceName01 associada ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você também pode provisionar um administrador do Azure AD para instância gerenciada do SQL chamando os seguintes comandos da CLI:

| Command | Descrição |
| --- | --- |
|[AZ SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisiona um administrador de Azure Active Directory para instância gerenciada do SQL. (Deve ser da assinatura atual) |
|[AZ SQL mi ad-admin Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Remove um administrador de Azure Active Directory para a instância gerenciada do SQL. |
|[AZ SQL mi ad-lista de administradores](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para instância gerenciada do SQL. |
|[AZ SQL mi-atualização de administração](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Atualiza o administrador de Active Directory para uma instância gerenciada do SQL. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Provisionar um administrador do Azure Active Directory para o servidor do Banco de Dados SQL do Azure

> [!IMPORTANT]
> Apenas execute estas etapas se estiver provisionando um servidor do Banco de Dados SQL do Azure ou um Data Warehouse.

Os dois procedimentos a seguir mostram como provisionar um administrador do Azure Active Directory para seu servidor do Azure SQL Server no Portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione a conexão para exibir uma lista suspensa dos Active Directories possíveis. Escolha o Active Directory correto como o AD do Azure padrão. Esta etapa vincula o Active Directory associado à assinatura ao Azure SQL Server, verificando se a mesma assinatura é usada tanto para o Azure AD quanto para o SQL Server. (O Azure SQL Server pode hospedar o Banco de Dados SQL do Azure ou o Azure SQL Data Warehouse.)

    ![choose-ad][8]

2. Pesquise e selecione **SQL Server**.

    ![Pesquisar e selecionar servidores SQL](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Nessa página, antes você selecionar **SQL servers**, você pode selecionar a **estrela** ao lado do nome para estabelecer a categoria como *favorito* e adicionar **SQL servers**à barra de navegação à esquerda.

3. Na página **SQL Server**, selecione **Administrador do Active Directory**.

4. Na página **Administrador do Active Directory**, selecione **Definir administrador**.

    ![SQL Servers Set Active Directory admin](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Na página **Adicionar administrador**, pesquise um usuário, selecione o usuário ou grupo que será um administrador e, em seguida, selecione **Selecionar**. A página de administração do Active Directory mostra todos os membros e grupos do Active Directory. Usuários ou grupos que estão esmaecidos não podem ser selecionados porque eles não têm suporte como administradores do AD do Azure. (Consulte a lista de administradores com suporte na seção **recursos e limitações do Azure ad** de [usar Azure Active Directory autenticação para autenticação com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication.md).) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para SQL Server.

    ![Selecionar administrador de Azure Active Directory](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Na parte superior da página **Administrador do Active Directory**, selecione **SALVAR**.

    ![salvar administrador](./media/sql-database-aad-authentication/save-admin.png)

O processo de alteração do administrador pode levar vários minutos. O novo administrador aparece na caixa **Administrador do Active Directory** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação do SQL Server. Se presente, a configuração de administração do AD do Azure falhará, revertendo sua criação e indicando que esse administrador (nome) já existe. Como esse usuário de autenticação do SQL Server não é parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um Administrador mais tarde, na parte superior da página **Administrador do Active Directory**, selecione **Remover administrador** e, em seguida, selecione **Salvar**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell para banco de dados SQL do Azure e Azure SQL Data Warehouse

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview). Para provisionar um administrador do AD do Azure, execute os seguintes comandos do Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para o banco de dados SQL do Azure e o Azure SQL Data Warehouse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisiona um administrador do Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. (Deve ser da assinatura atual) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador do Azure Active Directory para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |

Use o comando Get-Help do PowerShell para ver mais informações para cada um desses comandos. Por exemplo, `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

O seguinte script provisiona um grupo de administradores do Azure AD nomeado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo_server** em um grupo de recursos nomeado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do AD do Azure, há suporte apenas para o nome de exibição do AD do Azure.

> [!NOTE]
> O comando ```Set-AzSqlServerActiveDirectoryAdministrator``` do Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O seguinte exemplo usa a **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> A **ObjectID** do Azure AD é necessária quando o **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O seguinte exemplo remove um administrador do AD do Azure:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:

| Command | Descrição |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Provisiona um administrador do Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. (Deve ser da assinatura atual) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Remove um administrador do Azure Active Directory para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retorna informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou para o SQL Data Warehouse do Azure. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Atualiza o administrador do Active Directory para um Azure SQL Server ou SQL Data Warehouse do Azure. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Você também pode provisionar um administrador do Azure Active Directory usando as APIs REST. Para obter mais informações, confira [Referência da API REST de Gerenciamento de Serviços e Operações do Banco de Dados SQL do Azure](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Configurar os computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao Banco de Dados SQL do Azure ou ao SQL Data Warehouse do Azure usando identidades do Azure AD, você deve instalar o software a seguir:

- .NET Framework 4.6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- A Biblioteca de Autenticação do Active Directory do Azure para SQL Server (*ADALSQL.DLL*) está disponível em vários idiomas (tanto x86 quanto amd64) no centro de download da [Biblioteca de Autenticação do Microsoft Active Directory para Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Você pode atender a esses requisitos:

- A instalação do [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou o [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende ao requisito do .NET Framework 4.6.
- O SSMS instala a versão x86 do *ADALSQL.DLL*.
- O SSDT instala a versão amd64 do *ADALSQL.DLL*.
- O Visual Studio mais recente dos [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito do .NET Framework 4.6, mas não instala a versão amd64 necessária do *ADALSQL.DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure

> [!IMPORTANT]
> A instância gerenciada agora dá suporte a entidades de segurança de servidor do Azure AD (logons), que permite que você crie logons de usuários, grupos ou aplicativos do Azure AD. As entidades de segurança do servidor do Azure AD (logons) fornecem a capacidade de autenticar para sua instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente. Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration). Para obter a sintaxe na criação de entidades de segurança do servidor do Azure AD (logons), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

A autenticação do Active Directory do Azure exige que os usuários do banco de dados sejam criados como usuários do banco de dados independente. Um usuário de banco de dados independente com base em uma identidade do AD do Azure é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associada ao banco de dados. A identidade do AD do Azure pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. Funções de RBAC não são propagadas para o SQL Server, para o Banco de Dados SQL ou para o SQL Data Warehouse. As funções RBAC do Azure são usadas para gerenciar Recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **Colaborador do SQL Server** não concede acesso para se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.

> [!WARNING]
> Não há suporte para caracteres especiais, como dois-pontos `:` ou "e" comercial `&` quando incluídos como nomes de usuário nas instruções T-SQL CREATE LOGIN e CREATE USER.

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD como um usuário com, no mínimo, a permissão **ALTER ANY USER** . Em seguida, use a sintaxe Transact-SQL a seguir:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*nome_UPN_Azure_AD* pode ser o nome UPN de um usuário do Azure AD ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que represente um grupo de domínio do AD do Azure AD ou federado, forneça o nome para exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que representa um aplicativo que se conectará usando um token do Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Esse comando requer que o SQL Access do Azure AD (o "provedor externo") em nome do usuário conectado. Às vezes, ocorrerão circunstâncias que fazem com que o Azure AD retorne uma exceção para o SQL. Nesses casos, o usuário verá o erro SQL 33134, que deve conter a mensagem de erro específica do AAD. Na maioria das vezes, o erro dirá que o acesso foi negado ou que o usuário deve se registrar no MFA para acessar o recurso ou que o acesso entre aplicativos primários deve ser tratado por meio de autorização. Nos dois primeiros casos, o problema geralmente é causado pelas políticas de acesso condicional que são definidas no locatário do AAD do usuário: elas impedem que o usuário acesse o provedor externo. Atualizar as políticas de autoridade de certificação para permitir o acesso ao aplicativo ' 00000002-0000-0000-C000-000000000000 ' (a ID do aplicativo do API do Graph do AAD) deve resolver o problema. No caso de o erro dizer que o acesso entre os aplicativos primários deve ser manipulado por meio de autorização, o problema ocorre porque o usuário está conectado como uma entidade de serviço. O comando deverá ter sucesso se for executado por um usuário.

> [!TIP]
> Não é possível criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros Active Directories que são os usuários importados no Active Directory associado (conhecidos como usuários externos) podem ser adicionados a um grupo do Active Directory no Active Directory locatário. Ao criar um usuário de banco de dados independente para esse grupo AD, os usuários do Active Directory externo podem obter acesso ao Banco de Dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Removendo o administrador do Azure Active Directory para o Azure SQL Server impede que qualquer usuário da autenticação do Azure AD se conecte ao servidor. Se for necessário, os usuários do Azure AD inutilizáveis poderão ser removidos manualmente por um administrador do Banco de Dados SQL.

> [!NOTE]
> Se você receber um **Tempo Limite de Conexão Expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como falso. Para obter mais informações, consulte [Problema de tempo limite de conexão com o .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando você cria um usuário de banco de dados, o usuário recebe a permissão **CONNECT** e pode se conectar a esse banco de dados como membro da função **PUBLIC**. Inicialmente, as únicas permissões disponíveis para o usuário são as permissões concedidas à função **PUBLIC** ou as permissões concedidas a quaisquer grupos do Azure AD dos quais esse usuário é membro. A partir do momento que você provisionar um usuário de banco de dados independente baseado no AD do Azure, você pode conceder ao usuário permissões adicionais, do mesmo modo que você concede permissões para qualquer outro tipo de usuário. Normalmente, conceda permissões para funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importado para um domínio gerenciado como um usuário externo deve usar a identidade do domínio gerenciado.

> [!NOTE]
> Usuários do AD do Azure são marcados nos metadados do banco de dados com tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Conectar-se ao banco de dados do usuário ou data warehouse usando SSMS ou SSDT  

Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente com base no Azure AD (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte à autenticação do Azure Active Directory está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e o [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir Autenticação Multifator utilizando uma chamada telefônica, mensagem de texto, os cartões inteligentes com PIN ou a notificação de aplicativo móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando SSMS ou SSDT

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD usando o SQL Server Management Studio ou as Ferramentas de Banco de Dados do SQL Server.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Use este método se você efetuou logon no Windows usando suas credenciais do Azure Active Directory por meio de um domínio federado.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **Conectar ao Servidor** (ou **Conectar ao Mecanismo de Banco de Dados**), na caixa **Autenticação**, selecione **Active Directory - Integrado**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.

    ![Selecione Autenticação Integrada do AD][11]

2. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (A opção **ID de locatário ou o nome de domínio do AD**) tem suporte apenas para opções de **conexão Universal com MFA**, caso contrário, ela fica acinzentada).  

    ![Selecione o nome do banco de dados][13]

## <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use esse método para autenticação no BD SQL/DW com Azure AD para usuários do Azure AS nativo ou federado. Um usuário nativo é explicitamente criado no Azure AD e é autenticado usando nome de usuário e senha, enquanto um usuário federado é um usuário do Windows cujo domínio é federado com o Azure AD. O último método (usando usuário e senha) poderá ser usado quando um usuário quiser usar a credencial do Windows, mas o computador local não estiver associado ao domínio (por exemplo, usando um acesso remoto). Nesse caso, um usuário do Windows poderá indicar a conta de domínio e senha e poderá autenticar-se no BD SQL/DW usando credenciais federadas.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **Conectar ao Servidor** (ou **Conectar ao Mecanismo de Banco de Dados**), na caixa **Autenticação**, selecione **Active Directory - Senha**.

2. Na caixa **nome de usuário** , digite o nome de usuário do Azure Active Directory no formato nome de usuário **\@Domain.com**. Os nomes de usuário devem ser uma conta do Azure Active Directory ou uma conta de um federado de domínio com o Azure Active Directory.

3. Na caixa **Senha** , digite sua senha de usuário para a conta do Azure Active Directory ou conta de domínio federado.

    ![Selecione Autenticação de Senha do AD][12]

4. Selecione o botão **Opções** e, na página **Propriedades de Conexão**, na caixa **Conectar ao banco de dados**, digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Confira o gráfico na opção anterior.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para conectar-se de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um Banco de Dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticação integrada do Active Directory

Para usar a autenticação integrada do Windows, o Active Directory de seu domínio deve ser federado com o Azure Active Directory. Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em uma máquina ingressada no domínio com credenciais de domínio do usuário.

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do AD do Azure, a palavra-chave de Autenticação na cadeia de conexão de banco de dados deve ser definida como Integrada ao Active Directory. O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Para a conexão ao Banco de Dados SQL do Azure, não há suporte para a palavra-chave de cadeia de conexão `Integrated Security=True`. Ao realizar uma conexão ODBC, você precisará remover espaços e configurar a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticação de senha do Active Directory

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de Autenticação deve ser definida como Senha do Active Directory. A cadeia de conexão deve conter valores e palavras-chave de ID/UID de Usuário e Senha/PWD. O exemplo de código em C# a seguir usa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis em [Demonstração do GitHub de Autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD

Este método de autenticação permite que os serviços de camada intermediária se conectem ao Banco de Dados SQL do Azure ou ao SQL Data Warehouse do Azure obtendo um token do AAD (Azure Active Directory). Ele permite cenários sofisticados, incluindo a autenticação baseada em certificado. Você precisa concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registre seu aplicativo com Azure Active Directory e obtenha a ID do cliente para seu código.
2. Criar um usuário de banco de dados que representa o aplicativo. (Concluída anteriormente na etapa 6).
3. Crie um certificado no computador cliente que executa o aplicativo.
4. Adicionar o certificado como uma chave para seu aplicativo.

Exemplo de cadeia de conexão:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, confira [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte [Introdução à autenticação baseada em certificado no Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conexão usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` com o comando `-G` não funciona com as identidades do sistema e requer um logon principal do usuário.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Próximos passos

- Para obter uma visão geral de acesso e controle no Banco de Dados SQL, confira [Acesso e controle de Banco de Dados SQL](sql-database-control-access.md).
- Para obter uma visão geral de logons, usuários e funções de banco de dados no Banco de Dados SQL, confira [Logons, usuários e funções de banco de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre objetos de banco de dados, confira [Entidades](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de banco de dados, confira [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
