---
title: Provisionar novos locatários em um aplicativo multilocatário que usa o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure
keywords: tutorial do banco de dados SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 21f0bca3a16164ead4e0990842a968fd9b95c33f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Saiba como provisionar novos locatários e registrá-los no catálogo

Neste tutorial, você aprenderá sobre os padrões de provisionamento e catálogo de SaaS, e como eles são implementados no aplicativo Wingtip Tickets SaaS Database per Tenant. Você vai criar e inicializar novos bancos de dados de locatário e os registrará no catálogo de locatários do aplicativo. O catálogo é um banco de dados que mantém o mapeamento entre os vários locatários do aplicativo SaaS e seus dados. O catálogo desempenha uma função importante no direcionamento das solicitações de aplicativo e gerenciamento para o banco de dados correto.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]

> * Provisionar um novo único locatário
> * Provisionar um lote de locatários adicionais


Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo de banco de dados por locatário SaaS Wingtip Tickets é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo de SaaS

Em um aplicativo de SaaS multilocatário com suporte de banco de dados, é importante saber o local em que estão armazenadas as informações de cada locatário. No padrão de catálogo de SaaS, um banco de dados de catálogo é usado para manter o mapeamento entre cada locatário e o banco de dados onde seus dados estão armazenados. Esse padrão se aplica sempre que dados de locatário são distribuídos entre vários bancos de dados.

Cada locatário é identificado por uma chave no catálogo, que é mapeada para o local do seu banco de dados. No aplicativo Wingtip Tickets SaaS, a chave é formada por um hash do nome do locatário. Isso permite que o aplicativo crie a chave do nome de locatário incluído na URL do aplicativo. Outros esquemas de chave de locatário podem ser usados.  

O catálogo permite que o nome ou local do banco de dados seja alterado com impacto mínimo sobre o aplicativo.  Em um modelo de banco de dados multilocatário, isso também acomoda a 'movimentação' de um locatário entre bancos de dados.  O catálogo também pode ser usado para indicar se um locatário ou banco de dados está offline para manutenção ou outras ações. Isso é explorado no [tutorial de restauração de locatário único](saas-dbpertenant-restore-single-tenant.md).

O catálogo também pode armazenar outros metadados de locatário ou de banco de dados, como versão do esquema, plano de serviço ou SLAs oferecidos aos locatários, bem como outras informações que permitem o gerenciamento de aplicativos, o suporte ao cliente ou devops.  

Além do aplicativo SaaS, o catálogo pode habilitar as ferramentas de banco de dados.  No exemplo do banco de dados por locatário SaaS Wingtip Tickets, o catálogo é usado para permitir consultas entre locatários, explorado no [tutorial de análise ad hoc](saas-tenancy-cross-tenant-reporting.md). O gerenciamento de trabalhos entre bancos de dados é explorado nos tutoriais [gerenciamento de esquema](saas-tenancy-schema-management.md) e [análise de locatários](saas-tenancy-tenant-analytics.md). 

Nos exemplos de Wingtip Tickets SaaS, o catálogo é implementado usando os recursos de Gerenciamento de Fragmentos na [EDCL (Biblioteca de Cliente do Banco de Dados Elástico)](sql-database-elastic-database-client-library.md). O EDCL está disponível em Java e em .Net Framework. O EDCL permite que um aplicativo crie, gerencie e use um mapa de fragmentos com backup no banco de dados. Um mapa de fragmentos contém uma lista de fragmentos (bancos de dados) e o mapeamento entre chaves (locatários) e fragmentos.  Funções EDCL são usadas durante o provisionamento para criar as entradas no mapa de fragmentos e, no tempo de execução de locatário, por aplicativos para conexão ao banco de dados correto. O EDCL armazena informações de conexão em cache para minimizar o tráfego ao banco de dados de catálogo e acelerar o aplicativo.  

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis no banco de dados de catálogo, mas *não os edite*! Edite os dados de mapeamento somente com o uso de APIs da Biblioteca de Cliente do Banco de Dados Elástico. Manipular diretamente os dados de mapeamento gera o risco de corrupção do catálogo e não há suporte para isso.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de provisionamento de SaaS

Ao integrar um novo locatário em um aplicativo SaaS que usa um modelo de banco de dados de locatário único, é necessário provisionar um novo banco de dados de locatário.  O banco de dados deve ser criado no local e na camada de serviço apropriados, inicializado com o esquema e os dados de referência apropriados e registrado no catálogo sob a chave de locatário apropriada.  

Abordagens diferentes do provisionamento de banco de dados podem ser usadas, o que inclui a execução de scripts SQL, a implantação de um bacpac ou a cópia de um banco de dados de modelo.  

O provisionamento do banco de dados deve ser parte de sua estratégia de gerenciamento do esquema, que deve fazer com que os novos bancos de dados estejam provisionados com o esquema mais recente. Esse requisito é explorado no [tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md).  

O aplicativo de banco de dados por locatário Wingtip Tickets provisiona novos locatários copiando um banco de dados modelo chamado _basetenantdb_, implantado no servidor de catálogo.  O provisionamento pode ser integrado ao aplicativo como parte de uma experiência de inscrição e/ou com suporte offline por meio do uso de scripts. Este tutorial explora o provisionamento usando o PowerShell. Os scripts de provisionamento copiam o banco de dados _basetenantdb_ para criar um novo banco de dados de locatário em um pool elástico e o inicializam com informações específicas do locatário e o registram no mapa de fragmentos do catálogo.  Os bancos de dados de locatário recebem nomes com base no nome do locatário, mas esse esquema de nomeação não é uma parte crítica do padrão; como o catálogo mapeia a chave de locatário para o nome do banco de dados, pode ser usada qualquer convenção de nomenclatura. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo Wingtip Tickets SaaS Database Per Tenant

O código-fonte do aplicativo e os scripts do SaaS Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Passo a passo detalhado para provisionar e catalogar

Para entender como o aplicativo Wingtip Tickets implementa o novo provisionamento de locatário, adicione um ponto de interrupção e percorra o fluxo de trabalho durante o provisionamento de um locatário:

1. No _PowerShell ISE_, abra ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ e defina estes parâmetros:
   * **$TenantName** = o nome do novo local do evento (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos predefinidos de local: _blues_, classicalmusic, dance, jazz, judo, motor racing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = **1**, para *Provisionar um único locatário*.

1. Adicione um ponto de interrupção, colocando o cursor em qualquer lugar na linha que diz: *New-Tenant `* e pressione **F9**.

   ![ponto de interrupção](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Para executar o script, pressione **F5**.

1. Depois que a execução do script for interrompida no ponto de interrupção, pressione **F11** para entrar no código.

   ![depuração](media/saas-dbpertenant-provision-and-catalog/debug.png)



Rastreie a execução do script usando as opções de menu **Depurar** – **F10** e **F11** para passar por cima ou intervir nas funções chamadas. Para saber mais sobre como depurar scripts do PowerShell, confira [Dicas sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


As etapas a seguir não devem ser explicitamente seguidas porque são uma explicação do fluxo de trabalho pelo qual você passará ao depurar o script:

1. **Importar o módulo CatalogAndDatabaseManagement.psm1** que fornece um catálogo e uma abstração em nível de locatário em relação às funções de [Gerenciamento de Fragmentos](sql-database-elastic-scale-shard-map-management.md). Esse módulo encapsula a maior parte do padrão do catálogo e vale a pena explorar.
1. **Importar o módulo SubscriptionManagement.psm1** que contém funções para entrar no Azure e selecionar a assinatura do Azure com a qual você está trabalhando.
1. **Obter detalhes de configuração**. Intervenha em Get-Configuration (com F11) e veja como a configuração do aplicativo é especificada. Os nomes de recursos e outros valores específicos do aplicativo são definidos aqui, mas não altere esses valores até que você esteja familiarizado com os scripts.
1. **Obter o objeto de catálogo**. Intervenha em Get-Catalog, que compõe e retorna um objeto de catálogo usado no script de nível superior.  Essa função usa funções de Gerenciamento de Fragmentos importadas do **AzureShardManagement.psm1**. O objeto de catálogo é composto pelos seguintes elementos:
   * O $catalogServerFullyQualifiedName é criado usando o tronco padrão além do seu nome de usuário: _catalog-\<user\>.database.windows.net_.
   * O $catalogDatabaseName é recuperado da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado no banco de dados do catálogo.
   * O objeto $shardMap é inicializado por meio do mapa do fragmentos do _tenantcatalog_ no banco de dados de catálogo.
   Um objeto de catálogo é composto e retornado e usado no script de nível superior.
1. **Calcular a nova chave de locatário**. Uma função de hash é usada para criar a chave de locatário com base no nome do locatário.
1. **Verificar se a chave de locatário já existe**. O catálogo é verificado para garantir que a chave está disponível.
1. **O banco de dados do locatário é provisionado com New-TenantDatabase.** Use **F11** para intervir e ver como o banco de dados é provisionado usando um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    O nome do banco de dados é construído com o nome do locatário para deixar claro qual fragmento pertence a qual locatário, embora outras convenções de nomenclatura de banco de dados possam ser usadas.
    Um modelo do Resource Manager cria um banco de dados de locatário com a cópia de um banco de dados modelo _baseTenantDB_ no servidor de catálogo. Como alternativa, você pode criar um banco de dados e inicializá-lo com a importação de um bacpac ou executar um script de inicialização de um local conhecido.

    O modelo do Resource Manager está na pasta ...\Módulos de Aprendizado\Comum\: *tenantdatabasecopytemplate.json*

1. Depois que o banco de dados do locatário é criado, ele é, em seguida, **inicializado com o nome do local (locatário) e o tipo de local**. Outra inicialização também poderia ser feita aqui.

1. O **banco de dados de locatário é registrado no catálogo** com *Add-TenantDatabaseToCatalog* usando a chave do locatário. Use **F11** para ver os detalhes:

    * O banco de dados de catálogo é adicionado ao mapa de fragmentos (a lista de bancos de dados conhecidos).
    * O mapeamento que vincula o valor da chave ao fragmento é criado.
    * Metadados adicionais sobre o locatário (o nome do local) são adicionados à tabela *Locatários* no catálogo.  A tabela Locatários não faz parte do esquema ShardManagement e não é instalada pelo EDCL.  Esta tabela ilustra como o banco de dados Catálogo pode ser estendido para dar suporte a dados específicos de aplicativos adicionais.   


Depois que o provisionamento for concluído, a execução retornará ao script original *Demo-ProvisionAndCatalog*, que abre a página **Eventos** do novo locatário no navegador:

   ![events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote com 17 locatários. É recomendável provisionar esse lote de locatários antes de iniciar os outros tutoriais de Wingtip Tickets SaaS Database per Tenant, de forma que haja mais do que apenas alguns bancos de dados com os quais trabalhar.

1. No *ISE do PowerShell*, abra ...\\Módulos de Aprendizado\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* e altere o parâmetro *$DemoScenario* para 3:
   * **$DemoScenario** = **3** para *Provisionar um lote de locatários*.
1. Pressione **F5** para executar o script.

O script implanta um lote de locatários adicionais. Ele usa um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e delega o provisionamento de cada banco de dados a um modelo vinculado. O uso de modelos dessa maneira permite que Azure Resource Manager seja o agente do processo de provisionamento do seu script. Os modelos provisionam bancos de dados em paralelo e, se necessário, controlam as repetições. O script é idempotente e, portanto, se ele falhar ou parar por qualquer motivo, execute-o novamente.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificar se o lote de locatários foi implantado com êxito

* No [Portal do Azure](https://portal.azure.com), navegue até sua lista de servidores e abra o servidor *tenants1*. Clique em **Bancos de dados SQL** e verifique se o lote de 17 bancos de dados adicionais está na lista:

   ![lista de banco de dados](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de provisionamento

Outros padrões de provisionamento não mencionados nesse tutorial incluem:

**Pré-provisionamento de bancos de dados.** O padrão de pré-provisionamento explora o fato de que os bancos de dados de um pool elástico não adicionam custo extra. A cobrança é pelo pool elástico, não pelos bancos de dados e os bancos de dados ociosos não consomem nenhum recurso. Ao pré-provisionar bancos de dados em um pool e alocá-los quando necessário, o tempo de integração do locatário poderá ser reduzido consideravelmente. O número de bancos de dados pré-provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento esperada.

**Provisionamento automático.** No padrão de provisionamento automático, um serviço de provisionamento provisiona servidores, pools e bancos de dados automaticamente, conforme necessário, incluindo o pré-provisionamento de bancos de dados em pools elásticos, se desejado. E se os bancos de dados forem encerrados e excluídos, as lacunas nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento. Esse serviço poderá ser simples ou complexo – por exemplo, manipulação do provisionamento em várias áreas geográficas e poderá configurar a replicação geográfica para a recuperação de desastre. Com o padrão de provisionamento automático, um script ou aplicativo cliente envia uma solicitação de provisionamento para uma fila para ser processada pelo serviço de provisionamento e, em seguida, pesquisa o serviço para determinar a conclusão. Se o pré-provisionamento fosse usado, as solicitações seriam manipuladas rapidamente com o serviço provisionando um banco de dados de substituição em execução em segundo plano.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]

> * Provisionar um novo único locatário
> * Provisionar um lote de locatários adicionais
> * Intervir nos detalhes do provisionamento de locatários e do registro deles no catálogo

Experimente o [Tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados com base no aplicativo implantação inicial do aplicativo Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md)
* [Como depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
