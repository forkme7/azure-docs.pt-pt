---
title: "Implementar os recursos do Azure a vários grupos de recursos e subscrição | Microsoft Docs"
description: "Mostra como mais de um grupo de recursos e subscrição do Azure de destino durante a implementação."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: tomfitz
ms.openlocfilehash: 90cb87b3fe94b7b3b0eba1b261d29a1c8f4348d6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Implementar recursos do Azure em mais do que uma subscrição ou grupo de recursos

Normalmente, implementa todos os recursos no seu modelo para um único [grupo de recursos](resource-group-overview.md). No entanto, existem cenários onde pretende implementar um conjunto de recursos em conjunto, mas colocá-los em grupos de recursos diferente ou subscrições. Por exemplo, poderá implementar a máquina virtual de cópia de segurança do Azure Site Recovery para um grupo de recursos separada e uma localização. O Resource Manager permite-lhe utilizar modelos aninhados para o destino subscrições e grupos de recursos que a subscrição e o grupo de recursos utilizados para o modelo de principal.

> [!NOTE]
> Pode implementar em grupos de recursos apenas cinco numa única implementação. Normalmente, esta limitação significa que pode implementar um grupo de recursos especificado para o modelo de principal e até quatro grupos de recursos em implementações aninhadas ou ligados. No entanto, se o modelo de principal contém apenas os modelos de aninhada ou ligados e implementar não próprio quaisquer recursos, em seguida, pode incluir até cinco grupos de recursos em implementações aninhadas ou ligadas.

## <a name="specify-a-subscription-and-resource-group"></a>Especifique um grupo de recursos e subscrição

Para um outro recurso de destino, utilize um modelo aninhado ou ligado. O `Microsoft.Resources/deployments` tipo de recurso fornece parâmetros para `subscriptionId` e `resourceGroup`. Estas propriedades permitem-lhe especificar um grupo de recursos e subscrição diferente para a implementação aninhada. Todos os grupos de recursos tem de existir antes de executar a implementação. Se não especificar o grupo de recursos ou um ID de subscrição, a subscrição e grupo de recursos do modelo principal é utilizada.

A conta que utiliza para implementar o modelo tem de ter permissões para implementar o ID de subscrição especificada. Se a subscrição especificada de um inquilino do Azure Active Directory diferentes, deve [adicionar os utilizadores convidados a partir de outro diretório](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Para especificar um grupo de recursos diferente e uma subscrição, utilize:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Se os grupos de recursos estão na mesma subscrição, pode remover o **subscriptionId** valor.

O exemplo seguinte implementa duas contas de armazenamento - um grupo de recursos especificado durante a implementação, e especificar um grupo de recursos no `secondResourceGroup` parâmetro:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Se definir `resourceGroup` ao nome de um grupo de recursos que não existe, a implementação falhará.

Para implementar o modelo de exemplo, utilize o Azure PowerShell 4.0.0 ou posterior, ou do Azure CLI 2.0.0 ou posterior.

## <a name="use-the-resourcegroup-function"></a>Utilize a função de resourceGroup()

Para cruzada implementações do grupo de recursos, o [resourceGroup() função](resource-group-template-functions-resource.md#resourcegroup) resolve forma diferente, consoante como especificar o modelo aninhado. 

Se incorporar um modelo dentro de outro modelo, resourceGroup() no modelo aninhado é resolvido para o grupo de recursos principais. Um modelo incorporado utiliza o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Se ligar a um modelo separado, resourceGroup() no modelo ligado é resolvido para o grupo de recursos aninhados. Um modelo ligado utiliza o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="example-templates"></a>Modelos de exemplo

Os modelos seguintes demonstram várias implementações do grupo de recursos. Scripts para implementar os modelos são apresentadas depois da tabela.

|Modelo  |Descrição  |
|---------|---------|
|[Entre o modelo de subscrição](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Implementa uma conta de armazenamento para um grupo de recursos e uma conta de armazenamento para um segundo grupo de recursos. Inclua um valor para o ID de subscrição quando o segundo grupo de recursos está numa subscrição diferente. |
|[Entre o modelo de propriedades do grupo de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Demonstra como o `resourceGroup()` funcionar seja resolvido. Não implementa todos os recursos. |

### <a name="powershell"></a>PowerShell

Para o PowerShell, para implementar duas contas de armazenamento em dois grupos de recursos no **mesma subscrição**, utilize:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Para o PowerShell, para implementar duas contas de armazenamento para **duas subscrições**, utilize:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Para o PowerShell, para testar como as **objeto do grupo de recursos** é resolvido para o modelo de principal, inline modelo e modelo ligado, de utilização:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

No exemplo anterior, ambos **parentRG** e **inlineRG** resolver para **parentGroup**. **linkedRG** é resolvido para **linkedGroup**. O resultado do exemplo anterior é:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>CLI do Azure

Para a CLI do Azure, para implementar duas contas de armazenamento em dois grupos de recursos no **mesma subscrição**, utilize:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para a CLI do Azure, para implementar duas contas de armazenamento para **duas subscrições**, utilize:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Para a CLI do Azure, para testar como as **objeto do grupo de recursos** é resolvido para o modelo de principal, inline modelo e modelo ligado, de utilização:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

No exemplo anterior, ambos **parentRG** e **inlineRG** resolver para **parentGroup**. **linkedRG** é resolvido para **linkedGroup**. O resultado do exemplo anterior é:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Passos Seguintes

* Para compreender como definir parâmetros no modelo, consulte [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).
* Para sugestões sobre como resolver erros comuns de implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que necessita de um token SAS, consulte [implementar a modelo privado com o SAS token](resource-manager-powershell-sas-token.md).
