---
title: 'Gateway de VPN: locatário do Azure AD para conexões VPN P2S: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: alzam
ms.openlocfilehash: 6df8a9448873b418dc312ba572ba15d2da69bf32
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822715"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário de Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação de VPN aberta do P2S.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN®.
>

## <a name="tenant"></a>1. criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. criar usuários de locatário do Azure AD

Em seguida, crie duas contas de usuário. Crie uma conta de administrador global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário de locatário do Azure AD, você ajusta a função de diretório para o tipo de usuário que deseja criar.

Use as etapas neste [artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para seu locatário do Azure AD. Certifique-se de alterar a **função de diretório** para criar os tipos de conta:

* Administrador global
* Usuário

## <a name="enable-authentication"></a>3. habilitar a autenticação do Azure AD no gateway de VPN

1. Localize a ID de diretório do diretório que você deseja usar para autenticação. Ele é listado na seção Propriedades da página Active Directory.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copie a ID do diretório.

3. Entre no portal do Azure como um usuário que é atribuído à função de **administrador global** .

4. Em seguida, dê consentimento ao administrador. Copie e cole a URL que pertence ao seu local de implantação na barra de endereços do seu navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Governamental

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta de **administrador global** , se solicitado.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecione **aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Em seu Azure AD, em **aplicativos empresariais**, você vê a **VPN do Azure** listada.

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Habilite a autenticação do Azure AD no gateway de VPN executando os comandos a seguir, certifique-se de modificar o comando para refletir seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/"
    ```

9. Crie e baixe o perfil executando os comandos a seguir. Altere os valores-ResourcGroupName e-Name para que correspondam aos seus próprios.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

10. Depois de executar os comandos, você verá um resultado semelhante ao mostrado abaixo. Copie a URL do resultado para o navegador para baixar o arquivo zip do perfil.

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/profile.png)

11. Extraia o arquivo zip baixado.

12. Navegue até a pasta "AzureVPN" descompactada.

13. Anote o local do arquivo "azurevpnconfig. xml". O azurevpnconfig. xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.

## <a name="next-steps"></a>Próximos passos

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [Configurar a autenticação do Azure ad para a conexão ponto a site com o Azure](virtual-wan-point-to-site-azure-ad.md).
