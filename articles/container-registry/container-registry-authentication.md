---
title: Autenticar com um Registro de contêiner do Azure
description: Opções de autenticação para um registro de contêiner do Azure, incluindo o logon com uma identidade do Azure Active Directory, usando entidades de serviço e usando credenciais de administrador opcionais.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a55cba27c676b283a4da490f05dd6fc672e10d49
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032382"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um Registro de contêiner privado do Docker

Há várias maneiras de autenticar com um Registro de Contêiner do Azure, cada uma das quais é aplicável a um ou mais cenários de uso do registro.

As maneiras recomendadas incluem a autenticação em um registro diretamente por meio de [logon individual](#individual-login-with-azure-ad)ou seus aplicativos e orquestradores de contêiner podem executar a autenticação autônoma ou "sem periféricos" usando um serviço Azure Active Directory (AD do Azure) [ entidade de segurança](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar com o registro diretamente, assim como ao extrair imagens para sua estação de trabalho de desenvolvimento e ao enviar imagens por push para ela, autentique-se usando o comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) da [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Ao fazer logon com `az acr login`, a CLI usa o token criado quando você executou [az login](/cli/azure/reference-index#az-login) para, sem interrupções, autenticar a sessão com o registro. Depois de você fazer logon dessa maneira, suas credenciais são armazenadas em cache e os comandos `docker` subsequentes na sua sessão não requerem um nome de usuário ou senha. 

Para acesso ao Registro, o token usado pelo `az acr login` é válido por 1 hora, portanto, é recomendável que você sempre faça logon no Registro antes de executar um comando `docker`. Se o token expirar, você poderá atualizá-lo usando o comando `az acr login` novamente para se reautenticar. 

Usar `az acr login` com identidades do Azure fornece [acesso baseado em função](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, convém fazer logon em um registro com sua própria identidade individual do Azure AD. Para cenários entre serviços ou lidar com as necessidades de um grupo de trabalho no qual você não deseja gerenciar o acesso individual, também é possível fazer logon com uma [identidade gerenciada de recursos do Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Entidade de serviço

Se você atribuir uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao registro, seu aplicativo ou serviço poderá usá-lo na autenticação remota. As entidades de serviço permitem [acesso baseado em função](../role-based-access-control/role-assignments-portal.md) a um registro ao qual você pode atribuir várias entidades de serviço. Várias entidades de serviço permitem que você defina acesso diferente para diferentes aplicativos.

As funções disponíveis para um registro de contêiner incluem:

* **AcrPull**: efetuar pull

* **AcrPush**: efetuar pull e push

* **Proprietário**: pull, push e atribuir funções a outros usuários

Para obter uma lista completa de funções, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

Para que os scripts da CLI criem uma entidade de serviço para autenticação com um registro de contêiner do Azure e orientações sobre como usar uma entidade de serviço, consulte [autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Conta de administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que fica desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar as credenciais dele no portal do Azure ou usando a CLI do Azure ou outras ferramentas do Azure.

> [!IMPORTANT]
> A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não recomendamos o compartilhamento das credenciais da conta de administrador entre vários usuários. Todos os usuários que se autenticam com a conta do administrador aparecem como um único usuário com acesso de push e de pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam as credenciais dela. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço.
>

A conta do administrador é fornecida com duas senhas, que podem ser regeneradas. As duas senhas permitem manter conexões com o registro usando uma senha enquanto a outra é regenerada. Se a conta do administrador estiver habilitada, você poderá passar o nome de usuário e a senha para o comando `docker login` quando solicitado a informar a autenticação Básica no Registro. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as práticas recomendadas para gerenciar as credenciais de logon, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Para habilitar o usuário administrador para um registro existente, você pode usar o parâmetro `--admin-enabled` do comando [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) na CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Você pode habilitar o usuário administrador no Portal do Azure navegando em seu registro, selecionando **Chaves de acesso** em **CONFIGURAÇÕES** e, em seguida, **Habilitar** em **Usuário administrador**.

![Habilitar a interface do usuário administrador no Portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Próximas etapas

* [Enviar sua primeira imagem por push usando a CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
