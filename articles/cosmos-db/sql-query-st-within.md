---
title: ST_WITHIN na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_WITHIN no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40ecb26e7ac782d7831e6ef94c9d3cfc6a370cbb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349336"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Retorna uma expressão booliana que indica se o objeto GeoJSON (Ponto, Polígono ou LineString) especificado no primeiro argumento está dentro do GeoJSON (Ponto, Polígono ou LineString) no segundo argumento.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão de objeto de ponto geojson, polígono ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor booliano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como localizar todos os documentos da família em um polígono usando `ST_WITHIN`.  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
