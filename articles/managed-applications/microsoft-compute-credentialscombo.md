---
title: Elemento de interface do usuário CredentialsCombo| Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft.Compute.CredentialsCombo do Portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098613"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento de interface do usuário Microsoft.Compute.CredentialsCombo
Um grupo de controles com validação interna para chaves públicas SSH e senhas Windows e Linux.

## <a name="ui-sample"></a>Exemplo de interface do usuário

Para Windows, usuários veem:

![Windows Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Para o Linux com a senha selecionada, os usuários veem:

![senha Linux Microsoft.Compute.CredentialsCombo ](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Para o Linux com a chave pública de SSH selecionado, os usuários veem:

![Chave Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Esquema
Para Windows, use o esquema a seguir:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Para **Linux**, use o esquema a seguir:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- `osPlatform` deve ser especificada e pode ser **Windows** ou **Linux**.
- Se `constraints.required` é definido como **true**, as caixas de texto da chave pública SSH ou senha devem conter valores para serem validados com êxito. O valor padrão é **true**.
- Se `options.hideConfirmation` for definido como **true**, a segunda caixa de texto para confirmar a senha do usuário ficará oculta. O valor padrão é **false**.
- Se `options.hidePassword` é definido como **true**, a opção para usar a autenticação de senha fica oculta. Ele pode ser usado apenas quando `osPlatform` é **Linux**. O valor padrão é **false**.
- Outras restrições sobre as senhas permitidas podem ser implementadas usando a propriedade `customPasswordRegex`. A cadeia de caracteres em `customValidationMessage` é exibida quando uma senha falha a validação personalizada. O valor padrão para ambas as propriedades é **null**.

## <a name="sample-output"></a>Saída de exemplo
Se `osPlatform` é **Windows**, ou `osPlatform` é **Linux** e o usuário forneceu uma senha em vez de uma chave pública SSH, o controle retorna a seguinte saída:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Se `osPlatform` é **Linux**, e o usuário forneceuuma chave pública SSH, o controle retorna a seguinte saída:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
