---
title: Expressões condicionais para o mecanismo de regras da CDN do Azure-Verizon Premium
description: A documentação de referência para a CDN do Azure do mecanismo de regras do Verizon Premium combina condições e recursos.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082966"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Expressões condicionais do mecanismo de regras do Azure CDN do Verizon Premium

Este artigo lista descrições detalhadas das expressões condicionais para o [mecanismo de regras](cdn-verizon-premium-rules-engine.md)da CDN (rede de distribuição de conteúdo) do Azure.

A primeira parte de uma regra é a Expressão condicional.

Expressão condicional | DESCRIÇÃO
-----------------------|-------------
IF | Uma expressão IF é sempre uma parte da primeira instrução em uma regra. Como todas as outras expressões condicionais, essa instrução IF deve ser associada a uma correspondência. Se nenhuma expressão condicional adicional for definida, essa correspondência determinará o critério que deve ser atendido antes que um conjunto de recursos possa ser aplicado a uma solicitação.
AND IF | Uma expressão AND IF só pode ser adicionada após os seguintes tipos de expressões condicionais: IF e AND IF. Ela indica que há outra condição que deve ser atendida para a instrução IF inicial.
ELSE IF| Uma expressão ELSE IF especifica uma condição de alternativa que deve ser atendida antes da ocorrência de um conjunto de recursos específicos a essa instrução ELSE IF. A presença de uma instrução ELSE IF indica o fim da instrução anterior. A única expressão condicional que pode ser colocada após uma instrução ELSE IF é outra instrução ELSE IF. Isso significa que uma instrução ELSE IF só pode ser usada para especificar uma única condição adicional que deve ser atendida.

**Exemplo**: ![Condição de correspondência CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Uma regra subsequente poderá substituir as ações especificadas por uma regra anterior.
   > Exemplo: uma regra capturar tudo protege todas as solicitações por meio da Autenticação baseada em Token. Outra regra pode ser criada diretamente abaixo dessa, para criar uma exceção para determinados tipos de solicitações.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Condições de correspondência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)