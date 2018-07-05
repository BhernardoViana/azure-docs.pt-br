---
title: Noções básicas sobre políticas personalizadas do pacote de início no Azure Active Directory B2C | Microsoft Docs
description: Um tópico sobre as políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebcd7a677acde12558b0f566bce9172a0d00233b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442464"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Noções básicas sobre as políticas personalizadas do pacote starter de política personalizada do Azure AD B2C

Esta seção lista todos os principais elementos da política B2C_1A_base que acompanha o **pacote starter** e que é utilizada para criar suas próprias políticas por meio da herança da *política B2C_1A_base_extensions*.

Dessa forma, ela se concentra principalmente no tipo de declaração já definido, nas transformações de declarações, nas definições de conteúdo, nos provedores de declarações com seus perfis técnicos e nos percursos do usuário principal.

> [!IMPORTANT]
> A Microsoft não faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante. As alterações podem ser introduzidas a qualquer momento, antes do tempo de GA, no momento da GA, ou depois.

Suas próprias políticas e a política B2C_1A_base_extensions podem substituir essas definições e estender essa política pai fornecendo outros adicionais conforme necessário.

Os elementos principais da *política B2C_1A_base* são tipos de declaração, transformações de declarações e definições de conteúdo. Esses elementos podem estar suscetíveis a serem referenciados em suas próprias políticas, bem como na *política B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Esquemas de declarações

Esses esquemas de declarações são divididos em três seções:

1.  Uma primeira seção que lista as declarações mínimas necessárias para que os percursos do usuário funcionem corretamente.
2.  Uma segunda seção que lista as declarações necessárias para parâmetros de cadeia de caracteres de consulta e outros parâmetros especiais a serem transferidos para outros provedores de declarações, especialmente login.microsoftonline.com para autenticação. **Não modifique essas declarações**.
3.  E finalmente uma terceira seção que lista todas as declarações adicionais e opcionais que podem ser coletadas do usuário, armazenadas no diretório e enviadas em tokens durante a entrada. Novo tipo de declarações a serem coletadas do usuário e/ou enviadas no token podem ser adicionadas nesta seção.

> [!IMPORTANT]
> O esquema de declarações contém restrições em determinadas declarações, como senhas e nomes de usuário. A política de TF (Estrutura Confiável) trata o Microsoft Azure AD como qualquer outro provedor de declarações e todas as suas restrições são modeladas na política personalizada. Uma política pode ser modificada para adicionar mais restrições ou usar outro provedor de declarações para o armazenamento de credenciais, que terá suas próprias restrições.

Os tipos de declaração disponíveis estão listados abaixo.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Declarações que são necessárias para os percursos do usuário

As declarações a seguir são necessárias para que os percursos do usuário funcionem corretamente:

| Tipo de declarações | DESCRIÇÃO |
|-------------|-------------|
| *UserId* | Nome de Usuário |
| *signInName* | Nome de entrada |
| *tenantId* | ID (Identificador de Locatário) do objeto de usuário no Microsoft Azure Active Directory B2C |
| *objectId* | ID (Identificador de objeto) do objeto de usuário no Microsoft Azure Active Directory B2C |
| *password* | Senha |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Políticas de senha usadas pelo Microsoft Azure Active Directory B2C para determinar o nível de segurança da senha, expiração e etc. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | O número de telefone do usuário |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Endereço de email que pode ser usado para contatar o usuário |
| *signInNamesInfo.emailAddress* | Endereço de email que o usuário pode usar para a entrada |
| *otherMails* | Endereços de email que podem ser usados para contatar o usuário |
| *userPrincipalName* | Nome de usuário conforme armazenado no Microsoft Azure Active Directory B2C |
| *upnUserName* | Nome de usuário para criar o nome principal do usuário |
| *mailNickName* | Apelido de email do usuário conforme armazenado no Microsoft Azure Active Directory B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Declaração que especifica se os atributos foram coletados do usuário |
| *executed-PhoneFactor-Input* | Declaração que especifica se um novo número de telefone foi coletado do usuário |
| *authenticationSource* | Especifica se o usuário foi autenticado no provedor de identidade Social, login.microsoftonline.com ou conta local |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Declarações necessárias para parâmetros de cadeia de caracteres de consulta e outros parâmetros especiais

As seguintes declarações são necessárias para passar parâmetros especiais (incluindo alguns parâmetros de cadeia de caracteres de consulta) para outros provedores de declarações:

| Tipo de declarações | DESCRIÇÃO |
|-------------|-------------|
| *nux* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *nca* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *prompt* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *mkt* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *lc* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *grant_type* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *scope* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *client_id* | O parâmetro especial é passado para a autenticação de conta local para login.microsoftonline.com |
| *objectIdFromSession* | Parâmetro fornecido pelo provedor de gerenciamento de sessão padrão para indicar que a ID do objeto foi recuperada em uma sessão de SSO |
| *isActiveMFASession* | Parâmetro fornecido pelo gerenciamento de sessão de MFA para indicar que o usuário tem uma sessão ativa do MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Declarações adicionais (opcional) que podem ser coletadas

As seguintes declarações são declarações adicionais que podem ser coletadas dos usuários, armazenadas no diretório e enviadas no token. Como descrito antes, declarações adicionais podem ser adicionadas a essa lista.

| Tipo de declarações | DESCRIÇÃO |
|-------------|-------------|
| *givenName* | Nome do usuário (também conhecido como primeiro nome) |
| *surname* | Sobrenome do usuário (também conhecido como nome de família ou último nome) |
| *Extension_picture* | Imagem do usuário do social |

## <a name="claim-transformations"></a>Transformações de declaração

As transformações de declaração disponíveis estão listadas abaixo.

| Transformação de declaração | DESCRIÇÃO |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definições de conteúdo

Esta seção descreve as definições de conteúdo já declaradas na política *B2C_1A_base*. Essas definições de conteúdo são suscetíveis a serem referenciadas, substituídas e/ou estendidas conforme necessário nas suas próprias políticas, bem como na política *B2C_1A_base_extensions*.

| Provedor de declarações | DESCRIÇÃO |
|-----------------|-------------|
| *Facebook* | |
| *Local Account SignIn* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Autodeclarado* | |
| *Conta local* | |
| *Gerenciamento da Sessão* | |
| *Mecanismo da política do Trustframework* | |
| *TechnicalProfiles* | |
| *Emissor do token* | |

## <a name="technical-profiles"></a>Perfis técnicos

Esta seção descreve os perfis técnicos já declarados por provedor de declarações na política *B2C_1A_base*. Esses perfis técnicos são suscetíveis a serem referenciados, substituídos e/ou estendidos conforme necessário nas suas próprias políticas, bem como na política *B2C_1A_base_extensions*.

### <a name="technical-profiles-for-facebook"></a>Perfis técnicos para o Facebook

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *OAUTH do Facebook* | |

### <a name="technical-profiles-for-local-account-signin"></a>Perfis técnicos para entrada de conta Local

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Perfis técnicos de Phone Factor

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Perfis técnicos para Azure Active Directory

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *AAD-Common* | Perfil técnico incluído por outros perfis técnicos AAD-xxx |
| *AAD-UserWriteUsingAlternativeSecurityId* | Perfil técnico para logons sociais |
| *AAD-UserReadUsingAlternativeSecurityId* | Perfil técnico para logons sociais |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Perfil técnico para logons sociais |
| *AAD-UserWritePasswordUsingLogonEmail* | Perfil técnico para contas locais |
| *AAD-UserReadUsingEmailAddress* | Perfil técnico para contas locais |
| *AAD-UserWriteProfileUsingObjectId* | Perfil técnico para atualizar o registro de usuário usando o objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Perfil técnico para atualizar o registro de usuário usando o objectId |
| *AAD-UserWritePasswordUsingObjectId* | Perfil técnico para atualizar o registro de usuário usando o objectId |
| *AAD-UserReadUsingObjectId* | O perfil técnico é usado para ler os dados após a autenticação do usuário |

### <a name="technical-profiles-for-self-asserted"></a>Perfis técnicos para autodeclarados

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Perfis técnicos para conta local

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Perfis técnicos de gerenciamento de sessão

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | O nome do perfil está sendo usado para desambiguizar a sessão AAD entre a inscrição e a entrada |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Perfis técnicos para o mecanismo da política da estrutura confiável

Atualmente, nenhum perfil técnico está definido para o provedor de declarações do **TechnicalProfiles do mecanismo da política do Trustframework**.

### <a name="technical-profiles-for-token-issuer"></a>Perfis técnicos de emissor do Token

| Perfil técnico | DESCRIÇÃO |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Percursos do usuário

Esta seção descreve os percursos do usuário já declarados na política *B2C_1A_base*. Esses percursos do usuário são suscetíveis a serem referenciados, substituídos e/ou estendidos conforme necessário nas suas próprias políticas, bem como na política *B2C_1A_base_extensions*.

| Percurso do usuário | DESCRIÇÃO |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
