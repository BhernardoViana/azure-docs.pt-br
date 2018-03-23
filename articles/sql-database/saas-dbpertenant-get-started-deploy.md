---
title: Tutorial de SaaS Database-per-tenant – Banco de Dados SQL do Azure | Microsoft Docs
description: Implante e explore o aplicativo Wingtip Tickets SaaS multi-tenant, que demonstra o Database per Tenant e outros padrões de SaaS usando o Banco de Dados SQL do Azure.
keywords: tutorial do banco de dados SQL
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 8aa4c8691093779bd8febee80fca88efad93dc00
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Implantar e explorar um aplicativo SaaS multilocatário que usa o padrão por locatário do banco de dados com o Banco de Dados SQL do Azure

Neste tutorial, você implanta e explora o aplicativo Wingtip Tickets SaaS *Database per Tenant* (Wingtip). O aplicativo usa um padrão de banco de dados por locatário para armazenar os dados de vários locatários. O aplicativo foi projetado para demonstrar os recursos do Banco de dados SQL do Azure que habilitam de forma simples cenários SaaS.

Cinco minutos depois de clicar no botão azul rotulado **Implantar no Azure**, você tem um aplicativo de SaaS multilocatário. O aplicativo inclui um Banco de Dados SQL do Azure em execução na nuvem do Microsoft Azure. O aplicativo é implantado com três locatários de exemplo, cada qual com seu próprio banco de dados. Todos os bancos de dados são implantados em um *pool elástico* do SQL. O aplicativo é implantado em sua assinatura do Azure. Você tem acesso completo para explorar e trabalhar com os componentes individuais do aplicativo. O código-fonte C# do aplicativo e os scripts de gerenciamento estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant do GitHub][github-wingtip-dpt].

Neste tutorial, você aprende:

> [!div class="checklist"]
> - Como implantar o aplicativo SaaS do Wingtip.
> - Onde obter o código-fonte do aplicativo e scripts de gerenciamento.
> - Sobre os servidores, pools e bancos de dados que constituem o aplicativo.
> - Quantos locatários são mapeados para seus dados com o *catálogo*.
> - Como provisionar um novo locatário.
> - Como monitorar a atividade de locatário no aplicativo.

Uma [série de tutoriais relacionados](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferece a exploração de vários padrões de design e gerenciamento de SaaS. Os tutoriais de compilação além desta implantação inicial.
Quando você usa os tutoriais, pode ver como os diferentes padrões de SaaS são implementados examinando os scripts fornecidos.
Os scripts demonstram como recursos de Banco de Dados SQL do Microsoft Azure simplificam o desenvolvimento de aplicativos SaaS.

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Implantar o aplicativo Wingtip Tickets SaaS

#### <a name="plan-the-names"></a>Planejar os nomes

Nas etapas desta seção, você fornece um valor *user* que é usado para garantir que os nomes de recursos fiquem globalmente exclusivos, e um nome para o *grupo de recursos* que contém todos os recursos criados por uma implantação do aplicativo. Para uma pessoa denominada *Aline Faria*, sugerimos:
- *Usuário:*  **af1** *(Suas iniciais, mais um dígito. Use um valor diferente (por exemplo, af2) se você implantar o aplicativo novamente.)*
- *Grupo de recursos:* **wingtip-dpt-af1** *(wingtip-dpt indica que esse é o aplicativo de banco de dados por locatário. A anexação do nome de usuário af1 correlaciona o nome do grupo de recursos com os nomes dos recursos que ele contém.)*

Escolha seus nomes agora e os escreva. 

#### <a name="steps"></a>Etapas

1. Abra o modelo de implantação Wingtip Tickets SaaS Database per Tenant no portal do Azure clicando no botão azul **Implantar no Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. No modelo, insira os valores dos parâmetros necessários:

    > [!IMPORTANT]
    > Algumas autenticações e firewalls de servidor estão intencionalmente desprotegidos para fins de demonstração. É recomendável que você *crie um novo grupo de recursos*. Não use grupos de recursos, servidores ou pools existentes. Não use esse aplicativo, scripts ou qualquer recurso implantado para produção. Exclua esse grupo de recursos quando tiver terminado com o aplicativo para interromper a cobrança relacionada.

    - **Grupo de recursos** – selecione **Criar novo** e forneça o **Nome** exclusivo que você escolheu anteriormente para o grupo de recursos. 
    - **Localização** - selecione uma **Localização** na lista suspensa.
    - **Usuário** – use o valor Nome de usuário que você escolheu anteriormente.

1. Implante o aplicativo.

    - Clique se você concordar com os termos e condições.
    - Clique em **Comprar**.

1. Monitore o status da implantação clicando em **Notificações**, que é o ícone de sino à direita da caixa de pesquisa. A implantação do aplicativo Wingtip Tickets SaaS leva aproximadamente cinco minutos.

   ![implantação bem-sucedida](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Baixar e desbloquear os scripts de gerenciamento de Wingtip Tickets

Enquanto o aplicativo for implantado, baixe os scripts de gerenciamento e de código-fonte.

> [!IMPORTANT]
> Conteúdos executáveis (scripts, DLLs) podem ser bloqueados pelo Windows quando arquivos .zip são baixados de uma fonte externa e extraídos. Ao extrair os scripts de um arquivo zip, siga as etapas abaixo para desbloquear o arquivo. zip antes da extração. O desbloqueio garante que os scripts possam ser executados.

1. Navegue até o [repositório WingtipTicketsSaaS-DbPerTenant do GitHub][github-wingtip-dpt].
2. Clique em **Clonar ou baixar**.
3. Clique em **Fazer o download do ZIP** e salve o arquivo.
4. Clique com o botão direito do mouse no arquivo **WingtipTicketsSaaS-DbPerTenant-master.zip** e selecione **Propriedades**.
5. Na guia **Geral**, selecione **Desbloquear** e clique em **Aplicar**.
6. Clique em **OK**.
7. Extraia os arquivos.

Os scripts estão localizados na pasta *.\\WingtipTicketsSaaS-DbPerTenant-master\\Módulos de Aprendizado*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Atualizar o arquivo de configuração para esta implantação

Antes de executar os scripts, atualize os valores *grupo de recursos* e *usuário* no arquivo *User Config*. Defina essas variáveis como os valores definidos durante a implantação.

1. No **ISE do PowerShell**, abra ...\\Módulos de Aprendizado\\**UserConfig.psm1** 
2. Atualize **ResourceGroupName** e **Nomeie** com os valores específicos para sua implantação (somente nas linhas 10 e 11).
3. Salve as alterações!

Esses valores são referenciados em quase todos os scripts.

## <a name="run-the-application"></a>Executar o aplicativo

O aplicativo apresenta locais que organizam eventos. Os tipos de local são casas de shows, clubes de jazz e de esportes. No Wingtip Tickets, os locais estão registrados como locatários. Ser um locatário oferece ao local uma maneira fácil de listar eventos e vender ingressos para os clientes. Cada local obtém um site personalizado para listar seus eventos e vender ingressos.

Internamente no aplicativo, cada locatário obtém um banco de dados SQL implantado em um pool elástico do SQL.

Uma página central do **Hub de Eventos** fornece uma lista de links para os locatários em sua implantação.

1. Abra o *Hub de Eventos* no seu navegador da Web: http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net (substitua &lt;usuário&gt; pelo seu nome de usuário da implantação):

    ![hub de eventos](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Clique em **Fabrikam Jazz Club** no *Hub de Eventos*.

    ![Eventos](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Gerenciador de Tráfego do Azure

O aplicativo Wingtip usa o [*Gerenciador de Tráfego do Azure*](../traffic-manager/traffic-manager-overview.md) para controlar a distribuição de solicitações de entrada. A URL para acessar a página de eventos para um locatário específico usa o formato a seguir:

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

As partes do formato anterior são explicadas na tabela a seguir.

| Parte da URL        | DESCRIÇÃO       |
| :-------------- | :---------------- |
| http://events.wingtip-dpt | As partes de eventos do aplicativo Wingtip.<br /><br /> *-dpt* distingue a implementação do *banco de dados por locatário* dos tíquetes Wingtip de outras implementações. Por exemplo, o aplicativo por locatário *autônomo* (*-sa*) ou implementações do *banco de dados multilocatário* (*-mt*). |
| .*&lt;usuário&gt;* | *af1* em nosso exemplo. |
| .trafficmanager.net/ | Gerenciador de Tráfego do Azure, URL base. |
| fabrikamjazzclub | Identifica o locatário nomeado *Fabrikam Jazz Club*. |
| &nbsp; | &nbsp; |

1. O nome do locatário é analisado a partir da URL, pelo aplicativo de eventos.
2. O nome do locatário é usado para criar uma chave.
3. A chave é usada para acessar o catálogo, para obter o local do banco de dados do locatário.
    - O catálogo é implementado, usando *gerenciamento de mapas de fragmentos*.
4. O *Hub de Eventos* usa metadados estendidos no catálogo para construir a lista de URLs da página de eventos para cada locatário.

Em um ambiente de produção, normalmente, um registro DNS CNAME é criado para [*apontar um domínio da Internet da empresa*](../traffic-manager/traffic-manager-point-internet-domain.md) para o nome DNS do gerenciador de tráfego.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniciar a geração de carga nos bancos de dados de locatário

Agora que o aplicativo está implantado, vamos colocá-lo em funcionamento!
O script do PowerShell *Demo-LoadGenerator* começa uma execução de carga de trabalho em todos os bancos de dados de locatário.
A carga real em um aplicativo SaaS é esporádica e imprevisível.
Para simular esse tipo de carga, o gerador produz uma carga com picos aleatórias ou de intermitências de atividade em cada locatário.
As intermitências ocorrem em intervalos aleatórios.
Levará vários minutos para que o padrão de carga surja. Portanto, é melhor permitir que o gerador seja executado por pelo menos três ou quatro minutos antes de monitorar a carga.

1. No *ISE do PowerShell*, abra o script ...\\Módulos de Aprendizado\\Utilitários\\*Demo-LoadGenerator.ps1*.
1. Pressione **F5** para executar o script e iniciar o gerador de carga. (Deixe os valores de parâmetro padrão por ora.)
1. Você será solicitado a fazer logon na sua conta do Azure e, se necessário, selecionar a assinatura que deseja usar.

O script do gerador de carga inicia uma tarefa em segundo plano para cada banco de dados no catálogo e depois para.  Se você executar novamente o script do gerador de carga, primeiro irá parar todos os trabalhos em segundo plano que estejam em execução, antes de iniciar novos.

#### <a name="monitor-the-background-jobs"></a>Monitorar trabalhos em segundo plano

Se deseja controlar e monitorar os trabalhos em segundo plano, você pode usar os cmdlets a seguir:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Ações Demo-LoadGenerator.ps1

O *Demo-LoadGenerator.ps1* imita uma carga de trabalho ativa de transações de cliente. As etapas a seguir descrevem a sequência de ações que o *Demo-LoadGenerator.ps1* inicia:

1. O *Demo-LoadGenerator.ps1* inicia o *LoadGenerator.ps1* em primeiro plano.
    - Ambos os arquivos. ps1 são armazenados nas pastas *Módulos de aprendizado\\Utilitários\\*.

1. O *LoadGenerator.ps1* é executado repetidamente em todos os bancos de dados do locatário no catálogo.

1. O *LoadGenerator.ps1* inicia um trabalho em segundo plano do PowerShell para cada banco de dados do locatário: 
    - Por padrão, as tarefas em segundo plano executam por 120 minutos.
    - Cada trabalho causa uma carga baseada em CPU em um banco de dados de locatário, executando *sp_CpuLoadGenerator*.  A intensidade e a duração da carga variam de acordo com `$DemoScenario`. 
    - O *sp_CpuLoadGenerator* é executado repetidamente em torno de uma instrução SQL SELECT que causa uma alta carga de CPU. O intervalo de tempo entre os problemas do SELECT varia de acordo com os valores dos parâmetros para criar uma carga de CPU controlável. Os níveis de carga e os intervalos são randomizados para simular cargas mais realistas.
    - Esse arquivo .sql é armazenado em *WingtipTenantDB\\dbo\\StoredProcedures\\*.

1. Se `$OneTime = $false`, o gerador de carga inicia as tarefas em segundo plano e, em seguida, continua a executar, monitorando cada 10 segundos para todos os novos locatários que estão provisionados. Se definir `$OneTime = $true`, o LoadGenerator irá iniciar as tarefas em segundo plano e, em seguida, parar a execução em primeiro plano. Para este tutorial, deixe `$OneTime = $false`.

  Use Ctrl-C ou Stop Operation Ctrl-Break se você quiser parar ou reiniciar o gerador de carga. 

  Se você deixar o gerador de carga em primeiro plano, use outra instância do ISE do PowerShell para executar outros scripts do PowerShell.

&nbsp;

Antes de continuar na próxima seção, deixe o gerador de carga em execução no estado de invocação de trabalho.

## <a name="provision-a-new-tenant"></a>Provisionar um novo locatário

A implantação inicial cria três locatários de amostra. Agora você cria outro locatário para ver o impacto no aplicativo implantado. No aplicativo Wingtip, o fluxo de trabalho para provisionar novos locatários é explicado no [Tutorial de provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md). Nesta fase, você cria um novo locatário, o que leva menos de um minuto.

1. Abra um novo *ISE do PowerShell*.
1. Abra ...\\Módulos Learning\Provisão e Catálogo\\*Demo-ProvisionAndCatalog.ps1* .
2. Pressione **F5** para executar o script. (Deixe os valores padrão por ora.)

   > [!NOTE]
   > Vários scripts Wingtip SaaS usam *$PSScriptRoot* para navegar em pastas para chamar funções em outros scripts. Essa variável é avaliada apenas quando o script completo é executado ao pressionar **F5**.  Destacar e executar uma seleção com **F8** pode resultar em erros. Portanto, execute os scripts pressionando **F5**.

O novo banco de dados de locatário é:

- Criado em um pool Elástico do SQL.
- Inicializado.
- Registrado no catálogo.

Após o provisionamento bem-sucedido, o site *Eventos* do novo locatário aparecerá no seu navegador:

![Novo locatário](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Atualize o *Hub de Eventos* para fazer com que o novo locatário apareça na lista.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorar os servidores, os pools e os bancos de dados de locatário

Agora que você começou a executar uma carga na coleção de locatários, vamos analisar alguns dos recursos que foram implantados:

1. No [Portal do Azure](http://portal.azure.com), navegue até sua lista de servidores SQL e abra o servidor **catalog-dpt-&lt;USER&gt;**.
    - O servidor de catálogo contém dois bancos de dados, **tenantcatalog** e **basetenantdb** (um banco de dados modelo que é copiado para criar novos locatários).

   ![databases](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Volte à lista de servidores SQL.

3. Abra o servidor**tenants1-dpt-&lt;USER&gt;** que contém os bancos de dados de locatário.

4. Confira os itens a seguir:
    - Cada banco de dados de locatário é um banco de dados *elástico padrão* em um pool padrão de 50 eDTU.
    - O banco de dados *Red Maple Racing*, que é o banco de dados de locatário que você provisionou anteriormente.

   ![Servidor](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorar o pool

Depois de *LoadGenerator.ps1* ser executado por vários minutos, dados suficientes devem estar disponíveis para começar a olhar para alguns recursos de monitoramento. Esses recursos são criados em pools e bancos de dados.

Navegue até o servidor **tenants1-dpt-&lt;usuário&gt;** e clique em **Pool1** para exibir a utilização de recursos do pool. Nos gráficos a seguir, o gerador de carga foi executado em uma hora.

   ![monitorar pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- O primeiro gráfico, rotulado **Utilização de recursos**, mostra a utilização de eDTU do pool.
- O segundo gráfico mostra a utilização de eDTU dos cinco bancos de dados mais ativos no pool.

Os dois gráficos ilustram que os pools elásticos e o Banco de Dados SQL são bem adaptados às cargas de trabalho imprevisíveis do aplicativo SaaS.
Os gráficos mostram que 4 bancos de dados são cada um intermitências para até 40 eDTUs e ainda oferecem suporte confortavelmente a todos os bancos de dados para um pool de 50 eDTU. O pool de 50 eDTU pode dar suporte a cargas de trabalho ainda mais pesadas.
Se eles fossem provisionados como bancos de dados autônomos, cada base de dados precisaria ser um S2 (50 DTU) para suportar as explosões.
O custo de 4 bancos de dados independentes S2 seria quase 3 vezes o preço do pool.
Em situações reais, os clientes do Banco de Dados SQL executam até 500 bancos de dados em 200 pools de eDTU.
Para obter mais informações, consulte o [tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Para saber mais sobre pools elásticos, consulte [*O que é um pool elástico do SQL do Azure*](sql-database-elastic-pool.md)
- Para saber mais sobre os trabalhos elásticos, consulte [*Gerenciando bancos de dados de nuvem com escalonamento horizontal*](sql-database-elastic-jobs-overview.md)
- Para saber mais sobre aplicativos SaaS multilocatários, consulte [*Padrões de design para aplicativos SaaS multilocatários*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> - Como implantar o aplicativo Wingtip Tickets SaaS
> - Sobre os servidores, pools e bancos de dados que constituem o aplicativo
> - Que os locatários são mapeados para seus dados com o *catálogo*
> - Como provisionar novos locatários
> - Como exibir a utilização do pool para monitorar a atividade do locatário
> - Como excluir recursos de exemplo para interromper a cobrança relacionada

Em seguida, tente fazer o [Tutorial para provisionar e catalogar](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

