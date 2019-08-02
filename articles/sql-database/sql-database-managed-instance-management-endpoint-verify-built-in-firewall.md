---
title: Descobrir o firewall interno da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como verificar a proteção de firewall interno na Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: c98b0fd5669140559b4840e157394c2e8c6086ae
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567437"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verificando o firewall interno da Instância Gerenciada

A Instância Gerenciada [regras de segurança de entrada obrigatórios](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) exige que as portas de gerenciamento 9000, 9003, 1438, 1440, 1452 estejam abertas em **Qualquer fonte** no NSG (Grupo de Segurança de Rede) que protege a Instância Gerenciada. Embora essas portas estejam abertas no nível do NSG, elas são protegidas no nível da rede pelo firewall interno.

## <a name="verify-firewall"></a>Verificar o firewall

Para verificar essas portas, use qualquer ferramenta de verificador de segurança para testá-las. A captura de tela a seguir mostra como usar uma dessas ferramentas.

![Verificando o firewall interno](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre Instâncias Gerenciadas e conectividade, veja [Arquitetura de Conectividade da Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-connectivity-architecture.md).