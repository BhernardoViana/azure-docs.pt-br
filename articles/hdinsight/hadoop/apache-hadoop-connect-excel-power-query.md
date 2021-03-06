---
title: Conectar o Excel ao Apache Hadoop com o Power Query - Azure HDInsight
description: Saiba como aproveitar os componentes de business intelligence e usar o Power Query para Excel para acessar dados armazenados no Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: e64905cdfeac8d507df1c3dd92c245cb910a79b2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033564"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Conectar o Excel ao Apache Hadoop com o Power Query
Um dos principais recursos da solução de big data da Microsoft é a integração dos componentes de BI (business intelligence) da Microsoft com clusters Apache Hadoop no Azure HDInsight. Um dos principais exemplos é a capacidade de conectar o Excel com a conta de Armazenamento do Azure, que contém os dados associados ao seu cluster Hadoop usando o suplemento Microsoft Power Query para Excel. Este artigo explica como configurar e usar o Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Um cluster HDInsight**. Para configurar um, consulte Introdução [ao Azure HDInsight](./apache-hadoop-linux-tutorial-get-started.md).
* **estação de trabalho** que esteja executando Windows 7, Windows Server 2008 R2 ou um sistema operacional posterior.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autônomo ou Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalar o Power Query
O Power Query pode importar dados que foram retornados ou que foram gerados por um trabalho Hadoop em execução em um cluster HDInsight.

No Excel 2016, o Power Query foi integrado na faixa de opções Dados na seção Obter e Transformar. Para versões mais antigas do Excel, baixe Microsoft Power Query para Excel no [centro de download da Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e instale-o.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel
O suplemento do Power Query para Excel facilita a importação de dados de seu cluster HDInsight para o Excel onde ferramentas de BI, como o PowerPivot e o Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

**Para importar dados de um cluster HDInsight**

1. Abra o Excel.
2. Crie uma nova pasta de trabalho em branco.
3. Execute as etapas a seguir com base na versão do Excel:

   - Excel 2016

     - Clique no menu **Dados**, clique em **Obter Dados** da faixa de opções **Obter e Transformar Dados**, clique em **Do Azure** e, em seguida, em **Do Azure HDInsight (HDFS)** .

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   - Excel 2013/2010

     - Clique no menu **Power Query**, clique em **Do Azure** e, em seguida, clique em **Do Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)
       
       **Observação:** Se você não vir o menu de **Power Query** , vá para **arquivo** > **Opções** > **suplementos**e selecione suplementos de **com** na caixa suspensa **gerenciar** na parte inferior da página. Selecione o botão **Ir...** e verifique se a caixa do suplemento Power Query para Excel está marcada.
       
       **Observação:** O Power Query também permite que você importe dados do HDFS clicando em **De Outras Fontes**.
4. Para **Nome da conta**, insira o nome da conta de Armazenamento de Blobs do Azure associada ao cluster e, em seguida, clique em **OK**. Essa conta pode ser a conta de armazenamento padrão ou uma conta de armazenamento vinculada.  O formato é *https://&lt;StorageAccountName>.blob.core.windows.net/* .
5. Para **Chave de Conta**, insira a chave para a conta de Armazenamento de Blobs e clique em **Salvar**. (Você precisa inserir as informações da conta somente na primeira vez que acessar este repositório.)
6. No painel **Navegador** à esquerda do Editor de Consulta, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster.
7. Localize **HiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **../hive/warehouse/hivesampletable/** ) e clique em **Binário** à esquerda de HiveSampleData.txt. HiveSampleData.txt acompanha todo o cluster. Se desejar, você pode usar seu próprio arquivo.

    ![Importar dados do HDI Excel Power Query](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

8. Se desejar, você pode renomear os nomes das colunas. Quando estiver pronto, clique em **Fechar e Carregar**.  Os dados foram carregados em sua pasta de trabalho:

    ![Tabela importada do HDI Excel Power Query](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no banco de dados SQL do Azure. Também é possível carregar dados para o HDInsight. Para saber mais, consulte os seguintes artigos:

* [Visualizar dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregue os dados no HDInsight](./../hdinsight-upload-data.md).
