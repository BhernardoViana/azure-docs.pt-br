---
title: Portas além de 1433 para o Banco de Dados SQL | Microsoft Docs
description: As conexões cliente do ADO.NET para o Banco de Dados SQL do Azure pode ignorar o proxy e interagir diretamente com o banco de dados usando portas que não a 1433.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: a8c9eef968465ecf9c8a29df471955b89f3585a0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645082"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas além da 1433 para ADO.NET 4.5
Este tópico descreve o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente. 

> [!IMPORTANT]
> Para obter informações sobre a arquitetura de conectividade, confira [Arquitetura de conectividade de Banco de Dados SQL do Azure](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Fora versus dentro
Para conexões com o Banco de Dados SQL do Azure, devemos perguntar primeiro se o programa cliente é executado *fora* ou *dentro* do limite de nuvem do Azure. As subseções discutem dois cenários comuns.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fora:* o cliente é executado em seu computador desktop
A porta 1433 é a única porta que deve estar aberta no computador desktop que hospeda o aplicativo cliente do Banco de Dados SQL.

#### <a name="inside-client-runs-on-azure"></a>*Dentro:* o cliente é executado no Azure
Quando o cliente é executado dentro do limite de nuvem do Azure, ele usa o que podemos chamar de *rota direta* para interagir com o servidor de Banco de Dados SQL. Após o estabelecimento de uma conexão, as próximas interações entre o cliente e o banco de dados não envolvem nenhum Gateway de Banco de Dados SQL do Azure.

Esta é a sequência:

1. O ADO.NET 4.5 (ou posterior) inicia uma breve interação com a nuvem do Azure e recebe um número de porta identificado dinamicamente.
   
   * O número da porta identificado dinamicamente está no intervalo de 11000-11999 ou 14000-14999.
2. O ADO.NET conecta-se ao servidor de Banco de Dados SQL diretamente, sem um middleware entre os dois.
3. As consultas são enviadas diretamente ao banco de dados, e os resultados são retornados diretamente ao cliente.

Verifique se os intervalos de portas de 11000-11999 e 14000-14999 no computador cliente do Azure estão disponíveis para interações de cliente ADO.NET 4.5 com o Banco de Dados SQL.

* Em particular, as portas no intervalo devem estar livres de outros bloqueadores de saída.
* Em sua VM do Azure, o **Firewall do Windows com Segurança Avançada** controla as configurações de porta.
  
  * Você pode usar a [interface de usuário do firewall](http://msdn.microsoft.com/library/cc646023.aspx) a fim de adicionar uma regra para a qual você especifica o protocolo **TCP** junto com um intervalo de portas com a sintaxe **11000 a 11999**.

## <a name="version-clarifications"></a>Esclarecimentos da versão
Esta seção explica os identificadores que se referem a versões do produto. Ela também lista alguns emparelhamentos de versões entre produtos.

#### <a name="adonet"></a>ADO.NET
* O ADO.NET 4.0 dá suporte ao protocolo TDS 7.3, mas não ao 7.4.
* O ADO.NET 4.5 e posterior dá suporte ao protocolo TDS 7.4.

## <a name="related-links"></a>Links relacionados
* O ADO.NET 4.6 foi lançado em 20 de julho de 2015. Um comunicado do blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* O ADO.NET 4.5 foi lançado em 15 de agosto de 2012. Um comunicado do blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Uma postagem no blog sobre o ADO.NET 4.5.1 está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [Lista de versões do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md)
* [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md)

