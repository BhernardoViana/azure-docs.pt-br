---
title: Introdução à verificação de fluxo de IP no Observador de Rede do Azure | Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de verificação de fluxo de IP do Observador de Rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709601"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Introdução à verificação de fluxo de IP no Observador de Rede do Azure

A verificação do fluxo IP confere se um pacote é permitido ou negado para ou a partir de uma máquina virtual usando. As informações consistem em direção, protocolo, IP local, IP remoto, porta local e porta remota. Se o pacote for negado por um grupo de segurança, o nome da regra que negou o pacote será retornado. Embora qualquer IP de origem ou destino possa ser escolhido, a verificação de fluxo de IP ajuda os administradores a diagnosticarem rapidamente os problemas de conectividade a partir de ou com a Internet, e a partir de ou com o ambiente local.

A verificação de fluxo IP examina as regras para todos os grupos de segurança de rede (NSGs) aplicadas ao adaptador de rede, como uma NIC de máquina virtual ou sub-rede. O fluxo do tráfego é verificado com base nas configurações feitas para ou a partir dessa interface de rede. A verificação de fluxo de IP é útil ao confirmar se uma regra em um Grupo de Segurança da Rede está bloqueando o tráfego de entrada ou saída para ou a partir de uma máquina virtual.

Uma instância do Observador de Rede precisa ser criada em todas as regiões nas quais você pretende executar a verificação de fluxo de IP. O Observador de Rede é um serviço regional e só pode ser executado em recursos na mesma região. A instância usada não afeta os resultados da verificação de fluxo de IP, pois qualquer rota associada à NIC ou à sub-rede ainda é retornada.

![1][1]

## <a name="next-steps"></a>Próximas etapas

Visite o seguinte artigo para saber se um pacote é permitido ou negado para uma máquina virtual por meio do portal. [Verifique se o tráfego é permitido em uma VM com uma Verificação de Fluxo de IP usando o portal](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

