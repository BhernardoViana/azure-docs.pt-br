---
title: Configurar o PowerShell para criar uma VM para o Marketplace | Microsoft Docs
description: Instruções para configurar o Azure PowerShell e usar como um fluxo de processo opcional para criar imagens VM para implantar e vender no Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: e5175558f18dfc903c280ea6bbe487e0a3ee8189
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076596"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurar o Azure PowerShell para criar a oferta para o Azure Marketplace

Para obter informações detalhadas sobre como configurar o PowerShell no Azure, consulte, [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Uma abordagem simples é usar o método de certificado que baixa e importa um certificado necessário para autenticar. Para obter o certificado necessário, use o cmdlet **Get-AzurePublishSettingsFile** . Quando for solicitado, salve o arquivo. Para importar o certificado em uma sessão do PowerShell, use o cmdlet **Import-AzurePublishSettingsFile** .

Para configurar e armazenar as configurações de assinatura do Microsoft Azure comuns para a sessão do PowerShell, use os cmdlets **Set-AzureSubscription** e **Select-AzureSubscription**:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

O primeiro comando associa uma conta de armazenamento padrão com a assinatura (necessária para algumas operações de provisionamento da VM).  O segundo faz a assinatura do ano atual (reconhecido por outros cmdlets).

Para obter mais informações, consulte os seguintes artigos:
* [Introdução: publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* [Criar uma imagem de máquina virtual para o Marketplace](marketplace-publishing-vm-image-creation.md)

