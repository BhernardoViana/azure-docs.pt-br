---
title: Visão geral do Azure Blockchain Workbench
description: Visão geral do Azure Blockchain Workbench e seus recursos.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/20/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 16e2497f4e26d16c008161477fd7d573e59e84db
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955194"
---
# <a name="what-is-azure-blockchain-workbench"></a>O que é o Azure Blockchain Workbench?

O Azure Blockchain Workbench é uma coleção de serviços e recursos do Azure desenvolvidos para ajudar você a criar e implantar aplicativos de blockchain para compartilhar processos comerciais e dados com outras organizações. O Azure Blockchain Workbench oferece o scaffolding da infraestrutura para desenvolver aplicativos de blockchain, permitindo que os desenvolvedores foquem na criação de lógica de negócios e contratos inteligentes. Ele também facilita a criação de aplicativos de blockchain integrando vários recursos e serviços do Azure para ajudar a automatizar tarefas comuns de desenvolvimento.

## <a name="create-blockchain-applications"></a>Criar aplicativos de blockchain

Com o Blockchain Workbench, você pode definir aplicativos de blockchain usando a configuração e escrever o código de contrato inteligente. Você pode iniciar rapidamente o desenvolvimento de aplicativos de blockchain e focar em definir o seu contrato e escrever a lógica de negócios em vez de criar um scaffolding e configurar serviços de suporte.

## <a name="manage-applications-and-users"></a>Gerenciar aplicativos e usuários

O Azure Blockchain Workbench fornece um aplicativo Web e APIs REST para o gerenciamento de usuários e aplicativos de blockchain. Os administradores do Blockchain Workbench podem gerenciar o acesso ao aplicativo e atribuir funções de aplicativo aos seus usuários. Os usuários do Azure AD são mapeados automaticamente para os membros no aplicativo.

## <a name="integrate-blockchain-with-applications"></a>Integrar o blockchain com os aplicativos

Você pode usar as APIs REST do Blockchain Workbench e APIs com base em mensagens para se integrar a sistemas existentes. As APIs oferecem uma interface que permite a substituição ou o uso de várias tecnologias de Razão distribuído, armazenamento e ofertas de banco de dados.

O Blockchain Workbench pode transformar as mensagens enviadas para a sua API baseada em mensagens para criar transações em um formato esperado pela API nativa do que blockchain.  O Workbench pode entrar e rotear transações para o blockchain apropriado. 

O Workbench oferece automaticamente eventos para que o Barramento de Serviço e a Grade de Eventos enviem mensagens para clientes downstream. Os desenvolvedores podem se integrar a esses sistemas de mensagens para gerar transações e ver os resultados.

## <a name="deploy-a-blockchain-network"></a>Implantar uma rede de blockchain

O Azure Blockchain Workbench simplifica a configuração do Consortium Blockchain Network como solução pré-configurada com um modelo de solução do Azure Resource Manager. O modelo fornece uma implantação simplificada que implanta todos os componentes necessários para executar um consórcio. Atualmente, o Blockchain Workbench oferece suporte para Ethereum.

## <a name="use-active-directory"></a>Usar o Active Directory

Com os protocolos de blockchain existentes, as identidades de blockchain são representadas como um endereço na rede. O Azure Blockchain Workbench abstrai a identidade do blockchain, associando-a a uma identidade do Active Directory, simplificando a criação de aplicativos empresariais com as identidades do Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Sincronizar dados em cadeia com armazenamento fora da cadeia

O Azure Blockchain Workbench facilita a análise dos dados e eventos de blockchain com a sincronização automática dos dados no blockchain para armazenamento fora da cadeia. Em vez de extrair dados diretamente do blockchain, você pode consultar sistemas de banco de dados off-chain, como o SQL Server. Não é necessário conhecimento de Blockchain para usuários finais que estão executando tarefas de análise de dados.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Arquitetura do Blockchain Workbench](architecture.md)
