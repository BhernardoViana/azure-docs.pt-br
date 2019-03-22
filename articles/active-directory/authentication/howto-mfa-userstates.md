---
title: Estados do usuário autenticação multifator do Azure - Active Directory do Azure
description: Saiba mais sobre os estados do usuário na Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5411770e6f9d660557ab9360f026efe4c28a9256
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314375"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Como exigir a verificação em duas etapas para um usuário

Você pode adotar uma das duas abordagens para solicitar a verificação em duas etapas, sendo que ambas exigem o uso de uma conta de administrador global. A primeira opção é habilitar cada usuário para a Autenticação Multifator do Azure (MFA). Quando os usuários são habilitados individualmente, eles executam a verificação em duas etapas em cada entrada (com algumas exceções, como ao entrarem a partir de endereços IP confiáveis, ou quando o recurso de _dispositivos lembrados_ estiver ativado). A segunda opção é configurar uma política de acesso condicional que exige a verificação em duas etapas sob determinadas condições.

> [!TIP]
> Escolha um destes métodos para exigir a verificação em duas etapas, não ambos. A habilitação de um usuário para a Autenticação Multifator do Azure substitui quaisquer políticas de acesso condicional.

## <a name="choose-how-to-enable"></a>Escolha como habilitar

**Habilitado alterando o estado do usuário** - Esse é o método tradicional para exigir a verificação em duas etapas e é abordado neste artigo. Ele funciona tanto para MFA do Azure na nuvem como Servidor de MFA do Azure. O uso desse método exigirá que os usuários realizem a verificação em duas etapas **sempre que** entrarem e substituírem as políticas de acesso condicional. Esse é o método usado para as pessoas com licenças do Office 365 ou do Microsoft 365 Business, pois não incluem recursos de acesso condicional.

Habilitado pela política de acesso condicional - Esse é o meio mais flexível de habilitar a verificação em duas etapas para usuários. Habilitar usando política de acesso condicional somente funciona para MFA do Azure na nuvem e é um recurso Premium do Azure AD. Mais informações sobre esse método podem ser encontradas em [Implantar Autenticação Multifator do Microsoft Azure baseada em nuvem](howto-mfa-getstarted.md).

Habilitado pelo Azure AD Identity Protection - Esse método usa a política de risco do Azure AD Identity Protection para exigir a verificação em duas etapas com base apenas no risco de entrada para todos os aplicativos na nuvem. Este método requer o licenciamento do Azure Active Directory P2. Mais informações sobre esse método podem ser encontradas em [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mais informações sobre licenças e preços podem ser encontradas nas páginas de preço do [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="enable-azure-mfa-by-changing-user-state"></a>Habilitar o MFA do Azure alterando o estado do usuário

As contas de usuário na Autenticação Multifator do Azure apresentam os três estados distintos a seguir:

| Status | Descrição | Aplicativos que não usam navegador afetados | Aplicativos que usam o navegador afetados | Autenticação moderna afetada |
|:---:|:---:|:---:|:--:|:--:|
| Desabilitado |O estado padrão para um novo usuário não inscrito na MFA do Azure. |Não |Não |Não |
| Ativado |O usuário foi inscrito no MFA do Azure, mas não foi registrado. Eles receberão uma solicitação para se registrarem na próxima vez que entrarem. |Nº.  Eles continuarão a trabalhar até o processo ser concluído. | Sim. Depois que a sessão expirar, será exigido o registro da MFA do Azure.| Sim. Depois que o token de acesso expirar, será exigido o registro da MFA do Azure. |
| Imposto |O usuário foi inscrito e concluiu o processo de registro para usar a MFA do Azure. |Sim. Os aplicativos exigem senhas de aplicativo. |Sim. A MFA do Azure é exigida no logon. | Sim. A MFA do Azure é exigida no logon. |

O estado de um usuário reflete se um administrador o registrou na MFA do Azure e se ele concluiu o processo de registro.

Todos os usuários começam com o status *Desabilitado*. Quando você registra os usuários na MFA do Azure, seu estado é alterado para *Habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *Imposto*.  

### <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as etapas a seguir para acessar a página em que você pode exibir e gerenciar estados de usuário:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
2. Acesse **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários**.
3. Selecione **Autenticação Multifator**.
   ![Selecionar a Autenticação Multifator](./media/howto-mfa-userstates/selectmfa.png)
4. Uma nova página que exibe os estados do usuário é aberta.
   ![estados do usuário da Autenticação Multifator do Azure – captura de tela](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

1. Use as etapas anteriores para chegar à página de **usuários** da Autenticação Multifator do Azure.
2. Localize o usuário que deseja habilitar para a MFA do Azure. Talvez seja necessário alterar o modo de exibição na parte superior.
   ![Localizar usuário – captura de tela](./media/howto-mfa-userstates/enable1.png)
3. Marque a caixa, ao lado do nome.
4. À direita, em **etapas rápidas**, escolha **Habilitar** ou **Desabilitar**.
   ![Habilitar o usuário selecionado – captura de tela](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Usuários *Habilitados* mudam automaticamente para *Imposto* ao se registrarem na MFA do Azure. Não altere manualmente o estado do usuário para *Imposto*.

5. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Informe que eles receberão uma solicitação de registro na próxima vez que entrarem. Além disso, se sua organização usar aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Você também pode incluir um link para o [Guia do usuário final da MFA do Azure](../user-help/multi-factor-authentication-end-user.md) para ajudá-los a começar.

### <a name="use-powershell"></a>Usar o PowerShell

Para alterar o estado do usuário usando o [PowerShell do Azure AD](/powershell/azure/overview), altere `$st.State`. Há três opções de estado possíveis:

* Ativado
* Imposto
* Desabilitado  

Não mova os usuários diretamente para o estado *Imposto*. Caso os mova, aplicativos não baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro do MFA do Azure nem obteve uma [senha de aplicativo](howto-mfa-mfasettings.md#app-passwords).

Instale o módulo em primeiro lugar, usando:

       Install-Module MSOnline
       
> [!TIP]
> Não se esqueça de se conectar pela primeira vez usando **Connect-MsolService**


 Este exemplo de script do PowerShell habilita a MFA para um usuário individual:

        Import-Module MSOnline
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. Por exemplo, o script a seguir executa um loop por uma lista de usuários e habilita a MFA em suas contas:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }
    
Para desabilitar a MFA, use este script:

    Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
    
que também pode ser encurtado para:

    Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()

## <a name="next-steps"></a>Próximas etapas

Por que foi um usuário solicitado ou não é solicitado a executar a MFA? Consulte a seção [relatório de entradas do Microsoft Azure Active Directory nos relatórios do documento de autenticação multifator do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Para definir configurações adicionais, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraudes, consulte o artigo [Definir configurações de Autenticação Multifator do Microsoft Azure](howto-mfa-mfasettings.md)

Informações sobre como gerenciar configurações de usuário para Autenticação Multifator do Microsoft Azure podem ser encontradas no artigo [Gerenciar configurações do usuário com Autenticação Multifator do Microsoft Azure na nuvem](howto-mfa-userdevicesettings.md)
