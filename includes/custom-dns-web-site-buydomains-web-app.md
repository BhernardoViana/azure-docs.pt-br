---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531098"
---
Se você quiser um domínio, pode comprar domínios diretamente no [Portal de Gerenciamento do Azure](https://portal.azure.com). Use as etapas a seguir para comprar os nomes de domínio e atribuir ao seu aplicativo Web.

1. No seu navegador, abra o [Portal de Gerenciamento do Azure](https://portal.azure.com).
2. Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Configurações** e, em seguida, selecione **Domínios personalizados e SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Na folha **Domínios personalizados e SSL**, clique em **Comprar domínios**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. Na folha **Comprar domínios**, use a caixa de texto para digitar o nome de domínio que você deseja comprar. Os domínios disponíveis sugeridos serão mostrados apenas como mensagens da caixa de texto. Selecione o domínio que você deseja comprar.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Clique em **Informações de Contato** e preencha o formulário de informações de contato do domínio.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Clique em **Selecionar** na folha **Comprar domínios** e você verá as informações de compra na folha **Confirmação de compra**. Se você aceitar os termos legais e clicar em **Comprar**, seu pedido será enviado e você poderá monitorar o processo de compra em **Notificação**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Se você fez um pedido um domínio com êxito, pode gerenciar o domínio e atribuir ao seu aplicativo Web. Clique em **"..."** no lado direito do seu domínio. Depois disso, você pode **cancelar a compra** ou **gerenciar o domínio**. Clique em **Gerenciar Domínio** e assim poderemos associar o **subdomínio** ao nosso aplicativo Web na folha **Gerenciar Domínio**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Depois que a configuração estiver concluída, o nome do domínio personalizado será listado na seção **Vínculos de Hostname** de seu aplicativo Web.

Nesse ponto, você deve poder inserir o nome de domínio personalizado no navegador e ver se ele te leva com êxito ao aplicativo Web.

