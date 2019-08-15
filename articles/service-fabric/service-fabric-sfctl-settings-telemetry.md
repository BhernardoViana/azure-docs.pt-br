---
title: CLI do Azure Service Fabric – telemetria de configurações do sfctl | Microsoft Docs
description: Descreve os comandos de telemetria de configurações do sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035204"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Defina as configurações de telemetria locais para esta instância do sfctl.

A telemetria do sfctl coleta o nome do comando sem parâmetros fornecidos ou seus valores, versão do sfctl, tipo de sistema operacional, versão do Python, êxito ou falha do comando e a mensagem de erro retornada.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| set-telemetry | Ativa ou desliga a telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>set-telemetry da telemetria de configurações do sfctl
Ativa ou desliga a telemetria.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --off | Desliga a telemetria. |
| --on | Ativa a telemetria. Este é o valor padrão. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Desliga a telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Ativa a telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).