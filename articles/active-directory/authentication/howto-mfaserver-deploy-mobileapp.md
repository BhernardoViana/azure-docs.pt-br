---
title: Serviço Web de aplicativo móvel do servidor MFA do Azure | Microsoft Docs
description: Configure o servidor MFA para enviar notificações por push para usuários com o Aplicativo Autenticador da Microsoft.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: f6b50554e00e247d564f2be5bf579977f3bea9cf
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitar a autenticação de aplicativo móvel com o Servidor de Autenticação Multifator do Azure

O aplicativo Microsoft Authenticator oferece uma opção de verificação adicional fora de banda. Em vez de enviar uma chamada telefônica automatizada ou um SMS para o usuário durante o logon, a Autenticação Multifator do Azure envia uma notificação por push ao aplicativo Microsoft Authenticator no smartphone ou tablet do usuário. O usuário simplesmente toca em **Verificar** (ou digita um PIN e toca em "Autenticar") no aplicativo para concluir a entrada.

É preferível a utilização de um aplicativo móvel para a verificação em duas etapas quando a recepção do telefone não é confiável. Se você usar o aplicativo como um gerador de token OATH, ele não exigirá nenhuma conexão de rede ou Internet.

Dependendo do seu ambiente, convém implantar o serviço Web do aplicativo móvel no mesmo servidor do Servidor de Autenticação Multifator do Azure ou em outro servidor voltado para a internet.

> [!IMPORTANT]
> Se tiver instalado o servidor MFA versão 8.0 ou superior, a maioria das etapas a seguir não é necessária. A autenticação do aplicativo móvel pode ser configurada seguindo as etapas em [Configurar o aplicativo móvel](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Requisitos

Para usar o aplicativo Microsoft Authenticator e para que ele se comunique com êxito com o Serviço Web do Aplicativo Móvel, é preciso fazer o seguinte:

* Servidor de Autenticação Multifator do Azure v6.x ou superior
* Instalar o Serviço Web do Aplicativo Móvel em um servidor Web da Internet executando o [IIS (Serviços de Informações da Internet) IIS 7.x ou superior](http://www.iis.net/) da Microsoft®
* Verificar se o ASP.NET v4.0.30319 está instalado, registrado e definido como Permitido
* Os serviços de função obrigatórios incluem o ASP.NET e a Compatibilidade de Metabase do IIS 6
* O Serviço Web do Aplicativo Móvel está acessível por meio de uma URL pública
* O Serviço Web do Aplicativo Móvel está protegido com um certificado SSL.
* Instalar o SDK de Serviço Web da Autenticação Multifator do Azure no IIS 7.x ou superior no **mesmo servidor do Servidor de Autenticação Multifator do Azure**
* O SDK de Serviço Web da Autenticação Multifator do Azure está protegido com um certificado SSL.
* O Serviço Web do Aplicativo Móvel pode conectar-se ao SDK de Serviço Web da Autenticação Multifator do Azure por SSL
* O Serviço Web do Aplicativo Móvel pode autenticar-se no SDK de Serviço Web da MFA do Azure usando as credenciais de uma conta de serviço que seja membro do grupo de segurança "Administradores de PhoneFactor". Essa conta de serviço e o grupo existem no Active Directory se o Servidor de Autenticação Multifator do Azure está em um servidor ingressado em domínio. Essa conta de serviço e o grupo existem localmente no Servidor de Autenticação Multifator do Azure se ele não tiver ingressado em um domínio.

## <a name="install-the-mobile-app-web-service"></a>Instalar o Serviço Web de Aplicativos Móveis

Antes de instalar o Serviço Web do Aplicativo Móvel, esteja ciente dos seguintes detalhes:

* A instalação do serviço Web de aplicativos móveis não é necessária para a versão v 8.0 ou superior. Conclua somente as etapas em [Configurar o aplicativo móvel](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).
* Você precisa de uma conta de serviço que faça parte do grupo "Administradores do PhoneFactor". Essa conta pode ser a mesma usada para a instalação do portal do usuário.
* É recomendável abrir um navegador Web no servidor Web da Internet e navegar até a URL do SDK de Serviço Web que foi inserido no arquivo web.config. Se o navegador conseguir chegar ao serviço Web com êxito, ele deve solicitar credenciais. Insira o nome de usuário e a senha que foram inseridos no arquivo web.config exatamente como ele aparece no arquivo. Certifique-se de que nenhum aviso de certificado ou erro seja exibido.
* Se um proxy reverso ou firewall estiver à frente do servidor Web do Serviço Web do Aplicativo Móvel e executar o descarregamento de SSL, edite o arquivo web.config do Serviço Web do Aplicativo Móvel para que o Serviço Web do Aplicativo Móvel possa usar http em vez de https. O SSL ainda é exigido do Aplicativo Móvel para o firewall/proxy reverso. Adicione a seguinte chave à seção \<appSettings\>:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Instalar o SDK de Serviço Web

Seja qual for o cenário, se o SDK de Serviço Web da Autenticação Multifator do Azure ainda **não** estiver instalado no Servidor da MFA (Autenticação Multifator) do Azure, conclua as etapas a seguir.

1. Abra o console do Servidor de Autenticação Multifator.
2. Vá para o **SDK do serviço Web** e selecione **Instalar o SDK do serviço Web**.
3. Conclua a instalação usando os padrões, a não ser que você precise alterá-los por algum motivo.
4. Associe um certificado SSL ao site no IIS.

Se você tem dúvidas sobre como configurar um Certificado SSL em um servidor IIS, confira o artigo [Como configurar o SSL no IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

O SDK de Serviço Web deve ser protegido com um certificado SSL. Um certificado autoassinado é suficiente para essa finalidade. Importe o certificado para o repositório de "Autoridades de Certificação Confiáveis" da conta do Computador Local no servidor Web do Portal do Usuário, para que ele confie no certificado ao iniciar a conexão SSL.

![SDK do serviço Web de configuração do Servidor MFA](./media/howto-mfaserver-deploy-mobileapp/sdk.png)

### <a name="install-the-service"></a>Instalar o serviço

1. **No Servidor MFA**, navegue até o caminho de instalação.
2. Navegue até a pasta onde o Servidor MFA do Azure está instalado. O padrão é **C:\Program Files\Autenticação Multifator do Azure**.
3. Localize o arquivo de instalação **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Se o servidor **não** estiver voltado para a Internet, copie o arquivo de instalação para o servidor voltado para a Internet.
4. Se o Servidor MFA **não** estiver voltado para a Internet, alterne para um **servidor voltado para a Internet**.
5. Execute o arquivo de instalação **MultiFactorAuthenticationMobileAppWebServiceSetup64** como administrador, mude o site, se desejar, e encurte o nome do diretório virtual, se desejar.
6. Após concluir a instalação, navegue até **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (ou o diretório apropriado com base no nome do diretório virtual) e edite o arquivo Web.Config.

   * Localize a chave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e altere **value=""** para **value="DOMAIN\User"**, em que DOMAIN/User é uma conta de serviço que faz parte do Grupo “Administradores do PhoneFactor”.
   * Localize a chave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e altere **value=""** para **value="Password"**, em que Password é a senha da conta de serviço inserida na linha anterior.
   * Encontre a configuração **pfMobile App Web Service_pfwssdk_PfWsSdk** e altere o valor de **http://localhost:4898/PfWsSdk.asmx** para a URL do SDK do Serviço Web (por exemplo: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Salve o arquivo Web.Config e feche o Bloco de Notas.

   > [!NOTE]
   > Como o SSL é usado para esta conexão, você deve referenciar o SDK do Serviço Web pelo **FQDN (nome de servidor totalmente qualificado)** e **não pelo endereço IP**. O certificado SSL seria emitido para o FQDN e a URL usada deveria corresponder ao nome no certificado.

7. Se o site em que o Serviço Web do Aplicativo Móvel foi instalado ainda não tiver sido associado com um certificado assinado publicamente, instale o certificado no servidor, abra o Gerenciador do IIS e associe o certificado ao site.
8. Abra um navegador Web em qualquer computador e navegue até a URL na qual o Serviço Web de Aplicativos Móveis foi instalado (por exemplo, https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.
9. Para obter mais informações sobre os métodos disponíveis no SDK de serviços Web, consulte o arquivo de ajuda do servidor MFA.
10. Agora que o Serviço Web de Aplicativos Móveis está instalado, você precisa configurar o Servidor de Autenticação Multifator do Azure para trabalhar com o portal.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições do aplicativo móvel no Servidor de Autenticação Multifator do Azure

1. No console do Servidor de Autenticação Multifator, clique no ícone Portal do Usuário. Se os usuários têm permissão para controlar seus métodos de autenticação, marque **Aplicativo Móvel** na guia Configurações, em **Permitir que usuários selecionem o método**. Sem esse recurso habilitado, os usuários finais precisam entrar em contato com o Suporte Técnico para concluir a ativação para Aplicativos Móveis.
2. Marque a caixa **Permitir que usuários ativem o aplicativo móvel**.
3. Marque a caixa **Permitir registro de usuário**.
4. Clique no ícone **Aplicativos Móveis**.
5. Se estiver usando a versão v8.0 ou superior, ignore esta etapa: insira a URL sendo usada com o diretório virtual que foi criado durante a instalação de MultiFactorAuthenticationMobileAppWebServiceSetup64 (por exemplo: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) no campo **URL do Serviço Web dos Aplicativos Móveis:**.
6. Preencha o campo **Nome da conta** com o nome da empresa ou organização a ser exibido no aplicativo móvel para a conta.
   ![Configurações de Aplicativo Móvel de configuração do Servidor MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Próximas etapas

- [Cenários avançados com a Autenticação Multifator do Azure e VPNs de terceiros](howto-mfaserver-nps-vpn.md).
