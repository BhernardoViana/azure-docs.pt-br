---
title: Remova o TLS 1,0 e 1,1 do uso com o cache do Azure para Redis
description: Saiba como remover o TLS 1,0 e 1,1 do seu aplicativo ao se comunicar com o cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 74fcce412b2673a3ec9e4809cef018f1afbc3530
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812845"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Remova o TLS 1,0 e 1,1 do uso com o cache do Azure para Redis

Há um push em todo o setor para o uso exclusivo da TLS (Transport Layer Security) versão 1,2 ou posterior. As versões 1,0 e 1,1 do TLS são conhecidas como suscetíveis a ataques como fera e POODLE, e têm pontos fracos de vulnerabilidades e exposições (CVE) comuns. Eles também não dão suporte aos métodos de criptografia modernos e conjuntos de codificação recomendados pelos padrões de conformidade do setor de cartão de pagamento (PCI). Este [blog de segurança do TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explica algumas dessas vulnerabilidades mais detalhadamente.

Embora nenhuma dessas considerações apresente um problema imediato, recomendamos que você pare de usar o TLS 1,0 e 1,1 em breve. O cache do Azure para Redis deixará de oferecer suporte a essas versões de TLS em 31 de março de 2020. Após essa data, seu aplicativo será solicitado a usar o TLS 1,2 ou posterior para se comunicar com o cache.

Este artigo fornece diretrizes gerais sobre como detectar dependências dessas versões anteriores do TLS e removê-las do seu aplicativo.

## <a name="check-whether-your-application-is-already-compliant"></a>Verifique se seu aplicativo já está em conformidade

A maneira mais fácil de descobrir se seu aplicativo funcionará com o TLS 1,2 é definir o valor de **versão mínima do TLS** como TLS 1,2 em um cache de teste ou de preparo que ele usa. A configuração de **versão mínima do TLS** está nas [Configurações avançadas](cache-configure.md#advanced-settings) de sua instância de cache no portal do Azure. Se o aplicativo continuar funcionando conforme o esperado após essa alteração, provavelmente será compatível. Talvez seja necessário configurar algumas bibliotecas de cliente do Redis usadas pelo seu aplicativo especificamente para habilitar o TLS 1,2, para que eles possam se conectar ao cache do Azure para Redis sobre o protocolo de segurança.

## <a name="configure-your-application-to-use-tls-12"></a>Configurar seu aplicativo para usar o TLS 1,2

A maioria dos aplicativos usa bibliotecas de cliente do Redis para lidar com a comunicação com seus caches. Aqui estão as instruções para configurar algumas das bibliotecas de cliente populares, em várias linguagens de programação e estruturas, para usar o TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Os clientes do Redis .NET usam a versão mais antiga do TLS por padrão no .NET Framework 4.5.2 ou anterior e usam a versão mais recente do TLS no .NET Framework 4,6 ou posterior. Se você estiver usando uma versão mais antiga do .NET Framework, poderá habilitar o TLS 1,2 manualmente:

* **Stackexchange. Redis:** Defina `ssl=true` e `sslprotocols=tls12` na cadeia de conexão.
* **Perstack. Redis:** Siga as [instruções do perstack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Os clientes do Redis .NET Core usam a versão mais recente do TLS por padrão.

### <a name="java"></a>Java

Os clientes Redis Java usam o TLS 1,0 na versão 6 ou anterior do Java. Jedis, lettuce e Radisson não podem se conectar ao cache do Azure para Redis se o TLS 1,0 estiver desabilitado no cache. Atualmente, não há nenhuma solução alternativa conhecida.

No Java 7 ou posterior, os clientes Redis não usam o TLS 1,2 por padrão, mas podem ser configurados para ele. Lettuce e Radisson não dão suporte a essa configuração no momento. Eles serão interrompidos se o cache aceitar somente conexões TLS 1,2. Jedis permite que você especifique as configurações de TLS subjacentes com o seguinte trecho de código:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

O node Redis e o IORedis usam TLS 1,2 por padrão.

### <a name="php"></a>PHP

Predis no PHP 7 não funcionará porque o PHP 7 dá suporte apenas a TLS 1,0. No PHP 7.2.1 ou anterior, o Predis usa TLS 1,0 ou 1,1 por padrão. Você pode especificar o TLS 1,2 ao criar a instância do cliente:

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

No PHP 7,3 ou superior, o Predis usa a versão mais recente do TLS.

PhpRedis não dá suporte a TLS em nenhuma versão do PHP.

### <a name="python"></a>Python

Redis-py usa TLS 1,2 por padrão.

### <a name="go"></a>GO

O RediGO usa TLS 1,2 por padrão.

## <a name="additional-information"></a>Informações adicionais

- [Como configurar o cache do Azure para Redis](cache-configure.md)
