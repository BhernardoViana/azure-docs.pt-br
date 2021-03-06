---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 4f3236c0a167a2b6f7586c6cb5fea8e30f55a86c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954075"
---
## <a name="rest"></a>Implantar o arquivo ZIP com APIs REST 

Você pode usar as [APIs REST do serviço de implantação](https://github.com/projectkudu/kudu/wiki/REST-API) para implantar o arquivo .zip ao seu aplicativo no Azure. Para implantá-lo, envie uma solicitação POST para https://<app_name>.scm.azurewebsites.net/api/zipdeploy. A solicitação POST deve conter o arquivo .zip no corpo da mensagem. As credenciais de implantação para seu aplicativo são fornecidas na solicitação usando a autenticação BÁSICA HTTP. Para obter mais informações, consulte a [referência de implantação por push do .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Para a autenticação HTTP BÁSICA, você precisa das credenciais de implantação do Serviço de Aplicativo. Para ver como definir as credenciais de implantação, consulte [Definir e redefinir as credenciais de usuário](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com o cURL

O exemplo a seguir usa a ferramenta cURL para implantar um arquivo .zip. Substitua os espaços reservados `<username>`, `<password>`, `<zip_file_path>` e `<app_name>`. Quando solicitado pelo cURL, digite a senha.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Essa solicitação dispara a implantação de envio do arquivo .zip carregado. Você pode examinar as implantações atuais e anteriores, usando o ponto de extremidade `https://<app_name>.scm.azurewebsites.net/api/deployments`, conforme mostrado no exemplo de cURL a seguir. Novamente, substitua `<app_name>` com o nome do seu aplicativo e `<deployment_user>` com o nome de usuário de suas credenciais de implantação.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo a seguir usa [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) para carregar o arquivo. zip. Substitua os espaços reservados `<group-name>`, `<app-name>` e `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Essa solicitação dispara a implantação de envio do arquivo .zip carregado. 

Para examinar as implantações atuais e anteriores, execute os comandos a seguir. Novamente, substitua os `<deployment-user>`espaços `<deployment-password>`reservados, `<app-name>` e.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
