---
title: Ação de script – instalar pacotes Python com o Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters Spark no HDInsight para usar pacotes Python externos.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: c8d0b172682654c858a97b4ca2df99ec5079adaa
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041142"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usar ação de script para instalar pacotes Python externos em notebooks Jupyter em clusters do Apache Spark no HDInsight
> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)
>
>

Saiba como usar Ações de Script para configurar um cluster Apache Spark no HDInsight (Linux) para usar pacotes **Python** externos enviados pela comunidade que não estão incluídos de fábrica no cluster.

> [!NOTE]
> Você também pode configurar um notebook Jupyter usando a mágica `%%configure` para usar pacotes externos. Para obter instruções, confira [Usar pacotes externos com notebooks Jupyter em clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Você pode pesquisar o [índice do pacote](https://pypi.python.org/pypi) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio de [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) ou [conda-forge](https://conda-forge.org/feedstocks/).

Neste artigo, você aprenderá a instalar o pacote [TensorFlow](https://www.tensorflow.org/) usando a Ação de Script no seu cluster e usá-lo por meio do Jupyter Notebook.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Se você ainda não tiver um cluster Spark no HDInsight Linux, poderá executar ações de script durante a criação do cluster. Acesse a documentação em [como usar ações de script personalizadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar pacotes externos com blocos de notas Jupyter

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Ações de Script** no painel esquerdo. Execute a ação personalizada que instala o TensorFlow nos nós principais e de trabalho. O script de bash pode ser referenciado a partir de: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.shAcessar a documentação em [como usar ações de script personalizadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Há duas instalações Python no cluster. O Spark usará a instalação do Python Anaconda que se encontra em `/usr/bin/anaconda/bin`. Faça referência a essa instalação em suas ações personalizadas por meio de `/usr/bin/anaconda/bin/pip` e `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Abrir um PySpark Jupyter Notebook

    ![Criar um novo bloco de anotações do Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Criar um novo bloco de anotações do Jupyter")

4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Fornecer um nome para o bloco de anotações")

5. Agora, você fará `import tensorflow` e executará um exemplo de hello world. 

    Código a ser copiado:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    O resultado é semelhante a:
    
    ![Execução de código TensorFlow](./media/apache-spark-python-package-installation/execution.png "Executar código TensorFlow")

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar pacotes externos com notebooks Jupyter em clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
