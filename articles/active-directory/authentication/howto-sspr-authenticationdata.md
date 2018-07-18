---
title: Requisitos de dados SSPR do Azure AD | Microsoft Docs
description: Requisitos de dados para autoatendimento de redefinição de senha do Azure AD e como atendê-los
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5409bf198d0e3f6537619ef4698d9f2e31bd27c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257580"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Implantar redefinição de senha sem exigir registro do usuário final

Para implantar a redefinição de senha de autoatendimento (SSPR) do Azure Active Directory (Azure AD), os dados de autenticação precisam estar presentes. Algumas organizações fazem com que seus usuários insiram seus próprios dados de autenticação. Mas muitas organizações preferem a sincronização com os dados que já existem no Active Directory. Os dados sincronizados serão disponibilizados para o Azure AD e SSPR sem a necessidade de interação do usuário se você:
   * Formatar corretamente os dados em seu diretório local.
   * Configurar o [Azure AD Connect usando as configurações expressas](./../connect/active-directory-aadconnect-get-started-express.md).

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: +1 4255551234.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes que a chamada seja completada.

## <a name="fields-populated"></a>Campos populados

Se você usar as configurações padrão no Azure AD Connect, serão realizados os seguintes mapeamentos:

| Active Directory local | AD do Azure |
| --- | --- |
| telephoneNumber | Telefone comercial |
| Serviço Móvel | Telefone celular |

Quando um usuário confirma o número de telefone celular, o campo de telefone em informações de Contato de autenticação no Microsoft Azure Active Directory também será populado com esse número.

## <a name="authentication-contact-info"></a>Informações de contato de autenticação

Um Administrador Global pode definir manualmente as informações de contato de autenticação para um usuário conforme exibido na captura de tela a seguir.

![Contato][Contact]

Se o campo de telefone for preenchido e o telefone celular estiver habilitado na política de SSPR, o usuário verá o número na página de registro de redefinição de senha e a senha durante a redefinição de fluxo de trabalho. 

O campo de telefone alternativo não é usado para redefinição de senha.

Se o campo de telefone for preenchido e o email estiver habilitado na política de SSPR, o usuário verá o email na página de registro de redefinição de senha e a senha durante a redefinição de fluxo de trabalho.

Se o campo email alternativo for preenchido e o email estiver habilitado na política de SSPR, o usuário **não** verá o email na página de registro de redefinição de senha, mas ainda a verão durante a redefinição de fluxo de trabalho. 


## <a name="security-questions-and-answers"></a>Perguntas e respostas de segurança

As perguntas e respostas de segurança são armazenadas em seu locatário do Azure AD e podem ser acessadas somente por usuários pelo [Portal de registro do SSPR](https://aka.ms/ssprsetup). Os administradores não podem ver nem modificar o conteúdo das perguntas e respostas dos outros usuários.

### <a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário se registra

Quando um usuário se registra, a página de registro define os seguintes campos:

* **Telefone de autenticação**
* **Email de autenticação**
* **Perguntas e respostas de segurança**

Se você forneceu um valor para **Celular** ou **Email Alternativo**, os usuários poderão usar esses valores imediatamente para redefinir as senhas, mesmo que não tenham se registrado no serviço. Além disso, os usuários visualizam esses valores ao se registrarem pela primeira vez, e os modificam, se for desejado. Após o registro bem-sucedido, esses valores serão mantidos nos campos **Telefone de Autenticação** e **Email de Autenticação**, respectivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Definir e ler os dados de autenticação usando o PowerShell

Os campos a seguir podem ser definidos usando o PowerShell:

* **Email alternativo**
* **Celular**
* **Telefone comercial**: só poderá ser definido se não for sincronizar com um diretório local

### <a name="use-powershell-version-1"></a>Usar o PowerShell versão 1

Para começar, primeiramente é preciso [baixar e instalar o módulo PowerShell do Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Definir dados de autenticação com o PowerShell versão 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Ler dados de autenticação com o PowerShell versão 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Ler as opções de Telefone de Autenticação e Email de Autenticação

Para ler o **Telefone de Autenticação** e **Email de Autenticação** quando você usa o PowerShell versão 1, use os seguintes comandos:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Usar o PowerShell versão 2

Para começar, é preciso [baixar e instalar o módulo PowerShell do Azure AD versão 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Depois de instalá-lo, você poderá seguir as etapas abaixo para configurar cada campo.

Para instalar rapidamente de versões recentes do PowerShell que dão suporte ao Módulo de instalação, execute os seguintes comandos. (A primeira linha verifica se o módulo já está instalado.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Definir dados de autenticação com o PowerShell versão 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Ler dados de autenticação com o PowerShell versão 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Os administradores globais podem modificar informações de contato de autenticação de um usuário"
