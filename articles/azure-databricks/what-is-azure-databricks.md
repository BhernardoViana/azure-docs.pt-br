---
title: O que é o Azure Databricks?
description: Saiba mais sobre o que é o Azure Databricks e como ele leva o Spark no Databricks para o Azure. O Azure Databricks é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de serviços de nuvem do Microsoft Azure.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: overview
ms.date: 05/29/2018
ms.author: mamccrea
ms.custom: mvc
ms.openlocfilehash: f6a2f89828dbc13ca716ccb40bb05234b7e31464
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418799"
---
# <a name="what-is-azure-databricks"></a>O que é o Azure Databricks?

O Azure Databricks é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de serviços de nuvem do Microsoft Azure. Projetado com os fundadores do Apache Spark, O Databricks é integrado com o Azure para fornecer instalação com um clique, fluxos de trabalho simplificados e um workspace interativo que permite a colaboração entre os cientistas de dados, os engenheiros de dados e os analistas de negócios.

![O que é o Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "O que é o Azure Databricks?")

## <a name="apache-spark-based-analytics-platform"></a>Plataforma de análise com base no Apache Spark

O Azure Databricks abrange as tecnologias e os recursos completos de código aberto do cluster do Apache Spark. O Spark no Azure Databricks inclui os seguintes componentes:

![Apache Spark no Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark no Azure Databricks")

* **SQL e DataFrames do Spark**: SQL do Spark é o módulo de Spark para trabalhar com dados estruturados. Um DataFrame é uma coleção distribuída de dados organizados em colunas nomeadas. Ele é conceitualmente equivalente a uma tabela em um banco de dados relacional ou uma estrutura de dados em R/Python.

* **Streaming**: processamento de dados em tempo real e análise para aplicativos analíticos e interativos. Integra-se com HDFS, Flume e Kafka.

* **MLib**: biblioteca Machine Learning que consiste em algoritmos e utilitários de aprendizado comuns, incluindo classificação, regressão, clustering, filtragem colaborativa, redução de dimensionalidade, bem como primitivos de otimização subjacente.

* **GraphX**: gráficos e computação de gráfico para um amplo escopo de casos de uso desde análise cognitiva até exploração de dados.

* **API principal do Spark**: inclui suporte para R, SQL, Python, Scala e Java.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark no Azure Databricks

O Azure Databricks compila com base nos recursos do Spark fornecendo uma plataforma de nuvem de gerenciamento zero que inclui:

- Clusters do Spark totalmente gerenciados
- Um workspace interativo para exploração e visualização
- Uma plataforma para capacitar seus aplicativos favoritos baseados no Spark

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Clusters do Apache Spark totalmente gerenciados na nuvem

O Azure Databricks tem um ambiente de produção seguro e confiável na nuvem, gerenciado e com suporte de especialistas do Spark. Você pode:

* Criar clusters em segundos.
* Autoescalar clusters dinâmica e verticalmente, incluindo clusters sem servidor, e compartilhá-los entre equipes. 
* Usar clusters programaticamente usando as APIs REST. 
* Usar recursos de integração de dados seguros criados com base no Spark que permitem unificar seus dados sem centralização. 
* Obter acesso instantâneo para os recursos mais recentes do Apache Spark com cada versão.

### <a name="databricks-runtime"></a>Databricks Runtime
O Databricks Runtime é criado sobre o Apache Spark e é nativamente criado para a nuvem do Azure. 

Com a opção **Sem servidor**, o Azure Databricks abstrai completamente a complexidade da infraestrutura e a necessidade de experiência especializada para instalar e configurar sua infraestrutura de dados. A opção Sem servidor ajuda os cientistas de dados a iterar rapidamente como uma equipe.

Para engenheiros de dados, que se importam com o desempenho dos trabalhos de produção, o Azure Databricks fornece um mecanismo do Spark mais rápido e eficaz por meio de várias otimizações na camada de E/S e na camada de processamento (Databricks I/O).

### <a name="workspace-for-collaboration"></a>Workspace de colaboração

Por meio de um ambiente colaborativo e integrado, o Azure Databricks simplifica o processo de exploração de dados, criação de protótipos e de execução de aplicativos controlados por dados no Spark.

* Determine como usar dados com fácil exploração de dados.
* Documente seu progresso em blocos de notas em R, Python, Scala ou SQL.
* Visualize dados com apenas alguns cliques e use ferramentas conhecidas como Matplotlib, ggplot ou d3.
* Use painéis interativos para criar relatórios dinâmicos.
* Use o Spark e interaja com os dados simultaneamente.

## <a name="enterprise-security"></a>Segurança do Enterprise

O Azure Databricks fornece a segurança a nível empresarial do Azure, incluindo a integração do Azure Active Directory, controles com base em função e SLAs que protegem seus dados e o seu negócio.

* A integração com o Azure Active Directory permite que você execute soluções completas baseadas no Azure usando o Azure Databricks.
* O acesso baseado em funções do Azure Databricks permite que as permissões refinadas de usuário para blocos de notas, clusters, trabalhos e dados.
* SLAs de nível empresarial. 

## <a name="integration-with-azure-services"></a>Integração com serviços do Azure

O Azure Databricks integra-se totalmente com bancos de dados do Azure e armazena: SQL Data Warehouse, Cosmos DB, Data Lake Store e Armazenamento de Blob. 

## <a name="integration-with-power-bi"></a>Integração com o Power BI
Por meio da integração avançada com o Power BI, o Azure Databricks permite que você descubra e compartilhe seus insights de impacto de forma rápida e fácil. Você também pode usar outras ferramentas de BI, como o Software Tableau, por meio de pontos de extremidade de cluster JDBC/ODBC.

## <a name="next-steps"></a>Próximas etapas

* [Início rápido: executar um trabalho de Spark no Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Trabalhar com clusters do Spark](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Trabalhar com blocos de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Criar trabalhos do Spark](https://docs.azuredatabricks.net/user-guide/jobs.html)

 









