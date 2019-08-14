---
title: Versões da Threat Modeling Tool – Microsoft Threat Modeling Tool Azure | Microsoft Docs
description: Documentando as notas sobre a versão da Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: d9c91752a599debcd8fda6c8396141e6a92e0f06
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928095"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Versão 7.1.60126.1 de atualização da Threat Modeling Tool – 29/01/2019

A versão 7.1.60126.1 do Microsoft Threat Modeling Tool foi lançada em 29 de janeiro de 2019 e contêm as seguintes alterações:

- A versão mínima necessária do .NET foi aumentada para o [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- A versão mínima exigida do Windows foi aumentada para a [Atualização de Aniversário do Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) devido à dependência do .NET.
- Um recurso de alternância de validação de modelo foi adicionado ao menu Opções da ferramenta.
- Vários links nas Propriedades da Ameaça foram atualizados.
- Pequenas alterações na experiência do usuário na home page da ferramenta.
- O Microsoft Threat Modeling Tool agora herda as configurações do TLS do sistema operacional do host e é compatível com ambientes que exigem o TLS 1.2 ou posterior.

## <a name="feature-changes"></a>Alterações de recurso

### <a name="model-validation-option"></a>Opção de validação de modelo

Com base nos comentários dos clientes, foi adicionada uma opção à ferramenta para habilitar ou desabilitar a validação do modelo. Anteriormente, se o modelo usasse um único fluxo de dados unidirecional entre dois objetos, você poderia ter recebido uma mensagem de erro no quadro Mensagens indicando: ObjectsName requer pelo menos um 'Any'. Desabilitar a validação do modelo impedirá que esses avisos apareçam na exibição.

A opção de ativar e desativar a validação de modelo pode ser encontrada no menu Arquivo -> Configurações -> Opções. O valor padrão dessa configuração é Desabilitado.

![Opção de validação de modelo](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Requisitos do sistema

- Supported Operating Systems
  - [Atualização de Aniversário do Microsoft Windows 10 ou posterior](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)
- Versão necessária do .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="unsupported-systems"></a>Sistemas sem suporte

#### <a name="issue"></a>Problema

Os usuários de sistemas Windows 10 que não puderem instalar o .NET 4.7.1 ou posterior, como o Windows 10 Enterprise LTSB (versão 1507), não poderão abrir a ferramenta após a atualização. Essas versões mais antigas do Windows não são mais plataformas compatíveis com o Threat Modeling Tool e não devem instalar a atualização mais recente.

#### <a name="workaround"></a>Solução alternativa

Os usuários do Windows 10 Enterprise LTSB (versão 1507) que instalaram a atualização mais recente podem reverter para a versão anterior do Threat Modeling Tool por meio da caixa de diálogo de desinstalação em Aplicativos e Recursos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](threat-modeling-tool.md) e inclui informações [sobre como usar a ferramenta](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
