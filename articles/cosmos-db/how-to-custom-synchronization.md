---
title: Como implementar a sincronização personalizada para otimização visando maior disponibilidade e desempenho no Azure Cosmos DB
description: Saiba como implementar a sincronização personalizada para otimização visando maior disponibilidade e desempenho no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: f10e260432a93a0413d65d6f5814d00a50e9465a
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560278"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementar a sincronização personalizada para otimização visando maior disponibilidade e desempenho

O Azure Cosmos DB oferece [cinco níveis de consistência bem definidos](consistency-levels.md) para sua escolha visando equilibrar a compensação entre consistência, desempenho e disponibilidade. A coerência forte ajuda a garantir que os dados sejam replicados de forma síncrona e persistidos de maneira durável em todas as regiões nas quais a conta do Azure Cosmos está disponível. Essa configuração fornece o mais alto nível de durabilidade, mas ocorre em detrimento do desempenho e da disponibilidade. Se você quer que seu aplicativo controle ou diminua a durabilidade dos dados de acordo com a necessidade do aplicativo sem comprometer a disponibilidade, use a *sincronização personalizada* na camada de aplicativo para atingir o nível desejado de durabilidade.

A imagem a seguir ilustra o modelo de sincronização personalizada:

![Sincronização personalizada](./media/how-to-custom-synchronization/custom-synchronization.png)

Neste cenário, um contêiner do Azure Cosmos é replicado globalmente em várias regiões e em vários continentes. O uso da coerência forte em todas as regiões neste cenário afeta o desempenho. Para garantir um nível mais alto de durabilidade dos dados sem comprometer a latência de gravação, o aplicativo poderá usar dois clientes que compartilham o mesmo [token de sessão](how-to-manage-consistency.md#utilize-session-tokens).

O primeiro cliente pode gravar os dados na região local (por exemplo, Oeste dos EUA). O segundo cliente (por exemplo, no Leste dos EUA) é um cliente de leitura que é usado para garantir a sincronização. Com o fluxo do token de sessão da resposta de gravação para a leitura a seguir, a leitura garantirá a sincronização de gravações para o Leste dos EUA. O Azure Cosmos DB garante que as gravações sejam vistas por, pelo menos, uma região. Há a garantia de sobreviver a uma interrupção regional, caso a região de gravação original ficar inativa. Nesse cenário, todas as gravações são sincronizadas no Leste dos EUA, reduzindo a latência de empregar a coerência forte em todas as regiões. Em um cenário de vários mestres, em que as gravações ocorrem em todas as regiões, você pode estender esse modelo para sincronização em várias regiões em paralelo.

## <a name="configure-the-clients"></a>Configurar os clientes

O exemplo a seguir mostra uma camada de acesso a dados que instancia dois clientes para a sincronização personalizada:

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementar sincronização personalizada

Depois que os clientes forem inicializados, o aplicativo poderá executar gravações na região local (Oeste dos EUA) e forçar a sincronização das gravações no Leste dos EUA, conforme mostrado a seguir.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

Você pode estender o modelo para sincronização em várias regiões em paralelo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a distribuição global e a consistência no Azure Cosmos DB, leia estes artigos:

* [Escolher o nível de consistência correto no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md)
* [Particionamento e distribuição de dados no Azure Cosmos DB](partition-data.md)
