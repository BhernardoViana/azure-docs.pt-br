---
title: Carregar dados em ambientes de armazenamento do Azure – Processo de Ciência de Dados de Equipe
description: Mover dados de e para o Armazenamento de Blobs do Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 56c45bf6db79ded64574a44399712951e82c1c3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303580"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados em ambientes de armazenamento para análise

O Processo de Ciência de Dados de Equipe exige que os dados sejam incluídos ou carregados em uma variedade de ambientes de armazenamento diferentes para serem processados ou analisados da maneira mais apropriada em cada estágio do processo. Os destinos de dados geralmente usados para processamento incluem Armazenamento de Blobs do Azure, bancos de dados do SQL Azure, SQL Server na VM do Azure, HDInsight (Hadoop) e Azure Machine Learning. 

Os artigos a seguir descrevem como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.

* Para/Do [Armazenamento de Blobs do Azure](move-azure-blob.md)
* Para [SQL Server na VM do Azure](move-sql-server-virtual-machine.md)
* Para [Banco de Dados SQL do Azure](move-sql-azure.md)
* Para [Tabelas do Hive](move-hive-tables.md)
* Para [Tabelas particionadas SQL](parallel-load-sql-partitioned-tables.md)
* Do [SQL Server local](move-sql-azure-adf.md)

As necessidades técnicas e de negócios, bem como a localização inicial, o formato e o tamanho dos dados determinarão os ambientes de destino nos quais os dados precisarão ser ingeridos para atingir as metas da análise. Não é incomum para um cenário exigir dados a serem movidos entre vários ambientes para atingir a variedade de tarefas necessárias para construir um modelo de previsão. Essa sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpar, redução da resolução e treinamento de modelo.
