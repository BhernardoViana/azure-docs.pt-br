---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 77c3aee4fdb5f8b2ee7ed83f92917573ad6ad529
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643600"
---
Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure Active Directory** (*não* Azure ad B2C). Ou então, selecione **todos os serviços** e, em seguida, pesquise e selecione **Azure Active Directory**.
1. Em **gerenciar**, selecione **registros de aplicativo (Herdado)** .
1. Selecione **Novo registro de aplicativo**.
1. Insira um nome para o aplicativo. Por exemplo, *managementapp1*.
1. Para **tipo de aplicativo**, selecione **aplicativo Web/API**.
1. Insira qualquer URL válida na **URL de logon**. Por exemplo: `https://localhost`. O ponto de extremidade não precisa estar acessível, mas deve ser uma URL válida.
1. Selecione **Criar**.
1. Registre a **ID do aplicativo** que aparece na página Visão geral do **aplicativo registrado** . Você usará esse valor em uma etapa posterior.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *managementapp1*.
1. Selecione **contas somente neste diretório organizacional**.
1. Em **permissões**, desmarque a caixa de seleção *conceder consentimento de administrador às permissões OpenID e offline_access* .
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** que aparece na página Visão geral do aplicativo. Você usará esse valor em uma etapa posterior.