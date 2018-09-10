---
title: Use a consulta interativa com o Azure HDInsight
description: Saiba como usar a Consulta Interativa (Hive LLAP) com o HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: e83d51a18c7ab5861699114e4622bda167dab41d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042090"
---
# <a name="use-interactive-query-with-hdinsight"></a>Usar a Consulta Interativa com o HDInsight
Consulta Interativa (também chamado Hive LLAP ou [Processamento Analítico de Baixa Latência](https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um [tipo de cluster do Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). A Consulta Interativa dá suporte ao cache na memória, o que torna as consultas do Hive mais rápidas e muito mais interativas.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Um cluster de Consulta Interativa é diferente de um cluster Hadoop. Ele contém apenas o serviço do Hive. 

> [!NOTE]
> É possível acessar o serviço do Hive no cluster da Consulta Interativa somente por meio da exibição do Hive do Ambari, do Beeline e do driver Microsoft Hive Open Database Connectivity (Hive ODBC). Não é possível acessá-lo por meio do console do Hive, Templeton, a CLI do Azure (ferramenta de linha de comando do Azure) ou Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Criar um cluster de Consulta Interativa
Para obter mais informações sobre a criação de um cluster HDInsight, veja [Criar clusters Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Escolha o tipo de cluster Consulta Interativa.

## <a name="execute-hive-queries-from-interactive-query"></a>Executar consultas de Hive da Consulta Interativa
Para executar consultas de Hive, você tem as seguintes opções:

* Usar o Power BI

    Consulte [Visualizar dados de Hive de consulta interativa com o Power BI no Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Consulte [Visualizar Big Data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Usar Zeppelin

    Veja [Usar Zeppelin para executar consultas do Hive no Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).

* Usar o Virtual Studio

    Veja [Conectar-se ao Azure HDInsight e executar consultas Hive usando Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Usar o Visual Studio Code

    Veja [Usar o Visual Studio Code para o Hive, o LLAP ou o pySpark](../hdinsight-for-vscode.md).
* Execute o Hive usando a exibição do Hive do Ambari.
  
    Veja [Usar a Exibição do Hive com o Hadoop no Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Execute o Hive usando o Beeline.
  
    Veja [Usar o Hive com o Hadoop no HDInsight com Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    É possível usar o Beeline no nó de cabeçalho ou em um nó de borda vazio. Recomendamos usar o Beeline em um nó de borda vazio. Para obter informações sobre como criar um cluster HDInsight usando um nó de borda vazio, consulte [Use empty edge nodes in HDInsight](../hdinsight-apps-use-edge-node.md) (Usar nós de borda vazios no HDInsight).
* Execute o Hive usando o ODBC do Hive.
  
    Veja [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Para localizar a cadeia de conexão do JDBC (Java Database Connectivity):

1. Entre no Ambari usando a seguinte URL: https://\<nome do cluster\>.AzureHDInsight.net.
2. No menu à esquerda, selecione **Hive**.
3. Para copiar a URL, selecione o ícone da área de transferência:
   
   ![JDBC LLAP da Consulta Interativa no Hadoop HDInsight](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar clusters da Consulta Interativa no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Saiba como [visualizar Big Data com o Power BI no Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Saiba como [usar Zeppelin para executar consultas do Hive no Azure HDInsight ](../hdinsight-connect-hive-zeppelin.md).
* Saiba como [executar consultas Hive usando as Ferramentas do Data Lake para o Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Aprenda como [usar as Ferramentas do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* Saiba como [usar a Exibição do Hive com o Hadoop no HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Saiba como [usar o Beeline para enviar consultas de Hive no HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Saiba como [conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

