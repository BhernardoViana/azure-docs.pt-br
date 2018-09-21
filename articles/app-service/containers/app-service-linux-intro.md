---
title: Introdução ao Serviço de Aplicativo no Linux | Microsoft Docs
description: Saiba mais sobre o Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, linux, oss
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 8645443e5f41fd36b3439a6a7d49407eb0ccf6f8
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366566"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao Serviço de Aplicativo do Azure no Linux

O [Aplicativo Web](../app-service-web-overview.md) é uma plataforma de computação totalmente gerenciada que foi otimizada para hospedar sites e aplicativos Web. Os clientes podem usar o Serviço de Aplicativo no Linux para hospedar aplicativos Web nativos no Linux para as pilhas de aplicativos com suporte. As seções a seguir listam as pilhas de aplicativos que atualmente são suportadas.

## <a name="languages"></a>Linguagens

O Serviço de Aplicativo no Linux dá suporte a inúmeras imagens internas a fim de aumentar a conectividade do desenvolvedor. Se não houver suporte para o tempo de execução que seu aplicativo requer nas imagens internas, haverá instruções sobre como [criar sua própria imagem do Docker](tutorial-custom-docker-image.md) a ser implantada no Aplicativo Web para Contêineres.

| Linguagem | Versões com suporte |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1 |
| Java * | 8.0 |
| PHP | 5.6, 7.0, 7.2 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |
| Go | 1.0 |
| Apache Tomcat | 8.5, 9.0 |

Consulte [Criar um aplicativo Web Java no Serviço de Aplicativo no Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-java) para obter mais detalhes.

## <a name="deployments"></a>Implantações

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Ambientes de preparo
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) e CI/CD do DockerHub CI

## <a name="console-publishing-and-debugging"></a>Console, Publicação e Depuração

* Ambientes
* Implantações
* Console básico
* SSH

## <a name="scaling"></a>Dimensionamento

* Os clientes podem escalar ou reduzir verticalmente aplicativos Web, alterando a camada dos respectivos [planos do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Locais

Verifique o [Painel de Status do Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitações

O portal do Azure mostra somente os recursos que funcionam atualmente para o Aplicativo Web para Contêineres. Conforme habilitarmos mais recursos, eles ficarão visíveis no portal.

Alguns recursos, como a integração de rede virtual, a autenticação do Azure Active Directory/de terceiros ou as extensões de site do Kudu, ainda não estão disponíveis. Quando esses recursos estiverem disponíveis, atualizaremos nossa documentação e nosso blog sobre as alterações.

O Serviço de Aplicativo no Linux só tem suporte com os planos de serviço de aplicativo [Básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e não tem uma camada [Gratuita nem Compartilhada](https://azure.microsoft.com/pricing/details/app-service/plans/). Não é possível criar o Aplicativo Web para Contêineres em um plano do Serviço de Aplicativo que já esteja hospedando Aplicativos Web não Linux. Também há uma limitação atual no que diz respeito à não misturar aplicativos Windows e Linux no mesmo grupo de recursos.

## <a name="troubleshooting"></a>solução de problemas

Quando seu aplicativo falhar ao iniciar ou você quiser verificar o log do aplicativo, verifique os logs do Docker no diretório LogFiles. Acesse esse diretório por meio de seu site SCM ou via FTP.
Para registrar `stdout` e `stderr` por meio do contêiner, você precisa habilitar o **Registro em log do Contêiner do Docker** em **Logs de Diagnóstico**.

![Habilitando o log][2]

![Como usar o Kudu para exibir os logs do Docker][1]

Acesse o site SCM nas **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

## <a name="next-steps"></a>Próximas etapas

Veja os links a seguir para começar a usar o Serviço de Aplicativo no Linux. Você pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Usando o .NET Core no Serviço de Aplicativo do Azure no Linux](quickstart-dotnetcore.md)
* [Usando o Ruby no Serviço de Aplicativo do Azure no Linux](quickstart-ruby.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](app-service-linux-faq.md)
* [Suporte de SSH para o Serviço de Aplicativo do Azure no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação contínua do Hub do Docker com o Aplicativo Web para Contêineres](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
