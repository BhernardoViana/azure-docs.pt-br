---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD'
description: Habilitar a autenticação multifator para usuários VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 3700014752233e29d157f1695906babe4fa35c74
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822767"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) do Azure para usuários VPN

Se desejar que os usuários sejam solicitados a fornecer um segundo fator de autenticação antes de conceder acesso, você poderá configurar a MFA (autenticação multifator) do Azure para seu locatário do Azure AD. As etapas neste artigo ajudam você a habilitar um requisito para a verificação em duas etapas.

## <a name="prereq"></a>Forem

O pré-requisito para essa configuração é um locatário do Azure AD configurado usando as etapas em [configurar um locatário](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Abrir a página MFA

1. Entre no Portal do Azure.
2. Navegue até **Azure Active Directory-> todos os usuários**.
3. Selecione **autenticação multifator** para abrir a página autenticação multifator.

   ![Entrar](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Selecionar usuários

1. Na página **autenticação multifator** , selecione os usuários para os quais você deseja habilitar a MFA.
2. Selecione **Habilitar**.

   ![Selecionar](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Habilitar autenticação

1. Navegue até **Azure Active Directory-> aplicativos empresariais-> todos os aplicativos**.
2. Na página **aplicativos empresariais – todos os aplicativos** , selecione **VPN do Azure**.

   ![ID do Diretório](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Definir configurações de entrada

Na página **VPN do Azure – Propriedades** , defina as configurações de entrada.

1. Definir **habilitado para que os usuários entrem?** para **Sim**. Isso permite que todos os usuários no locatário do AD se conectem à VPN com êxito.
2. Definir **atribuição de usuário necessária?** para **Sim** se você quiser limitar a entrada somente a usuários que têm permissões para a VPN do Azure.
3. Salve suas alterações.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Próximos passos

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [Configurar a autenticação do Azure ad para a conexão ponto a site com o Azure](virtual-wan-point-to-site-azure-ad.md).
