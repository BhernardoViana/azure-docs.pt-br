---
title: Habilitar o suporte para Avere vFXT - Azure
description: Como habilitar o suporte a transferências de Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 0f5eee20b0487fb5fce82047f40d137effb87ead
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164392"
---
# <a name="enable-support-uploads"></a>Habilitar uploads de suporte

O vFXT Avere para o Azure pode carregar automaticamente os dados de suporte sobre o cluster. Esses carregamentos permitem que a equipe de suporte fornecem o melhor serviço possível para o cliente.

## <a name="steps-to-enable-uploads"></a>Etapas para ativar uploads

Siga as etapas do painel de controle Avere para ativar o suporte. (Leia [Acesse o cluster vFXT](avere-vfxt-cluster-gui.md) para saber como abrir o painel de controle do Avere.)

1. Navegue até a **configurações** guia na parte superior.
1. Clique no link **Suporte** à esquerda e aceite a política de privacidade.

   ![Captura de tela mostrando o painel de Controle do Avere e uma janela pop-up com o botão Confirmar para aceitar a política de privacidade](media/avere-vfxt-privacy-policy.png)

1. Clique no triângulo à esquerda de **Informações do cliente** para expandir a seção.
1. Clique no botão **Revalidar informações de carregamento**.
1. Defina o nome do suporte do cluster em **Nome do Cluster Exclusivo** - certifique-se de que ele identifique exclusivamente seu cluster para suportar a equipe.
1. Marque as caixas de **Monitoramento de Estatísticas**, **Upload de Informações Gerais** e **Upload de Informações de Falhas**.
1. Clique em **Enviar**.

   ![Captura de tela que contém a seção de informações do cliente concluída da página de configurações de suporte](media/avere-vfxt-support-info.png)

1. Clique no triângulo à esquerda de **Secure Proactive Support (SPS)** para expandir a seção.
1. Marque a caixa **habilitar Link do SPS**.
1. Clique em **Enviar**.

   ![Captura de tela que contém a seção Suporte Proativo Seguro na página de configurações de suporte](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Próximas etapas

Se você precisar adicionar um sistema de armazenamento no local ao cluster, siga as instruções em [Configurar armazenamento](avere-vfxt-add-storage.md). 

Se você estiver pronto para começar a anexação de clientes para o cluster, leia [montar o cluster de vFXT Avere](avere-vfxt-mount-clients.md).