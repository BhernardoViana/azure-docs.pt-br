---
title: Escala de aumento do Apache Kafka - Azure HDInsight
description: Saiba como configurar discos gerenciados para o cluster Apache Kafka no Azure HDInsight para aumentar a escalabilidade.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9ad7330960540e0bddc0130736265df402d582ff
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009311"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurar o armazenamento e a escalabilidade para o Apache Kafka no HDInsight

Saiba como configurar o número de discos gerenciados usados pelo Apache Kafka no HDInsight.

O Kafka no HDInsight usa o disco local das máquinas virtuais no cluster HDInsight. Como o Kafka tem E/S bastante pesadas, os [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) são usados para fornecer a alta taxa de transferência e fornecer mais armazenamento por nó. Se os discos rígidos virtuais (VHD) tradicionais tiverem sido usados para o Kafka, cada nó estará limitado a 1 TB. Com os discos gerenciados, você pode usar vários discos para alcançar 16 TB para cada nó no cluster.

O diagrama a seguir fornece uma comparação entre o Kafka no HDInsight antes dos discos gerenciados e o Kafka no HDInsight com os discos gerenciados:

![Diagrama mostrando o Kafka no HDInsight usando um único vhd por vm versus vários discos gerenciados por vm](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Configurar discos gerenciados: portal do Azure

1. Siga as etapas de [Criar um cluster HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md) para compreender as etapas comuns para criar um cluster usando o portal. Não conclua o processo de criação do portal.

2. Na seção __Tamanho do cluster__, use o campo __Discos por nó de trabalho__ para configurar o número de discos.

    > [!NOTE]
    > O tipo de disco gerenciado pode ser __Standard__ (HDD) ou __Premium__ (SSD). Os discos Premium são usados com as VMs das séries DS e GS. Todos os outros tipos VM usam o padrão.

    ![Imagem da seção de tamanho de cluster com os discos por nó de trabalho realçados](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Configurar discos gerenciados: modelo do Gerenciador de Recursos

Para controlar o número de discos usados por nós de trabalho em um cluster do Kafka, use a seção a seguir do modelo:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Você pode encontrar um modelo completo que demonstra como configurar discos gerenciados em [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como trabalhar com o Kafka no HDInsight, veja os seguintes documentos:

* [Usar MirrorMaker para criar uma réplica de Kafka no HDInsight](apache-kafka-mirroring.md)
* [Usar Apache Storm com Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Usar o Apache Spark com o Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Conectar-se ao Kafka no HDInsight (preview) por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)

* [Blog do HDInsight em discos gerenciados com o Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
