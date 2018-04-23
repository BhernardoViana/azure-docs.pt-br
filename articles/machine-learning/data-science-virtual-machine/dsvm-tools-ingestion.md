---
title: Ferramentas de ingestão de dados da Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Ferramentas de ingestão de dados de Máquina Virtual de Ciência de Dados
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 7aeb0476fffb8c9e5cf2b0b5d89a2a387bd6364a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Ferramentas de ingestão de dados de Máquina Virtual de Ciência de Dados

Uma das primeiras etapas técnicas em um projeto de AI ou ciência de dados é identificar os conjuntos de dados a serem usados e colocá-los em seu ambiente de análise. A DSVM (Máquina Virtual de Ciência de Dados) oferece ferramentas e bibliotecas para trazer dados de diferentes origens para um armazenamento de dados analíticos localmente na DSVM ou em uma plataforma de dados na nuvem ou local. 

Aqui estão algumas ferramentas de movimentação de dados que fornecemos na DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados dos blobs de armazenamento do Azure para o Azure Data Lake Store. Você também pode copiar dados entre contas do Azure Data Lake Store.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importar de vários blobs do armazenamento do Azure para o Azure Data Lake Store.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `adlcopy` para obter ajuda.    |
| Links para exemplos      | [Como usar AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Ferramentas relacionadas ao DSVM      | AzCopy, Linha de Comando do Azure     |

## <a name="azure-command-line"></a>Linha de comando do Azure

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta de gerenciamento para o Azure. Também contém verbos de comando para mover dados de plataformas de dados do Azure, como blobs de armazenamento do Azure, Armazenamento do Azure Data Lake     |
| Versões do DSVM com suporte      | Windows, Linux     |
| Usos típicos      | Importar e exportar dados para e do armazenamento do Azure, Azure Data Lake Store      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `az` para obter ajuda.    |
| Links para exemplos      | [Usar a CLI do Azure](https://docs.microsoft.com/cli/azure)     |
| Ferramentas relacionadas ao DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para copiar dados de e para arquivos locais, blobs de armazenamento do Azure, arquivos e tabelas.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Copiar arquivos para o armazenamento de blobs, copiar blobs entre contas.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `azcopy` para obter ajuda.    |
| Links para exemplos      | [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Ferramentas relacionadas ao DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Ferramenta de migração de dados do Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta para importar dados de várias fontes, incluindo arquivos JSON, arquivos CSV, SQL, MongoDB, armazenamento de Tabela do Azure, Amazon DynamoDB e coleções de API de SQL do Azure Cosmos DB no Azure Cosmos DB.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importar arquivos de uma VM para CosmosDB, importar dados do armazenamento de tabela do Azure para CosmosDB ou importar dados de um banco de dados SQL Server para CosmosDB.     |
|  Como usar/executar?    |   Para usar a versão de linha de comando, abra um prompt de comando e digite `dt`. Para usar a ferramenta de GUI, abra um prompt de comando e digite `dtui`.    |
| Links para exemplos      | [Dados de importação do CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Ferramentas relacionadas ao DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta SQL Server para copiar dados entre o SQL Server e um arquivo de dados.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Importar um arquivo CSV para uma tabela do SQL Server, exportar uma tabela do SQL Server para um arquivo.      |
|  Como usar/executar?    |   Abra um prompt de comando e digite `bcp` para obter ajuda.    |
| Links para exemplos      | [Utilitário de Cópia em Massa](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Ferramentas relacionadas ao DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para montar um contêiner de blobs do Azure no sistema de arquivos Linux.      |
| Versões do DSVM com suporte      | Linux      |
| Usos típicos      | Leitura e gravação para blobs em um contêiner      |
|  Como usar/executar?    |   Execute _blobfuse_ em um terminal.    |
| Links para exemplos      | [blobfuse no GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Ferramentas relacionadas ao DSVM      | Linha de comando do Azure      |


## <a name="microsoft-data-management-gateway"></a>Gateway de Gerenciamento de Dados da Microsoft

|    |           |
| ------------- | ------------- |
| O que é?   | Uma ferramenta para conectar fontes de dados locais a serviços de nuvem para consumo.      |
| Versões do DSVM com suporte      | Windows      |
| Usos típicos      | Conectar uma VM a uma fonte de dados local.      |
|  Como usar/executar?    |   Inicie "Gateway de Gerenciamento de Dados Microsoft" no Menu Iniciar.    |
| Links para exemplos      | [Gateway de gerenciamento de dados](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Ferramentas relacionadas ao DSVM      | AzCopy, AdlCopy, bcp    |
