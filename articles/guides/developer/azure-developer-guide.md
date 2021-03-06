---
title: Guia de introdução para desenvolvedores no Azure | Microsoft Docs
description: Este artigo fornece informações essenciais para os desenvolvedores que buscam começar a usar a plataforma de Microsoft Azure para suas necessidades de desenvolvimento.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: c8ab954471e597cfea5c6f56cd45b2191aa73242
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424067"
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para desenvolvedores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

O Azure é uma plataforma de nuvem completa que pode hospedar seus aplicativos existentes e simplificar o desenvolvimento de novos aplicativos. O Azure pode até mesmo aprimorar os aplicativos locais. O Azure integra os serviços de nuvem que você precisa para desenvolver, testar, implantar e gerenciar seus aplicativos, tudo isso ao mesmo tempo em que aproveita as eficiências da computação em nuvem.

Ao hospedar seus aplicativos no Azure, você pode começar por algo pequeno e facilmente escalar seu aplicativo à medida que aumenta a demanda do cliente. O Azure também oferece a confiabilidade necessária para aplicativos de alta disponibilidade, incluindo failover entre diferentes regiões. O [portal do Azure](https://portal.azure.com) permite que você gerencie facilmente todos os serviços do Azure. Além disso, também é possível gerenciar seus serviços programaticamente, utilizando modelos e APIs e específicos do serviço.

Este guia é uma introdução à plataforma Azure para desenvolvedores de aplicativos. Este guia fornece diretrizes e direção necessárias para começar a criar novos aplicativos no Azure ou migrar aplicativos existentes para o Azure.

## <a name="where-do-i-start"></a>Por onde começo?

Com todos os serviços que o Azure oferece, pode ser uma tarefa intimidante descobrir quais serviços você precisa para dar suporte à sua arquitetura de solução. Esta seção destaca os serviços do Azure que normalmente são utilizados pelos desenvolvedores. Para obter uma lista de todos os serviços do Azure, consulte a [Documentação do Azure](../../index.md).

Primeiro, você deve decidir como hospedar o aplicativo no Azure. É necessário gerenciar toda a sua infraestrutura como uma VM (máquina virtual). É possível utilizar as instalações de gerenciamento da plataforma que o Azure fornece? Você precisa de uma estrutura sem servidor para hospedar a execução de código somente?

Seu aplicativo precisa de armazenamento em nuvem para qual o Azure oferece várias opções. Você pode usufruir da autenticação empresarial do Azure. Além disso, há ferramentas para monitoramento e desenvolvimento baseado em nuvem e a maioria dos serviços de hospedagem oferece integração DevOps.

Agora, vejamos alguns dos serviços específicos que recomendamos investigar para suas aplicações.

### <a name="application-hosting"></a>Hospedagem de aplicativos

O Azure fornece várias ofertas de computação baseada em nuvem para executar seu aplicativo, de modo que você não precise se preocupar com os detalhes da infraestrutura. Você pode facilmente escalar verticalmente ou escalar horizontalmente seus recursos à medida que o uso do aplicativo aumenta.

O Azure oferece serviços que dão suporte ao desenvolvimento de aplicativos e necessidades de hospedagem. O Azure fornece IaaS (Infraestrutura como Serviço) para oferecer-lhe controle total sobre a hospedagem do aplicativo. As ofertas de PaaS (plataforma como serviço) do Azure fornecem os serviços totalmente gerenciados necessários para capacitar seus aplicativos. Há até mesmo uma verdadeira hospedagem sem servidor no Azure, onde tudo o que você precisa fazer é escrever seu código.

![Opções de hospedagem de aplicativo do Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Serviço de aplicativo do Azure 

Ao buscar o caminho mais rápido para publicar seus projetos baseados na web, considere o Serviço de Aplicativo do Azure. O Serviço de Aplicativo facilita a extensão de seus aplicativos Web para oferecer suporte aos clientes móveis e publicar facilmente as APIs REST consumidas. Essa plataforma fornece autenticação utilizando provedores sociais, dimensionamento automático baseado em tráfego, teste em produção e implantações baseadas em contêiner e contínuas.

Você pode criar aplicativos Web, back-ends de aplicativos móveis e aplicativos de API.

Como todos os três tipos de aplicativos compartilham o runtime do Serviço de Aplicativo, é possível hospedar um site, oferecer suporte a clientes móveis e expor suas APIs no Azure, tudo a partir do mesmo projeto ou solução. Para saber mais sobre o Serviço de Aplicativo, consulte [O que são Aplicativos Web do Azure](../../app-service/overview.md).

O Serviço de Aplicativo foi projetado com o DevOps em mente. Ele dá suporte a várias ferramentas para publicação e implantações de integração contínua. Essas ferramentas incluem WebHooks do GitHub, Jenkins, Azure DevOps, TeamCity e outros.

É possível migrar seus aplicativos existentes para o Serviço de Aplicativo, utilizando a [ferramenta de migração online](https://www.migratetoazure.net/).

> **Quando usar**: Use o serviço de aplicativo quando estiver migrando aplicativos Web existentes para o Azure e quando precisar de uma plataforma de hospedagem totalmente gerenciada para seus aplicativos Web. Al´me disso, é possível utilizar o Serviço de Aplicativo quando precisar dar suporte a clientes móveis ou expor APIs REST com seu aplicativo.
> 
> **Introdução**: O Serviço de Aplicativo facilita a criação e implantação de seu primeiro [aplicativo Web](../../app-service/app-service-web-get-started-dotnet.md), [aplicativo móvel](../../app-service-mobile/app-service-mobile-ios-get-started.md) ou [aplicativo de API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Experimente agora**: o Serviço de Aplicativo permite que você forneça um aplicativo de curta duração para experimentar a plataforma sem precisar inscrever-se em uma conta do Azure. Experimente a plataforma e [crie seu aplicativo do Serviço de Aplicativo do Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Como um provedor da IaaS (Infraestrutura como Serviço), o Azure permite implantar ou migrar seu aplicativo para VMs Linux ou Windows. Juntamente com a Rede Virtual do Azure, as Máquinas Virtuais do Azure dão suporte para a implantação de VMs Linux ou Windows para o Azure. Com VMs, você tem total controle sobre a configuração da máquina. Ao utilizar VMs, você será responsável por toda a instalação de software para servidores, configuração, manutenção e patches do sistema operacional.

Devido ao nível de controle que você possui com VMs, será possível executar uma ampla gama de cargas de trabalho do servidor no Azure que não se ajustam em um modelo de PaaS. Essas cargas de trabalho incluem servidores de banco de dados, Windows Server Active Directory e Microsoft SharePoint. Para obter mais informações, consulte a documentação sobre Máquinas Virtuais para [Linux](/azure/virtual-machines/linux/) ou [Windows](/azure/virtual-machines/windows/).

> **Quando utilizar**: utilize Máquinas Virtuais quando quiser ter um controle total sobre a infraestrutura do aplicativo ou migrar cargas de trabalho do aplicativo local para o Azure sem precisar fazer alterações.
> 
> **Introdução**: Criar uma [VM Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) ou [VM Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) a partir do portal do Azure.

#### <a name="azure-functions-serverless"></a>Azure Functions (sem servidor)

Em vez de se preocupar em criar e gerenciar um aplicativo inteiro ou a infraestrutura para executar seu código, e se você pudesse apenas escrever seu código e executá-lo em resposta a eventos ou agendar?  [Azure Functions](../../azure-functions/functions-overview.md) é uma oferta de estilo "sem servidor" que permite escrever apenas o código que você precisa. Com o functions, você pode disparar a execução de código com solicitações HTTP, WebHooks, eventos de serviço de nuvem ou em um agendamento. É possível codificar em sua linguagem de desenvolvimento de preferência, como C\#, F\#, Node.js, Python ou PHP. Com a cobrança baseada no consumo, você paga apenas pelo tempo que seu código é executado, e escalas do Azure, conforme necessário.

> **Quando utilizar**: utilize o Azure Functions quando tiver um código disparado por outros serviços do Azure, por eventos baseados na Web ou em um agendamento. Também é possível utilizar o Azure Functions quando não há necessidade de sobrecarga de um projeto hospedado completo ou quando você somente quer pagar o tempo que o código é executado. Para saber mais, consulte [Visão Geral do Azure Functions](../../azure-functions/functions-overview.md).
> 
> **Introdução**: Seguir o tutorial de iniciação rápida do Azure Functions para [criar sua primeira função](../../azure-functions/functions-create-first-azure-function.md) a partir do portal.
> 
> **Experimente agora**: o Azure Functions permite que você execute seu código sem precisar inscrever-se em uma conta do Azure. Experimente agora em e [crie sua primeira função do Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos. Essa plataforma facilita a criação, o empacotamento, a implantação e o gerenciamento de microserviços escalonáveis e confiáveis. Ele também fornece recursos abrangentes de gerenciamento de aplicativos, como: 

* Provisionamento
* Implantando 
* Monitoramento
* Atualização/aplicação de patch
* Excluindo 

Os aplicativos executados em um pool compartilhado de máquinas podem começar pequenos e escalar para centenas ou milhares de máquinas, conforme necessário.

O Service Fabric dá suporte para WebAPI com Open Web Interface para .NET (OWIN) e ASP.NET Core. Ele fornece SDKs para a construção de serviços no Linux em tanto em .NET Core como Java. Para saber mais sobre o Service Fabric, consulte a documentação do [Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Quando utilizar:** o Service Fabric é uma boa escolha quando você está criando um aplicativo ou reescrevendo um aplicativo existente para utilizar uma arquitetura de microsserviço. Utilize o Service Fabric quando precisar de mais controle ou acesso direto à infraestrutura subjacente.
> 
> **Introdução:** [Criar seu primeiro aplicativo Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Aprimore seus aplicativos com os serviços do Azure

Junto com a hospedagem de aplicativos, o Azure fornece ofertas de serviço que podem aprimorar a funcionalidade. O Azure também pode melhorar o desenvolvimento e a manutenção de seus aplicativos, tanto na nuvem quanto no local.

#### <a name="hosted-storage-and-data-access"></a>Armazenamento hospedado e acesso a dados

A maioria dos aplicativos deve armazenar dados, portanto, você decide hospedar seu aplicativo no Azure, considere um ou mais dos seguintes serviços de armazenamento e dados.

- **Azure Cosmos DB**: um serviço de banco de dados multimodelo distribuído globalmente. Esse banco de dados permite dimensionar de forma elástica a taxa de transferência e o armazenamento em qualquer número de regiões geográficas com um SLA abrangente. 
  
  > **Quando usar:** quando seu aplicativo precisa de bancos de dados de documento, de tabela ou de grafo, incluindo bancos de dados MongoDB, com vários modelos de consistência bem definidos. 
  > 
  > **Introdução**: [compilar um aplicativo Web do Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se você for um desenvolvedor do MongoDB, consulte [Compilar um aplicativo Web do MongoDB com o Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Armazenamento do Azure**: oferece armazenamento durável e altamente disponível para blobs, filas, arquivos e outros tipos de dados não relacionais. O armazenamento fornece a base de armazenamento para VMs.

  > **Quando utilizar**: quando seu aplicativo armazena dados não relacionais, como pares chave-valor (tabelas), blobs, compartilhamentos de arquivos ou mensagens (filas).
  > 
  > **Introdução**: Escolher entre um desses tipos de armazenamento: [blobs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelas](../../cosmos-db/table-storage-how-to-use-dotnet.md),     [filas](../../storage/queues/storage-dotnet-how-to-use-queues.md) ou [arquivos](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Banco de Dados SQL do Azure**: uma versão baseada no Azure do mecanismo do Microsoft SQL Server para armazenar dados tabulares relacionais na nuvem. O Banco de Dados SQL fornece desempenho previsível, escalabilidade sem tempo de inatividade, continuidade dos negócios e proteção de dados.

  > **Quando utilizar**: quando seu aplicativo requer armazenamento de dados com integridade referencial, suporte transacional e suporte para consultas TSQL.
  > 
  > **Introdução**: [criar um banco de dados SQL em minutos utilizando o portal do Azure](../../sql-database/sql-database-get-started.md).


É possível utilizar o [Azure Data Factory](../../data-factory/introduction.md) para mover dados locais existentes para o Azure. Caso não esteja pronto para mover dados para a nuvem, as [Conexões Híbridas](../../biztalk-services/integration-hybrid-connection-overview.md) nos Serviços BizTalk permitem que você conecte o aplicativo hospedado no Serviço de Aplicativo para recursos locais. Além disso, é possível conectar-se aos dados do Azure e aos serviços de armazenamento a partir de seus aplicativos locais.

#### <a name="docker-support"></a>Suporte ao Docker

Os contêineres do Docker, uma forma de virtualização de SO, permitem implantar aplicativos de forma mais eficiente e previsível. Um aplicativo em contêineres funciona em produção da mesma maneira que em seus sistemas de desenvolvimento e teste. É possível gerenciar contêineres utilizando ferramentas do Docker padrão. Você pode utilizar suas ferramentas de software livre populares e habilidades existentes para implantar e gerenciar aplicativos baseados em contêineres no Azure.

O Azure fornece várias maneiras de utilizar contêineres em seus aplicativos.

- **Extensão de VM do Docker do Azure**: permite configurar sua VM com ferramentas do Docker para atuar como um host do Docker.

  > **Quando utilizar**: quando quiser gerar implantações de contêiner consistentes para seus aplicativos em uma VM ou, quando quiser utilizar o [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Introdução**: [Criar um ambiente do Docker no Azure utilizando a extensão de VM do Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Serviço de Contêiner do Azure**: permite criar, configurar e gerenciar um cluster de máquinas virtuais pré-configuradas para executar aplicativos em contêineres. Para saber mais sobre o Serviço de Contêiner, consulte  a [Introdução ao Serviço de Contêiner do Azure](../../container-service/container-service-intro.md).

  > **Quando utilizar**: quando for necessário criar ambientes escalonáveis prontos para produção que fornecem ferramentas de gerenciamento e agendamento adicionais ou, quando estiver implantando um cluster Docker Swarm.
  > 
  > **Introdução**: [Implantar um cluster de Serviço de Contêiner](../../container-service/dcos-swarm/container-service-deployment.md).

- **Máquina do Docker**: permite instalar e gerenciar um Mecanismo de Docker em hosts virtuais utilizando comandos docker-machine.

  >**Quando utilizar**: quando for necessário desenvolver rapidamente protótipo de um aplicativo criando um host do Docker único.

- **Imagem de Docker personalizada para Serviço de Aplicativo**: permite utilizar contêineres do Docker de um registro de contêiner ou um contêiner de cliente ao implantar um aplicativo Web no Linux.

  > **Quando utilizar**: ao implantar um aplicativo Web no Linux para uma imagem de Docker.
  > 
  > **Introdução**: [Utilizar uma imagem de Docker personalizada para Serviço de Aplicativo no Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Autenticação

É crucial não apenas saber quem está utilizando seus aplicativos, como também impedir o acesso não autorizado aos seus recursos. O Azure fornece várias maneiras de autenticar os clientes do aplicativo.

- **Azure AD (Azure Active Directory)** : o serviço de gerenciamento de acesso e identidade baseado em nuvem multilocatário da Microsoft. É possível adicionar SSO (login único) aos seus aplicativos integrando com o Azure AD. É possível acessar as propriedades do diretório utilizando diretamente a API do Graph do Azure AD ou a API do Microsoft Graph. É possível integrar com o suporte do Azure AD para a estrutura de autorização OAuth2.0 e Open ID Connect, utilizando os pontos de extremidade HTTP/REST nativos e as bibliotecas multiplataformas de autenticação do Azure AD.

  > **Quando utilizar**: quando quiser fornecer uma experiência de SSO, trabalhe com dados baseados em grafo ou autentique usuários baseados em domínio.
  > 
  > **Introdução**: Para saber mais, consulte o [Guia do desenvolvedor do Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Autenticação do Serviço de Aplicativo**: ao escolher o Serviço de Aplicativo para hospedar seu aplicativo, você também obterá suporte de autenticação interna para o Azure AD, juntamente com provedores de identidade social — incluindo Facebook, Google, Microsoft e Twitter.

  > **Quando utilizar**: quando quiser habilitar a autenticação em um aplicativo do Serviço de Aplicativo utilizando o Azure AD, os provedores de identidade social ou ambos.
  > 
  > **Introdução**: Para saber mais sobre a autenticação no Serviço de Aplicativo, consulte [Autenticação e autorização no Serviço de Aplicativo do Azure](../../app-service/overview-authentication-authorization.md).

Para saber mais sobre as melhores práticas de segurança no Azure, consulte [Padrões e melhores práticas de segurança do Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitoramento

Com seu aplicativo em funcionamento no Azure, você precisa monitorar o desempenho, observar problemas e ver como os clientes estão usando seu aplicativo. O Azure fornece várias opções de monitoramento.

-   **Application Insights do Visual Studio**: um serviço de análise extensível hospedado no Azure que integra-se com o Visual Studio para monitorar seus aplicativos Web em tempo real. Ele fornece os dados de que você precisa para melhorar o desempenho e a usabilidade de seus aplicativos continuamente. Essa melhoria ocorrerá se você hospedar seus aplicativos no Azure ou não.

    >**Introdução**: Seguir o tutorial do [Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: um serviço que ajuda você a Visualizar, consultar, rotear, arquivar e agir sobre as métricas e os logs que você gera com a infraestrutura e os recursos do Azure. O monitor é uma fonte única para monitorar recursos do Azure e fornece as exibições de dados que você vê no portal do Azure.
 
    >**Introdução**: [Introdução ao Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integração de DevOps

Quer seja provisionando VMs ou publicando seus aplicativos Web com integração contínua, o Azure se integra com a maioria das ferramentas de DevOps populares. Você pode trabalhar com as ferramentas que já tem e maximizar sua experiência existente com o suporte para ferramentas como: 

* Jenkins 
* GitHub 
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Introdução**: Para consultar as opções de DevOps para um aplicativo do Serviço de Aplicativo, consulte [Implantação contínua para Serviço de Aplicativo do Azure](../../app-service/deploy-continuous-deployment.md).
> 
> **Experimente agora:** [Experimente várias das integrações DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Regiões do Azure

O Azure é uma plataforma de nuvem global que geralmente está disponível em muitas regiões em todo o mundo. Ao provisionar um serviço, aplicativo ou VM no Azure, você será solicitado a selecionar uma região. Essa região representa um datacenter específico onde seu aplicativo é executado ou onde os dados são armazenados. Essas regiões correspondem a locais específicos, os quais são publicados na página [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a melhor região para seu aplicativo e dados

Um dos benefícios de utilizar o Azure é que você pode implantar seus aplicativos em vários datacenters em todo o mundo. A região que você escolher poderá afetar o desempenho do aplicativo. Por exemplo, é melhor escolher uma região que esteja mais próxima da maioria dos seus clientes para reduzir a latência nas solicitações de rede. Talvez você também queira selecionar sua região para atender aos requisitos legais para distribuir seu aplicativo em determinados países/regiões. Sempre a melhor prática será armazenar os dados do aplicativo no mesmo datacenter ou em um datacenter o mais próximo possível do datacenter que hospeda o aplicativo.

### <a name="multi-region-apps"></a>Aplicativos de várias regiões

Embora seja improvável, não é impossível que todo um datacenter fique offline devido a um evento como um desastre natural ou uma falha na Internet. Uma melhor prática é hospedar aplicativos de negócios vitais em mais de um datacenter para fornecer a máxima disponibilidade. Utilizar várias regiões também pode reduzir a latência para usuários globais e oferecer oportunidades adicionais de flexibilidade ao atualizar aplicativos.

Alguns serviços, como Máquina Virtual e Serviços de Aplicativos utilizam o [Gerenciador de Tráfego do Azure](../../traffic-manager/traffic-manager-overview.md) para habilitar o suporte de várias regiões com failover entre regiões para suportar aplicativos empresariais de alta disponibilidade. Por exemplo, consulte [Arquitetura de referência do Azure: executar um aplicativo Web em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Quando utilizar**: quando você tiver aplicativos empresariais de alta disponibilidade que beneficiam-se de failover e replicação.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como fazer para gerenciar meus aplicativos e projetos?

O Azure fornece um avançado conjunto de experiências para você criar e gerenciar seus projetos, aplicativos e recursos do Azure — tanto programaticamente, quanto no [portal do Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comando e PowerShell

O Azure fornece duas maneiras de gerenciar seus aplicativos e serviços na linha de comando. Você pode usar ferramentas como bash, terminal, prompt de comando ou sua ferramenta de linha de comando de sua escolha. Normalmente, você pode fazer as mesmas tarefas na linha de comando como no portal do Azure — como criar e configurar máquinas virtuais, redes virtuais, aplicativos Web e outros serviços.

-   [CLI do Azure](../../xplat-cli-install.md): permite conectar-se a uma assinatura do Azure e programar várias tarefas em relação aos recursos do Azure a partir da linha de comando.

-   [Azure PowerShell](../../powershell-install-configure.md): fornece um conjunto de módulos com cmdlets que permitem gerenciar recursos do Azure utilizando o Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O [portal do Azure](https://portal.azure.com) é um aplicativo baseado na Web. Você pode usar o portal do Azure para criar, gerenciar e remover recursos e serviços do Azure. Ele inclui:

* Um painel configurável
* Ferramentas de gerenciamento de recursos do Azure
* Acesso a configurações de assinatura e informações de cobrança. Para obter mais informações, consulte a [Visão geral do portal do Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>APIs REST

O Azure é desenvolvido em um conjunto de APIs REST que suportam a interface do usuário do Portal do Azure. A maioria dessas APIs REST também é suportada para permitir que você programa e gerencie seus recursos e aplicativos do Azure a partir de qualquer dispositivo habilitado para Internet. Para o conjunto completo da documentação de API REST, consulte a [Referência de SDK do REST do Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Juntamente com as APIs REST, muitos serviços do Azure também permitem que você gerencie programaticamente recursos de seus aplicativos usando SDKs do Azure específicos da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Serviços como [Aplicativos Móveis](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Serviços de Mídia do Azure](../../media-services/previous/media-services-dotnet-how-to-use.md) fornecem SDKs do lado do cliente para permitir que você acesse serviços dos aplicativos Web e clientes móveis.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
A execução do aplicativo no Azure provavelmente envolve o trabalho com vários serviços do Azure. Esses serviços seguem o mesmo ciclo de vida e podem ser considerados como uma unidade lógica. Por exemplo, um aplicativo Web pode usar Aplicativos Web, Banco de Dados SQL, Armazenamento, Cache Redis do Azure e serviços de Rede de Distribuição de Conteúdo do Azure. O [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) permite que você trabalhe com os recursos em seu aplicativo como um grupo. É possível implantar, atualizar ou excluir todos os recursos em uma operação coordenada única.

Juntamente com o agrupamento lógico e o gerenciamento de recursos relacionados, o Azure Resource Manager inclui recursos de implantação que permitem personalizar a implantação e a configuração de recursos relacionados. Por exemplo, você pode usar a implantação do Resource Manager e configurar um aplicativo. Esse aplicativo pode consistir em várias máquinas virtuais, um balanceador de carga e um banco de dados SQL do Azure como uma única unidade.

Essas implantações são desenvolvidas utilizando um modelo do Azure Resource Manager, que é um documento no formato JSON. Os modelos permitem que você defina uma implantação e gerencie seus aplicativos utilizando modelos declarativos, em vez de scripts. Seus modelos podem funcionar para diferentes ambientes, como teste, de preparo e produção. Por exemplo, você pode usar modelos para adicionar um botão a um repositório GitHub que implanta o código no repositório para um conjunto de serviços do Azure com um único clique.

> **Quando utilizar**: utilize modelos do Resource Manager quando quiser uma implantação baseada em modelo para seu aplicativo, de modo que você possa gerenciar programaticamente utilizando APIs REST, a CLI do Azure e o Azure PowerShell.
> 
> **Introdução**: Para a introdução ao modelo, consulte [Criando modelos do Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Compreendendo contas, assinaturas e cobrança

Como desenvolvedores, gostamos de mergulhar diretamente no código e tentar começar o mais rápido possível, fazendo com que nossos aplicativos sejam executados. Nós, certamente, queremos incentivá-lo a começar a trabalhar no Azure da maneira mais fácil possível. Para torná-lo mais fácil, o Azure oferece uma [avaliação gratuita](https://azure.microsoft.com/free/). Alguns serviços ainda possuem uma funcionalidade "Experimente gratuitamente", como o [Serviço de Aplicativo do Azure](https://tryappservice.azure.com/) que não exige a criação de uma conta. Tão divertido quanto é se aprofundar em codificar e implantar seu aplicativo no Azure, também é importante levar algum tempo para entender como o Azure funciona. Especificamente, você deve entender como ele funciona a partir de um ponto de vista de contas de usuário, assinaturas e cobrança.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para criar ou trabalhar com uma assinatura do Azure, você deve ter uma conta do Azure. Uma conta do Azure é simplesmente uma identidade no Azure AD ou em um diretório, como uma organização corporativa ou de estudante, que confia no Azure AD. Caso não pertença a essa organização, sempre será possível criar uma assinatura utilizando sua Conta da Microsoft, que é confiável pelo Azure AD. Para saber mais sobre como integrar o Windows Server Active Directory local com o Azure AD, consulte [Integração de suas identidades locais com o Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Cada assinatura do Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Para saber mais, consulte [Como as assinaturas do Azure são associadas ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Além de definir identidades de conta individuais do Azure, também chamadas de *usuários*, você pode definir *grupos* no Azure AD. Criar grupos de usuários é uma boa maneira de gerenciar o acesso aos recursos em uma assinatura utilizando o controle de acesso baseado em função (RBAC). Para saber como criar grupos, consulte [Criar um grupo na versão prévia do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Também é possível criar e gerenciar grupos [utilizando o PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerencie suas assinaturas

Uma assinatura é um agrupamento de serviços do Azure que está vinculada a uma conta do Azure. Uma única conta do Azure pode conter várias assinaturas. A cobrança dos serviços do Azure é feita por assinatura. Para obter uma lista das ofertas de assinatura disponíveis por tipo, consulte os [Detalhes da oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). As assinaturas do Azure têm um administrador de conta que tem controle total sobre a assinatura. Eles também têm um administrador de serviços que tem controle sobre todos os serviços na assinatura. Para obter informações sobre os administradores clássicos de assinatura, confira [Adicionar ou alterar administradores de assinatura do Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Contas individuais podem receber controle detalhado dos recursos do Azure usando o [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Grupos de recursos

Ao fornecer novos serviços do Azure, isso é feito em uma determinada assinatura. Os serviços do Azure individuais, que também são chamados de recursos, são criados no contexto de um grupo de recursos. Os grupos de recursos facilitam a implantação e o gerenciamento dos recursos de seu aplicativo. Um grupo de recursos deve conter todos os recursos para o aplicativo que você quer trabalhar como uma unidade. É possível mover recursos entre grupos de recursos e até diferentes assinaturas. Para saber mais sobre como mover recursos, consulte [Mover recursos para o novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).

O Azure Resource Explorer é uma ótima ferramenta para visualizar os recursos já criados em sua assinatura. Para saber mais, consulte [Utilizar o Azure Resource Explorer para visualizar e modificar recursos](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Conceder acesso aos recursos

Quando você permite o acesso aos recursos do Azure, é sempre uma prática recomendada fornecer aos usuários o privilégio mínimo necessário para realizar uma determinada tarefa.

- **RBAC (controle de acesso baseado em função)** : no Azure, é possível conceder acesso a contas de usuários (entidades) em um escopo especificado: assinatura, grupo de recursos ou recursos individuais. O RBAC permite que você implante recursos em um grupo de recursos e conceda permissões a um usuário ou grupo específico. Ele também permite que você limite o acesso apenas aos recursos que pertencem ao grupo de recursos de destino. Além disso, é possível conceder acesso a um recurso único, como uma máquina virtual ou uma rede virtual. Para conceder acesso, você atribui uma função ao usuário, ao grupo ou à entidade de serviço. Há muitas funções predefinidas, no entanto, você também pode definir suas próprias funções personalizadas. Para saber mais, confira [O que é RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

  > **Quando usar**: quando você precisa de gerenciamento de acesso refinado para usuários e grupos ou quando precisa transformar um usuário em proprietário de uma assinatura.
  > 
  > **Para começar**: para saber mais, confira [Gerenciar o acesso usando RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

- **Objetos de entidade de serviço**: junto com o fornecimento de acesso a entidades de usuário e grupos, você pode conceder o mesmo acesso a uma entidade de serviço.

  > **Quando utilizar**: ao gerenciar programaticamente recursos do Azure ou conceder acesso para aplicativos. Para obter mais informações, consulte [Criar entidade de serviço e aplicativo do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Marcas

O Azure Resource Manager permite atribuir marcas personalizadas para recursos individuais. As marcas, que são pares chave-valor, podem ser úteis quando for necessário organizar recursos para cobrança ou monitoramento. As marcas fornecem uma maneira de rastrear recursos em vários grupos de recursos. Você pode atribuir marcas das seguintes maneiras:

* No portal 
* No modelo de Azure Resource Manager 
* Usando a API REST
* Usando a CLI do Azure
* Usando o PowerShell 

Além disso, é possível atribuir várias marcas para cada recurso. Para saber mais, consulte [Usando marcas para organizar os recursos do Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Cobrança

Na mudança da computação local para serviços hospedados na nuvem, acompanhar e estimar o uso do serviço e os custos relacionados são preocupações significativas. É importante estimar o custo dos novos recursos a serem executados mensalmente. Você também pode projetar como a cobrança procura um determinado mês com base nos gastos atuais.

#### <a name="get-resource-usage-data"></a>Obter dados de uso do recurso

O Azure fornece um conjunto de API REST de cobrança que fornecem acesso ao consumo do recurso e informações de metadados para assinaturas do Azure. Essas APIs de cobrança fornecem-lhe a capacidade de prever melhor e gerenciar os custos do Azure. Você pode acompanhar e analisar os gastos em incrementos por hora e criar alertas de gastos. Você também pode prever a cobrança futura com base nas tendências de uso atuais.

>**Introdução**: Para saber mais sobre o uso das APIs de cobrança, consulte [Visão geral de APIs de uso e RateCard de cobrança do Azure](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever os custos futuros

Embora seja desafiador estimar os custos antecipadamente, o Azure tem ferramentas que podem ajudar. Ele tem uma [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudar a estimar o custo dos recursos implantados. Você também pode usar os recursos de cobrança no portal e as APIs REST de cobrança para estimar custos futuros com base no consumo atual.

>**Introdução**: Consulte [Visão geral de APIs de uso e RateCard de cobrança do Azure](../../billing-usage-rate-card-overview.md).
