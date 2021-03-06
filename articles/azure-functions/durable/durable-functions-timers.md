---
title: Temporizadores nas Funções Duráveis – Azure
description: Saiba como implementar temporizadores duráveis na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 463d5e6c253643c82935c82c7dee5996c8e44b5f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706114"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Temporizadores nas Funções Duráveis (Azure Functions)

As [Funções Duráveis](durable-functions-overview.md) fornecem *temporizadores duráveis* para uso em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Temporizadores duráveis devem ser usados em funções de orquestrador em vez de `Thread.Sleep` ou `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript).

Você cria um temporizador durável chamando o método `CreateTimer` (.NET) ou o método `createTimer` (JavaScript) da [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger). O método retorna uma tarefa que é concluída em uma data e hora especificadas.

## <a name="timer-limitations"></a>Limitações do temporizador

Quando você cria um temporizador que expira às 4:30 PM, o Framework de tarefa durável subjacente enfileira uma mensagem que se torna visível somente às 4:30 PM. Ao executar no plano de consumo de Azure Functions, a mensagem de temporizador recentemente visível garantirá que o aplicativo de funções seja ativado em uma VM apropriada.

> [!NOTE]
> * Temporizadores duráveis atualmente estão limitados a 7 dias. Se forem necessários atrasos maiores, eles poderão ser simulados usando as APIs do temporizador em um loop de `while`.
> * Sempre use `CurrentUtcDateTime` em vez de `DateTime.UtcNow` no .NET ou `currentUtcDateTime` em vez de `Date.now` ou `Date.UTC` em JavaScript ao calcular o tempo de acionamento para temporizadores duráveis. Para obter mais informações, consulte o artigo [restrições de código de função do Orchestrator](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Uso para atrasos

O exemplo a seguir ilustra como usar temporizadores duráveis para atrasar a execução. O exemplo está emitindo uma notificação de cobrança todos os dias por 10 dias.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> O exemplo C# anterior tem como alvo Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Evite loops infinitos em funções de orquestrador. Para obter informações sobre como implementar de forma segura e eficiente cenários de loop infinito, consulte [Orquestrações eternas](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Uso para tempo limite

Este exemplo ilustra como usar temporizadores duráveis para implementar tempos limite.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> O exemplo C# anterior tem como alvo Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Use um `CancellationTokenSource` para cancelar um temporizador durável (.NET) ou chamar `cancel()` no `TimerTask` retornado (JavaScript) se seu código não aguardar a conclusão. O Framework de tarefa durável não alterará o status de uma orquestração para "concluído" até que todas as tarefas pendentes sejam concluídas ou canceladas.

Esse mecanismo de cancelamento não encerra as execuções da função de atividade em andamento ou da suborquestração. Em vez disso, ele simplesmente permite que a função de orquestrador ignore o resultado e prossiga. Se seu aplicativo de funções usar o plano de consumo, você ainda será cobrado por qualquer tempo e memória consumida pela função de atividade abandonada. Por padrão, funções em execução pelo Plano de Consumo têm um tempo limite de cinco minutos. Se esse limite for ultrapassado, o host do Azure Functions será reciclado para interromper toda a execução e evitar uma situação de cobrança sem controle. O [tempo limite da função é configurável](../functions-host-json.md#functiontimeout).

Para obter um exemplo mais detalhado de como implementar tempos limite em funções de orquestrador, consulte o artigo sobre a [interação humana & tempos limite-verificação de telefone](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba como acionar e manipular eventos externos](durable-functions-external-events.md)
