---
title: Saiba como configurar e gerenciar a Vida Útil no Azure Cosmos DB
description: Saiba como configurar e gerenciar a vida útil em um contêiner e um item no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 51022956ad32059771b8d56f0ae177fb5165b450
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873600"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Configurar a vida útil no Azure Cosmos DB

No Azure Cosmos DB, você pode optar por configurar a TTL (vida útil) no nível do contêiner ou substituí-la no nível de um item após a definição do contêiner. Você pode configurar a TTL para um contêiner usando o portal do Azure ou os SDKs específicos da linguagem. As substituições de TTL no nível do item podem ser configuradas usando os SDKs.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Habilitar vida útil em um contêiner usando o portal do Azure

Use as etapas a seguir para habilitar a vida útil de um contêiner sem prazo de expiração. Habilite essa opção para permitir que a TTL seja substituída no nível do item. Você também pode definir a TTL inserindo um valor diferente de zero para os segundos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Cosmos ou selecione uma conta existente.

3. Abra o painel **Data Explorer**.

4. Selecione um contêiner existente, expanda-o e modifique os valores abaixo:

   * Abra a janela **Escala e Configurações**.
   * Em **Configuração**, localize **Vida Útil**.
   * Selecione **Ativado (não há padrão)** ou selecione **Ativado** e defina um valor de TTL
   * Clique em **Salvar** para salvar as alterações.

   ![Configurar a vida útil no portal do Azure](./media/how-to-time-to-live/how-to-time-to-live-portal.png)

* Quando DefaultTimeToLive é nulo, sua vida útil está desativada
* Quando DefaultTimeToLive é -1, sua configuração de vida útil está ativada (não padrão)
* Quando DefaultTimeToLive tem qualquer outro valor inteiro (exceto 0), a configuração de vida útil está ativada

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Habilitar a vida útil em um contêiner usando CLI do Azure ou o PowerShell

Para criar ou habilitar TTL em um contêiner, consulte

* [Criar um contêiner com TTL usando CLI do Azure](manage-with-cli.md#create-a-container-with-ttl)
* [Criar um contêiner com TTL usando o PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Habilitar vida útil em um contêiner usando o SDK

### <a id="dotnet-enable-noexpiry"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

### <a id="dotnet-enable-noexpiry"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Definir a vida útil em um contêiner usando o SDK

Para definir a vida útil de um contêiner, você precisará fornecer um número positivo diferente de zero que indica o período de tempo em segundos. Com base no valor de TTL configurado, todos os itens no contêiner após a última modificação do carimbo de hora do item `_ts` serão excluídos.

### <a id="dotnet-enable-withexpiry"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

### <a id="dotnet-enable-withexpiry"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
});
```

### <a id="nodejs-enable-withexpiry"></a>SDK do NodeJS

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Definir a vida útil de um item

Além de definir uma vida útil padrão de um contêiner, você pode definir a vida útil de um item. A definição da vida útil no nível do item substituirá a TTL padrão do item no contêiner.

* Para definir a TTL em um item, é necessário fornecer um número positivo diferente de zero que indique o período, em segundos, em que o item expira após a modificação de seu último carimbo de data/hora (`_ts`).

* Se o item não tiver um campo TTL, por padrão, a TTL definida para o contêiner será aplicada ao item.

* Se a TTL estiver desabilitada no nível do contêiner, o campo TTL no item será ignorado até que a TTL seja habilitada novamente no contêiner.

### <a id="portal-set-ttl-item"></a>Portal do Azure

Use as etapas a seguir para habilitar a vida útil de um item:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Cosmos ou selecione uma conta existente.

3. Abra o painel **Data Explorer**.

4. Selecione um contêiner existente, expanda-o e modifique os valores abaixo:

   * Abra a janela **Escala e Configurações**.
   * Em **Configuração**, localize **Vida Útil**.
   * Selecione **Ativado (não há padrão)** ou selecione **Ativado** e defina um valor de vida útil. 
   * Clique em **Salvar** para salvar as alterações.

5. Em seguida, navegue até o item para o qual você deseja definir a vida útil, adicione a propriedade `ttl` e selecione **Atualizar**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>SDK do .NET (qualquer)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>SDK do NodeJS

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

## <a name="reset-time-to-live"></a>Redefinir tempo de vida útil

Você pode redefinir a vida útil de um item executando uma operação de gravação ou atualização no item. A operação de gravação ou atualização definirá o `_ts` como a hora atual, e a TTL até que o item expire será iniciada novamente. Se você quiser alterar a TTL de um item, poderá atualizar o campo exatamente como atualizaria qualquer outro campo.

### <a id="dotnet-extend-ttl-item"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-extend-ttl-item"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="turn-off-time-to-live"></a>Desativar a vida útil

Se a vida útil foi definida em um item e você não deseja mais que esse item expire, acesse o item, remova o campo TTL e substitua o item no servidor. Quando o campo TTL for removido do item, o valor TTL padrão atribuído ao contêiner será aplicado ao item. Defina o valor de TTL como -1 para impedir que um item expire e não herde o valor de TTL do contêiner.

### <a id="dotnet-turn-off-ttl-item"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

### <a id="dotnet-turn-off-ttl-item"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```

## <a name="disable-time-to-live"></a>Desabilitar a vida útil

Para desabilitar a vida útil de um contêiner e interromper o processo em segundo plano de verificação de itens expirados, a `DefaultTimeToLive` no contêiner deve ser excluída. A exclusão dessa propriedade é diferente de defini-la como -1. Quando você a define como -1, novos itens adicionados ao contêiner terão vida útil eterna; no entanto, você pode substituir esse valor em itens específicos no contêiner. Quando você remover a propriedade de vida útil do contêiner, os itens nunca expirarão, mesmo se tiverem substituído explicitamente o valor de vida útil padrão anterior.

### <a id="dotnet-disable-ttl"></a>SDK do .NET v2 (Microsoft. Azure. DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

### <a id="dotnet-disable-ttl"></a>SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre vida útil no seguinte artigo:

* [Vida útil](time-to-live.md)
