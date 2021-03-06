---
title: 'Gateway de VPN: cliente VPN para conexões P2S do protocolo OpenVPN: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: alzam
ms.openlocfilehash: 2836a89f491d731a11e6bc6fc56e0d049f01ac9a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151413"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication-preview"></a>Configurar um cliente VPN para conexões de protocolo P2S OpenVPN: autenticação do Azure AD (versão prévia)

Este artigo ajuda você a configurar um cliente VPN para se conectar a uma rede virtual usando VPN ponto a site e autenticação Azure Active Directory. Antes de poder se conectar e autenticar usando o Azure AD, você deve primeiro configurar seu locatário do Azure AD. Para obter mais informações, consulte [configurar um locatário do Azure ad](openvpn-azure-ad-tenant.md).

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

## <a name="profile"></a>Trabalhando com perfis de cliente

Para se conectar, você precisa baixar o cliente VPN do Azure (versão prévia) e configurar um perfil de cliente VPN em cada computador que deseja se conectar à VNet. Você pode criar um perfil de cliente em um computador, exportá-lo e, em seguida, importá-lo para computadores adicionais.

### <a name="to-download-the-azure-vpn-client"></a>Para baixar o cliente VPN do Azure

Use este [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) para baixar o cliente VPN do Azure (versão prévia).

### <a name="cert"></a>Para criar um perfil de cliente baseado em certificado

Ao trabalhar com um perfil baseado em certificado, verifique se os certificados apropriados estão instalados no computador cliente. Para obter mais informações sobre certificados, consulte [instalar certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Para criar um perfil de cliente RADIUS

  ![radiano](./media/openvpn-azure-ad-client/create/create-radius1.jpg)

### <a name="export"></a>Para exportar e distribuir um perfil de cliente

Quando tiver um perfil de trabalho e precisar distribuí-lo para outros usuários, você poderá exportá-lo usando as seguintes etapas:

1. Realce o perfil do cliente VPN que você deseja exportar, selecione **...** e, em seguida, selecione **Exportar**.

    ![Export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Selecione o local no qual você deseja salvar esse perfil, deixe o nome do arquivo como está e, em seguida, selecione **salvar** para salvar o arquivo XML.

    ![Export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Para importar um perfil de cliente

1. Na página, selecione **importar**.

    ![importar](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navegue até o arquivo XML do perfil e selecione-o. Com o arquivo selecionado, selecione **abrir**.

    ![importar](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Especifique o nome do perfil e selecione **salvar**.

    ![importar](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Selecione **conectar** para conectar-se à VPN.

    ![importar](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Uma vez conectado, o ícone ficará verde e diria **conectado**.

    ![importar](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Para excluir um perfil de cliente

1. Selecione as reticências ao lado do perfil do cliente que você deseja excluir. Em seguida, selecione **remover**.

    ![excluir](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Selecione **remover** para excluir.

    ![excluir](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Criar uma conexão

1. Na página, selecione **+** e **+ Adicionar**.

    ![connection](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Preencha as informações de conexão. Se você não tiver certeza dos valores, contate o administrador. Depois de preencher os valores, selecione **salvar**.

    ![connection](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Selecione **conectar** para conectar-se à VPN.

    ![connection](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Selecione as credenciais apropriadas e, em seguida, selecione **continuar**.

    ![connection](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Uma vez conectado com êxito, o ícone ficará verde e informará **conectado**.

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Para se conectar automaticamente

Essas etapas ajudam a configurar sua conexão para se conectar automaticamente com o Always on.

1. Na home page do cliente VPN, selecione configurações de **VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Selecione **Sim** na caixa de diálogo Alternar aplicativos.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Verifique se a conexão que você deseja definir ainda não está conectada e, em seguida, realce o perfil e marque a caixa de seleção **conectar automaticamente** .

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Selecione **conectar** para iniciar a conexão VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnosticar problemas de conexão

1. Para diagnosticar problemas de conexão, você pode usar a ferramenta de **diagnóstico** . Selecione o **...** ao lado da conexão VPN que você deseja diagnosticar para revelar o menu. Em seguida, selecione **diagnosticar**.

    ![tect](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na página **Propriedades da conexão** , selecione **Executar diagnóstico**.

    ![tect](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Entre com suas credenciais.

    ![tect](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Exiba os resultados do diagnóstico.

    ![tect](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [criar um locatário de Azure Active Directory para conexões VPN abertas do P2S que usam a autenticação do Azure ad](openvpn-azure-ad-tenant.md).
