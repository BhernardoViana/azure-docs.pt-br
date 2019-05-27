---
title: Atributos comuns de segurança do Azure Resource Manager
description: Uma lista de verificação de atributos de segurança comuns para avaliar o Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002266"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Atributos de segurança do Azure Resource Manager

Este artigo documenta os atributos de segurança incorporados ao Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim |  |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de rede virtual</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | HTTPS/TLS. |
| Tratamento de chaves de criptografia (CMK, BYOK, etc.)| N/D | O Azure Resource Manager não armazena nenhum conteúdo do cliente, apenas os dados de controle. |
| Criptografia de nível de coluna (serviços de dados do Azure)| Sim | |
| Chamadas criptografadas à API| Sim | |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte de ponto de extremidade de serviço| Não | |
| Suporte à injeção de rede virtual| Sim | |
| Isolamento de rede e suporte de firewall| Não |  |
| Forçado suporte por túnel| Não |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Não | |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Authentication| Sim | [O Azure Active Directory](/azure/active-directory) com base.|
| Autorização| Sim | |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Auditoria e log de plano de controle e gerenciamento| Sim | Exponha todas as operações (PUT, POST, DELETE) realizadas em seus recursos; de gravação de logs de atividades ver [exibir logs de atividades para auditar ações em recursos](resource-group-audit.md). |
| Auditoria e log de plano de dados| N/D | |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  |
