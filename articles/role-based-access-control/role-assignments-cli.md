---
title: Gerir o controlo de acesso baseado em funções (RBAC) com a CLI do Azure | Microsoft Docs
description: Saiba como gerir baseada em funções controlo de acesso (RBAC) com a interface de linha de comandos do Azure através da lista de funções e as ações de função e atribuir funções para os âmbitos de subscrição e a aplicação.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: f783b08b25b7dd00351537f4dd404d9c8d02044d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gerir o controlo de acesso baseado em funções com a interface de linha de comandos do Azure

> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [CLI do Azure](role-assignments-cli.md)
> * [API REST](role-assignments-rest.md)


Com o controlo de acesso baseado em funções (RBAC), definem o acesso de utilizadores, grupos e principais de serviço através da atribuição de funções num determinado âmbito. Este artigo descreve como gerir atribuições de funções através da interface de linha de comandos do Azure (CLI).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a CLI do Azure para gerir as atribuições de função, tem de ter os seguintes pré-requisitos:

* [CLI 2.0 do Azure](/cli/azure). Pode utilizá-la no seu browser com o [Azure Cloud Shell](../cloud-shell/overview.md) ou pode [instalá-la](/cli/azure/install-azure-cli) no macOS, Linux e Windows e executá-la a partir da linha de comandos.

## <a name="list-role-definitions"></a>Definições de função de lista

Para listar todas as definições de funções disponíveis, utilize [lista de definição de função az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

O exemplo seguinte lista o nome e descrição de todas as definições de funções disponíveis:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

O exemplo seguinte apresenta uma lista todas as definições de função incorporada:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Lista de ações de uma definição de função

Para listar as ações de uma definição de função, utilize [lista de definição de função az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

As listas de exemplo seguintes o *contribuinte* definição de função:

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

As listas de exemplo seguintes o *ações* e *notActions* do *contribuinte* função:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

O exemplo seguinte lista as ações do *contribuinte de Máquina Virtual* função:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-role-assignments"></a>Listar atribuições de função

### <a name="list-role-assignments-for-a-user"></a>Listar atribuições de função para um utilizador

Para listar as atribuições de funções para um utilizador específico, utilize [lista de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Por predefinição, serão apresentadas apenas atribuições confinadas à subscrição. Para ver as atribuições de um âmbito por recurso ou grupo, utilize `--all`.

O exemplo seguinte lista as atribuições de funções que são atribuídas diretamente para o *patlong@contoso.com* utilizador:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Listar atribuições de função para um grupo de recursos

Para listar as atribuições de função que existem para um grupo de recursos, utilize [lista de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

O exemplo seguinte lista as atribuições de funções para o *pharma-vendas-projectforecast* grupo de recursos:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="create-role-assignments"></a>Criar atribuições de função

### <a name="create-a-role-assignment-for-a-user"></a>Criar uma atribuição de função para um utilizador

Para criar uma atribuição de função para um utilizador no âmbito do grupo de recursos, utilize [de criação da atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

O seguinte exemplo atribui o *contribuinte de Máquina Virtual* função *patlong@contoso.com* utilizador o *pharma-vendas-projectforecast* âmbito do grupo de recursos:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Criar uma atribuição de função para um grupo

Para criar uma atribuição de função para um grupo, utilize [de criação da atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O seguinte exemplo atribui o *leitor* função para o *Ann Mack equipa* grupo com o ID 22222222-2222-2222-2222-222222222222 no âmbito de subscrição. Para obter o ID do grupo, pode utilizar [lista de grupos do ad az](/cli/azure/ad/group#az-ad-group-list) ou [mostrar de grupo do ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

O seguinte exemplo atribui o *contribuinte de Máquina Virtual* função para o *Ann Mack equipa* grupo com o ID 22222222-2222-2222-2222-222222222222 num âmbito de recursos para uma rede virtual denominada *pharma vendas-projeto rede*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Criar uma atribuição de função para uma aplicação

Para criar uma função para uma aplicação, utilize [de criação da atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O seguinte exemplo atribui o *contribuinte de Máquina Virtual* função a uma aplicação com o ID de objeto 44444444-4444-4444-4444-444444444444 no *pharma-vendas-projectforecast* grupo de recursos âmbito. Para obter o ID de objeto da aplicação, pode utilizar [lista de aplicações do ad az](/cli/azure/ad/app#az-ad-app-list) ou [mostrar de aplicação de ad az](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

Para remover uma atribuição de função, utilize [eliminar a atribuição de função az](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

O exemplo a seguir remove o *contribuinte de Máquina Virtual* atribuição de função do *patlong@contoso.com* utilizador no *pharma-vendas-projectforecast* recursos grupo:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

O exemplo a seguir remove o *leitor* função a partir do *Ann Mack equipa* grupo com o ID 22222222-2222-2222-2222-222222222222 no âmbito de subscrição. Para obter o ID do grupo, pode utilizar [lista de grupos do ad az](/cli/azure/ad/group#az-ad-group-list) ou [mostrar de grupo do ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Funções personalizadas

### <a name="list-custom-roles"></a>Lista de funções personalizadas

Para listar as funções que estão disponíveis para atribuição a um âmbito, utilize [lista de definição de função az](/cli/azure/role/definition#az-role-definition-list).

Ambos os exemplos seguintes listam todas as funções personalizadas na subscrição atual:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, utilize [Criar definição de função az](/cli/azure/role/definition#az-role-definition-create). A definição de função pode ser uma descrição JSON ou um caminho para um ficheiro que contém uma descrição JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

O exemplo seguinte cria uma função personalizada com o nome *operador de Máquina Virtual*. Esta função personalizada atribui acesso a todas as operações de leitura de *Microsoft. Compute*, *Microsoft*, e *Network* fornecedores e atribui acesso a recursos Para começar, reiniciar e monitorizar máquinas virtuais. Esta função personalizada pode ser utilizada em duas subscrições. Este exemplo utiliza um ficheiro JSON como entrada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, primeiro utilizar [lista de definição de função az](/cli/azure/role/definition#az-role-definition-list) ao obter a definição de função. Segundo, efetue as alterações pretendidas para a definição de função. Por último, utilize [atualização az da definição de função](/cli/azure/role/definition#az-role-definition-update) para guardar a definição de função atualizada.

```azurecli
az role definition update --role-definition <role_definition>
```

O exemplo seguinte adiciona o *Microsoft.Insights/diagnosticSettings/* operação para o *ações* do *operador de Máquina Virtual* função personalizada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize [eliminar a definição de função az](/cli/azure/role/definition#az-role-definition-delete). Para especificar a role para eliminar, utilize o nome da função ou o ID de função. Para determinar o ID de função, utilize [lista de definição de função az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

O exemplo seguinte elimina a *operador de Máquina Virtual* função personalizada:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

