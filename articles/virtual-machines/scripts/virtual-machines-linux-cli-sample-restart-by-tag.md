---
title: Exemplo do Script da CLI do Azure - Reiniciar VMs | Microsoft Docs
description: Exemplo do Script da CLI do Azure - Reiniciar VMs por etiqueta e ID
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: a9f7cf8ba492004cb6d9e359bfb392448dfbe813
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2018
---
# <a name="restart-vms"></a>Reiniciar VMs

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Este exemplo mostra várias formas de obter algumas VMs e reiniciá-las.

A primeira permite reiniciar todas as VMs no grupo de recursos.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

A segunda permite obter as VMs marcadas com `az resouce list` e filtra pelos recursos que são VMs e reinicia-as.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Este exemplo funciona numa shell do Bash. Para obter as opções de execução de scripts da CLI do Azure num cliente Windows, veja [Executar a CLI do Azure no Windows](../windows/cli-options.md).


## <a name="sample-script"></a>Script de exemplo

O exemplo tem três scripts.
O primeiro aprovisiona as máquinas virtuais.
Utiliza a opção no-wait para que o comando seja devolvido sem aguardar o aprovisionamento de cada VM.
O segundo aguarda o aprovisionamento completo das VMs.
O terceiro script reinicia todas as VMs que foram aprovisionadas e, em seguida, apenas as VMs marcadas.

### <a name="provision-the-vms"></a>Aprovisionar as VMs

Este script cria um grupo de recursos e, em seguida, cria três VMs a reiniciar.
Duas delas estão marcadas.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Wait

Este script verifica o estado de aprovisionamento a cada 20 segundos até que as três VMs estejam aprovisionadas ou falhe o aprovisionamento de uma delas.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Reiniciar as VMs

Este script reinicia todas as VMs no grupo de recursos e, em seguida, reinicia apenas as VMs marcadas.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos, as VMs e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Cria as máquinas virtuais.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilizado com `--query` para garantir que as VMs são aprovisionadas antes de serem reiniciadas e, em seguida, para obter os IDs das VMs para reiniciá-las. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilizado com `--query` para obter os IDs das VMs através da etiqueta. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Reinicia as VMs. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Pode ver exemplos do script da CLI da máquina virtual adicionais na [Documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
