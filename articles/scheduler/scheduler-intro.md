---
title: O que é o Agendador do Azure? | Microsoft Docs
description: Saiba como criar, agendar e executar tarefas automatizadas que chamam serviços dentro ou fora do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300896"
---
# <a name="what-is-azure-scheduler"></a>O que é o Agendador do Azure?

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível.

O [Agendador do Azure](https://azure.microsoft.com/services/scheduler/) ajuda você a criar [trabalhos](../scheduler/scheduler-concepts-terms.md) que são executados na nuvem, descrevendo declarativamente ações. O serviço então agenda e executa automaticamente essas ações. Por exemplo, você pode chamar serviços dentro e fora do Azure, como chamar pontos de extremidade HTTP ou HTTPS, e também publicar mensagens em filas de Armazenamento do Azure e filas ou tópicos do Barramento de Serviço do Azure. Você pode executar trabalhos imediatamente ou em um momento posterior. O Agendador dá facilmente suporte para [agendas complexas e recorrência avançadas](../scheduler/scheduler-advanced-complexity.md). O Agendador especifica quando executar trabalhos, mantém um histórico dos resultados do trabalho que você pode examinar e, em seguida, agenda de modo previsível e confiável cargas de trabalho para serem executadas.

Embora você possa usar o Agendador para criar, manter e executar cargas de trabalho agendadas, o Agendador não hospeda cargas de trabalho nem executa código. O serviço apenas *invoca* os serviços ou o código hospedados em outro lugar, por exemplo, no Azure, localmente ou com outro provedor. O Agendador pode invocar por meio de HTTP, HTTPS, uma fila de Armazenamento, uma fila do Barramento de Serviço ou um tópico do Barramento de Serviço. Para criar, gerenciar e agendar trabalhos, você pode usar o [portal do Azure](../scheduler/scheduler-get-started-portal.md), código, a [API REST do Agendador](https://docs.microsoft.com/rest/api/scheduler/) ou a [referência dos cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md). Por exemplo, você pode criar, exibir, atualizar, gerenciar ou excluir programaticamente trabalhos e [coleções de trabalhos](../scheduler/scheduler-concepts-terms.md) por meio de scripts e no portal do Azure.

Outros recursos de agendamento do Azure também usam o Agendador em segundo plano, por exemplo, [Azure WebJobs](../app-service/webjobs-create.md), que é um recurso de [Aplicativos Web](https://azure.microsoft.com/services/app-service/web/) no Serviço de Aplicativo do Azure. Você pode gerenciar a comunicação dessas ações usando a [API REST do Agendador](https://docs.microsoft.com/rest/api/scheduler/). ajuda a gerenciar a comunicação dessas ações.

Aqui estão alguns cenários em que o Agendador pode ajudá-lo:

* **Executar ações de aplicativo recorrentes**: Por exemplo, colete dados periodicamente do Twitter em um feed.

* **Realizar manutenção diária**: Como remoção diária de logs, realização de backups e outras tarefas de manutenção. 

  Por exemplo, como administrador, você talvez queira fazer backup de seu banco de dados à 1h diariamente nos próximos nove meses.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Agendador no portal do Azure](scheduler-get-started-portal.md)
* Saiba mais sobre [planos e cobrança do Agendador do Azure](scheduler-plans-billing.md)
* Saiba mais sobre [como compilar agendas complexas e recorrência avançada com o Agendador do Microsoft Azure](scheduler-advanced-complexity.md)