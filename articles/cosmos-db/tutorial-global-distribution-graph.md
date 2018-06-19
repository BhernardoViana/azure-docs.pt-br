---
title: Tutorial de distribuição global do Azure Cosmos DB para a API do Graph | Microsoft Docs
description: Saiba como configurar a distribuição global do Azure Cosmos DB usando a API do Graph.
services: cosmos-db
keywords: distribuição global, graph, gremlin
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 61004a735f731cfd1303cf40b6e60cba496e942a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763673"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Configurar a distribuição global do Azure Cosmos DB usando a API do Graph

Neste artigo, mostramos como usar o Portal do Azure para configurar a distribuição local do Azure Cosmos DB e, em seguida, conectar-se usando a API do Graph.

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando as [APIs do Graph](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Conectar-se a uma região preferencial usando a API do Graph com o SDK do .NET

A API do Graph é exposta como uma biblioteca de extensões sobre a API do SQL.

Para aproveitar a [distribuição global](distribute-data-globally.md), os aplicativos cliente podem especificar a lista de preferências ordenadas de regiões a serem usadas para executar operações de documento. Isso pode ser feito definindo a política de conexão. Com base na configuração da conta do Azure Cosmos DB, na disponibilidade regional atual e na lista de preferências especificada, o ponto de extremidade mais adequado será escolhido pelo SDK para executar operações de gravação e leitura.

Essa lista de preferências é especificada ao inicializar uma conexão usando os SDKs. Os SDKs aceitam um parâmetro opcional "PreferredLocations", que é uma lista ordenada de regiões do Azure.

* **Gravações**: O SDK envia automaticamente todas as gravações para a região de gravação atual.
* **Leituras**: Todas as leituras são enviadas para a primeira região disponível na lista PreferredLocations. Se a solicitação falhar, o cliente não fará o envio para a próxima região da lista, e assim por diante.  Os SDKs só tentam fazer a leitura de regiões especificadas na lista PreferredLocations. Portanto, por exemplo, se a conta do Cosmos DB estiver disponível em três regiões, mas o cliente especificar apenas duas das regiões de não gravação para PreferredLocations, nenhuma leitura será atendida fora da região de gravação, mesmo no caso de failover.

O aplicativo pode verificar o ponto de extremidade de gravação e o ponto de extremidade de leitura atuais escolhidos pelo SDK marcando duas propriedades, WriteEndpoint e ReadEndpoint, disponíveis no SDK versão 1.8 e superiores. Se a propriedade PreferredLocations não estiver definida, todas as solicitações serão atendidas da região de gravação atual.

### <a name="using-the-sdk"></a>Usar o SDK

Por exemplo, no SDK do .NET, o parâmetro `ConnectionPolicy` para o construtor `DocumentClient` tem uma propriedade chamada `PreferredLocations`. Essa propriedade pode ser definida para uma lista de nomes de região. Os nomes de exibição para [Regiões do Azure][regions] podem ser especificados como parte de `PreferredLocations`.

> [!NOTE]
> As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizá-las a qualquer momento. O SDK lida com essa alteração automaticamente.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando as APIs do SQL

Agora você pode prosseguir para o próximo tutorial e aprender a desenvolver localmente usando o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

