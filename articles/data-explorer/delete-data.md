---
title: Excluir dados do Azure Data Explorer
description: Este artigo descreve os cenários de exclusão em massa no Azure Data Explorer, incluindo limpeza e exclusões com base em retenção.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9c1b21e119a38c6d306b9c564ab7958ba21a1c41
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445661"
---
# <a name="delete-data-from-azure-data-explorer"></a>Excluir dados do Azure Data Explorer

O Azure Data Explorer dá suporte a várias abordagens de exclusão em massa, que abordamos neste artigo. Ele não dá suporte à exclusão por registro em tempo real, porque é otimizado para acesso rápido para leitura.

* Se uma ou mais tabelas não forem necessárias, exclua-as usando os comandos “soltar tabela” ou “soltar tabelas”.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Se os dados antigos não forem mais necessários, exclua-os alterando o período de retenção no nível do banco de dados ou tabela.

    Considere um banco de dados ou tabela que é definida para 90 dias de retenção. As necessidades de negócios mudam, então agora somente 60 dias de dados são necessários. Nesse caso, exclua os dados mais antigos de uma das seguintes maneiras.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Para obter mais informações, consulte [Política de retenção](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

Se você precisar de auxílio com problemas de exclusão de dados, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
