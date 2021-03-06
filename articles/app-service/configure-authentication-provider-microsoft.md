---
title: Configurar a autenticação da Microsoft
description: Saiba como configurar a autenticação da conta da Microsoft como um provedor de identidade para seu aplicativo do serviço de aplicativo.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: f9158a4094b7d2ec148c2cae85decb3ad959b7c3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671919"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Configurar seu aplicativo do serviço de aplicativo para usar o logon da conta da Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar a conta da Microsoft como um provedor de autenticação. 

## <a name="register-microsoft-account"> </a>Registrar seu aplicativo na conta da Microsoft

1. Vá para [**registros de aplicativo**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) na portal do Azure. Se necessário, entre com seu conta Microsoft.
1. Selecione **novo registro**e insira um nome de aplicativo.
1. Em **URIs de redirecionamento**, selecione **Web**e, em seguida, insira `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`. Substitua *\<app-domain-name >* pelo nome de domínio do seu aplicativo.  Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Certifique-se de usar o esquema HTTPS na URL.

1. Selecione **Registrar**.
1. Copie a **ID do aplicativo (cliente)** . Você precisará dela mais tarde.
1. No painel esquerdo, selecione **certificados & segredos** > **novo segredo do cliente**. Insira uma descrição, selecione a duração da validade e selecione **Adicionar**.
1. Copie o valor que aparece na página **certificados & segredos** . Depois de sair da página, ela não será exibida novamente.

    > [!IMPORTANT]
    > A senha é uma credencial de segurança importante. Não compartilhe a senha com ninguém nem distribua-a em um aplicativo cliente.

## <a name="secrets"> </a>Adicionar informações da conta da Microsoft ao seu aplicativo de Serviço de Aplicativo

1. Vá para seu aplicativo no [Azure portal].
1. Selecione **configurações** > **autenticação/autorização**e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
1. Em **provedores de autenticação**, selecione **conta da Microsoft**. Cole a ID do aplicativo (cliente) e o segredo do cliente obtidos anteriormente. Habilite todos os escopos necessários para seu aplicativo.
1. Selecione **OK**.

   O serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.

1. Adicional Para restringir o acesso a conta Microsoft usuários, defina **a ação a ser tomada quando a solicitação não for autenticada** para **fazer logon com a conta da Microsoft**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para conta Microsoft para autenticação.

   > [!CAUTION]
   > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
