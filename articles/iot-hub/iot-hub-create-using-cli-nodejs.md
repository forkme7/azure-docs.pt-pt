---
title: Criar um IoT hub com a CLI do Azure (azure.js) | Microsoft Docs
description: Como criar um hub IoT do Azure utilizando a CLI do Azure de várias plataformas (azure.js).
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 7e761ba66f4b28dd7cc59dc8998c23810dd2b7d1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Criar um IoT hub com a CLI do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar a CLI do Azure (azure.js) para criar e gerir os hubs IoT do Azure através de programação. Este artigo mostra como utilizar a CLI do Azure (azure.js) para criar um hub IoT.

Pode concluir a tarefa utilizando uma das seguintes versões CLI:

* CLI do Azure (azure.js) – a CLI para os clássica e resource Gestão modelos de implementação, tal como descrito neste artigo.
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) - a próxima geração CLI para o modelo de implementação de gestão de recursos.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [CLI do Azure 0.10.4] [ lnk-CLI-install] ou posterior. Se já tiver a CLI do Azure instalado, pode validar a versão atual na linha de comandos com o seguinte comando:

```azurecli
azure --version
```

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). A CLI do Azure tem de estar no modo Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Definir a conta e a subscrição do Azure

1. Na linha de comandos, início de sessão, escrevendo o seguinte comando:

   ```azurecli
    azure login
   ```

   Utilize o browser web sugerida e o código para se autenticar.
1. Se tiver várias subscrições do Azure, ligar para o Azure concede acesso a todas as subscrições Azure associadas com as suas credenciais. Pode ver as subscrições do Azure e identificar qual é o predefinido, utilizando o comando:

   ```azurecli
    azure account list
   ```

   Para definir o contexto de subscrição na qual pretende executar o resto da utilização de comandos:

   ```azurecli
    azure account set <subscription name>
   ```

1. Se não tiver um grupo de recursos, pode criar uma com o nome **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> O artigo [utilizar a CLI do Azure para gerir recursos do Azure e os grupos de recursos] [ lnk-CLI-arm] fornece mais informações sobre como utilizar a CLI do Azure para gerir recursos do Azure.

## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Parâmetros necessários:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**. O nome do grupo de recursos. O formato é sensível a maiúsculas e minúsculas alfanumérica, caráter de sublinhado e hífenes, comprimento de 1-64.
* **nome**. O nome do hub IoT a ser criado. O formato é sensível a maiúsculas e minúsculas alfanuméricos, hífenes, comprimento de 3 a 50.
* **Localização**. A localização (região/datacenter do azure) para aprovisionar o IoT hub.
* **sku-name**. O nome do sku, uma das: [F1 S1, S2, S3]. Para obter detalhes sobre cada sku de mensagens em fila, consulte [preços do IoT Hub do Azure](https://azure.microsoft.com/pricing/details/iot-hub/). Atualmente, os escalões básicos só estão disponíveis através do portal. 
* **unidades**. O número de unidades aprovisionados. Para obter mais informações sobre limites de unidade, consulte [preços do IoT Hub do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Para ver todos os parâmetros disponíveis para a criação, pode utilizar o comando de ajuda na linha de comandos:

```azurecli
azure iothub create -h
```

Exemplo rápido: criar um IoT Hub chamado **exampleIoTHubName** no grupo de recursos **exampleResourceGroup**, execute o seguinte comando:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Este comando da CLI do Azure cria um S1 Standard IoT Hub para o qual é-lhe faturado. Pode eliminar o hub IoT **exampleIoTHubName** utilizando os seguintes comandos:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como desenvolver para o IoT Hub, consulte o artigo seguinte:

* [SDKs do IoT][lnk-sdks]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [No portal do Azure para gerir o IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
