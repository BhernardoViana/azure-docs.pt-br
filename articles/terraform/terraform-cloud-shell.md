---
title: Usar o Terraform com o Azure Cloud Shell
description: Use o Terraform com o Azure Cloud Shell para simplificar a autenticação e a configuração do modelo.
services: terraform
ms.service: azure
keywords: terraform, devops, conjunto de dimensionamento, máquina virtual, rede, módulos
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: ab2fd0c7fa546201d6eb19f727053a9ac54fa854
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66169919"
---
# <a name="terraform-cloud-shell-development"></a>Desenvolvimento do Terraform Cloud Shell 

O Terraform funciona muito bem em uma linha de comando do Bash como o macOS Terminal ou o Bash no Windows ou Linux. Executar suas configurações do Terraform na experiência do Bash do [Azure Cloud Shell](/azure/cloud-shell/overview) traz algumas vantagens exclusivas para acelerar seu ciclo de desenvolvimento.

Este artigo de conceitos aborda os recursos do Cloud Shell que ajudam você a escrever scripts do Terraform implantados no Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credencial

O Terraform vem instalado e imediatamente disponível no Cloud Shell. Scripts do Terraform são autenticados com o Azure quando conectado ao Cloud Shell para gerenciar a infraestrutura sem qualquer configuração adicional. A autenticação automática ignora a necessidade de criar manualmente uma entidade de serviço do Active Directory e configurar as variáveis de provedor do Azure Terraform.


## <a name="using-modules-and-providers"></a>Usando módulos e provedores

Os módulos do Azure Terraform exigem credenciais para acessar e alterar os recursos na sua assinatura do Azure. Ao trabalhar no Cloud Shell, adicione o seguinte código aos seus scripts para usar os módulos do Azure Terraform no Cloud Shell:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

O Cloud Shell passa os valores necessários ao provedor `azurerm` por meio de variáveis de ambiente ao usar qualquer um dos comandos de CLI `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de desenvolvedor do Cloud Shell

Arquivos e estados de shell são mantidos no Armazenamento do Azure entre as sessões do Cloud Shell. Use o [Gerenciador de Armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar arquivos para o Cloud Shell do seu computador local.

A CLI do Azure está disponível no Cloud Shell e é uma ótima ferramenta para testar configurações e verificar seu trabalho após a conclusão de `terraform apply`ou`terraform destroy`.


## <a name="next-steps"></a>Próximas etapas

[Criar um pequeno cluster de VM usando o Registro do Módulo](terraform-create-vm-cluster-module.md)
[Criar um pequeno cluster de VM usando o HCL personalizado](terraform-create-vm-cluster-with-infrastructure.md)
