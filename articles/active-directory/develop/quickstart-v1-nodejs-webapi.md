---
title: Proteger uma API Web com o Azure AD | Microsoft Docs
description: Saiba como criar uma API Web REST do Node.js que se integre ao Azure AD para autenticação.
services: active-directory
documentationcenter: nodejs
author: rwike77
manager: CelesteDG
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dcbe10c08d581e2ec01e1616da49b7a1b31018d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545988"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Início Rápido: proteger uma API Web com o Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Neste início rápido, você aprenderá a proteger um ponto de extremidade de API [Restify](http://restify.com/) com o [Microsoft Passport](http://passportjs.org/) usando o módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) para lidar com a comunicação com o Azure AD (Azure Active Directory).

O escopo deste início rápido abrange as preocupações relacionadas à proteção de pontos de extremidade de API. As preocupações sobre entrar e manter os tokens de autenticação não são implementadas aqui e são de responsabilidade de um aplicativo cliente. Para obter detalhes relativos a uma implementação de cliente, examine [Entrada e saída do aplicativo Web Node.js com o Azure AD](quickstart-v1-openid-connect-code.md).

O exemplo de código completo associado a este artigo está disponível no [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos.

### <a name="create-the-sample-project"></a>Criar o projeto de exemplo

O aplicativo para servidores requer algumas dependências do pacote para dar suporte ao Restify e ao Passport, bem como dados da conta que são passados para o Azure AD.

Para começar, adicione o código a seguir em um arquivo chamado `package.json`:

```Shell
{
  "name": "active-directory-webapi-nodejs",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "4.0.0",
    "restify": "7.7.0"
  }
}
```

Uma vez que `package.json` é criado, execute `npm install` no prompt de comando para instalar as dependências do pacote.

#### <a name="configure-the-project-to-use-active-directory"></a>Configure o projeto para usar o Active Directory

Para começar a configurar o aplicativo, há alguns valores específicos da conta que você pode obter com a CLI do Azure. É a maneira mais fácil de começar a usar a CLI é usar o Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Insira o comando a seguir no Cloud Shell:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Os [argumentos](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) para o comando `create` incluem:

| Argumento  | DESCRIÇÃO |
|---------|---------|
|`display-name` | Nome amigável do registro |
|`homepage` | URL em que os usuários podem entrar e usar o aplicativo |
|`identifier-uris` | URIs exclusivos separados por espaços que o Azure AD pode usar para este aplicativo |

Antes de você pode se conectar ao Azure Active Directory, você precisa das seguintes informações:

| NOME  | DESCRIÇÃO | Nome da variável no arquivo de configuração |
| ------------- | ------------- | ------------- |
| Nome do locatário  | O [nome do locatário](quickstart-create-new-tenant.md) que você deseja usar para autenticação | `tenantName`  |
| ID do cliente  | A ID do cliente é o termo de OAuth usado para a _ID do aplicativo_ do AAD. |  `clientID`  |

Com base na resposta de registro no Azure Cloud Shell, copie o valor `appId` e crie um novo arquivo denominado `config.js`. Em seguida, adicione o código a seguir e substitua seus valores com os tokens entre colchetes:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```

Para obter mais informações sobre as configurações individuais, examine a documentação do módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

### <a name="implement-the-server"></a>Implementar o servidor

O módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) apresenta duas estratégias de autenticação: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) e de [Portador](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). O servidor implementado neste artigo usa a estratégia de Portador para proteger o ponto de extremidade de API.

### <a name="step-1-import-dependencies"></a>Etapa 1: Importar dependências

Crie um novo arquivo chamado `app.js` e cole o texto a seguir:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require ('restify').plugins
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

Nesta seção de código:

- Os módulos `restify` e de plug-ins são referenciados para a configuração de um servidor Restify.
- Os módulos `passport` e `passport-azure-ad` são responsáveis pela comunicação com o Azure AD.
- A variável `config` é inicializada com valores do arquivo `config.js` criado na etapa anterior.
- Uma matriz é criada para `authenticatedUserTokens` para armazenar os tokens de usuário conforme eles são passados para pontos de extremidade protegidos.
- O `serverPort` é definido do arquivo de configuração ou da porta do ambiente de processo.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Etapa 2: Criar uma instância de uma estratégia de autenticação

Ao proteger um ponto de extremidade, você deve fornecer uma estratégia responsável por determinar se a solicitação atual se origina ou não de um usuário autenticado. Aqui a variável `authenticatonStrategy` é uma instância da classe `BearerStrategy` de `passport-azure-ad`. Adicione o código a seguir após as instruções `require`.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```

Essa implementação usa o registro automático adicionando tokens de autenticação para a matriz `authenticatedUserTokens` se eles ainda não existem.

Depois de criar uma nova instância da estratégia, você deve passá-la para o Passport por meio do método `use`. Adicione o código a seguir a `app.js` para usar a estratégia no Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Etapa 3: Configuração de Servidor

Com a estratégia de autenticação definida, você pode configurar o servidor Restify com algumas configurações básicas e definido para usar o Passport para segurança.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Este servidor será inicializado e configurado para analisar os cabeçalhos de autorização e, em seguida, será definido para usar o Passport.

### <a name="step-4-define-routes"></a>Etapa 4: Definir rotas

Agora você pode definir rotas e decidir qual delas proteger com o Azure AD. Esse projeto inclui duas rotas em que o nível raiz é aberto e a rota `/api` é definida para exigir autenticação.

Em `app.js`, adicione o código a seguir para a rota de nível raiz:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

A rota de raiz permite todas as solicitações por meio da rota e retorna uma mensagem que inclui um comando para testar a rota `/api`. Por outro lado, a rota `/api` é bloqueada usando [`passport.authenticate`](http://passportjs.org/docs/authenticate). Adicione o código a seguir após a rota raiz.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Essa configuração permite apenas solicitações autenticadas que incluem um acesso de token de portador para `/api`. A opção de `session: false` é usada para desabilitar sessões para exigir que um token seja passado com cada solicitação à API.

Por fim, o servidor está configurado para escutar na porta configurada chamando o método `listen`.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Execute o exemplo

Agora que o servidor está implementado, você pode iniciar o servidor abrindo um prompt de comando e digitando:

```shell
npm start
```

Com o servidor em execução, você pode enviar uma solicitação para o servidor para testar os resultados. Para demonstrar a resposta do roteiro raiz, abra um shell de busca e insira o código a seguir:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Se você tiver configurado seu servidor corretamente, a resposta deverá ser semelhante a:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Em seguida, você pode testar a rota que requer autenticação, inserindo o seguinte comando no shell de busca:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Se você tiver configurado o servidor corretamente, o servidor deverá responder com um status de `Unauthorized`.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Agora que você criou uma API segura, você pode implementar um cliente capaz de passar tokens de autenticação para a API.

## <a name="next-steps"></a>Próximas etapas

* Você deve implementar uma contraparte de cliente para se conectar ao servidor que manipula a entrada, a saída e o gerenciamento de tokens. Para exemplos de código, consulte os aplicativos cliente em [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) e [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android).
* Para um tutorial passo a passo, veja [Entrada e saída do aplicativo Web Node.js com o Azure AD](quickstart-v1-openid-connect-code.md).
