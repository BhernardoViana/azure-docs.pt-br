---
title: Atributos comuns de segurança do armazenamento do Azure
description: Uma lista de verificação de atributos de segurança comuns para avaliar o armazenamento do Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 922273e3805004f6af068ea748c16f5675810144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001454"
---
# <a name="security-attributes-for-azure-storage"></a>Atributos de segurança para o armazenamento do Azure

Este artigo documenta os atributos de segurança incorporados no armazenamento do Azure. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivo

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia em repouso:<ul><li>Criptografia no servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia (como do lado do cliente, Always Encrypted, etc.)</ul>| Sim |  |
| Criptografia em trânsito:<ul><li>Criptografia do ExpressRoute</li><li>Na criptografia de rede virtual</li><li>Criptografia de rede virtual a rede virtual</ul>| Sim | Suporte a mecanismos HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de ser transmitido para o serviço. |
| Tratamento de chaves de criptografia (CMK, BYOK, etc.)| Sim | Ver [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Criptografia de nível de coluna (serviços de dados do Azure)| N/D |  |
| Chamadas à API criptografadas| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte de ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de rede virtual| N/D |  |
| Isolamento de rede e suporte de firewall| Sim | |
| Forçado suporte por túnel| N/D |  |

## <a name="detection"></a>Detecção

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Monitoramento (Log analytics, insights de aplicativo, etc.) de suporte do Azure| Sim | Métricas do Azure Monitor disponível agora, registra a visualização inicial |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Authentication| Sim | Azure Active Directory, chave compartilhada, o token de acesso compartilhado. |
| Autorização| Sim | Autorização de suporte por meio de RBAC, ACLs POSIX e Tokens de SAS |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Auditoria e log de plano de controle e gerenciamento | Sim | Log de atividades do Azure Resource Manager |
| Auditoria e log de plano de dados| Sim | Os Logs de diagnóstico do serviço e registro em log do Azure Monitor visualização inicial  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Atributo de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Suporte a controle de versão do provedor de recursos por meio de APIs do Gerenciador de recursos do Azure |