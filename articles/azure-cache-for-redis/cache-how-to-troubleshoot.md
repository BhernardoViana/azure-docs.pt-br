---
title: Como solucionar problemas do Cache do Azure para Redis | Microsoft Docs
description: Saiba como solucionar problemas comuns com Cache do Azure para Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 5a8542007d67cb76eaf615da6c5d1db1b1555ea3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234910"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Como solucionar problemas do Cache do Azure para Redis
Este artigo fornece diretrizes para a solução de problemas das seguintes categorias de problemas do Cache do Azure para Redis.

* [Solução de problemas do lado do cliente](#client-side-troubleshooting) - Esta seção fornece diretrizes sobre como identificar e resolver problemas causados pelo aplicativo que conecta o Cache do Azure para Redis.
* [Solução de problemas do lado do servidor](#server-side-troubleshooting) - Esta seção fornece diretrizes sobre como identificar e resolver problemas causados no lado do servidor do Azure Cache para Redis.
* [Exceções de tempo limite do StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - esta seção fornece informações sobre como solucionar problemas ao usar o cliente StackExchange.Redis.

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
> 
> 

## <a name="client-side-troubleshooting"></a>Solução de problemas do lado do cliente
Esta seção aborda a solução de problemas que ocorrem devido a alguma condição do aplicativo cliente.

* [Demanda de memória do cliente](#memory-pressure-on-the-client)
* [Intermitência de tráfego](#burst-of-traffic)
* [Alto nível de uso da CPU do cliente](#high-client-cpu-usage)
* [Largura de Banda Excedida do Lado do Cliente](#client-side-bandwidth-exceeded)
* [Solicitação/resposta grande](#large-requestresponse-size)
* [O que aconteceu com meus dados no Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Demanda de memória do cliente
#### <a name="problem"></a>Problema
A demanda de memória no computador cliente leva a todo tipo de problemas de desempenho, que podem atrasar o processamento de dados que foram enviados pela instância do Redis sem nenhum atraso. Quando há grande demanda de memória, o sistema normalmente precisar paginar dados da memória física para a memória virtual que está no disco. Esse tipo de *falha de página* faz com que o sistema fique significativamente mais lento.

#### <a name="measurement"></a>Medida
1. Monitorar o uso de memória no computador para certificar-se de que ele não ultrapasse a memória disponível. 
2. Monitorar o contador de desempenho `Page Faults/Sec` . A maioria dos sistemas têm algumas falhas de página até mesmo durante a operação normal, portanto, fique atento a picos no contador de desempenho de falhas página que correspondem aos tempos limite.

#### <a name="resolution"></a>Resolução
Atualize o cliente para um tamanho maior de VM cliente com mais memória ou analise seus padrões de uso de memória para reduzir o consumo de memória.

### <a name="burst-of-traffic"></a>Intermitência de tráfego
#### <a name="problem"></a>Problema
A intermitência de tráfego, combinada com configurações de `ThreadPool` ruins, podem resultar em atrasos no processamento de dados que já foram enviados pelo servidor Redis, mas ainda não foram consumidos no lado do cliente.

#### <a name="measurement"></a>Medida
Monitor como suas estatísticas de `ThreadPool` mudam ao longo do tempo usando um código [como este](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Você também pode examinar a mensagem `TimeoutException` do StackExchange.Redis. Veja um exemplo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Na mensagem anterior, há várias questões interessantes:

1. Observe que, na seção `IOCP` e na seção `WORKER`, você tem um valor `Busy` que é maior que o valor `Min`. Essa diferença significa que suas configurações de `ThreadPool` precisam de ajuste.
2. Você também pode ver `in: 64221`. Esse valor indica que 64.211 bytes foram recebidos na camada de soquete de kernel, mas ainda não foram lidos pelo aplicativo (por exemplo, StackExchange.Redis). Normalmente, essa diferença significa que seu aplicativo não está lendo dados da rede na velocidade em que o servidor os está enviando para você.

#### <a name="resolution"></a>Resolução
Configure suas [Definições de ThreadPool](https://gist.github.com/JonCole/e65411214030f0d823cb) para certificar-se de que o pool de threads seja escalado verticalmente com rapidez em cenários de intermitência.

### <a name="high-client-cpu-usage"></a>Alto nível de uso da CPU do cliente
#### <a name="problem"></a>Problema
Um alto nível de uso da CPU do cliente é uma indicação de que o sistema não é capaz de acompanhar o trabalho que foi solicitado a executar. Essa situação significa que o cliente pode falhar ao processar uma resposta do Redis de forma oportuna, embora o Redis tenha enviado a resposta rapidamente.

#### <a name="measurement"></a>Medida
Monitorar o uso de CPU geral do sistema por meio do Portal do Azure ou do contador de desempenho associado. Tenha cuidado para não monitorar o uso de CPU do *processo* , porque um único processo pode representar pouco uso da CPU enquanto o uso de CPU do sistema geral pode ser alto. Fique atento a picos de uso de CPU que correspondem aos tempos limite. Como resultado do alto nível de uso da CPU, você também poderá ver valores altos de `in: XXX` em mensagens de erro `TimeoutException`, conforme descrito na seção [Intermitência de tráfego](#burst-of-traffic).

> [!NOTE]
> O StackExchange 1.1.603 e versões posteriores incluem a métrica `local-cpu` em mensagens de erro `TimeoutException`. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
> 
> 

#### <a name="resolution"></a>Resolução
Atualizar para uma VM maior, com mais capacidade de CPU, ou investigar o que está causando os picos de CPU. 

### <a name="client-side-bandwidth-exceeded"></a>Largura de banda excedida do lado do cliente
#### <a name="problem"></a>Problema
Dependendo da arquitetura dos computadores cliente, eles poderão apresentar limitações na largura de banda de rede disponível. Se o cliente excede a largura de banda disponível, sobrecarregando a capacidade da rede, os dados não são processados no lado do cliente com a mesma velocidade que o servidor os envia. Essa situação pode resultar em tempos limite excedidos.

#### <a name="measurement"></a>Medida
Monitorar como seu uso de largura de banda muda ao longo do tempo usando um código [como este](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Esse código pode não ser executado corretamente em alguns ambientes com permissões restritas (como sites do Azure).

#### <a name="resolution"></a>Resolução
Aumentar o tamanho da VM do cliente ou reduzir o consumo de largura de banda da rede.

### <a name="large-requestresponse-size"></a>Solicitação/resposta grande
#### <a name="problem"></a>Problema
Uma solicitação/resposta grande pode causar tempos limite. Por exemplo, suponha que o valor do tempo limite configurado no seu cliente seja de um segundo. Seu aplicativo solicita duas chaves (por exemplo, "A" e "B") ao mesmo tempo (usando a mesma conexão de rede física). A maioria dos clientes dá suporte ao "pipelining" de solicitações, de modo que as duas solicitações, "A" e "B", sejam transmitidas ao servidor uma após a outra sem esperar pelas respostas. O servidor devolve as respostas na mesma ordem. Se a resposta "A" for grande o suficiente, ela poderá consumir a maior parte do tempo limite das solicitações subsequentes. 

O exemplo a seguir demonstra esse cenário. No cenário, as solicitações "A" e "B" são enviadas rapidamente, o servidor começa a enviar respostas "A" e "B" rapidamente, mas, devido ao tempo para a transferência de dados, "B" fica preso atrás da outra solicitação e atinge o tempo limite, embora o servidor tenha respondido rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Medida
Essa é uma solicitação/resposta difícil de se medir. Basicamente, você precisa instrumentar o código do cliente para monitorar grandes solicitações e respostas. 

#### <a name="resolution"></a>Resolução
1. O Redis é otimizado para um grande número de valores pequenos, em vez de alguns valores grandes. A solução preferida é dividir seus dados em valores menores relacionados. Consulte a postagem [What is the ideal value size range for redis? Is 100KB too large? (Qual é o intervalo de tamanho ideal para o Redis? 100 KB é muito?)](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para obter detalhes sobre por que valores menores são recomendados.
2. Aumente o tamanho da VM (para cliente e Cache do Azure para Servidor de Redis), para obter recursos de largura de banda maiores, reduzindo os tempos de transferência de dados para respostas maiores. Obter mais largura de banda apenas no servidor ou apenas no cliente pode não ser suficiente. Meça o uso de largura de banda e compare-o com os recursos do tamanho da VM que você tem no momento.
3. Aumentar o número de objetos `ConnectionMultiplexer` que você usa e fazer round robin das solicitações através de diferentes conexões.

### <a name="what-happened-to-my-data-in-redis"></a>O que aconteceu com meus dados no Redis?
#### <a name="problem"></a>Problema
Eu esperava que certos dados estivessem em minha instância do Cache do Azure para Redis, mas parece que não estão lá.

#### <a name="resolution"></a>Resolução
Consulte [What happened to my data in Redis? (O que aconteceu com meu dados no Redis?)](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para ver as possíveis causas e resoluções.

## <a name="server-side-troubleshooting"></a>Solução de problemas do servidor
Esta seção aborda a solução de problemas que ocorrem devido a alguma condição do servidor de cache.

* [Demanda de memória do servidor](#memory-pressure-on-the-server)
* Alto nível de uso da CPU/carga de servidor
* [Largura de banda do servidor ultrapassada](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Demanda de memória do servidor
#### <a name="problem"></a>Problema
Uma grande demanda da memória do servidor acarreta todo tipo de problemas de desempenho, que podem atrasar o processamento de solicitações. Quando há grande demanda de memória, o sistema normalmente precisar paginar dados da memória física para a memória virtual que está no disco. Esse tipo de *falha de página* faz com que o sistema fique significativamente mais lento. Há diversas causas possíveis para essa demanda de memória: 

1. Você preencheu toda a capacidade do cache com dados. 
2. O Redis está passando por uma grande fragmentação da memória, que frequentemente causada pelo armazenamento de objetos grandes (o Redis é otimizado para objetos pequenos. Consulte a postagem [What is the ideal value size range for redis? Is 100 KB too large? (Qual é o intervalo de tamanho ideal para o Redis? 100 KB é muito?)](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para obter mais detalhes). 

#### <a name="measurement"></a>Medida
O Redis expõe duas métricas que podem ajudá-lo a identificar esse problema. A primeira é `used_memory` e a outra é `used_memory_rss`. [Essas métricas](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) estão disponíveis no Portal do Azure ou por meio do comando [INFO do Redis](https://redis.io/commands/info).

#### <a name="resolution"></a>Resolução
Há várias alterações possíveis que você pode fazer para ajudar a manter a integridade do uso de memória:

1. [Configurar uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e definir tempos de expiração em suas chaves. Essa configuração pode não ser suficiente se você tem fragmentação.
2. [Configurar um valor para maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que seja grande o suficiente para compensar pela fragmentação da memória.
3. Dividir os objetos grandes armazenados em cache em objetos menores relacionados.
4. [Dimensionar](cache-how-to-scale.md) para um tamanho de cache maior.
5. Se estiver usando um [cache Premium com o cluster Redis habilitado](cache-how-to-premium-clustering.md), você poderá [aumentar o número de fragmentos](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Alto nível de uso da CPU/carga de servidor
#### <a name="problem"></a>Problema
Um alto nível de uso da CPU pode significar que o lado do cliente pode falhar ao processar uma resposta do Redis de forma oportuna, embora o Redis tenha enviado a resposta rapidamente.

#### <a name="measurement"></a>Medida
Monitorar o uso de CPU geral do sistema por meio do Portal do Azure ou do contador de desempenho associado. Tenha cuidado para não monitorar o uso de CPU do *processo* , porque um único processo pode representar pouco uso da CPU enquanto o uso de CPU do sistema geral pode ser alto. Fique atento a picos de uso de CPU que correspondem aos tempos limite.

#### <a name="resolution"></a>Resolução
* Revise todos os conselhos e alertas mencionados no [Assistente do Cache do Azure para Redis](cache-configure.md#azure-cache-for-redis-advisor).
* Examine também as outras recomendações neste tópico e as [melhores práticas para o Redis do Azure](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) para ver se todas as opções foram utilizadas para otimizar ainda mais seu cache e seu cliente. 
* Examine os gráficos de [desempenho do Cache do Azure para Redis](cache-faq.md#azure-cache-for-redis-performance) e verifique se você está próximo aos limites superiores da camada atual. Se necessário, [Dimensione](cache-how-to-scale.md) para uma camada de cache maior com mais capacidade de CPU. Se você já estiver usando a camada Premium, talvez seja interessante [escalar horizontalmente com clustering](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Largura de banda do servidor ultrapassada
#### <a name="problem"></a>Problema
Dependendo do tamanho das instâncias de cache, elas poderão apresentar limitações na largura de banda de rede disponível. Se o servidor excede a largura de banda disponível, os dados não são enviados ao cliente tão rapidamente. Essa situação pode resultar em tempos limite excedidos.

#### <a name="measurement"></a>Medida
Você pode monitorar a métrica `Cache Read` , que é a quantidade de dados lidos do cache, em MB/s, durante o intervalo de relatório especificado. Esse valor corresponde à largura de banda de rede usada por esse cache. Se quiser configurar alertas para limites de largura de banda de rede do lado do servidor, você poderá criá-los usando este contador `Cache Read`. Compare suas leituras com os valores [nesta tabela](cache-faq.md#cache-performance) para ver os limites de largura de banda observados para vários tamanhos e camadas de preços de cache.

#### <a name="resolution"></a>Resolução
Se você estiver consistentemente perto da largura de banda máxima observada para o tamanho do cache e o tipo de preço, considere [dimensionar](cache-how-to-scale.md) para um tamanho ou tipo de preço que tenha maior largura de banda de rede, usando os valores [nesta tabela](cache-faq.md#cache-performance) como guia.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite do StackExchange.Redis
O StackExchange.Redis usa uma configuração chamada `synctimeout` para operações síncronas, que tem um valor padrão de 1.000 ms. Se uma chamada síncrona não for concluída no tempo determinado, o cliente do StackExchange.Redis gerará um erro de tempo limite semelhante ao exemplo a seguir:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Essa mensagem de erro contém métricas que podem ajudar a indicar a causa e a possível resolução do problema. A tabela a seguir contém detalhes sobre as métricas da mensagem de erro.

| Métrica da mensagem de erro | Detalhes |
| --- | --- |
| inst |Na última fração de tempo: 0 comandos foram emitidos |
| mgr |O gerenciador do soquete está executando `socket.select`, o que significa que ele está solicitando que o sistema operacional indique um soquete que tenha algo a fazer. Basicamente, o leitor não está lendo ativamente na rede porque ele acha que não há nada a fazer |
| fila |Existem 73 operações em andamento no total |
| qu |6 das operações em andamento estão na fila de "não enviadas" e ainda não foram gravadas na rede de saída |
| qs |67 das operações em andamento foram enviadas ao servidor, mas ainda não há uma resposta disponível. A resposta pode ser `Not yet sent by the server` ou `sent by the server but not yet processed by the client.` |
| qc |0 das operações em andamento viram respostas, mas ainda não foram marcados como concluídas devido a espera do loop de conclusão |
| wr |Há um gravador ativo (ou seja, as 6 solicitações não enviadas não estão sendo ignoradas) bytes/activewriters |
| mergulhar |Não há nenhum leitor ativo e zero bytes estão disponíveis para serem lidos no NIC bytes/activereaders |

### <a name="steps-to-investigate"></a>Etapas para investigar
1. Como uma melhor prática, verifique se você está usando o seguinte padrão para se conectar ao usar o cliente do StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Para saber mais, confira [Conectar-se ao cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Verifique se o Cache do Azure para Redis e o aplicativo cliente estão na mesma região no Azure. Por exemplo, você pode receber tempos limite quando o cache está no Leste dos EUA e o cliente está no Oeste dos EUA e a solicitação não é concluída dentro do intervalo de `synctimeout` , ou você pode receber tempos limite quando estiver depurando de seu computador de desenvolvimento local. 
   
    É altamente recomendável ter o cache e o cliente na mesma região do Azure. Se você tem um cenário que inclui chamadas entre regiões, é necessário definir o intervalo de `synctimeout` como um valor maior que o intervalo padrão de 1.000 ms, incluindo uma propriedade `synctimeout` na cadeia de conexão. O exemplo a seguir mostra um snippet de cadeia de conexão StackExchange.Azure do Cache para Redis com `synctimeout` de 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
3. Se houver solicitações restritas por limitações de largura de banda no servidor ou no cliente, levará mais tempo para conclui-las, o que resultará em tempos limite excedidos. Para ver se o seu tempo limite se deve à largura de banda de rede do servidor, consulte [Largura de banda do servidor ultrapassada](#server-side-bandwidth-exceeded). Para ver se o tempo limite excedido se deve à largura de banda de rede do cliente, consulte [Largura de banda excedida do lado do cliente](#client-side-bandwidth-exceeded).
4. Você está sendo limitado à CPU no servidor ou no cliente?
   
   * Verifique se você está sendo limitado pela CPU no cliente, o que pode fazer com que a solicitação não seja processada dentro do intervalo `synctimeout`, provocando um tempo limite excedido. Mover para um tamanho de cliente maior ou distribuir a carga pode ajudar a controlar esse problema. 
   * Verifique se você está sendo limitado pela CPU no servidor monitorando a [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `CPU`. Solicitações recebidas enquanto o Redis está limitado à CPU podem fazer com que essas solicitações cheguem ao tempo limite. Para resolver essa condição, você pode distribuir a carga entre vários fragmentos em um cache Premium ou atualizar para um tipo de preço ou tamanho maior. Para obter mais informações, consulte [Largura de banda do servidor ultrapassada](#server-side-bandwidth-exceeded).
5. Há comandos que levam muito tempo para serem processados no servidor? Comandos de execução longa que levam muito tempo para serem processados no servidor do Redis podem resultar em tempos limite. Alguns exemplos de comandos de execução longa são `mget` com um grande número de chaves, `keys *` ou scripts lua mal escritos. É possível conectar a instância do Cache do Azure para Redis usando o cliente redis-cli ou usar o [Console Redis](cache-configure.md#redis-console) e executar o comando [SlowLog](https://redis.io/commands/slowlog) para verificar se há solicitações que demoram mais que o esperado. O Servidor do Redis e o StackExchange.Redis são otimizados para várias solicitações pequenas, em vez de menos solicitações grandes. Dividir os dados em partes menores pode melhorar as coisa. 
   
    Para obter informações sobre como conectar o ponto de extremidade SSL ao Cache do Azure para Redis usando redis-cli e stunnel, consulte a postagem de blog [Anunciando o provedor de estado de sessão ASP.NET para a versão de teste do Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx). Para obter mais informações, consulte [SlowLog](https://redis.io/commands/slowlog).
6. Uma carga alta no servidor Redis pode resultar em tempos limite. Você pode monitorar a carga do servidor monitorando a [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load`. Uma carga de servidor de 100 (valor máximo) significa que o servidor Redis está ocupado, sem tempo ocioso, processando de solicitações. Para ver se determinadas solicitações estão consumindo toda a capacidade do servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, confira Alto nível de uso da CPU/carga de servidor.
7. Houve qualquer outro evento no lado do cliente que possa ter causado um problema na rede? Verifique no cliente (Web, função de trabalho ou VM de IaaS) se ocorreu algo como o aumento ou a redução do número de instâncias de cliente ou a implantação de uma nova versão do cliente ou se a o dimensionamento automático está habilitado. Em nossos testes, descobrimos que a Dimensionamento automático para aumentar/reduzir pode fazer com que a conectividade de rede de saída seja perdida durante alguns segundos. O código do StackExchange.Redis é resiliente a tais eventos e se reconectará. Durante o tempo de reconexão, quaisquer solicitações da fila poderão atingir o tempo limite.
8. Houve uma grande solicitação anterior a várias pequenas solicitações para o Cache do Azure para Redis que atingiu o tempo limite? O parâmetro `qs` na mensagem de erro informa quantas solicitações foram enviadas do cliente ao servidor mas ainda não processaram uma resposta. Esse valor pode continuar crescendo porque o StackExchange.Redis usa uma única conexão TCP e só pode ler uma resposta por vez. Embora a primeira operação tenha atingido o tempo limite, isso não impede que dados sejam enviados/recebidos do servidor e outras solicitações são bloqueadas até que a grande solicitação seja concluída, causando tempos limite excedidos. Uma solução é minimizar a chance de tempos limite, garantindo que o cache seja grande o suficiente para sua carga de trabalho e dividindo valores grandes em partes menores. Outra solução possível é usar um pool de objetos `ConnectionMultiplexer` no seu cliente e escolher o `ConnectionMultiplexer` menos carregado ao enviar uma nova solicitação. Isso deve impedir que um único tempo limite faça com que outras solicitações também atinjam o tempo limite.
9. Se você estiver usando `RedisSessionStateProvider`, certifique-se de que você configurou corretamente o tempo limite para novas tentativas. `retryTimeoutInMilliseconds` deve ser maior do que `operationTimeoutInMilliseconds`; caso contrário, não há novas tentativas. No exemplo a seguir, `retryTimeoutInMilliseconds` é definido como 3000. Para obter mais informações, consulte [provedor de estado de sessão ASP.NET para Cache do Azure para Redis](cache-aspnet-session-state-provider.md) e [Como usar os parâmetros de configuração do provedor de estado de sessão e do provedor de cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


1. Verifique o uso de memória no servidor do Cache do Azure para Redis [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se uma política de remoção estiver em vigor, o Redis começará a remover chaves quando `Used_Memory` atingir o tamanho do cache. Idealmente, `Used Memory RSS` deve ser apenas um pouco maior do que `Used memory`. Uma grande diferença significa que há fragmentação de memória (interna ou externa). Quando `Used Memory RSS` é menor que `Used Memory`, significa que parte da memória cache foi trocada pelo sistema operacional. Se a troca ocorrer, você poderá esperar que haja algumas latências significativas. Como o Redis não tem controle sobre como suas alocações são mapeadas para as páginas de memória, um valor alto de `Used Memory RSS` costuma ser o resultado de um aumento no uso de memória. Quando o Redis libera memória, a memória é dada de volta para o alocador e o alocador pode ou não pode fornecer a memória novamente ao sistema. Pode haver uma discrepância entre o valor de `Used Memory` e o consumo de memória, conforme relatado pelo sistema operacional. Isso pode ocorrer devido ao fato de a memória foi usada e liberada pelo Redis, mas não fornecida de volta para o sistema. Para ajudar a atenuar problemas de memória, você pode executar as seguintes etapas:
   
   * Atualizar o cache para um tamanho maior para que você não tenha problemas com as limitações de memória no sistema.
   * Definir tempos de expiração das chaves para que valores mais antigos sejam removidos de forma proativa.
   * Monitorar a métrica de cache `used_memory_rss`. Quando esse valor se aproximar do tamanho de seu cache, provavelmente você começará a ver problemas de desempenho. Distribuir os dados em vários fragmentos se você estiver usando um cache premium ou atualizar para um tamanho de cache maior.
   
   Para obter mais informações, confira [Demanda de memória do servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informações adicionais
* [Qual oferta de Cache do Azure para Redis e tamanho eu devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Como medir e testar o desempenho do meu cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
* [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)

