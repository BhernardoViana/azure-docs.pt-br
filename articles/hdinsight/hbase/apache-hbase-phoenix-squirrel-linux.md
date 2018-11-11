---
title: Use o Apache Phoenix e o SQLLine com o HBase no Azure HDInsight
description: Saiba como usar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar o SQLLine no seu computador para se conectar a um cluster HBase no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: c87596099995cbd34a0ee32ec287de81ec741bda
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008055"
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Usar o Apache Phoenix com clusters do HBase baseados em Linux no HDInsight
Saiba como usar o [Apache Phoenix](http://phoenix.apache.org/) no Azure HDInsight e como usar o SQLLine. Para obter mais informações sobre o Phoenix, consulte [Phoenix em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para conhecer a gramática do Phoenix, consulte [Gramática do Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Para obter informações sobre a versão do Phoenix no HDInsight, consulte [Novidades nas versões de cluster Hadoop fornecidas pelo HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Usar o SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder usar o SQLLine, você deve ter os seguintes itens:

* **Um cluster HBase no HDInsight**. Para criar um, veja [Introdução ao Apache HBase no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

Ao se conectar a um cluster HBase, é necessário se conectar a uma das VMs do ZooKeeper. Cada cluster HDInsight tem três VMs do ZooKeeper.

**Para descobrir o nome do host do ZooKeeper**

1. Abra o Ambari em **https://\<nome do cluster\>.azurehdinsight.net**.
2. Para se conectar, insira o nome de usuário HTTP (cluster) e a senha.
3. No menu à esquerda, selecione **ZooKeeper**. Três instâncias do **Servidor do ZooKeeper** serão listadas.
4. Clique em uma das instâncias do **Servidor do ZooKeeper** listadas. No painel **Resumo**, encontre o **Nome do host**. Ele é semelhante a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Usar SQLLine**

1. Conecte-se ao cluster usando o SSH. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. No SSH, use os seguintes comandos para executar o SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Execute os seguintes comandos para criar uma tabela do HBase e insira alguns dados:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Para obter mais informações, consulte o [Manual do SQLLine](http://sqlline.sourceforge.net/#manual) e [Gramática do Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Apache Phoenix no HDInsight. Para saber mais, consulte os seguintes artigos:

* [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].
  O HBase é um banco de dados NoSQL de código aberto Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
* [Provisionar clusters HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet].
  Com a integração da rede virtual, os clusters HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
* [Configurar a replicação do HBase no HDInsight](apache-hbase-replication.md). Saiba como configurar a replicação do HBase em dois datacenters Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


