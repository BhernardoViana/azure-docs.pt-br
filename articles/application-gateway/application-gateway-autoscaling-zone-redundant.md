---
title: Dimensionamento automático e o Gateway de aplicativo com redundância de zona no Azure
description: Este artigo apresenta o Standard_v2 de aplicativo do Azure e WAF_v2 SKU, que inclui recursos de dimensionamento automático e com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/22/2019
ms.author: victorh
ms.openlocfilehash: 8e17c5e34ec3e2397c3054b1d0e0d97dbf410db2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65986883"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>Dimensionamento automático e o Gateway de aplicativo com redundância de zona 

Gateway de aplicativo e de Firewall de aplicativo da Web (WAF) também estão disponíveis em um SKU de WAF_v2 e Standard_v2. O SKU do v2 oferece aprimoramentos de desempenho e adiciona suporte para novos recursos críticos, como o dimensionamento automático, redundância de zona e suporte para os VIPs estáticos. Os recursos sob o padrão e SKU do WAF existentes continuarão a ter suporte na nova SKU v2, com algumas exceções listadas na [comparação](#differences-with-v1-sku) seção.

A nova SKU v2 inclui os seguintes aprimoramentos:

- **Dimensionamento automático**: implantações do Gateway de Aplicativo ou do WAF com o SKU de dimensionamento automático podem ser expandidas ou reduzidas com base na mudança dos padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Essa SKU oferece a verdadeira elasticidade. No Standard_v2 e WAF_v2 SKU, o Gateway de aplicativo pode operar em capacidade fixa (dimensionamento automático desabilitado) e no modo de dimensionamento automático habilitado. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. Modo de dimensionamento automático é útil para aplicativos que ver variação no tráfego de aplicativo.
- **Redundância de Zona**: Um Gateway de aplicativo ou implantação do WAF pode abranger várias zonas de disponibilidade a remover a necessidade de provisionar instâncias de Gateway de aplicativo separadas em cada zona com um Gerenciador de tráfego. Você pode escolher uma única zona ou em várias zonas em que as instâncias de Gateway de aplicativo são implantadas, que torna mais resistente a falhas de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.

  Redundância de zona está disponível apenas em que as zonas do Azure estão disponíveis. Em outras regiões, há suporte para todos os outros recursos. Para obter mais informações, consulte [o que são zonas de disponibilidade no Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP estático**: Tipo de aplicativo gateway v2 SKU dá suporte ao VIP estático exclusivamente. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado para o ciclo de vida da implantação, mesmo após uma reinicialização.
- **Reconfiguração do cabeçalho**: O Gateway de aplicativo permite que você adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP com SKU de v2. Para obter mais informações, consulte [cabeçalhos HTTP de reconfiguração com o Gateway de aplicativo](rewrite-http-headers.md)
- **Integração do Key Vault (versão prévia)**: V2 do Gateway de aplicativo dá suporte à integração com o Key Vault (em visualização pública) para certificados de servidor que estão anexados para os ouvintes HTTPS habilitado. Para obter mais informações, consulte [terminação SSL com certificados do Key Vault](key-vault-certs.md).
- **Controlador de entrada de serviço de Kubernetes do Azure (visualização)**: O controlador de entrada do Gateway de aplicativo v2 permite que o Gateway de aplicativo do Azure a ser usado como a entrada para um Azure Kubernetes AKS (serviço) conhecido como Cluster AKS. Para obter mais informações, consulte o [página de documentação](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Aprimoramentos de desempenho**: O v2 SKU oferece até 5 X e melhor SSL descarregamento de desempenho em comparação com o SKU Standard/WAF.
- **Tempo mais rápido de implantação e atualização** SKU v2 fornece tempo de implantação e atualização mais rápido em comparação com o SKU Standard/WAF. Isso também inclui alterações de configuração do WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte

O Standard_v2 e WAF_v2 SKU está disponível nas seguintes regiões: Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA, Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Centro dos EUA, Europa Setentrional, Europa Ocidental, Sudeste Asiático, França Central, Oeste do Reino Unido, Leste do Japão, Oeste do Japão. Regiões adicionais serão adicionadas no futuro.

## <a name="pricing"></a>Preços

Com o SKU do v2, o modelo de preços é orientado pelo consumo e não está mais anexado para contagens de instância ou tamanhos. Os preços da SKU v2 tem dois componentes:

- **Preço fixo** -isso é por hora (ou hora parcial) preço para provisionar um Standard_v2 ou WAF_v2 Gateway.
- **Preço unitário da capacidade** -isso é o custo baseado em consumo que é cobrado adicionais ao custo fixo. Encargo de unidade de capacidade é também calculado por hora ou parcial por hora. Há três dimensões para a unidade de capacidade – taxa de transferência, as conexões persistentes e unidade de computação. Unidade de computação é uma medida de capacidade de processador consumida. Fatores que afetam a unidade de computação são conexões de TLS/s, cálculos de reescrita de URL e o processamento da regra WAF. Conexão persistente é uma medida de conexões TCP estabelecidas para o gateway de aplicativo em um determinado intervalo de cobrança. Taxa de transferência é médio Megabits/s processadas pelo sistema em um determinado intervalo de cobrança.

Cada unidade de capacidade é composta de no máximo: 1 unidade, ou as conexões persistentes 2500 ou taxa de transferência 2.22 Mbps de computação.

Diretrizes de unidade de computação:

- **Standard_v2** -cada unidade de computação é capaz de aproximadamente 50 conexões por segundo com certificado TLS de chave RSA de 2048 bits.
- **WAF_v2** - cada computação unidade pode dar suporte a aproximadamente 10 solicitações simultâneas por segundo para solicitações de 30 a 70% mistura de tráfego com 70% menor do que 2 KB GET/POST e restantes superior. Desempenho de WAF não é afetado pelo tamanho da resposta no momento.

> [!NOTE]
> Cada instância pode atualmente dar suporte a aproximadamente 10 unidades de capacidade.
> O número de solicitações para que uma unidade de computação pode manipular depende de vários critérios, como tamanho de chave de certificado TLS, o algoritmo de troca de chaves, regravações de cabeçalho e no caso de tamanho de solicitação de entrada de WAF. É recomendável que você execute testes de aplicativo para determinar a taxa de solicitação por unidade de computação. Unidade de capacidade e a unidade de computação serão disponibilizados como uma métrica antes de cobrança é iniciado.

**Preço no Leste dos EUA**:

|              Nome do SKU                             | Preço fixo ($/ h)  | Preço unitário da capacidade (CU / $-h)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Para obter mais informações sobre preços, consulte o [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/). A cobrança está agendada para iniciar em 1 de julho de 2019.

**Exemplo 1**

Um Standard_v2 de Gateway de aplicativo seja provisionado sem dimensionamento automático em modo de dimensionamento manual com capacidade fixa de cinco instâncias.

Preço fixo = 744(hours) * US $0,20 = US $148.8 <br>
Unidades de capacidade = 744 unidade de capacidade (horas) 10 por instância * cinco instâncias * US $0,008 por hora de unidade de capacidade = US $297.6

Preço total = $148.8 + US $297.6 = US $446.4

**Exemplo 2**

Standard_v2 um Gateway de aplicativo é provisionado por um mês e durante esse tempo, ele recebe 25 novo SSL conexões/s, média de transferência de dados 8.88 Mbps. Supondo que as conexões têm curtas duração, o preço seria:

Preço fixo = 744(hours) * US $0,20 = US $148.8

Preço unitário de capacidade = 744(hours) * Max (unidade de computação de 25/50 para conexões/s, 8.88/2.22 a unidade de capacidade para taxa de transferência) * US $0,008 = 744 * 4 * 0,008 = US $23,81

Preço total = $148.8 + 23,81 = US $172.61

**Exemplo 3**

Um WAF_v2 de Gateway de aplicativo é provisionado por um mês. Durante esse tempo, ele recebe 25 novo SSL conexões/s, média de transferência de dados 8.88 Mbps e faz 80 solicitações por segundo. Supondo que as conexões são de curtas duração, e que o cálculo de unidade de computação para o aplicativo dá suporte a 10 RPS por unidade de computação, o preço seria:

Preço fixo = 744(hours) * US $0,36 = US $267.84

Preço unitário de capacidade = 744(hours) * Max (computação de unidade Max(25/50 for connections/sec, 80/10 WAF RPS) 8.88/2.22 a unidade de capacidade para taxa de transferência) * US $0.0144 = 744 * 8 * 0.0144 = US $85.71

Preço total = $267.84 + US $85.71 = US $353.55

## <a name="scaling-application-gateway-and-waf-v2"></a>Dimensionando o WAF e o Gateway de aplicativo v2

O Gateway de aplicativo e WAF podem ser configurados para escala em dois modos:

- **Dimensionamento automático** – com o dimensionamento automático habilitado, o Gateway de aplicativo e WAF v2 SKUs escalar ou reduzir verticalmente com base nos requisitos de tráfego do aplicativo. Esse modo oferece melhor elasticidade ao seu aplicativo e elimina a necessidade de adivinhar a contagem de tamanho ou a instância de gateway do aplicativo. Esse modo também permite que você economize ao não exigir a execução de gateways com capacidade de máxima provisionada para a carga de tráfego máximo esperado. Os clientes devem especificar uma contagem de instância, opcionalmente, máximo e mínimo. Capacidade mínima garante que o Gateway de aplicativo e WAF v2 não se enquadram abaixo da contagem mínima de instâncias especificada, mesmo na ausência de tráfego. Você será cobrado por essa capacidade mínima até mesmo na ausência de qualquer tráfego. Você também pode especificar uma contagem máxima de instâncias, que garante que o Gateway de aplicativo não ultrapassará o número especificado de instâncias. Você continuará a ser cobrado pela quantidade de tráfego atendido pelo Gateway. As contagens de instância podem variar de 0 para 125. O valor padrão para a contagem máxima de instâncias é 20, se não especificado.
- **Manual** – como alternativa, você pode escolher em que o gateway não usa dimensionamento automático de modo Manual. Nesse modo, se houver mais tráfego do que o que o Gateway de aplicativo ou WAF pode manipular, ele pode resultar em perda de tráfego. Com o modo manual, especificando a contagem de instâncias é obrigatório. Contagem de instâncias pode variar de 1 a instâncias de 125.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de recursos entre o SKU de v1 e v2 SKU

A tabela a seguir compara os recursos disponíveis com cada SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância de zona                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador de entrada de serviço do Kubernetes (AKS) do Azure |          | &#x2713; |
| Integração do Azure Key Vault                       |          | &#x2713; |
| Reescreva cabeçalhos HTTP (S)                           |          | &#x2713; |
| Roteamento baseado em URL                                 | &#x2713; | &#x2713; |
| Hospedagem de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento do tráfego                               | &#x2713; | &#x2713; |
| Firewall do aplicativo Web (WAF)                    | &#x2713; | &#x2713; |
| Encerramento do protocolo SSL            | &#x2713; | &#x2713; |
| Criptografia SSL de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte para WebSocket                                 | &#x2713; | &#x2713; |
| Suporte do HTTP/2                                    | &#x2713; | &#x2713; |
| Descarga de conexão                               | &#x2713; | &#x2713; |

> [!NOTE]
> A v2 de dimensionamento automático agora é compatível com a SKU [investigações de integridade padrão](application-gateway-probe-overview.md#default-health-probe) para monitorar a integridade de todos os recursos em seu pool de back-end e realce os membros de back-end que são considerados não íntegro automaticamente. Investigação de integridade padrão é configurada automaticamente para o back-ends que não têm qualquer configuração de investigação personalizada. Para obter mais informações, consulte [investigações de integridade no gateway de aplicativo](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Diferenças com o SKU do v1

|Diferença|Detalhes|
|--|--|
|Certificado de autenticação|Sem suporte.<br>Para saber mais, confira [Visão geral de SSL de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinando Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede|Sem suporte|
|Rota Definida pelo Usuário (UDR) na sub-rede de Gateway de Aplicativo|Sem suporte|
|NSG para o intervalo de porta de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 to 65534 para Standard SKU.<br>Consulte mais informações em [Perguntas Frequentes](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Logs de desempenho no diagnóstico do Azure|Sem suporte.<br>As métricas do Azure devem ser usadas.|
|Cobrança|Cobrança agendada para iniciar em 1 de julho de 2019.|
|Modo FIPS|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Integração do Netwatcher|Sem suporte.|
|Integração do Centro de suporte do Azure|Ainda não está disponível.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Tráfego da web direto com o Gateway de aplicativo do Azure - portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).