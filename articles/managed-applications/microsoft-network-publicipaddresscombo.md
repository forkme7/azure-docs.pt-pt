---
title: Elemento de IU PublicIpAddressCombo do Azure | Microsoft Docs
description: Descreve o elemento de IU Microsoft.Network.PublicIpAddressCombo para o portal do Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: bced85abaf24beb0997ec906a68e5947f0e67cca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element
Um grupo de controlos para selecionar um endereço IP público novo ou existente.

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Se o utilizador seleciona 'None' para o endereço IP público, a caixa de texto de etiqueta de nome de domínio está oculto.
- Se o utilizador seleciona um endereço IP público existente, a caixa de texto de etiqueta de nome de domínio está desativada. O seu valor é a etiqueta de nome de domínio do endereço IP selecionado.
- As domínio nome sufixo (por exemplo, westus.cloudapp.azure.com) as atualizações automaticamente com base na localização selecionada.

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Se `constraints.required.domainNameLabel` está definido como **verdadeiro**, o utilizador tem de fornecer uma etiqueta de nome de domínio quando criar um novo endereço IP público. Os endereços IP público existente sem uma etiqueta não estão disponíveis para seleção.
- Se `options.hideNone` está definido como **verdadeiro**, em seguida, a opção para selecionar **nenhum** para o IP público endereço está oculto. O valor predefinido é **falso**.
- Se `options.hideDomainNameLabel` está definido como **verdadeiro**, em seguida, a caixa de texto etiqueta de nome de domínio está oculto. O valor predefinido é **falso**.
- Se `options.hideExisting` for VERDADEIRO, o utilizador não é possível escolher um endereço IP público existente. O valor predefinido é **falso**.

## <a name="sample-output"></a>Resultado da amostra
Se o utilizador não seleciona nenhum endereço IP público, é esperado o seguinte resultado:
```json
{
  "newOrExistingOrNone": "none"
}
```

Se o utilizador seleciona um endereço IP novo ou existente, é esperado o seguinte resultado:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- Quando `options.hideNone` está especificado como **verdadeiro**, `newOrExistingOrNone` apenas terá um valor de **novo** ou **existente**.
- Quando `options.hideDomainNameLabel` está especificado como **verdadeiro**, `domainNameLabel` é não declarado.

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
