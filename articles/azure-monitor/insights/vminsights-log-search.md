---
title: Como consultar logs do Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Azure Monitor para VMs solução coleta dados de log e métricas para o e este artigo descreve os registros e inclui exemplos de consultas.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/29/2019
ms.openlocfilehash: 69ed49c0e1b90b4086a40bd15f5d276c6cfe137f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162227"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Como consultar logs do Azure Monitor para VMs (versão prévia)
Azure Monitor para VMs coleta métricas de desempenho e conexão, dados de inventário de computador e processo e informações de estado de integridade e as encaminha para o espaço de trabalho Log Analytics no Azure Monitor.  Esses dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Azure monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="map-records"></a>Registros do mapa
Um registro é gerado por hora para cada computador e processo exclusivo, além dos registros que são gerados quando um processo ou computador inicia ou é integrado ao Azure Monitor para o recurso de Mapeamento de VMs. Esses registros têm as propriedades descritas nas tabelas a seguir. Os campos e valores nos eventos ServiceMapComputer_CL mapeiam para campos do recurso do Computador na API do ServiceMap do Azure Resource Manager. Os campos e valores nos eventos ServiceMapProcess_CL mapeiam para os campos do recurso do Processo na API do ServiceMap do Azure Resource Manager. O campo ResourceName_s corresponde ao campo de nome no recurso do Gerenciador de Recursos correspondente. 

Há propriedades geradas internamente que você pode usar para identificar computadores e processos exclusivos:

- Computador: use *ResourceId* ou *ResourceName_s* para identificar exclusivamente um computador em um espaço de trabalho do Log Analytics.
- Processo: use *ResourceId* para identificar exclusivamente um processo em um espaço de trabalho do Log Analytics. O *ResourceName_s* é exclusivo no contexto do computador no qual o processo está em execução (MachineResourceName_s) 

Como vários registros podem existir para um processo e computador específicos em um intervalo de tempo específico, as consultas podem retornar mais de um registro para o mesmo computador ou processo. Para incluir apenas o registro mais recente, adicione `| summarize arg_max(TimeGenerated, *) by ResourceId` à consulta.

### <a name="connections-and-ports"></a>Conexões e portas
O recurso de métricas de conexão introduz duas novas tabelas em logs de Azure Monitor-VMConnection e VMBoundPort. Essas tabelas fornecem informações sobre as conexões de um computador (entrada e saída), bem como as portas de servidor abertas ou ativas neles. Os ConnectionMetrics também são expostos por meio de APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo. As conexões TCP resultantes da *aceitação* em um soquete de escuta são de entrada, enquanto aquelas criadas pela *conexão* a um determinado IP e porta são de saída. A direção de uma conexão é representada pela propriedade Direction, o que pode ser definida para **entrada** ou **saída**. 

Os registros nessas tabelas são gerados a partir de dados relatados pelo Dependency Agent. Cada registro representa uma observação sobre um intervalo de tempo de 1 minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registro contém informações para identificar a respectiva entidade, ou seja, conexão ou porta, bem como as métricas associadas àquela entidade. Atualmente, apenas as atividades de rede que usam TCP via IPv4 são relatadas. 

#### <a name="common-fields-and-conventions"></a>Campos e convenções comuns 
Os seguintes campos e convenções se aplicam a VMConnection e VMBoundPort: 

- Computador: nome de domínio totalmente qualificado do computador de relatório 
- AgentId: o identificador exclusivo de um computador com o agente de Log Analytics  
- Machine: o nome do recurso de Azure Resource Manager para o computador exposto por ServiceMap. Ele está no formato *m-{GUID}* , em que *GUID* é o mesmo GUID que AgentId  
- Processo: o nome do recurso de Azure Resource Manager para o processo exposto pelo ServiceMap. Ele está no formato *p-{cadeia de caracteres hexadecimal}* . O processo é exclusivo em um escopo de computador e gerar uma ID de processo exclusiva entre computadores, combinar campos de máquina e processo. 
- ProcessName: nome do executável do processo de relatório.
- Todos os endereços IP são cadeias de caracteres no formato canônico IPv4, por exemplo *13.107.3.160* 

Para gerenciar o custo e a complexidade, os registros de conexão não representam as conexões de rede física individuais. Várias conexões de rede física são agrupadas em uma conexão lógica, o que é refletido na respectiva tabela.  Ou seja, os registros na tabela *VMConnection* representam um agrupamento lógico, não as conexões físicas individuais sendo observadas. As conexões de rede física que compartilham o mesmo valor para os atributos a seguir durante o intervalo especificado de um minuto são agregadas em um único registro lógico em *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
|Direção |Direção da conexão, o valor é *entrada* ou *saída* |
|Computador |O FQDN do computador |
|Processo |Identidade do processo ou grupos de processos, iniciando/aceitando a conexão |
|SourceIp |Endereço IP da origem |
|DestinationIp |Endereço IP do destino |
|DestinationPort |Número da porta de destino |
|Protocolo |Protocolo usado para a conexão.  O valor é *tcp*. |

Para levar em conta o impacto do agrupamento, são fornecidas informações sobre o número de conexões físicas agrupadas nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
|LinksEstablished |O número de conexões de rede física que foram estabelecidas durante o intervalo de tempo de geração de relatórios |
|LinksTerminated |O número de conexões de rede física que foram terminadas durante o intervalo de tempo de geração de relatórios |
|LinksFailed |O número de conexões de rede física que falharam durante o intervalo de tempo de geração de relatórios. Essa informação está disponível atualmente apenas para as conexões de saída. |
|LinksLive |O número de conexões de rede física que estavam abertas no final do intervalo de tempo de geração de relatórios|

#### <a name="metrics"></a>Métricas

Além das métricas de contagem de conexões, as informações sobre o volume de dados enviados e recebidos em determinada conexão lógica ou porta de rede também estão incluídas nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
|BytesSent |Número total de bytes enviados durante o intervalo de tempo de geração de relatórios |
|BytesReceived |Número total de bytes recebidos durante o intervalo de tempo de geração de relatórios |
|Respostas |O número de respostas observadas durante o intervalo de tempo de geração de relatórios. 
|ResponseTimeMax |O maior tempo de resposta (milissegundos) observado durante o intervalo de tempo de geração de relatórios. Se não houve valor, a propriedade ficará em branco.|
|ResponseTimeMin |O menor tempo de resposta (milissegundos) observado durante o intervalo de tempo de geração de relatórios. Se não houve valor, a propriedade ficará em branco.|
|ResponseTimeSum |A soma de todos os tempos de resposta (milissegundos) observados durante o intervalo de tempo de geração de relatórios. Se não houve valor, a propriedade ficará em branco.|

O terceiro tipo de dados relatado é o tempo de resposta: quanto tempo quem chama fica aguardando uma solicitação enviada em uma conexão para ser processada e respondida pelo ponto de extremidade remoto. O tempo de resposta relatado é uma estimativa do tempo real de resposta do protocolo do aplicativo subjacente. Ele é calculado usando uma heurística baseada na observação do fluxo de dados entre a origem e o destino final de uma conexão de rede física. Conceitualmente, é a diferença entre a hora em que o último byte de uma solicitação deixa o emissor e a hora em que o último byte da resposta volta para ele. Esses dois carimbos de data/hora são usados para delinear os eventos de solicitação e resposta em determinada conexão física. A diferença entre eles representa o tempo de resposta de uma única solicitação. 

Na primeira versão do recurso, nosso algoritmo é uma aproximação que pode funcionar com vários graus de sucesso, dependendo do protocolo do aplicativo real usado em determinada conexão de rede. Por exemplo, a abordagem atual funciona bem para os protocolos baseados em solicitação-resposta, como o HTTP(S), mas não funciona com protocolos baseados em fila de mensagens ou unidirecionais.

Aqui estão alguns pontos importantes a considerar:

1. Se um processo aceita conexões no mesmo endereço IP, mas em várias interfaces de rede, é relatado um registro separado para cada interface. 
2. Os registros com IP curinga não conterão nenhuma atividade. Eles são incluídos para representar o fato de que uma porta no computador está aberta para o tráfego de entrada.
3. Para reduzir o nível de detalhes e o volume de dados, os registros com IP curinga serão omitidos quando houver um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registro IP curinga for omitido, a propriedade de registro IsWildcardBind com o endereço IP específico será definida para "True" para indicar que a porta está exposta em cada interface do computador que gera os relatórios.
4. As portas que são associadas somente em uma interface específica têm IsWildcardBind definido como *false*.

#### <a name="naming-and-classification"></a>Nomenclatura e Classificação
Para sua conveniência, o endereço IP da extremidade remota de uma conexão é incluído na propriedade RemoteIp. No caso das conexões de entrada, RemoteIp é igual a SourceIp; já para conexões de saída, é igual a DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes DNS aceitos relatados pelo computador para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification são reservadas para uso futuro. 

#### <a name="geolocation"></a>Geolocalização
*VMConnection* também inclui informações de localização geográfica para a extremidade remota de cada registro de conexão nas seguintes propriedades do registro: 

| Propriedade | Descrição |
|:--|:--|
|RemoteCountry |O nome do país/região que hospeda o RemoteIp.  Por exemplo: *Estados Unidos* |
|RemoteLatitude |A latitude da localização geográfica. Por exemplo: *47,68* |
|RemoteLongitude |A longitude da localização geográfica. Por exemplo: *-122,12* |

#### <a name="malicious-ip"></a>IP malicioso
Todas as propriedades RemoteIp na tabela *VMConnection* são verificadas em um conjunto de IPs com atividades maliciosas conhecidas. Se RemoteIp for identificado como malicioso, as propriedades a seguir serão preenchidas (elas ficam em branco quando o IP não é considerado malicioso) nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
|MaliciousIP |Endereço de RemoteIp |
|IndicatorThreadType |O indicador de ameaça detectado é um dos seguintes valores, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Descrição |Descrição da ameaça observada. |
|TLPLevel |O TLP (Traffic Light Protocol) é um dos valores definidos, *Branco*, *Verde*, *Âmbar*, *Vermelho*. |
|Confiança |Os valores são *0 – 100*. |
|Gravidade |Os valores são *0 – 5*, onde *5* é o mais grave e *0* não é grave. O valor padrão é *3*.  |
|FirstReportedDateTime |A primeira vez que o provedor relatou o indicador. |
|LastReportedDateTime |A última vez que o indicador foi visto pelo Interflow. |
|IsActive |Indica que os indicadores estão desativados com o valor *Verdadeiro* ou *Falso*. |
|ReportReferenceLink |Links para relatórios relacionados a um dado observável. |
|AdditionalInformation |Fornece informações adicionais, se aplicáveis, sobre a ameaça observada. |

### <a name="ports"></a>Portas 
As portas em um computador que aceitam ativamente o tráfego de entrada ou podem potencialmente aceitar tráfego, mas ficam ociosas durante a janela de tempo de relatório, são gravadas na tabela VMBoundPort.  

Cada registro em VMBoundPort é identificado pelos seguintes campos: 

| Propriedade | Descrição |
|:--|:--|
|Processo | Identidade do processo (ou grupos de processos) com os quais a porta está associada.|
|IP | Endereço IP da porta (pode ser IP de curinga, *0.0.0.0*) |
|Porta |O número da porta |
|Protocolo | O protocolo.  Exemplo, *TCP* ou *UDP* (somente *TCP* tem suporte no momento).|
 
A identidade que uma porta é derivada dos cinco campos acima e é armazenada na propriedade portid. Essa propriedade pode ser usada para localizar rapidamente registros para uma porta específica ao longo do tempo. 

#### <a name="metrics"></a>Métricas 
Os registros de porta incluem métricas que representam as conexões associadas a eles. No momento, as seguintes métricas são relatadas (os detalhes de cada métrica são descritos na seção anterior): 

- BytesSent e BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Aqui estão alguns pontos importantes a considerar:

- Se um processo aceita conexões no mesmo endereço IP, mas em várias interfaces de rede, é relatado um registro separado para cada interface.  
- Os registros com IP curinga não conterão nenhuma atividade. Eles são incluídos para representar o fato de que uma porta no computador está aberta para o tráfego de entrada. 
- Para reduzir o nível de detalhes e o volume de dados, os registros com IP curinga serão omitidos quando houver um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registro de IP curinga for omitido, a propriedade *IsWildcardBind* para o registro com o endereço IP específico será definida como *true*.  Isso indica que a porta é exposta em todas as interfaces da máquina de relatórios. 
- As portas que são associadas somente em uma interface específica têm IsWildcardBind definido como *false*. 

### <a name="servicemapcomputer_cl-records"></a>Registros ServiceMapComputer_CL
Os registros com um tipo de *ServiceMapComputer_CL* têm dados de inventário para servidores com o Agente de dependência. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para um computador dentro do workspace |
| ResourceName_s | O identificador exclusivo para um computador dentro do workspace |
| ComputerName_s | O FQDN do computador |
| Ipv4Addresses_s | Uma lista dos endereços IPv4 do servidor |
| Ipv6Addresses_s | Uma lista dos endereços IPv6 do servidor |
| DnsNames_s | Uma matriz de nomes DNS |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemFullName_s | O nome completo do sistema operacional  |
| Bitness_s | O número de bit do computador (32-BIT ou 64-BIT)  |
| PhysicalMemory_d | A memória física em MB |
| Cpus_d | O número de CPUs |
| CpuSpeed_d | A velocidade da CPU em MHz|
| VirtualizationState_s | *desconhecido*, *físico*, *virtual*, *hipervisor* |
| VirtualMachineType_s | *hyperv*, *vmware*, e assim por diante |
| VirtualMachineNativeMachineId_g | A ID da VM conforme atribuída pelo seu hipervisor |
| VirtualMachineName_s | O nome da VM |
| BootTime_t | O tempo de inicialização |

### <a name="servicemapprocess_cl-type-records"></a>Registros do tipo ServiceMapProcess_CL Type
Os registros com um tipo de *ServiceMapProcess_CL* têm dados de inventário para processos conectados com TCP em servidores com o Agente de dependência. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para um processo dentro do workspace |
| ResourceName_s | O identificador exclusivo para um processo dentro do computador no qual está sendo executado|
| MachineResourceName_s | O nome do recurso do computador |
| ExecutableName_s | O nome do processo executável |
| StartTime_t | O tempo de início do pool de processos |
| FirstPid_d | O primeiro PID no pool de processos |
| Description_s | A descrição do processo |
| CompanyName_s | O nome da empresa |
| InternalName_s | O nome interno |
| ProductName_s | O nome do produto |
| ProductVersion_s | A versão do produto |
| FileVersion_s | A versão do arquivo |
| CommandLine_s | A linha de comando |
| ExecutablePath_s | O caminho para o arquivo executável |
| WorkingDirectory_s | O diretório de trabalho |
| UserName | A conta sob a qual o processo está sendo executado |
| UserDomain | O domínio sob o qual o processo está sendo executado |

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo

### <a name="list-all-known-machines"></a>Lista todas as máquinas conhecidas
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Quando a VM foi reiniciada pela última vez
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Resumo das VMs do Azure por imagem, localização e SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Lista a capacidade de memória física de todos os computadores gerenciados.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s
```

### <a name="list-computer-name-dns-ip-and-os"></a>Listar o nome, DNS, IP e sistema operacional do computador.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comando
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Localizar uma máquina (registro mais recente) por nome de recurso
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar um computador (registro mais recente) pelo endereço IP
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listar todos os processos conhecidos em um computador especificado
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Listar todos os computadores que executam o SQL Server
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões de produtos exclusivas de curl no meu datacenter
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computadores de todos os computadores executando CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s
```

### <a name="bytes-sent-and-received-trends"></a>Os bytes enviaram e receberam tendências
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Quais VMs do Azure transmitem a maioria dos bytes
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Vincular tendências de status
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Tendência com falhas de conexão
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Portas associadas
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Número de portas abertas entre computadores
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Pontuar processos em seu espaço de trabalho pelo número de portas que eles abriram
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Comportamento agregado para cada porta
Essa consulta pode ser usada para pontuar portas por atividade, por exemplo, portas com maior tráfego de entrada/saída, portas com a maioria das conexões
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Resumir as conexões de saída a partir de um grupo de computadores
```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Próximos passos
* Se você for novo na gravação de consultas de log em Azure Monitor, examine [como usar log Analytics](../../azure-monitor/log-query/get-started-portal.md) no portal do Azure para gravar consultas de log.

* Saiba mais sobre [como escrever consultas de pesquisa](../../azure-monitor/log-query/search-queries.md).
