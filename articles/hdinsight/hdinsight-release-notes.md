---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha dicas e detalhes de desenvolvimento para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926417"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de código-fonte aberto Apache Hadoop e Apache Spark no Azure.

## <a name="release-date-11072019"></a>Data de lançamento: 11/07/2019

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao 4,0.

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Novos recursos

### <a name="hdinsight-identity-broker-hib-preview"></a>Agente de identidade do HDInsight (HIB) (visualização)

O HIB (agente de identidade do HDInsight) permite que os usuários entrem no Apache Ambari usando a MFA (autenticação multifator) e obtenham os tíquetes Kerberos necessários sem precisar de hashes de senha no Azure Active Directory Domain Services (AAD-DS). Atualmente, o HIB só está disponível para clusters implantados por meio do modelo ARM.

### <a name="kafka-rest-api-proxy-preview"></a>Proxy da API REST do Kafka (visualização)

O proxy da API REST do Kafka fornece uma implantação de um único clique do proxy REST altamente disponível com o cluster Kafka por meio da autorização do AAD e do protocolo OAuth seguros. 

### <a name="auto-scale"></a>Dimensionamento automático

O dimensionamento automático para o Azure HDInsight agora está disponível em todas as regiões para tipos de cluster Apache Spark e Hadoop. Esse recurso possibilita o gerenciamento de cargas de trabalho de análise de Big Data de forma mais econômica e produtiva. Agora você pode otimizar o uso de seus clusters HDInsight e pagar apenas pelo que precisa.

Dependendo dos seus requisitos, você poderá escolher entre o dimensionamento automático baseado em carga e baseado em agendamento. O dimensionamento automático baseado em carga pode dimensionar o tamanho do cluster para cima e para baixo com base nas necessidades do recurso atual, enquanto o dimensionamento automático baseado em agenda pode alterar o tamanho do cluster com base em uma agenda predefinida. 

O suporte a AutoEscala para a carga de trabalho do HBase e do LLAP também é visualização pública. Para obter mais informações, consulte [dimensionar automaticamente clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Gravações aceleradas do HDInsight para o Apache HBase 

As gravações aceleradas usam discos gerenciados do SSD premium do Azure para melhorar o desempenho do log de gravação antecipada do Apache HBase (WAL). Para obter mais informações, consulte [Gravações aceleradas do Azure HDInsight para o Apache HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes).

### <a name="custom-ambari-db"></a>BD Ambari personalizado

O HDInsight agora oferece uma nova capacidade para permitir que os clientes usem seu próprio BD SQL para Ambari. Agora, os clientes podem escolher o BD SQL correto para Ambari e atualizá-lo facilmente com base em seu próprio requisito de crescimento de negócios. A implantação é feita com um modelo de Azure Resource Manager. Para obter mais informações, consulte [Configurar clusters do HDInsight com um AMBARI DB personalizado](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>As máquinas virtuais da série F agora estão disponíveis com o HDInsight

As VMs (máquinas virtuais) da série F são boas opções para começar com o HDInsight com requisitos leves de processamento. A um preço de lista inferior por hora, a série F é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por vCPU. Para obter mais informações, consulte [selecionando o tamanho correto da VM para o cluster HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Reprovação

### <a name="g-series-virtual-machine-deprecation"></a>Substituição da máquina virtual da série G
Nesta versão, as VMs da série G não são mais oferecidas no HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Reprovação de máquina virtual Dv1
A partir desta versão, o uso de VMs Dv1 com o HDInsight foi preterido. Qualquer solicitação de cliente para Dv1 será servida com o Dv2 automaticamente. Não há nenhuma diferença de preço entre as VMs Dv1 e Dv2.

## <a name="behavior-changes"></a>Alterações de comportamento

### <a name="cluster-managed-disk-size-change"></a>Alteração do tamanho do disco gerenciado do cluster
O HDInsight fornece o espaço em disco gerenciado com o cluster. A partir desta versão, o tamanho do disco gerenciado de cada nó no novo cluster criado é alterado para 128 GB.

## <a name="upcoming-changes"></a>Alterações futuras
As seguintes alterações ocorrerão nas versões futuras. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrando para conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight agora usa máquinas virtuais do Azure para provisionar o cluster. A partir de dezembro, o HDInsight usará os conjuntos de dimensionamento de máquinas virtuais do Azure em vez disso. Veja mais sobre os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBase 2,0 a 2,1
Na próxima versão do HDInsight 4,0, a versão do HBase será atualizada da versão 2,0 para a 2,1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Reprovação de máquina virtual de série A para cluster ESP
As VMs da série a podem causar problemas de cluster ESP devido à capacidade relativamente baixa de CPU e memória. Na próxima versão, as VMs da série A serão preteridas para a criação de novos clusters ESP.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a tornar as melhorias de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração de versão do componente
Não há nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
