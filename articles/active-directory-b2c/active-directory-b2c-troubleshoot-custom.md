---
title: Application Insights para solucionar problemas de políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: como configurar o Application Insights para rastrear a execução de políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df5d710792d8c47e491f5b06d88f4050e8eb4a01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508066"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: coletando logs

Este artigo fornece etapas para coletar logs do Azure AD B2C para que você possa diagnosticar problemas com suas políticas personalizadas.

>[!NOTE]
>Atualmente, os logs de atividade detalhados descritos aqui são projetados **APENAS** para ajudar no desenvolvimento de políticas personalizadas. Não use o modo de desenvolvimento em produção.  Os logs coletam todas as declarações enviadas entre os provedores de identidade durante o desenvolvimento.  Se for usado em produção, o desenvolvedor assumirá a responsabilidade pela PII (Informações de identificação particular) coletadas no log do App Insights que ele possui.  Esses logs detalhados são coletados apenas quando a política é colocada em **MODO DE DESENVOLVIMENTO**.


## <a name="use-application-insights"></a>Usar o Application insights

O Azure AD B2C oferece suporte a um recurso para envio de dados ao Application Insights.  O Application Insights fornece uma maneira de diagnosticar exceções e visualizar os problemas de desempenho do aplicativo.

### <a name="setup-application-insights"></a>Configurar o Application Insights

1. Vá para o [Portal do Azure](https://portal.azure.com). Certifique-se de que você esteja no locatário com sua assinatura do Azure (não no locatário do Azure AD B2C).
1. Clique em **+ Novo** no menu de navegação esquerdo.
1. Pesquise e selecione **Application Insights** e clique em **Criar**.
1. Preencha o formulário e clique em **Criar**. Selecione **Geral** para o **Tipo de Aplicativo**.
1. Após a criação do recurso, abra o recurso Application Insights.
1. Localize **Propriedades** no menu à esquerda e clique nele.
1. Copie a **Chave de Instrumentação** e salve-a na próxima seção.

### <a name="set-up-the-custom-policy"></a>Configurar a política personalizada

1. Abra o arquivo RP (por exemplo, SignUpOrSignin.xml).
1. Adicione os atributos a seguir ao elemento `<TrustFrameworkPolicy>`:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se ele ainda não existir, adicione um nó filho `<UserJourneyBehaviors>` ao nó `<RelyingParty>`. Ele deve estar localizado imediatamente após o `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Adicione o seguinte nó como um filho do elemento `<UserJourneyBehaviors>`. Substitua `{Your Application Insights Key}` pela **Chave de Instrumentação** que você obteve do Application Insights na seção anterior.

   ```XML
   <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   ```

   * `DeveloperMode="true"` informa ao ApplicationInsights para agilizar a telemetria por meio do pipeline de processamento, bom para o desenvolvimento, mas com restrição em grandes volumes.
   * `ClientEnabled="true"` envia o script do lado do cliente do ApplicationInsights para rastrear erros de exibição de página e do lado do cliente (não é necessário).
   * `ServerEnabled="true"` envia o JSON UserJourneyRecorder existente como um evento personalizado para o Application Insights.
   Exemplo:

   ```XML
   <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
   </TrustFrameworkPolicy>
   ```

3. Carregue a política.

### <a name="see-the-logs-in-application-insights"></a>Veja os logs de no Application Insights

>[!NOTE]
> Há um breve atraso (menos de cinco minutos) antes que você possa ver novos logs no Application Insights.

1. Abra o recurso do Application Insights que você criou no [Portal do Azure](https://portal.azure.com).
1. No menu **Visão geral**, clique em **Análise**.
1. Abra uma nova guia no Application Insights.
1. Aqui está uma lista de consultas que você pode usar para ver os logs

| Consulta | DESCRIÇÃO |
|---------------------|--------------------|
traces | Veja todos os logs gerados pelo Azure AD B2C |
rastreamentos \| onde carimbo de data/hora > ago(1d) | Veja todos os logs gerados pelo Azure AD B2C para o último dia

As entradas podem ser longas.  Exporte para CSV para uma análise mais detalhada.

Saiba mais sobre essa ferramentas de análise [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>A comunidade desenvolveu um visualizador userjourney para ajudar os desenvolvedores de identidade.  Não tem suporte da Microsoft e é disponibilizado estritamente como está.  Ele faz leitura da instância do Application Insights e fornece uma visão bem estruturada dos eventos do percurso do usuário.  Obtenha o código-fonte e o implante em sua própria solução.

A versão do visualizador que faz leitura dos eventos do Application Insights está localizada [aqui](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>Atualmente, os logs de atividade detalhados descritos aqui são projetados **APENAS** para ajudar no desenvolvimento de políticas personalizadas. Não use o modo de desenvolvimento em produção.  Os logs coletam todas as declarações enviadas entre os provedores de identidade durante o desenvolvimento.  Se for usado em produção, o desenvolvedor assumirá a responsabilidade pela PII (Informações de identificação particular) coletadas no log do App Insights que ele possui.  Esses logs detalhados são coletados apenas quando a política é colocada em **MODO DE DESENVOLVIMENTO**.

[Repositório GitHub para obter exemplos de política personalizada sem suporte e ferramentas relacionadas](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Próximas etapas

Explore os dados no Application Insights para ajudar a entender como o Identity Experience Framework por trás do B2C trabalha para fornecer suas próprias experiências de identidade.
