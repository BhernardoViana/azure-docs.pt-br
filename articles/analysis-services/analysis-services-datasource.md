---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de dados e conectores com suporte para os modelos de dados tabulares 1200 e superiores no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7616bcdaf2a2ae6f80b0c1e964f311ef5409a64f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707162"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

As fontes de dados e conectores mostrados no Assistente para obter dados ou tabela de importação no Visual Studio com Analysis Services projetos são mostrados para Azure Analysis Services e SQL Server Analysis Services. No entanto, nem todas as fontes de dados e conectores mostrados têm suporte no Azure Analysis Services. Os tipos de fontes de dados aos quais você pode se conectar dependem de vários fatores, como nível de compatibilidade de modelo, conectores de dados disponíveis, tipo de autenticação e suporte a gateway de dados local. As tabelas a seguir descrevem as fontes de dados com suporte para Azure Analysis Services.

## <a name="azure-data-sources"></a>Fontes de dados do Azure

|Fonte de dados  |Na memória  |DirectQuery  |Notas |
|---------|---------|---------|---------|
|Banco de dados SQL do Azure      |   SIM      |    SIM      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure SQL Data Warehouse      |   SIM      |   SIM       |<sup>[2](#azprovider)</sup>|
|Armazenamento de Blobs do Azure      |   SIM       |    Não      | <sup>[1](#tab1400a)</sup> |
|Armazenamento de Tabelas do Azure     |   SIM       |    Não      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  SIM        |  Não        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   SIM       |    Não      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   SIM       |    Não      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|HDFS do Azure HDInsight    |     SIM     |   Não       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   SIM       |   Não       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Observações:**    
<a name="tab1400a">1</a> - Tabular 1.400 e modelos superiores somente.  
<a name="azprovider">2</a> -quando especificado como uma fonte de dados de *provedor* nos modelos tabulares 1200 e superior, os modelos na memória e DirectQuery exigem o Microsoft OLE DB driver para SQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11,0 ou .NET Framework provedor de dados para SQL Server.    
Há suporte para <a name="azsqlmanaged">3</a> -instância gerenciada do banco de dados SQL do Azure. Como a instância gerenciada é executada na VNet do Azure com um endereço IP privado, o ponto de extremidade público deve ser habilitado na instância. Se não estiver habilitado, um [Gateway de dados local](analysis-services-gateway.md) será necessário.    
Não há suporte para <a name="databricks">4</a> -Azure Databricks usando o conector do Spark no momento.   
no momento, não há suporte para o conector de <a name="gen2">5</a> ADLS Gen2, no entanto, o conector do armazenamento de BLOBs do Azure pode ser usado com uma fonte de dados ADLS Gen2.   

## <a name="other-data-sources"></a>Outras fontes de dados

|Fonte de dados | Na memória | DirectQuery |Notas   |
|  --- | --- | --- | --- |
|Banco de Dados do Access     |  SIM | Não |  |
|Active Directory     |  SIM | Não | <sup>[152](#tab1400b)</sup>  |
|Serviços de análise     |  SIM | Não |  |
|Analytics Platform System     |  SIM | Não |  |
|Arquivo CSV  |SIM | Não |  |
|Dynamics 365     |  SIM | Não | <sup>[152](#tab1400b)</sup> |
|Pasta de trabalho do Excel     |  SIM | Não |  |
|Exchange      |  SIM | Não | <sup>[152](#tab1400b)</sup> |
|Pasta      |SIM | Não | <sup>[152](#tab1400b)</sup> |
|IBM Informix  |SIM | Não |  |
|Documento JSON      |  SIM | Não | <sup>[152](#tab1400b)</sup> |
|Linhas de binário      | SIM | Não | <sup>[152](#tab1400b)</sup> |
|Banco de dados MySQL     | SIM | Não |  |
|Feed OData      |  SIM | Não | <sup>[152](#tab1400b)</sup> |
|Consulta ODBC     | SIM | Não |  |
|OLE DB     |   SIM | Não |  |
|Oracle  | SIM  |SIM  | <sup>[99](#oracle)</sup> |
|Banco de Dados PostgreSQL   | SIM | Não | <sup>[152](#tab1400b)</sup> |
|Objetos do Salesforce|  SIM | Não | <sup>[152](#tab1400b)</sup> |
|Relatórios do Salesforce |SIM | Não | <sup>[152](#tab1400b)</sup> |
|SAP HANA     |  SIM | Não |  |
|SAP Business Warehouse    |  SIM | Não | <sup>[152](#tab1400b)</sup> |
|Lista do SharePoint      |   SIM | Não | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |SIM   | SIM  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|SQL Server Data Warehouse |SIM   | SIM  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Banco de dados Sybase     |  SIM | Não |  |
|Teradata | SIM  | SIM  | <sup>[254](#teradata)</sup> |
|Arquivo TXT  |SIM | Não |  |
|Tabela XML    |  SIM | Não | <sup>[152](#tab1400b)</sup> |
| | | |

**Observações:**    
<a name="tab1400b">6</a> -somente modelos de tabela 1400 e superiores.  
<a name="sqlim">7</a> -quando especificado como uma fonte de dados de *provedor* em modelos tabulares 1200 e superiores, especifique o driver do Microsoft OLE DB para SQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11,0 ou .NET Framework provedor de dados para SQL Server.  
<a name="instgw">8</a> -se especificar MSOLEDBSQL como o provedor de dados, talvez seja necessário baixar e instalar o [Driver do Microsoft OLE DB para SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) no mesmo computador que o gateway de dados local.  
<a name="oracle">9</a> -para modelos de tabela 1200 ou como uma fonte de dados de *provedor* em modelos de tabela 1400 +, especifique o Oracle provedor de dados para .net.  
<a name="teradata">10</a> -para modelos de tabela 1200 ou como uma fonte de dados de *provedor* em modelos de tabela 1400 +, especifique o Teradata provedor de dados para .net.   
<a name="filesSP">11</a> -não há suporte para os arquivos no SharePoint local.

Conectar-se a fontes de dados locais de um servidor Azure Analysis Services requer um [Gateway local](analysis-services-gateway.md). Ao usar um gateway, serão necessários provedores de 64 bits. 

## <a name="understanding-providers"></a>Entendendo provedores

Ao criar projetos de modelo tabular 1400 e superiores no Visual Studio, por padrão, você não especifica um provedor de dados ao se conectar a uma fonte de dados usando **obter dados**. Os modelos tabulares 1400 e superiores usam [Power Query](/power-query/power-query-what-is-power-query.md) conectores para gerenciar conexões, consultas de dados e mashups entre a fonte de dados e Analysis Services. Às vezes, elas são chamadas de conexões de fonte de dados *estruturadas* nessas configurações de propriedade de conexão definidas para você. No entanto, você pode habilitar fontes de dados herdadas. Quando habilitado, você pode usar o **Assistente de importação de tabela** para se conectar a determinadas fontes de dados tradicionalmente suportadas em tabela 1200 e modelos inferiores como *herdado*ou fontes de dados de *provedor* . Quando especificado como uma fonte de dados de provedor, você pode especificar um provedor de dados específico e outras propriedades avançadas de conexão. Por exemplo, você pode se conectar a um SQL Server local data warehouse ou até mesmo um banco de dados SQL do Azure como uma fonte de dado herdada. Em seguida, você pode selecionar o driver de OLE DB para SQL Server provedor de dados MSOLEDBSQL. Nesse caso, a seleção de um provedor de dados OLE DB pode fornecer um desempenho aprimorado sobre o conector de Power Query. 

Ao usar o assistente de importação de tabela no Visual Studio, as conexões com qualquer fonte de dados exigem um provedor de dados. Um provedor de dados padrão é selecionado para você. Você pode alterar o provedor de dados, se necessário. O tipo de provedor escolhido pode depender do desempenho, independentemente de o modelo estar usando armazenamento na memória ou DirectQuery, e qual Analysis Services plataforma na qual você implanta o modelo.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Especificar fontes de dados do provedor em projetos de modelo de tabela 1400 e superiores

Para habilitar as fontes de dados do provedor, no Visual Studio, clique em **ferramentas** > **Opções** > **Analysis Services tabular** > **importação de dados**, selecione **habilitar fontes de dados herdadas**.

![Habilitar fontes de dados herdadas](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Com as fontes de dados herdadas habilitadas, no **Gerenciador de modelos de tabela**, clique com o botão direito do mouse em fontes de **dados** > **importar da fonte de dados (Herdado**

![Fontes de dados herdadas no Gerenciador de modelos de tabela](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Assim como nos projetos de modelo tabular 1200, use o **Assistente de importação de tabela** para se conectar a uma fonte de dados. Na página conectar, clique em **avançado**. Especifique o provedor de dados e outras configurações de conexão nas **Propriedades avançadas definidas**.

![Propriedades avançadas de fontes de dados herdadas](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no Visual Studio ou no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="oauth-credentials"></a>Credenciais do OAuth

Para modelos de tabela no nível de compatibilidade 1400 e superior, o banco de dados SQL do Azure, o Azure SQL Data Warehouse, o Dynamics 365 e a lista do SharePoint dão suporte a credenciais OAuth. Azure Analysis Services gerencia a atualização de token para fontes de dados OAuth para evitar tempos limite para operações de atualização de execução longa. Para gerar tokens válidos, defina as credenciais usando o SSMS.

## <a name="next-steps"></a>Próximos passos
[Gateway Local](analysis-services-gateway.md)   
[Gerenciar seu serviço](analysis-services-manage.md)   

