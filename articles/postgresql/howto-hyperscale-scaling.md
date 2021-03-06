---
title: Dimensionar grupo de servidores-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Ajuste os recursos de memória do grupo de servidores, disco e CPU para lidar com o aumento de carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 5d8bbe493887c5340f0943a585eb6ff250bd3728
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977549"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Dimensionar um grupo de servidores de hiperescala (Citus)

Banco de dados do Azure para PostgreSQL-Citus (hiperescala) fornece dimensionamento de autoatendimento para lidar com o aumento da carga. O portal do Azure facilita a adição de novos nós de trabalho e a aumentar a capacidade dos nós existentes.

## <a name="add-worker-nodes"></a>Adicionar nós de trabalho

Para adicionar nós, vá para a guia **Configurar** em seu grupo de servidores de hiperescala (Citus).  Arrastar o controle deslizante para **contagem de nós de trabalho** altera o valor.

![Controles deslizantes de recursos](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Clique no botão **salvar** para fazer o valor alterado entrar em vigor.

> [!NOTE]
> Depois de aumentado e salvo, o número de nós de trabalho não pode ser reduzido usando o controle deslizante.

### <a name="rebalance-shards"></a>Reequilibrar fragmentos

Para aproveitar os nós recém-adicionados, você deve reequilibrar os [fragmentos](concepts-hyperscale-distributed-data.md#shards)de tabela distribuída, o que significa mover alguns fragmentos de nós existentes para os novos. Primeiro, verifique se os novos trabalhadores concluíram com êxito o provisionamento. Em seguida, inicie o rebalanceador de fragmento, conectando-se ao nó de coordenador de cluster com psql e executando:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A função `rebalance_table_shards` reequilibra todas as tabelas no grupo de [colocalização](concepts-hyperscale-colocation.md) da tabela nomeada em seu argumento. Portanto, você não precisa chamar a função para cada tabela distribuída, basta chamá-la em uma tabela representativa de cada grupo de colocalização.

## <a name="increase-vcores-or-storage-space"></a>Aumentar vCores ou espaço de armazenamento

Além de adicionar novos nós, você pode aumentar os recursos dos nós existentes. Vá para a guia **Configurar** em seu grupo de servidores Citus (hiperescala) e arraste o controle deslizante para **vCores** e **armazenamento** para alterar esses valores para todos os nós de trabalho. Certifique-se de clicar em **salvar** para aplicar as alterações.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [as opções de desempenho](concepts-hyperscale-configuration-options.md)do grupo de servidores.
