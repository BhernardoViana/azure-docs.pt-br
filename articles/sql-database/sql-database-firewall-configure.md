---
title: Regras de firewall de IP do SQL Data Warehouse e do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como configurar um banco de dados SQL ou um firewall do SQL Data Warehouse com regras de firewall de IP no nível de servidor para gerenciar o acesso e configurar um banco de dados individual ou em pool com regras de firewall de IP no nível de banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568177"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Regras de firewall de IP do SQL Data Warehouse e do Banco de Dados SQL do Azure

O [Banco de Dados SQL](sql-database-technical-overview.md) e o [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) do Microsoft Azure fornecem um serviço de banco de dados relacional para o Azure e para outros aplicativos baseados na Internet. Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.
> [!IMPORTANT]
> Este artigo faz *não* se aplica à **Instância Gerenciada do Banco de Dados SQL do Azure**. Consulte o seguinte artigo sobre [Conectar-se a uma Instância Gerenciada](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regras da rede virtual como alternativas às regras IP

Além das regras IP, o firewall também gerencia *regras da rede virtual*. As regras de rede virtual são baseadas nos pontos de extremidade de serviço de rede virtual. Regras da rede virtual podem ser preferíveis a regras de IP em alguns casos. Para saber mais, consulte [Pontos de extremidade de serviço de rede virtual para o Banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Visão geral

Inicialmente, todo acesso ao SQL Server do Azure está bloqueado pelo firewall do Banco de Dados SQL. Para acessar um servidor de banco de dados, é preciso especificar uma ou mais regras de firewall de IP no nível do servidor que permitam acesso ao seu SQL Server do Azure. Use as regras de firewall de IP para especificar quais intervalos de endereço IP da Internet têm permissão e se os aplicativos do Azure podem tentar se conectar ao seu SQL Server do Azure.

Para conceder acesso seletivo a apenas um dos bancos de dados em seu servidor SQL do Azure, crie uma regra no nível do banco de dados para o banco de dados exigido. Especifique um intervalo de endereços IP na regra de firewall de IP do banco de dados que esteja fora do intervalo de endereços IP especificado na regra de firewall de IP no nível do servidor e verifique se o endereço IP do cliente se enquadra no intervalo especificado na regra no nível de banco de dados.

> [!IMPORTANT]
> O SQL Data Warehouse dá suporte somente às regras de firewall de IP no nível do servidor e não dá suporte às regras de firewall de IP no nível do banco de dados.

As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Azure SQL Server ou Banco de Dados SQL, conforme exibido no diagrama a seguir:

   ![Diagrama descrevendo a configuração de firewall.][1]

- **Regras de firewall de IP no nível de servidor:**

  Essas regras permitem que os clientes acessem todo o Azure SQL Server, ou seja, todos os bancos dentro do mesmo servidor do Banco de Dados SQL. Essas regras são armazenadas no banco de dados **mestre** . As regras de firewall de IP no nível de servidor podem ser configuradas por meio do portal ou usando instruções Transact-SQL. Para criar regras de firewall de IP de nível de servidor usando o portal do Azure ou o PowerShell, você deve ser o proprietário da assinatura ou um colaborador da assinatura. Para criar uma regra de firewall de IP de nível de servidor usando o Transact-SQL, você deve se conectar à instância do Banco de Dados SQL como o logon da entidade de segurança no nível do servidor ou o administrador do Azure Active Directory (o que significa que uma regra de firewall de IP de nível de servidor deve ser criada primeiro por um usuário com permissões do nível do Azure).

- **Regras de firewall de IP no nível de banco de dados:**

  Essas regras permitem que os clientes acessem certos bancos de dados (seguros) dentro do mesmo servidor do Banco de Dados SQL. Você pode criar essas regras para cada banco de dados (incluindo o banco de dados **mestre**), e elas serão armazenadas em bancos de dados individuais. As regras de firewall de IP no nível do banco de dados para bancos de dados mestre e usuário só podem ser criadas e gerenciadas por instruções Transact-SQL, e somente depois da configuração do primeiro firewall no nível do servidor. Se você especificar um intervalo de endereços IP na regra de firewall de IP no nível do banco de dados que está fora do intervalo especificado na regra de firewall de IP no nível do servidor, somente os clientes com endereços IP no intervalo de nível de banco de dados poderão acessar o banco de dados. Você pode ter no máximo 128 regras de firewall de IP no nível do banco de dados para um banco de dados. Para saber mais sobre como configurar regras de firewall de IP no nível do banco de dados, veja o exemplo mais adiante neste artigo e confira [sp_set_database_firewall_rule (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Recomendação

A Microsoft recomenda o uso de regras de firewall de IP no nível do banco de dados sempre que possível, a fim de tornar seu banco de dados mais portátil. Use regras de firewall de IP no nível do servidor para administradores e quando você tem muitos bancos de dados com os mesmos requisitos de acesso e não quer gastar tempo configurando cada um individualmente.

> [!IMPORTANT]
> Banco de Dados SQL do Azure dá suporte a um máximo de 128 regras de firewall de IP.
> [!NOTE]
> Para obter informações sobre bancos de dados portáteis no contexto de continuidade de negócios, confira [Requisitos de autenticação para a recuperação de desastres](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Conectando pela Internet

Quando um computador tenta se conectar ao seu servidor de banco de dados pela Internet, o firewall primeiro verifica o endereço IP de origem da solicitação com base nas regras de firewall de IP no nível de banco de dados para o banco de dados que a conexão está solicitando:

- Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall de IP no nível do banco de dados, a conexão será concedida ao Banco de Dados SQL que contém a regra.
- Se o endereço IP da solicitação não estiver dentro de um dos intervalos especificados nas regras de firewall de IP no nível do banco de dados, ocorrerá a verificação das regras de firewall de IP no nível do servidor. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall de IP no nível do servidor, a conexão será concedida. Regras de firewall de IP de nível de servidor se aplicam a todos os bancos de dados SQL no SQL Server do Azure.  
- Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em uma das regras de firewall de IP no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

> [!NOTE]
> Para acessar o Banco de Dados SQL do Azure de seu computador local, verifique se o firewall em sua rede e computador local permite a comunicação de saída na porta TCP 1433.

### <a name="connecting-from-azure"></a>Conexão pelo Azure

Para permitir que os aplicativos do Azure se conectem ao seu SQL Server do Azure, as conexões do Azure devem estar habilitadas. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se há permissão para conexões do Azure. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que conexões do Azure são permitidas. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor do Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Esta opção configura o firewall para permitir todas as conexões do Azure, incluindo as conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.

## <a name="creating-and-managing-ip-firewall-rules"></a>Criar e gerenciar regras de firewall de IP

A primeira configuração de firewall no nível do servidor pode ser criada usando o [Portal do Azure](https://portal.azure.com/) ou com programação usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [CLI do Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) ou a [API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). As regras de firewall de IP no nível de servidor subsequentes podem ser criadas e gerenciados usando esses métodos e por meio de Transact-SQL.

> [!IMPORTANT]
> As regras de firewall de IP no nível de banco de dados só podem ser criadas e gerenciadas usando com o Transact-SQL.

Para melhorar o desempenho, as regras de firewall de IP de nível de servidor são temporariamente armazenadas em cache no nível do banco de dados. Para atualizar o cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Você pode usar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md) para auditar alterações de firewall no nível do servidor e no nível de banco de dados.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Gerenciar regras de firewall de IP de nível de servidor usando o portal do Azure

Para definir uma regra de firewall de IP no nível do servidor no portal do Azure, você pode acessar a página de Visão geral de seu Banco de Dados SQL do Azure ou a página de Visão geral do seu servidor do Banco de Dados SQL.

> [!TIP]
> Para ver um tutorial, consulte [Criar um banco de dados usando o Portal do Azure](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Na página de visão geral de banco de dados

1. Para definir uma regra de firewall de IP no nível de servidor da página de visão geral do banco de dados, clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem a seguir: A página **Configurações do firewall** do servidor do Banco de Dados SQL é aberta.

      ![regra de firewall de IP do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e clique em **Salvar**. Uma regra de firewall de IP no nível do servidor é criada para seu endereço IP atual.

      ![definir regra de firewall de IP no nível do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Da página de visão geral de servidor

A página de visão geral de seu servidor é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170403.database.windows.net**) e fornece opções para configurações adicionais.

1. Para definir uma regra no nível de servidor na página de visão geral do servidor, clique em **Firewall** no menu esquerdo em Configurações:

2. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e clique em **Salvar**. Uma regra de firewall de IP no nível do servidor é criada para seu endereço IP atual.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Gerenciar regras de firewall de IP usando o Transact-SQL

| Exibição do catálogo ou Procedimento armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Exibe as regras de firewall de IP atuais no nível de servidor |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Cria ou atualiza as regras de firewall de IP no nível de servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove as regras de firewall de IP no nível de servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Banco de dados |Exibe as regras de firewall de IP atuais no nível de banco de dados |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Banco de dados |Cria ou atualiza as regras de firewall de IP no nível de banco de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bancos de dados |Remove as regras de firewall de IP no nível de banco de dados |

Os exemplos a seguir examinam as regras existentes, habilitam um intervalo de endereços IP no servidor Contoso e excluem uma regra de firewall de IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall de IP no nível do servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para excluir uma regra de firewall de IP no nível de servidor, execute o procedimento armazenado sp_delete_firewall_rule. O exemplo a seguir exclui a regra denominada ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Gerenciar regras de firewall de IP de nível de servidor usando o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Retorna as regras de firewall atuais no nível de servidor |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria as regras de firewall no nível de servidor |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall existente no nível de servidor |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall no nível de servidor |

O exemplo a seguir define uma regra de firewall de IP no nível de servidor usando o PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Para exemplos do PowerShell no contexto de um início rápido, confira [Criar banco de dados – PowerShell](sql-database-powershell-samples.md) e [Criar um banco de dados individual e configurar uma regra de firewall de IP no nível do servidor do Banco de Dados SQL usando o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Gerenciar regras de firewall de IP de nível de servidor usando a CLI do Azure

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall de IP do servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall de IP em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra o detalhe de uma regra de firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall de IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Exclui uma regra de firewall IP|

O exemplo a seguir define uma regra de firewall de IP no nível de servidor usando a CLI do Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Para obter exemplos da CLI do Azure no contexto de um Início Rápido, confira [Criar DB – CLI do Azure](sql-database-cli-samples.md) e [Criar um banco de dados individual e configurar uma regra de firewall de IP de Banco de Dados SQL usando a CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Gerenciar regras de firewall de IP de nível de servidor usando a API REST do Azure

| API | Nível | Descrição |
| --- | --- | --- |
| [Listar regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Servidor |Exibe as regras de firewall de IP atuais no nível de servidor |
| [Criar ou Atualizar uma Regra de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Servidor |Cria ou atualiza as regras de firewall de IP no nível de servidor |
| [Excluir regra de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Servidor |Remove as regras de firewall de IP no nível de servidor |
| [Obter regras de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Servidor | Obtém regras de firewall de IP no nível de servidor |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de servidor versus no nível de banco de dados

P. Os usuários de um banco de dados devem ser totalmente isolados de outro banco de dados?
Em caso afirmativo, conceda o acesso usando regras de firewall de IP no nível de banco de dados. Isso evita o uso de regras de firewall de IP no nível de servidor, o que permite o acesso pelo firewall a todos os bancos de dados, reduzindo a profundidade das defesas.

P. Os usuários no endereço IP precisam de acesso a todos os bancos de dados?
Use regras de firewall de IP no nível de servidor para reduzir o número de vezes que você deverá configurar as regras de firewall de IP.

P. A pessoa ou a equipe que configura as regras de firewall de IP tem acesso somente por meio do portal do Azure, do PowerShell ou da API REST?
É necessário usar regras de firewall de IP no nível de servidor. As regras de firewall de IP no nível de banco de dados só podem ser configuradas com o Transact-SQL.  

P. A pessoa ou a equipe que configura as regras de firewall de IP proíbe a permissão de alto nível no nível de banco de dados?
Use regras de firewall de IP no nível de servidor. A configuração de regras de firewall de IP no nível de banco de dados com o Transact-SQL exige, no mínimo, a permissão `CONTROL DATABASE` no nível de banco de dados.  

P. A pessoa ou a equipe que configura ou audita as regras de firewall de IP gerencia centralmente as regras de firewall de IP para vários (talvez centenas) de bancos de dados?
Essa seleção depende de suas necessidades e do ambiente. As regras de firewall de IP no nível de servidor podem ser mais fáceis de serem configuradas, mas o script pode configurar regras no nível de banco de dados. E mesmo se você usar regras de firewall de IP no nível de servidor, poderá precisar auditar as regras de firewall de IP do banco de dados para ver se os usuários com a permissão `CONTROL` no banco de dados criaram regras de firewall de IP no nível de banco de dados.

P. Posso usar uma combinação das regras de firewall de IP no nível de servidor e de banco de dados?
Sim. Alguns usuários, como administradores, podem precisar de regras de firewall de IP no nível de servidor. Outros usuários, como usuários de um aplicativo de banco de dados, podem precisar de regras de firewall de IP no nível de banco de dados.

## <a name="troubleshooting-the-database-firewall"></a>Solucionando problemas do firewall de banco de dados

Considere os seguintes pontos quando o acesso ao serviço de Banco de Dados SQL do Microsoft Azure não se comportar conforme o esperado:

- **Configuração de firewall local:**

  Antes que o computador possa acessar o Banco de Dados SQL do Azure, talvez seja necessário criar uma exceção de firewall em seu computador para a porta TCP 1433. Talvez seja necessário abrir portas adicionais se você estiver criando conexões dentro do limite de nuvem do Azure. Para obter mais informações, consulte a seção **Banco de Dados SQL: Fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Conversão de endereços de rede (NAT):**

  Devido à NAT, o endereço IP usado pelo computador para se conectar ao Banco de Dados SQL do Azure pode ser diferente do endereço IP exibido nas configurações de IP do computador. Para exibir o endereço IP usado pelo computador para se conectar ao Azure, faça logon no Portal e navegue até a guia **Configurar** no servidor que hospeda o seu banco de dados. Na seção **Endereços IP Permitidos**, o **Endereço IP do Cliente Atual** é exibido. Clique em **Adicionar** nos **Endereços IP Permitidos** para permitir que este computador acesse o servidor.

- **As alterações à lista de permissões ainda não entraram em vigor ainda:**

  Pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados SQL do Azure entrem em vigor.

- **O logon não está autorizado ou uma senha incorreta foi usada:**

  Se um logon não tiver permissões no servidor do Banco de Dados SQL, ou se a senha usada estiver incorreta, a conexão com o servidor do Banco de Dados SQL do Azure será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente deve fornecer as credenciais de segurança necessárias. Para saber mais sobre a preparação de logons, consulte [Gerenciando banco de dados, logons e usuários no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

- **Endereço IP dinâmico:**

  Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:
  
  - Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos seus computadores cliente que acessam o servidor do banco de dados SQL do Azure e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall de IP.
  - Obtenha o endereçamento IP estático para os computadores cliente e, em seguida, adicione os endereços IP como regras de firewall de IP.

## <a name="next-steps"></a>Próximas etapas

- Confirme se o seu ambiente de rede corporativa permite a comunicação de entrada dos intervalos de endereços IP de computação (incluindo intervalos SQL) usados pelos data centers Microsoft Azure. Pode ser necessário colocar esses endereços IP na lista de permissões, ver [Microsoft Azure intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) do datacenter  
- Para um início rápido sobre como criar uma regra de firewall de IP no nível do servidor, veja [Criar um Banco de Dados SQL do Azure](sql-database-single-database-get-started.md).
- Para obter ajuda com a conexão de um Banco de Dados SQL do Azure a partir de aplicativos de fonte aberta ou de terceiros, consulte [Exemplos de código do cliente de início rápido para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obter informações sobre as portas adicionais que talvez você precise abrir, consulte a seção **Banco de Dados SQL: Fora versus dentro** de [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para obter uma visão geral de segurança do Banco de Dados SQL do Azure, consulte [Protegendo seu banco de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
