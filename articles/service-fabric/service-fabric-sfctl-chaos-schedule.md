---
title: CLI do Azure Service Fabric - agenda do sfctl chao | Microsoft Docs
description: Descreve os comandos da agenda sfctl chaos da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 443a8186ac6e57360105e59e30f84db997cd2251
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897551"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Obtenha e defina a agenda do caos.

## <a name="commands"></a>Comandos

|Command|Descrição|
| --- | --- |
| get | Obter a Agenda de Caos define quando e como executar o Caos. |
| set | Defina a agenda usada por Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>obter agenda do sfctl chaos
Obter a Agenda de Caos define quando e como executar o Caos.

Obtém a versão da Agenda de Caos em uso e Agenda de Caos que define quando e como executar o Caos.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --timeout -t | O tempo limite do servidor para executar a operação em segundos. Esse tempo limite especifica a duração de tempo que o cliente está disposto a aguardar a conclusão da operação solicitada. O valor padrão para esse parâmetro é 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-chaos-schedule-set"></a>definir agenda do sfctl chaos
Defina a agenda usada por Chaos.

O Caos agendará automaticamente execuções com base na Agenda do Caos. O agendamento de caos será atualizado se a versão fornecida corresponder à versão no servidor. Ao atualizar o cronograma de caos, a versão no servidor é incrementada em 1. A versão no servidor voltará para 0 depois de atingir um grande número. Se o Caos é executado quando esta chamada é feita, a chamada falhará.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --chaos-parameters-dictionary | Lista codificada JSON que representa um mapeamento de nomes de cadeia de caracteres para ChaosParameters a ser usado por trabalhos codificados para Trabalhos. |
| --expiry-date-utc | Data e hora de quando parar de usar a Agenda para agendar o Caos.  Padrão\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Lista codificada JSON de ChaosScheduleJobs representando quando executar o Caos e com quais parâmetros executar o Caos. |
| --start-date-utc | Data e hora de quando iniciar o uso da Agenda para agendar o Caos.  Padrão\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Padrão\: 60. |
| --versão | Número de versão da Agenda. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

O comando a seguir define uma agenda (supondo que a agenda atual tenha a versão 0) que inicia em 01-01-2016 e expira em 01-01-2038 que executa o Caos 24 horas por dia, 7 dias por semana. O Caos serão agendado no cluster para esse horário.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]


## <a name="next-steps"></a>Próximos passos
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
