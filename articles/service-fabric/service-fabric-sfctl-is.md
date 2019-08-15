---
title: CLI do Azure Service Fabric - sfctl is | Microsoft Docs
description: Descreve os comandos do sfctl is da CLI do Service Fabric.
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
ms.openlocfilehash: 998a94350250402d4face64f64e12f32cf5b2a36
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036490"
---
# <a name="sfctl-is"></a>sfctl is
Consultar e enviar comandos para o serviço de infraestrutura.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| command | Invoca um comando administrativo na instância do serviço de infraestrutura especificada. |
| query | Invoca uma consulta de somente leitura na instância do serviço de infraestrutura especificada. |

## <a name="sfctl-is-command"></a>comando sfctl is
Invoca um comando administrativo na instância do serviço de infraestrutura especificada.

Para clusters que têm uma ou mais instâncias do serviço de infraestrutura configurado, essa API fornece uma maneira para enviar comandos de infraestrutura específicos para uma determinada instância do serviço de infraestrutura. Comandos disponíveis e seus formatos de resposta correspondentes variam de acordo com a infraestrutura na qual o cluster está em execução. Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --id do serviço | A identidade do serviço de infraestrutura. <br><br> É o nome completo do serviço de infraestrutura sem o esquema de URI "fabric\:". Esse parâmetro é necessário somente para clusters que têm mais de uma instância do serviço de infraestrutura em execução. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-is-query"></a>consulta sfctl is
Invoca uma consulta de somente leitura na instância do serviço de infraestrutura especificada.

Para clusters que têm uma ou mais instâncias do serviço de infraestrutura configurado, essa API fornece uma maneira para enviar consultas de infraestrutura específicos para uma determinada instância do serviço de infraestrutura. Comandos disponíveis e seus formatos de resposta correspondentes variam de acordo com a infraestrutura na qual o cluster está em execução. Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --comando [obrigatório] | O texto do comando a ser invocado. O conteúdo do comando é específico da infraestrutura. |
| --id do serviço | A identidade do serviço de infraestrutura. <br><br> É o nome completo do serviço de infraestrutura sem o esquema de URI "fabric\:". Esse parâmetro é necessário somente para clusters que têm mais de uma instância do serviço de infraestrutura em execução. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).