---
title: Chaves exclusivas no Azure Cosmos DB | Microsoft Docs
description: Saiba como usar chaves exclusivas em seu Banco de dados do Azure Cosmos DB.
services: cosmos-db
keywords: restrição de chave exclusiva, violação de restrição de chave exclusiva
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: rafats
ms.openlocfilehash: 5811cb1e08ed5d02038da2a4460ae4b63580833b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696132"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Chaves exclusivas no Azure Cosmos DB

Chaves exclusivas oferecem aos desenvolvedores a capacidade de adicionar uma camada de integridade dos dados ao seu banco de dados. Ao criar uma política de chave exclusiva quando um contêiner for criado, você garante a exclusividade de um ou mais valores por [chave de partição](partition-data.md). Depois que um contêiner foi criado com uma política de chave exclusiva, será impedida a criação de itens novos ou atualizados com valores que duplicam valores especificados pela restrição de chave exclusiva.   

> [!NOTE]
> Chaves exclusivas têm suporte nas versões mais recentes de SDKs SQL do [.NET](sql-api-sdk-dotnet.md) e [.NET Core](sql-api-sdk-dotnet-core.md) e [API do MongoDB](mongodb-feature-support.md#unique-indexes). Atualmente, a API de Tabela e API do Gremlin não dão suporte a chaves exclusivas. 
> 
>

## <a name="use-case"></a>Caso de uso

Como exemplo, vamos ver como um banco de dados do usuário associado a um [aplicativo social](use-cases.md#web-and-mobile-applications) poderia se beneficiar de uma política de chave exclusiva em endereços de email. Ao fazer do endereço de email do usuário uma chave exclusiva, você assegura que cada registro tenha um endereço de email exclusivo, e nenhum novo registro pode ser criado com endereços de email duplicados. 

Se você realmente desejar que os usuários possam criar diversos registros com o mesmo endereço de email, mas não com o mesmo nome, sobrenome e endereço de email, você pode adicionar outros caminhos para a política de chave exclusiva. Portanto, em vez de criar uma chave exclusiva baseada em um endereço de email, você pode criar uma chave exclusiva que seja uma combinação do nome, sobrenome e email. Nesse caso, cada combinação exclusiva dos três caminhos é permitida, de modo que o banco de dados pode conter itens que tenham os valores de caminho a seguir. Cada um desses registros seria aprovado pela política de chave exclusiva.  

**Valores permitidos para uma chave exclusiva de firstName (nome), lastName (sobrenome) e email**

|Nome|Sobrenome|Endereço de email|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Se você tentasse inserir outro registro com qualquer uma das combinações listadas na tabela acima, receberia um erro indicando que a restrição de chave exclusiva não foi atendida. O erro que o Azure Cosmos DB retorna é "Recurso com a ID ou o nome especificado já existe." ou "Recurso com a id, o nome ou índice exclusivo especificados já existe." 

## <a name="using-unique-keys"></a>Usando chaves exclusivas

Chaves exclusivas devem ser definidas quando o contêiner for criado, e a chave exclusiva tem seu escopo definido para a chave de partição. Para compilar o exemplo anterior, se você particionar baseado no CEP, os registros da tabela podem acabar sendo duplicados em cada partição.

Contêineres existentes não podem ser atualizados para usar chaves exclusivas.

Depois que um contêiner é criado com uma política de chave exclusiva, a política não pode ser alterada, a menos que o contêiner seja recriado. Se você tiver dados existentes aos quais gostaria de implementar chaves exclusivas, crie o novo contêiner e depois use a ferramenta de migração de dados apropriada para mover os dados para o novo contêiner. Para contêineres do SQL, use a [Ferramenta de migração de dados](import-data.md). Para contêineres do MongoDB, use [mongoimport.exe ou mongorestore.exe](mongodb-migrate.md).

Pode ser incluído um máximo de 16 valores de caminho (por exemplo, /firstName, /lastName, /address/zipCode etc.) em cada chave exclusiva. 

Cada política de chave exclusiva pode ter no máximo 10 combinações ou restrições de chaves exclusivas e os caminhos combinados para todas as propriedades de índice exclusivo não devem exceder 60 caracteres. Assim, o exemplo anterior que usa nome, sobrenome e endereço de email é apenas uma restrição e usa três dos 16 possíveis caminhos disponíveis. 

Encargos de solicitação de unidade para criar, atualizar e excluir um item são um pouco mais altos quando há uma política de chave exclusiva no contêiner. 

Não há suporte para chaves exclusivas esparsas. Se estiverem faltando valores para alguns caminhos exclusivos, eles são tratados como um valor nulo especial, o qual participa da restrição de exclusividade.

## <a name="sql-api-sample"></a>Exemplo de API do SQL

O exemplo de código a seguir mostra como criar um contêiner do SQL novo com duas restrições de chave exclusivas. A primeira restrição é a de firstName, lastName e email descrita no exemplo anterior. A segunda restrição é o endereço/CEP dos usuários. Um arquivo JSON de exemplo que usa os caminhos dessa política de chave exclusiva segue o exemplo de código. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
          }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Exemplo de documento JSON.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> Observe que o nome da chave exclusiva diferencia maiúsculas de minúsculas. Conforme mostrado no exemplo acima, o nome exclusivo é definido para /address/zipcode. Se os dados tiverem um ZipCode, "null" será inserido na chave exclusiva como CEP diferente de ZipCode. Devido a essa diferenciação de maiúsculas de minúsculas, todos os outros registros com ZipCode que não poderão ser inseridos como "null" duplicado violarão a restrição de chave exclusiva.

## <a name="mongodb-api-sample"></a>Exemplo de API do MongoDB

O exemplo de comando a seguir mostra como criar um índice exclusivo nos campos firstName, lastName e email da coleção de usuários para a API do MongoDB. Isso garante a exclusividade para uma combinação de todos os três campos em todos os documentos na coleção. Para coleções de API do MongoDB, o índice exclusivo é criado depois de a coleção ser criada, mas antes de preencher a coleção.

> [!NOTE]
> O formato de chave exclusivo para contas da API do MongoDB é diferente das contas de API do SQL, onde você não precisa especificar o caractere de barra invertida (/) antes do nome do campo. 

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Configurar chaves exclusivas usando o portal do Azure

Nas seções acima, você encontrará exemplos de código que mostram como definir restrições de chave exclusivas quando uma coleção é criada usando a API de SQL ou a API do MongoDB. Mas também é possível definir chaves exclusivas quando você cria uma coleção por meio da interface do usuário da Web no portal do Azure. 

- Navegue até o **Data Explorer** na sua conta do Cosmos DB
- Clique em **Nova Coleção**
- Na seção de chaves exclusiva, **é possível adicionar as restrições de chave exclusiva desejadas clicando em **Adicionar chave exclusiva**

![Definir chaves exclusivas no Data Explorer](./media/unique-keys/unique-keys-azure-portal.png)

- Se você deseja criar uma restrição de chave exclusiva no caminho lastName, adicione `/lastName`.
- Se você deseja criar uma restrição de chave exclusiva na combinação lastName firstName, adicione `/lastName,/firstName`

Quando terminar, clique em **OK** para criar a coleção.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar chaves exclusivas para itens em um banco de dados. Se você estiver criando um contêiner pela primeira vez, analise [Particionando dados no Azure Cosmos DB](partition-data.md), uma vez que chaves exclusivas e chaves de partição dependem umas das outras. 


