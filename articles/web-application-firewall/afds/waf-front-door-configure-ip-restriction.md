---
title: Configurar a regra WAF de restrição de IP para o serviço de porta frontal do Azure
description: Saiba como configurar uma regra de firewall do aplicativo Web para restringir os endereços IP para um ponto de extremidade de serviço do Azure front door existente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 2917b2f04e7c5a4896c52861ab7eab4e0eb00b5d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186692"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Configurar uma regra de restrição de IP com um firewall do aplicativo Web para o serviço de porta frontal do Azure
Este artigo mostra como configurar regras de restrição de IP em um WAF (firewall do aplicativo Web) para o serviço de porta frontal do Azure usando o CLI do Azure, Azure PowerShell ou um modelo de Azure Resource Manager.

Uma regra de controle de acesso baseada em endereço IP é uma regra de WAF personalizada que permite controlar o acesso aos seus aplicativos Web. Ele faz isso especificando uma lista de endereços IP ou intervalos de endereços IP em formato CIDR (roteamento entre domínios sem classificação).

Por padrão, seu aplicativo Web pode ser acessado pela Internet. Se você quiser limitar o acesso a clientes de uma lista de endereços IP conhecidos ou intervalos de endereços IP, você poderá criar uma regra de correspondência de IP que contenha a lista de endereços IP como valores correspondentes e definir operador como "Not" (negar é verdadeiro) e a ação a ser **bloqueada**. Depois que uma regra de restrição de IP é aplicada, as solicitações originadas de endereços fora dessa lista de permissão recebem uma resposta de 403 Proibido.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurar uma política de WAF com o CLI do Azure

### <a name="prerequisites"></a>pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configure o ambiente da CLI e crie um perfil de serviço do Azure front door.

#### <a name="set-up-the-azure-cli-environment"></a>Configurar o ambiente de CLI do Azure
1. Instale o [CLI do Azure](/cli/azure/install-azure-cli)ou use Azure cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **experimentar** nos comandos da CLI que seguem e, em seguida, entre em sua conta do Azure na sessão de Cloud shell que é aberta. Depois que a sessão for iniciada, insira `az extension add --name front-door` para adicionar a extensão de serviço de porta frontal do Azure.
 2. Se você estiver usando a CLI localmente no bash, entre no Azure usando `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Criar um perfil de serviço de porta frontal do Azure
Crie um perfil de serviço do Azure front door seguindo as instruções descritas em [início rápido: criar uma porta frontal para um aplicativo Web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Crie uma política de WAF usando o comando [AZ Network front-door WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . No exemplo a seguir, substitua o nome da política *IPAllowPolicyExampleCLI* por um nome de política exclusivo.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adicionar uma regra de controle de acesso de IP personalizada

Use o comando [AZ Network front-door WAF-Policy personalizada-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) para adicionar uma regra de controle de acesso de IP personalizada para a política de WAF que você acabou de criar.

Nos exemplos a seguir:
-  Substitua *IPAllowPolicyExampleCLI* pela sua política exclusiva criada anteriormente.
-  Substitua *IP-address-Range-1*, *IP-address-Range-2* por seu próprio intervalo.

Primeiro, crie uma regra de permissão de IP para a política criada na etapa anterior. Observação **--adiar** é necessário porque uma regra deve ter uma condição de correspondência a ser adicionada na próxima etapa.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Em seguida, adicione a condição de correspondência à regra:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Localizar a ID de uma política de WAF 
Localize a ID de uma política de WAF usando o comando [AZ Network front-door WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . Substitua *IPAllowPolicyExampleCLI* no exemplo a seguir pela sua política exclusiva que você criou anteriormente.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Vincular uma política de WAF a um host de front-end do serviço de porta frontal do Azure

Defina a ID de *WebApplicationFirewallPolicyLink* do serviço de porta frontal do Azure para a ID da política usando o comando [AZ Network front-door Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Substitua *IPAllowPolicyExampleCLI* pela sua política exclusiva que você criou anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, a política WAF é aplicada a **FrontendEndpoints [0]** . Você pode vincular a política WAF a qualquer um dos seus front-ends.
> [!Note]
> Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para vincular uma política de WAF a um front-end de serviço de porta frontal do Azure. As atualizações de política subsequentes são aplicadas automaticamente ao front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurar uma política de WAF com Azure PowerShell

### <a name="prerequisites"></a>pré-requisitos
Antes de começar a configurar uma política de restrição de IP, configure o ambiente do PowerShell e crie um perfil de serviço do Azure front door.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
Azure PowerShell fornece um conjunto de cmdlets que usam o modelo de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar recursos do Azure.

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar no PowerShell usando suas credenciais do Azure e, em seguida, instale o módulo AZ.

1. Conecte-se ao Azure usando o comando a seguir e, em seguida, use uma caixa de diálogo interativa para entrar.
    ```
    Connect-AzAccount
    ```
 2. Antes de instalar um módulo de serviço de porta frontal do Azure, verifique se você tem a versão atual do módulo PowerShellGet instalada. Execute o comando a seguir e reabra o PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Instale o módulo AZ. FrontDoor usando o comando a seguir. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Criar um perfil de serviço de porta frontal do Azure
Crie um perfil de serviço do Azure front door seguindo as instruções descritas em [início rápido: criar uma porta frontal para um aplicativo Web global altamente disponível](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definir uma condição de correspondência de IP
Use o comando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) para definir uma condição de correspondência de IP.
No exemplo a seguir, substitua *IP-address-Range-1*, *IP-address-Range-2* por seu próprio intervalo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Criar uma regra de permissão de IP personalizada

Use o comando [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) para definir uma ação e definir uma prioridade. No exemplo a seguir, as solicitações não dos IPs de cliente que correspondem à lista serão bloqueadas.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurar uma política de WAF
Localize o nome do grupo de recursos que contém o perfil de serviço de porta frontal do Azure usando `Get-AzResourceGroup`. Em seguida, configure uma política de WAF com a regra de IP usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Vincular uma política de WAF a um host de front-end do serviço de porta frontal do Azure

Vincule um objeto de política WAF a um host front-end existente e atualize as propriedades do serviço de porta frontal do Azure. Primeiro, recupere o objeto de serviço de porta frontal do Azure usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, defina a propriedade **WebApplicationFirewallPolicyLink** para a ID de recurso de *$IPAllowPolicyExamplePS*, criada na etapa anterior, usando o comando [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, a política WAF é aplicada a **FrontendEndpoints [0]** . Você pode vincular uma política de WAF a qualquer um de seus front-ends. Você precisa definir a propriedade **WebApplicationFirewallPolicyLink** apenas uma vez para vincular uma política de WAF a um front-end de serviço de porta frontal do Azure. As atualizações de política subsequentes são aplicadas automaticamente ao front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurar uma política de WAF com um modelo do Resource Manager
Para exibir o modelo que cria uma política de serviço do Azure front door e uma política de WAF com regras de restrição de IP personalizadas, vá para [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um perfil de serviço de porta frontal do Azure](../../frontdoor/quickstart-create-front-door.md).
