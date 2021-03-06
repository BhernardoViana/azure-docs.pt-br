---
title: GRAUS na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL de graus em Azure Cosmos DB para retornar o ângulo correspondente em graus para um ângulo especificado em radianos
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871322"
---
# <a name="degrees-azure-cosmos-db"></a>GRAUS (Azure Cosmos DB)
 Retorna o ângulo correspondente, em graus, para um ângulo especificado em radianos.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o número de graus em um ângulo de radiano PI/2.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
