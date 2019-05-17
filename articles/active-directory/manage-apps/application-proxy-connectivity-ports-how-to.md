---
title: Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy | Microsoft Docs
description: Descubra quais portas abrir para o Application Proxy do Azure AD funcionar corretamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e69f2e5049ca290a17c058c9d18dc7c6ec91f49
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783556"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Como abrir as portas de firewall necessárias para um aplicativo de Application Proxy

Para ver uma lista completa das portas necessárias e a função de cada porta, consulte a seção pré-requisitos da [documentação de Application Proxy](application-proxy-add-on-premises-application.md). observe que o Application Proxy usa apenas as portas de saída.

Você também pode verificar se você tem todas as portas abertas necessárias, abrindo o [ferramenta de teste de portas do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) da sua rede local. Mais marcas de seleção verde significa maior resiliência. 

## <a name="app-proxy-regions"></a>Regiões de Proxy de aplicativo

Estamos trabalhando em uma maneira para que você saiba que essas regiões precisam estar verdes para você. Por enquanto, certifique-se de que todas estão verdes. EUA Central também é necessária, independentemente de qual região você está.

Para certificar-se de que a ferramenta oferece os resultados certos, certifique-se de:

-   Abrir a ferramenta em um navegador do servidor na qual você instalou o conector.

-   Certifique-se de que quaisquer proxies ou firewalls aplicáveis a seu conector também estejam aplicados a esta página. Isso pode ser feito no Internet Explorer, vá para **as configurações**  - &gt; **opções da Internet**  - &gt; **conexões**  - &gt; **Configurações de LAN**. Nessa página, você vê o campo "Use um Servidor Proxy para sua LAN". Marque essa caixa e coloque o endereço do proxy no campo "Endereço".

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
