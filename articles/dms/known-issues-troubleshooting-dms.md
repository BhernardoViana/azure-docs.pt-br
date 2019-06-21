---
title: Artigo sobre conhecidos problemas/erros comuns associados ao uso do serviço de migração de banco de dados do Azure | Microsoft Docs
description: Saiba mais sobre como solucionar problemas de conhecidos problemas/erros comuns associados ao uso do serviço de migração de banco de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: 1d639a8b1d5c7a5dd2b7bac7c5e020be7c8b1c50
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190954"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Solucionar problemas do serviço de migração de banco de dados do Azure e erros comuns

Este artigo descreve alguns problemas comuns e que os usuários do serviço de migração de banco de dados do Azure podem se deparar com erros. O artigo também inclui informações sobre como resolver esses problemas e erros.

## <a name="migration-activity-in-queued-state"></a>Atividade de migração no estado na fila

Quando você cria novas atividades em um projeto de serviço de migração de banco de dados do Azure, as atividades permanecem no estado na fila.

| Causa         | Resolução |
| ------------- | ------------- |
| Esse problema ocorre quando a instância do serviço de migração de banco de dados do Azure atingiu a capacidade máxima para tarefas contínuas que são executados simultaneamente. Qualquer nova atividade está na fila até que a capacidade esteja disponível. | Valide o serviço de migração de dados de instância tem executando atividades entre projetos. Você pode continuar a criar novas atividades que são automaticamente adicionadas à fila para execução. Assim que concluir a qualquer uma das atividades existentes em execução, a próxima atividade na fila começa a ser executado e o status mudará para o estado de execução automaticamente. Você não precisa realizar nenhuma ação adicional para iniciar a migração da atividade na fila.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Número máximo de bancos de dados selecionados para migração

O seguinte erro ocorre quando a instância gerenciada do criando uma atividade para um projeto de migração de banco de dados para mover para o banco de dados SQL ou um banco de dados do SQL Azure:

* **Erro**: Erro de validação de configurações de migração","errorDetail":"objetos de número máximo de mais de '4' de 'Bancos de dados' foi selecionado para a migração."

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro será exibido quando você selecionou mais de quatro bancos de dados para uma atividade de migração única. No momento, cada atividade de migração é limitada a quatro bancos de dados. | Selecione os bancos de dados de quatro ou menos por atividade de migração. Se você precisar migrar mais de quatro bancos de dados em paralelo, provisione outra instância do serviço de migração de banco de dados do Azure. Atualmente, cada assinatura oferece suporte a até duas instâncias de serviço de migração de banco de dados do Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erros de migração do MySQL para o MySQL do Azure com falhas de recuperação

Quando você migra do MySQL para o banco de dados do Azure para MySQL usando o serviço de migração de banco de dados do Azure, a atividade de migração falhar com o seguinte erro:

* **Erro**: Erro de migração de banco de dados - tarefa 'TaskID' foi suspenso devido a falhas de recuperação sucessivas de [n].

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro pode ocorrer quando o usuário que está fazendo a migração não tem a função ReplicationAdmin e/ou privilégios de cliente de replicação, replicação de réplica e SUPER (versões anteriores ao MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Verifique se o [privilégios pré-requisito](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) para o usuário conta são configuradas com precisão no banco de dados do Azure para a instância do MySQL. Por exemplo, as etapas a seguir podem ser seguidas para criar um usuário chamado migrateuser privilégios necessários:<br>1. Criar usuário migrateuser@'%' identificado por 'secret'; <br>2. Conceda privilégios sobre db_name.* para 'migrateuser'@'%' identificado por 'secret'; Repita essa etapa para conceder acesso de mais bancos de dados <br>3. Subordinado de replicação de concessão em *.* para 'migrateuser'@'%' identificado por 'secret';<br>4. Cliente de replicação de concessão em *.* para 'migrateuser'@'%' identificado por 'secret';<br>5. Privilégios de liberação. |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Erro ao tentar parar o serviço de migração de banco de dados do Azure

Receber erro a seguir ao interromper a instância do serviço de migração de banco de dados do Azure:

* **Erro**: Falha ao parar serviço. Erro: {'error': {'code': 'InvalidRequest', 'da mensagem':' uma ou mais atividades atualmente em execução. Para interromper o serviço, aguarde até que as atividades concluiram ou pare essas atividades manualmente e tente novamente.'}}

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro é exibida quando a instância de serviço que você está tentando parar inclui atividades que ainda estão em execução ou presentes em projetos de migração. <br><br><br><br><br><br> | Certifique-se de que não há nenhuma atividade em execução na instância do serviço de migração de banco de dados do Azure você está tentando parar. Você também pode excluir as atividades ou projetos antes de tentar parar o serviço. As etapas a seguir ilustram como remover projetos para limpar a instância do serviço de migração, excluindo todas as tarefas em execução:<br>1. Install-Module-nome datamigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription - SubscriptionName "<subName>" <br> 4. Remover AzureRmDataMigrationProject-nome <projectName> - ResourceGroupName <rgName> - ServiceName <serviceName> - DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Erro ao tentar iniciar o serviço de migração de banco de dados do Azure

Receber erro a seguir ao iniciar a instância do serviço de migração de banco de dados do Azure:

* **Erro**: Serviço não conseguir iniciar. Erro: {'errorDetail': 'o serviço falhou ao iniciar, entre em contato com o suporte da Microsoft'}

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro será exibido quando a instância anterior falhou internamente. Esse erro ocorre raramente e a equipe de engenharia está ciente dele. <br> | Exclua a instância do serviço que você não pode iniciar e, em seguida, provisionar um novo para substituí-lo. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Erro ao restaurar o banco de dados enquanto a instância gerenciada SQL migrando para o Azure SQL DB

Quando você executa uma migração online do SQL Server para uma instância gerenciada do banco de dados SQL, a substituição falhará com o seguinte erro:

* **Erro**: Operação de restauração falhou para 'operationId' da Id da operação. O código 'AuthorizationFailed', mensagem ' cliente 'clientId' com a id de objeto 'objectId' não tem autorização para executar a ação 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' no escopo ' /subscriptions/ subscriptionId'. '.

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse erro indica que a entidade de segurança de aplicativo que está sendo usada para a migração online do SQL Server para uma instância gerenciada do banco de dados SQL não têm contribui permissão na assinatura. Determinadas chamadas de API com a instância gerenciada no momento exigem essa permissão na assinatura para a operação de restauração. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Use o `Get-AzureADServicePrincipal` cmdlet do PowerShell com `-ObjectId` disponíveis da mensagem de erro para listar o nome de exibição da ID do aplicativo que está sendo usado.<br><br> Validar as permissões para este aplicativo e certifique-se a [função de Colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) no nível da assinatura. <br><br> Azure banco de dados de migração de serviço de equipe de engenharia está trabalhando para restringir a necessário acesso do local atual contribuir com a função na assinatura. Se você tiver um requisito de negócios que não permite o uso de contribuir com a função, entre em contato com o suporte do Azure para obter ajuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Erro ao excluir a NIC associada com o serviço de migração de banco de dados do Azure

Quando você tenta excluir uma placa de Interface de rede associado com o serviço de migração de banco de dados do Azure, a tentativa de exclusão falhar com este erro:

* **Erro**: Não é possível excluir a NIC associada ao serviço de migração de banco de dados do Azure porque o serviço DMS utilizando o NIC

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse problema ocorre quando a instância do serviço de migração de banco de dados do Azure ainda pode estar presente e o consumo da NIC. <br><br><br><br><br><br><br><br> | Para excluir esta NIC, exclua a instância de serviço do DMS que exclui automaticamente a NIC usada pelo serviço.<br><br> **Importante**: Verifique se a instância do serviço de migração de banco de dados do Azure que está sendo excluída não tem nenhuma atividade em execução.<br><br> Depois que todos os projetos e atividades associadas à instância do serviço de migração de banco de dados do Azure são excluídas, você pode excluir a instância de serviço. A NIC usada pela instância de serviço é limpo automaticamente como parte da exclusão do serviço. |

## <a name="connection-error-when-using-expressroute"></a>Erro de Conexão ao usar o ExpressRoute

Quando você tenta se conectar à fonte no Assistente de projeto de serviço de migração de banco de dados do Azure, a conexão falha após tempo limite prolongada se a fonte é usando o ExpressRoute para conectividade.

| Causa         | Resolução    |
| ------------- | ------------- |
| Ao usar [ExpressRoute](https://azure.microsoft.com/services/expressroute/), serviço de migração de banco de dados do Azure [requer](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) três pontos de extremidade de serviço na sub-rede da rede Virtual associada ao serviço de provisionamento:<br> – Ponto de extremidade do barramento de serviço<br> – Ponto de extremidade armazenamento<br> – Direcionar o ponto de extremidade de banco de dados (por exemplo, o ponto de extremidade do SQL, o ponto de extremidade do Cosmos DB)<br><br><br><br><br> | [Habilitar](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) os pontos de extremidade de serviço necessários para a conectividade de ExpressRoute entre a origem e o serviço de migração de banco de dados do Azure. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Erro de tempo limite ao migrar um banco de dados MySQL para o MySQL do Azure

Ao migrar um banco de dados MySQL para um banco de dados do Azure para MySQL por meio do serviço de migração de banco de dados do Azure, a migração falhará com o seguinte erro de tempo limite:

* **Erro**: Erro de migração de banco de dados - Falha ao carregar arquivo - Falha ao iniciar o processo de carregamento de arquivo ' n' RetCode: SQL_ERROR SqlState: HY000 NativeError: Mensagem 1205: [MySQL] [ODBC Driver] [mysqld] espera de bloqueio de tempo limite excedido; Tente reiniciar a transação

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse erro ocorre quando a migração falhar devido a tempo limite de espera de bloqueio durante a migração. | Considere aumentar o valor do parâmetro de servidor **'innodb_lock_wait_timeout'** . O maior valor permitido é 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Erro ao conectar-se ao SQL Server de origem ao usar a porta dinâmica ou instância nomeada

Quando você tenta conectar-se o serviço de migração de banco de dados do Azure para a fonte do SQL Server que é executado na instância nomeada ou uma porta dinâmica, a conexão falhará com este erro:

* **Erro**: -1 - falhado de conexão de SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e que o SQL Server está configurado para permitir conexões remotas. (provedor: Interfaces de rede do SQL, erro: 26 - erro ao localizar servidor/instância especificada)

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse problema ocorre quando a instância do SQL Server de origem que o serviço de migração de banco de dados do Azure tenta se conectar a tem uma porta dinâmica ou está usando uma instância nomeada. O serviço navegador do SQL Server escuta a porta UDP 1434 para conexões de entrada a uma instância nomeada ou ao usar uma porta dinâmica. A porta dinâmica pode mudar cada vez que o serviço do SQL Server é reiniciado. Você pode verificar a porta dinâmica atribuída a uma instância por meio da configuração de rede no SQL Server Configuration Manager.<br><br><br> |Verifique se que o serviço de migração de banco de dados do Azure pode se conectar à fonte de serviço do SQL Server Browser na porta UDP 1434 e a instância do SQL Server por meio da porta TCP dinamicamente atribuída conforme aplicável. |

## <a name="additional-known-issues"></a>Mais problemas conhecidos

* [Limitações de problemas conhecidos/migração com migrações on-line para o banco de dados SQL](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitações de problemas conhecidos/migração com migrações on-line para o banco de dados do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitações de problemas conhecidos/migração com migrações on-line para o banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Próximas etapas

* Ler o artigo [PowerShell de serviço de migração de banco de dados do Azure](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Ler o artigo [como configurar parâmetros do servidor no banco de dados do Azure para MySQL usando o portal do Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Ler o artigo [visão geral de pré-requisitos para usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte a [perguntas Frequentes sobre como usar o serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/faq).
