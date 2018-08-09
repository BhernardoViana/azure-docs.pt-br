---
title: Provisionar a Máquina Virtual de Ciência de Dados do Windows no Azure | Microsoft Docs
description: Configure e crie uma Máquina Virtual de Ciência de Dados no Azure para realizar a análise e o aprendizado de máquina.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: b749d8a904bc40eba3346cc03d9274236380c80d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450286"
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Provisionar a Máquina Virtual de Ciência de Dados do Windows no Azure
A Máquina Virtual de Ciência de Dados da Microsoft é uma imagem de VM (máquina virtual) do Microsoft Azure pré-instalada e configurada com diversas ferramentas populares que são usadas para a análise de dados e o aprendizado de máquina. As ferramentas incluídas são:

* [Azure Machine Learning](../service/index.yml) Workbench
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Distribuição do Anaconda Python
* Bloco de anotações do Jupyter (com R, Python, PySpark kernels)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Instância independente do Spark para desenvolvimento e teste local
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Ferramentas Machine learning e Data Analytics
  * Estruturas de aprendizado profundo: um conjunto avançado de estruturas de AI, que inclui o [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/), o [TensorFlow](https://www.tensorflow.org/), o [Encadeador](https://chainer.org/), o mxNet e o Keras, está incluído na VM.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): um sistema de machine learning rápido com suporte a técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.
  * [Rattle](http://rattle.togaware.com/): (R Analytical Tool To Learn Easily) uma ferramenta que facilita o uso de análise de dados e o aprendizado de máquina em R. Inclui a exploração de dados baseados em GUI e a modelagem com geração automática de código R.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/): um software de mineração de dados visual e machine learning em Java.
  * [Apache Drill](https://drill.apache.org/): um Mecanismo de consulta SQL livre de esquema para Hadoop, NoSQL e Armazenamento em Nuvem.  Oferece suporte a interfaces ODBC e JDBC para habilitar consultas NoSQL e arquivos de ferramentas de BI padrão, como Power BI, Excel, Tableau.
* Bibliotecas em R e Python para uso em Azure Machine Learning e outros serviços do Azure
* Git incluindo Git Bash para trabalhar com repositórios de código de origem, incluindo GitHub, Visual Studio Team Services e fornece vários utilitários de linha de comando populares do Linux (incluindo awk, sed, perl, grep, find, wget, curl etc.) acessíveis no git bash e no prompt de comando. 

Fazer a ciência de dados envolve a iteração em uma sequência de tarefas:

1. Localizar, carregar e pré-processar dados
1. Compilar e testar modelos
1. Implantar os modelos para consumo em aplicativos inteligentes

Cientistas de dados usam várias ferramentas para concluir essas tarefas. Pode ser muito demorado encontrar as versões apropriadas do software e baixar e instalá-las. A Máquina Virtual de Ciência de Dados da Microsoft pode facilitar essa carga fornecendo uma imagem pronta para uso que pode ser provisionada no Azure com todas as diversas ferramentas populares pré-instaladas e configuradas. 

A Máquina Virtual de Ciência de Dados da Microsoft impulsiona seu projeto de análise. Ela permite que você trabalhe nas tarefas em várias linguagens, incluindo R, Python, SQL e C#. O Visual Studio fornece um IDE para desenvolver e testar seu código que é fácil de usar. O SDK do Azure incluído na VM permite que você compile seus aplicativos usando vários serviços na plataforma de nuvem da Microsoft. 

Não há encargos de software para esta imagem da VM de ciência de dados. Você só paga pelas taxas de uso do Azure que dependem do tamanho da máquina virtual que provisionar. Mais detalhes sobre as taxas de computação podem ser encontradas na seção Detalhes de preço na página [Máquina virtual de ciência de dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) . 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Outras versões da Máquina Virtual de Ciência de Dados
Uma imagem do [Ubuntu](dsvm-ubuntu-intro.md) também está disponível, com muitas ferramentas semelhantes, além de algumas estruturas de aprendizado aprofundado. Uma imagem do [CentOS](linux-dsvm-intro.md) também está disponível. Também oferecemos uma [edição do Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) da máquina virtual de ciência de dados, embora algumas ferramentas estejam disponíveis somente na edição do Windows Server 2016.  Caso contrário, este artigo também se aplicará à edição do Windows Server 2012.

## <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma Máquina Virtual de Ciência de Dados da Microsoft, você deve ter o seguinte:

* **Uma assinatura do Azure**: para obter uma, confira [Obter avaliação gratuita do Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Criar sua Máquina Virtual de Ciência de Dados da Microsoft
Para criar uma instância da Máquina Virtual de Ciência de Dados da Microsoft, siga estas etapas:

1. Navegue até a máquina virtual no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
1. Selecione o botão **Criar** na parte inferior para ser levado para um assistente![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
1. O assistente usado para criar a Máquina Virtual de Ciência de Dados da Microsoft exige **entradas** para cada uma das **quatro etapas** enumeradas à direita desta figura. Aqui estão as entradas necessárias para configurar cada uma das seguintes etapas:
   
   1. **Noções básicas**
      
      1. **Nome**: o nome do servidor de ciência de dados que você está criando.
      1. **Tipo de disco da VM**: escolha entre o SSD ou o HD. Para a instância de GPU NC_v1 (baseado no NVidia Tesla K80), escolha **HDD** como o tipo de disco. 
      1. **Nome de Usuário**: ID de logon da conta de administrador.
      1. **Senha**: senha da conta de administrador.
      1. **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado.
      1. **Grupo de Recursos**: é possível criar um novo grupo ou usar um existente.
      1. **Local**: selecione o datacenter mais apropriado. Normalmente, é o datacenter que contém a maioria dos seus dados ou que está mais próximo de sua localização física para o acesso mais rápido à rede.
   1. **Tamanho**: selecione um dos tipos de servidor que atenda aos seus requisitos funcionais e restrições de custo. Você pode obter mais opções de tamanhos de VM selecionando “Exibir Tudo”.
   1. **Configurações**:
      
      1. **Usar Managed Disks**: escolha a opção Gerenciado se você quiser que o Azure gerencie os discos da VM.  Caso contrário, você precisará especificar uma conta de armazenamento nova ou existente. 
      1. **Outros parâmetros**: normalmente, você simplesmente usa os valores padrão. Caso queira considerar o uso de valores não padrão, passe o ponteiro do mouse sobre o link informativo para obter ajuda sobre o campo específico.
    a. **Resumo**: verifique se todas as informações inseridas estão corretas e clique em **Criar**. **OBSERVAÇÃO**: a VM não tem encargos adicionais além dos de computação para o tamanho do servidor que você escolheu na etapa **Tamanho**. 

> [!NOTE]
> O provisionamento deve levar cerca de 10 a 20 minutos. O status do provisionamento é exibido no Portal do Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Como acessar uma Máquina Virtual de Ciência de Dados da Microsoft
Depois de criar a máquina virtual, você poderá entrar na área de trabalho remotamente usando as credenciais da conta de administrador configurada anteriormente na seção **Noções básicas** . 

Depois de criar e provisionar sua VM, você estará pronto para começar a usar as ferramentas que estão instaladas e configuradas nela. Há blocos do menu Iniciar e ícones da área de trabalho para várias das ferramentas. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas Instaladas na Máquina Virtual de Ciência de Dados da Microsoft



### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Se você quiser usar as bibliotecas corporativas da Microsoft no R ou no Python escalonável para sua análise, a VM deverá ter o Microsoft ML Server Developer Edition (conhecido anteriormente como Microsoft R Server) instalado. O Microsoft ML Server é uma plataforma de análise corporativa amplamente implantável, disponível para R e Python, escalonável, com suporte comercial e segura. Com suporte para diversas estatísticas de Big Data, modelos de previsão e recursos de Machine Learning, o ML Server é compatível com uma gama completa de análises: exploração, análise, visualização e modelagem. Ao usar e estender o software livre R e o Python, o Microsoft ML Server é totalmente compatível com scripts e funções de R / Python e pacotes CRAN / pip / Conda, para analisar os dados em escala empresarial. Ele também aborda as limitações de memória interna do R de software livre, adicionando o processamento paralelo e em partes dos dados. Isso permite que você execute análises nos dados muito maiores do que o que cabe na memória principal.  O Visual Studio Community Edition incluído na VM contém as Ferramentas do R para Visual Studio e a extensão Ferramentas Python para Visual Studio, que fornece um IDE completo para trabalhar com o R ou com o Python. Nós também fornecemos outros IDEs, bem como o [RStudio](http://www.rstudio.com) e o [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) na VM. 

### <a name="python"></a>Python
Para o desenvolvimento com Python, as distribuições 2.7 e 3.6 do Anaconda Python foram instaladas. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados. Você pode usar Ferramentas Python para Visual Studio (PTVS) que são instaladas na edição do Visual Studio 2017 Community ou um dos IDEs agrupado com Anaconda como IDLE ou Spyder. Você pode iniciar um desses pesquisando na barra de pesquisa (tecla **Win** + **S**).

> [!NOTE]
> Para apontar as Ferramentas Python para Visual Studio para o Anaconda Python 2.7, você precisa criar ambientes personalizados para cada versão. Para definir esses caminhos de ambiente no Visual Studio 2017 Community Edition, navegue até **Ferramentas** -> **Ferramentas do Python** -> **Ambientes do Python** e clique em **+ Personalizado**. 
> 
> 

O Anaconda Python 3.6 é instalado em C:\Anaconda e o Anaconda Python 2.7 é instalado em c:\Anaconda\envs\python2. Consulte a [documentação do PTVS](/visualstudio/python/installing-python-interpreters.md) para obter as etapas detalhadas. 

### <a name="jupyter-notebook"></a>Bloco de anotações do Jupyter
A distribuição do Anaconda também acompanha um notebook Jupyter, um ambiente de compartilhamento de código e de análise. Um servidor de notebook Jupyter foi previamente configurado com os kernels do Python 2.7, Python 3.x, PySpark, Julia e R. Há um ícone de área de trabalho chamado “Bloco de anotações do Jupyter” para inicializar o servidor do Jupyter e o navegador a fim de acessar o servidor do Notebook. 

Empacotamos vários notebooks de exemplo no Python e no R. Os notebooks Jupyter mostrarão como trabalhar com o Microsoft ML Server, o SQL Server ML Services (análise no banco de dados), o Python, o Kit de Ferramentas de Serviços Cognitivos da Microsoft, o Tensorflow e com outras tecnologias do Azure depois que você acessar o Jupyter. Você pode ver o link para os exemplos na home page do notebook após a autenticação no notebook Jupyter usando a senha criada na etapa anterior. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community Edition
Visual Studio Community edition instalada na VM. É uma versão gratuita do IDE popular da Microsoft que pode ser usada para fins de avaliação e para equipes pequenas. Você pode consultar os termos de licenciamento [aqui](https://www.visualstudio.com/support/legal/mt171547).  Abra o Visual Studio clicando duas vezes no ícone da área de trabalho ou no menu **Iniciar** . Você também pode pesquisar programas com **Win** + **S** e inserindo “Visual Studio”. Nesse local, você pode criar projetos em linguagens como C#, Python, R e node.js. Também há plugins instalados que facilitam o trabalho com os serviços do Azure, como o Catálogo de Dados do Azure, o Azure HDInsight (Hadoop, Spark) e o Azure Data Lake. Agora há também um plug-in chamado ```Visual Studio Tools for AI``` que é integrado para o Azure Machine Learning e ajuda a compilar aplicativos de AI rapidamente. 

> [!NOTE]
> Você poderá receber uma mensagem informando que seu período de avaliação expirou. Insira suas credenciais da conta da Microsoft ou crie uma nova conta gratuita e insira-a para obter acesso ao Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
Uma versão de desenvolvedor do SQL Server 2017 com os Serviços do ML para execução na análise no banco de dados é fornecida na VM com o R ou com o Python. Os Serviços do ML fornecem uma plataforma para desenvolver e implantar aplicativos inteligentes. Você pode usar essas linguagens avançadas e poderosas e os vários pacotes da comunidade para criar modelos e gerar previsões para seus dados do SQL Server. Você pode manter a análise próxima aos dados porque os serviços do ML (no banco de dados) integram as linguagens R e Python no SQL Server. Isso elimina os custos e os riscos de segurança associados à movimentação de dados.

> [!NOTE]
> O SQL Server Developer Edition só pode ser usado para fins de desenvolvimento e teste. Você precisa de uma licença para executá-la em produção. 
> 
> 

Você pode acessar o SQL Server iniciando o **SQL Server Management Studio**. O nome da VM é populado como o Nome do Servidor. Use a Autenticação do Windows quando estiver conectado como o administrador no Windows. Quando estiver no SQL Server Management Studio, você pode criar outros usuários, criar bancos de dados, importar dados e executar consultas SQL. 

Para habilitar a análise no banco de dados usando os serviços ML do SQL, execute o comando a seguir como uma ação de tempo no SQL Server Management Studio depois de entrar como administrador do servidor. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Várias ferramentas do Azure são instaladas na VM:

* Há um atalho da área de trabalho para acessar a documentação do SDK do Azure. 
* **AzCopy**: usado para mover dados para dentro e fora de sua Conta de Armazenamento do Microsoft Azure. Para ver o uso, digite **Azcopy** em um prompt de comando. 
* **Gerenciador de Armazenamento do Microsoft Azure**: usado para procurar os objetos armazenados em sua Conta de Armazenamento do Azure e transferir dados para dentro e fora do Armazenamento do Azure. Você pode digitar **Gerenciador de Armazenamento** na pesquisa ou encontrá-lo no menu Iniciar do Windows para acessar essa ferramenta. 
* **Adlcopy**: usado para mover dados para o Azure Data Lake. Para ver o uso, digite **adlcopy** em um prompt de comando. 
* **dtui**: usado para mover dados para dentro e fora do Azure Cosmos DB, um banco de dados NoSQL na nuvem. Digite **dtui** no prompt de comando. 
* **Integration Runtime do Azure Data Factory**: permite a movimentação de dados entre as fontes de dados locais e a nuvem. É usado em ferramentas como o Azure Data Factory. 
* **Microsoft Azure PowerShell**: uma ferramenta usada para administrar os recursos do Azure na linguagem de script do PowerShell também é instalada em sua VM. 

### <a name="power-bi"></a>Power BI
Para ajudá-lo a compilar ótimos painéis e visualizações, o **Power BI Desktop** foi instalado. Use essa ferramenta para extrair dados de fontes diferentes, criar painéis e relatórios e publicá-los na nuvem. Para saber mais, confira o site do [Power BI](http://powerbi.microsoft.com) . Você pode encontrar o Power BI Desktop no menu Iniciar. 

> [!NOTE]
> Você precisa de uma conta do Office 365 para acessar o Power BI. 
> 
> 

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

O Azure Machine Learning Workbench é um aplicativo de área de trabalho e uma interface de linha de comando. O Workbench tem preparação de dados interna que aprende as etapas de preparação dos seus dados conforme você as executa. Ele também fornece gerenciamento de projetos, histórico de execuções e integração ao bloco de anotações para aumentar sua produtividade. Você pode aproveitar as melhores estruturas de software livre, incluindo o TensorFlow, o Kit de Ferramentas Cognitivas, o Spark ML e o scikit-learn para desenvolver seus modelos. No DSVM, fornecemos um ícone de área de trabalho para instalar o Azure Machine Learning Workbench no diretório %LOCALAPPDATA% do usuário individual. Cada usuário que precisar utilizar o Workbench deverá executar uma ação única de clicar duas vezes no ícone de área de trabalho ```AzureML Workbench Setup``` para instalar a instância do Workbench. O Azure Machine Learning também cria e usa um ambiente de Python por usuário, que é extraído no %LOCALAPPDATA%\amlworkbench\python.

## <a name="additional-microsoft-development-tools"></a>Ferramentas de desenvolvimento adicionais da Microsoft
O [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) pode ser usado para descobrir e baixar outras ferramentas de desenvolvimento da Microsoft. Também é um atalho para a ferramenta fornecida na área de trabalho de Máquina de Virtual de Ciência de Dados da Microsoft.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na VM
| item | Diretório |
| --- | --- |
| Configurações de servidor do bloco de anotações do Jupyter |C:\ProgramData\jupyter |
| Diretório base de amostras do Bloco de Anotações do Jupyter |c:\dsvm\notebooks e c:\users\<username>\notebooks|
| Outras amostras |c:\dsvm\samples |
| Anaconda (padrão: Python 3.6) |c:\Anaconda |
| Ambiente do Anaconda Python 2.7 |c:\Anaconda\envs\python2 |
| Python autônomo do servidor de ML da Microsoft  | C:\Arquivos de Programas\Microsoft\ML Server\PYTHON_SERVER |
| Instância padrão R (Servidor ML autônomo) |C:\Arquivos de Programas\Microsoft\ML Server\R_SERVER |
| Diretório de instância no banco de dados de serviços de ML do SQL |C:\Arquivos de Programas\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (por usuário) | %localappdata%\amlworkbench | 
| Ferramentas diversas |c:\dsvm\tools |

> [!NOTE]
> Na edição do Windows Server 2012 do DSVM e na edição do Windows Server 2016 antes de março de 2018, o ambiente padrão do Anaconda é Python 2.7. O ambiente secundário é Python 3.5 localizado em c:\Anaconda\envs\py35. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Veja algumas das próximas etapas para continuar sua aprendizagem e exploração. 

* Explore as várias ferramentas de ciência de dados na VM de ciência de dados clicando no menu Iniciar e conferindo as ferramentas listadas no menu.
* Saiba mais sobre os Serviços de Azure Machine Learning e o Workbench visitando a [página de início rápido e os tutoriais](../service/index.yml) do produto. 
* Navegue até **C:\Arquivos de Programas\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** para obter amostras de como usar a biblioteca RevoScaleR no R, que dá suporte à análise de dados em escala empresarial.  
* Leia o artigo: [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](http://aka.ms/dsvmtenthings)
* Saiba como criar soluções completas de análise sistematicamente usando o [Processo de Ciência de Dados de Equipe](../team-data-science-process/index.yml).
* Visite a [Galeria de IA do Azure](http://gallery.cortanaintelligence.com) para obter exemplos de Machine Learning e de análise de dados que usam o Azure Machine Learning e os serviços de dados relacionados no Azure. Também fornecemos um ícone no menu **Iniciar** e na área de trabalho na máquina virtual para essa galeria.

