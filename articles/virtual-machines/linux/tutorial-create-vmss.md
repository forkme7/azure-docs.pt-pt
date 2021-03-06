---
title: "Criar um Conjunto de Dimensionamento de Máquinas Virtuais para Linux no Azure | Microsoft Docs"
description: "Criar e implementar uma aplicação de elevada disponibilidade em VMs do Linux com um conjunto de dimensionamento de máquinas virtuais"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 263983017e08dcc9a8e614c159ef5afaaf1d924e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Criar um Conjunto de Dimensionamento de Máquinas Virtuais e implementar uma aplicação de elevada disponibilidade no Linux
Um conjunto de dimensionamento de máquinas virtuais permite implementar e gerir um conjunto de máquinas virtuais idênticas e de dimensionamento automático. Pode dimensionar o número de VMs no conjunto de dimensionamento manualmente ou definir regras para dimensionar automaticamente com base na utilização de recursos, como CPU, exigência de memória ou tráfego de rede. Neste tutorial, vai implementar um conjunto de dimensionamento de máquinas virtuais no Azure. Saiba como:

> [!div class="checklist"]
> * Utilizar o cloud-init para criar uma aplicação para dimensionar
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou reduzir o número de instâncias num conjunto de dimensionamento
> * Criar regras de dimensionamento automático
> * Ver informações de ligação para instâncias de conjunto de dimensionamento
> * Utilizar discos de dados num conjunto de dimensionamento


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.22 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="scale-set-overview"></a>Descrição geral de Conjunto de Dimensionamento
Um conjunto de dimensionamento de máquinas virtuais permite implementar e gerir um conjunto de máquinas virtuais idênticas e de dimensionamento automático. As VMs num conjunto de dimensionamento estão distribuídas por domínios de atualização e de falha lógicos num ou mais *grupos de posicionamento*. Estes são grupos de VMs configuradas de forma parecida, à semelhança dos [conjuntos de disponibilidade](tutorial-availability-sets.md).

As VMs são criadas conforme necessário num conjunto de dimensionamento. Pode definir regras de dimensionamento automático para controlar como e quando as VMs são adicionadas ou removidas do conjunto de dimensionamento. Estas regras podem ser acionadas com base em métricas, como a carga da CPU, a utilização da memória ou o tráfego de rede.

Os conjuntos de dimensionamento suportam até 1000 VMs quando utiliza uma imagem da plataforma Azure. Para cargas de trabalho com requisitos de instalação ou de personalização de VM significativos, poderá querer [Criar uma imagem de VM personalizada](tutorial-custom-images.md). Pode criar até 300 VMs num conjunto de dimensionamento quando utiliza uma imagem personalizada.


## <a name="create-an-app-to-scale"></a>Criar uma aplicação para dimensionar
Para uma utilização de produção, poderá querer [Criar uma imagem de VM personalizada](tutorial-custom-images.md) que inclui a sua aplicação instalada e configurada. No âmbito deste tutorial, vamos personalizar as VMs no primeiro arranque para ver rapidamente um conjunto de dimensionamento em ação.

Num tutorial anterior, explorou o tópico [Como personalizar uma máquina virtual do Linux no primeiro arranque](tutorial-automate-vm-deployment.md) com o cloud-init. Pode utilizar o mesmo ficheiro de configuração cloud-init para instalar o NGINX e executar uma aplicação Node.js "Hello World" simples. 

Na shell atual, crie um ficheiro com o nome *cloud-init.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. Certifique-se de que o ficheiro de inicialização da cloud é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Para poder criar um conjunto de dimensionamento, crie primeiro um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupScaleSet* na localização *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss#az_vmss_create). O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet*, utiliza o ficheiro cloud-init para personalizar a VM e gera chaves SSH, caso não existam:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos. Existem tarefas em segundo plano que continuam a ser executadas após a CLI do Azure devolver o utilizador à linha de comandos. Pode demorar mais alguns minutos antes de poder aceder à aplicação.


## <a name="allow-web-traffic"></a>Permitir tráfego Web
Foi criado automaticamente um balanceador de carga como parte do conjunto de dimensionamento de máquinas virtuais. O balanceador de carga distribui o tráfego por um conjunto de VMs definidas através das regras do balanceador de carga. Pode saber mais sobre a configuração e os conceitos de balanceador de carga no próximo tutorial, [Como fazer o balanceamento de carga de máquinas de virtuais no Azure](tutorial-load-balancer.md).

Para permitir que o tráfego chegue à aplicação Web, crie uma regra com [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Testar a aplicação
Para ver a aplicação Node.js na Web, obtenha o endereço IP público do seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). O exemplo seguinte obtém o endereço IP para *myScaleSetLBPublicIP* criada como parte do conjunto de dimensionamento:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Introduza o endereço IP público num browser. A aplicação é apresentada, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu o tráfego:

![Executar aplicação Node.js](./media/tutorial-create-vmss/running-nodejs-app.png)

Para ver o conjunto de dimensionamento em ação, pode forçar a atualização do browser para ver o balanceador de carga distribuir o tráfego por todas as VMs que têm a sua aplicação em execução.


## <a name="management-tasks"></a>Tarefas de gestão
Ao longo do ciclo de vida do conjunto de dimensionamento, poderá ter de executar uma ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizam várias tarefas do ciclo de vida. A CLI 2.0 do Azure fornece uma maneira rápida de executar essas tarefas. Seguem-se algumas tarefas comuns.

### <a name="view-vms-in-a-scale-set"></a>Ver VMs num conjunto de dimensionamento
Para ver uma lista de VMs em execução no seu conjunto de dimensionamento, utilize [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) da seguinte forma:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

O resultado é semelhante ao seguinte exemplo:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentar ou reduzir as instâncias de VM
Para ver o número de instâncias atualmente existentes num conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss#az_vmss_show) e consulte *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Em seguida, pode aumentar ou reduzir manualmente o número de máquinas virtuais existentes no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss#az_vmss_scale). O exemplo seguinte define o número de VMs no seu conjunto de dimensionamento como *3*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Configurar regras de dimensionamento automático
Em vez de dimensionar manualmente o número de instâncias no seu conjunto de dimensionamento, pode definir regras de dimensionamento automático. Estas regras monitorizaram as instâncias no seu conjunto de dimensionamento e respondem em conformidade com base nas métricas e nos limiares definidos por si. O exemplo seguinte aumenta horizontalmente o número de instâncias em uma instância quando a carga média da CPU é superior a 60% ao longo de um período de 5 minutos. Se, em seguida, a carga média da CPU descer abaixo dos 30% ao longo de um período de 5 minutos, as instâncias são reduzidas horizontalmente em uma instância. O seu ID de subscrição é utilizado para criar os URIs de recursos para os diversos componentes do conjunto de dimensionamento. Para criar estas regras com [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create), copie e cole o seguinte perfil de comando de dimensionamento automático:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Para reutilizar o perfil de dimensionamento automático, pode criar um ficheiro JSON (JavaScript Object Notation) e transmiti-lo para o comando `az monitor autoscale-settings create` com o parâmetro `--parameters @autoscale.json`. Para obter mais informações de estrutura sobre a utilização do dimensionamento automático, consulte [melhores práticas de dimensionamento automático](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Obter informações de ligação
Para obter informações de ligação sobre as VMs nos seus conjuntos de dimensionamento, utilize [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info). Este comando produz o endereço IP público e a porta para cada VM que lhe permite ligar-se com SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Utilizar discos de dados com conjuntos de dimensionamento
Pode criar e utilizar discos de dados com conjuntos de dimensionamento. Num tutorial anterior, explorou o tópico [Gerir discos do Azure](tutorial-manage-disks.md) que descreve as melhores práticas e as melhorias de desempenho para a criação de aplicações em discos de dados em vez de no disco do SO.

### <a name="create-scale-set-with-data-disks"></a>Criar conjunto de dimensionamento com discos de dados
Para criar um conjunto de dimensionamento e anexar discos de dados, adicione o parâmetro `--data-disk-sizes-gb` ao comando [az vmss create](/cli/azure/vmss#az_vmss_create). O exemplo seguinte cria um conjunto de dimensionamento com discos de dados de *50* GB anexados a cada instância:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

Quando são removidas instâncias de um conjunto de dimensionamento, também são removidos os discos de dados anexados.

### <a name="add-data-disks"></a>Adicionar discos de dados
Para adicionar um disco de dados a instâncias no seu conjunto de dimensionamento, utilize [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). O exemplo seguinte adiciona um disco de *50* GB a cada instância:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Desanexar discos de dados
Para remover um disco de dados de instâncias no seu conjunto de dimensionamento, utilize [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach). O exemplo seguinte remove o disco de dados em LUN *2* de cada instância:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um conjunto de dimensionamento de máquinas virtuais. Aprendeu a:

> [!div class="checklist"]
> * Utilizar o cloud-init para criar uma aplicação para dimensionar
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou reduzir o número de instâncias num conjunto de dimensionamento
> * Criar regras de dimensionamento automático
> * Ver informações de ligação para instâncias de conjunto de dimensionamento
> * Utilizar discos de dados num conjunto de dimensionamento

Avance para o próximo tutorial para saber mais sobre conceitos de balanceamento de carga para máquinas virtuais.

> [!div class="nextstepaction"]
> [Fazer o balanceamento de carga de máquinas virtuais](tutorial-load-balancer.md)
