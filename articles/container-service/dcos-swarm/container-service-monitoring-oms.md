---
title: Monitorizar o cluster do Azure DC/SO - operações de gestão
description: Monitorize um cluster DC/OS do serviço de contentor do Azure com a análise de registos.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: b326e5b686e14cefac4e6376bd3f26787ea1d10d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Monitor de um cluster DC/OS do serviço de contentor do Azure com a análise de registos

Análise de registos é baseado na nuvem IT solução de gestão que o ajuda a gerir e proteger no local e a infraestrutura de nuvem. da Microsoft Contentor é uma solução na análise de registos, que ajuda-o a ver o inventário de contentor, o desempenho e a registos numa única localização. Pode de auditoria, resolver problemas de contentores através da visualização de registos de localização centralizada e localizar inúteis consumir contentor em excesso num anfitrião.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre a solução de contentor, consulte o [análise de registos do contentor solução](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configurar a análise de registos do universo de DC/OS


Este artigo pressupõe que configurou um DC/OS e implementar aplicações do contentor web simples no cluster.

### <a name="pre-requisite"></a>Pré-requisito
- [Subscrição do Microsoft Azure](https://azure.microsoft.com/free/) -pode obter esta gratuitamente.  
- Inicie o programa de configuração de área de trabalho de análise - consulte "Passo 3" abaixo
- [DC/SO CLI](https://dcos.io/docs/1.8/usage/cli/install/) instalado.

1. No dashboard do DC/OS, clique em universo e procure OMS conforme mostrado abaixo.

![](media/container-service-monitoring-oms/image2.png)

2. Clique em **Instalar**. Verá um pop cópias de segurança com as informações de versão e um **Instalar pacote** ou **avançadas instalação** botão. Ao clicar em **avançadas instalação**, que leva ao **propriedades de configuração específicas do OMS** página.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Aqui, será solicitado para introduzir o `wsid` (ID de área de trabalho de análise de registos) e `wskey` (a chave primária para o id da área de trabalho). Para obter ambos `wsid` e `wskey` tem de criar uma conta no <https://mms.microsoft.com>.
Siga os passos para criar uma conta. Quando tiver terminado a criar a conta, tem de obter o `wsid` e `wskey` clicando **definições**, em seguida, **origens ligadas**e, em seguida, **servidores Linux**, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

4. Selecione o número de instâncias que pretende e clique no botão 'Rever e instalar'. Normalmente, deverá ter o número de instâncias igual ao número da VM tem no seu cluster de agente. Instala o agente do OMS para Linux como contentores individuais em cada VM que se pretende recolher informações de monitorização e informações de registo.

## <a name="setting-up-a-simple-oms-dashboard"></a>Configurar um dashboard do OMS simple

Depois de ter instalado o agente do OMS para Linux nas VMs, o passo seguinte consiste em configurar o dashboard do OMS. Existem duas formas para efetuar este procedimento: Portal do OMS ou o Portal do Azure.

### <a name="oms-portal"></a>Portal do OMS 

Inicie sessão portal do OMS (<https://mms.microsoft.com>) e vá para o **Galeria de solução**.

![](media/container-service-monitoring-oms/image6.png)

Assim que estiver no **Galeria de solução**, selecione **contentores**.

![](media/container-service-monitoring-oms/image7.png)

Depois de selecionar a solução de contentor, verá o mosaico na página do Dashboard de descrição geral do OMS. Depois dos dados de transmissões em contentor estão indexados, verá o mosaico preenchido com as informações nos mosaicos de vista de solução.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Portal do Azure 

Inicie sessão no portal do Azure em <https://portal.microsoft.com/>. Aceda a **Marketplace**, selecione **monitorização + gestão** e clique em **ver todos os**. Em seguida, escreva `containers` na pesquisa. Verá "contentores" nos resultados da pesquisa. Selecione **contentores** e clique em **criar**.

![](media/container-service-monitoring-oms/image9.png)

Assim que clicar em **criar**, irá pedir-lhe para a sua área de trabalho. Selecione a sua área de trabalho ou se não tiver uma, crie uma nova área de trabalho.

![](media/container-service-monitoring-oms/image10.PNG)

Assim que tiver selecionado a sua área de trabalho, clique em **criar**.

![](media/container-service-monitoring-oms/image11.png)

Para obter mais informações sobre a solução de contentor de análise do registo, consulte o [análise de registos do contentor solução](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Como dimensionar o agente do OMS com ACS DC/OS 

No caso de terá de ter instalado o agente do OMS pouca a contagem de nós real ou são como aumentar verticalmente VMSS adicionando mais VM, pode fazê-ao aumentar a `msoms` serviço.

Pode ir para o separador de serviços de IU do DC/OS ou Marathon e aumentar verticalmente o número de nós.

![](media/container-service-monitoring-oms/image12.PNG)

Isto irá implementar para outros nós que ainda não tem implementado o agente do OMS.

## <a name="uninstall-ms-oms"></a>Desinstalar o MS OMS

Para desinstalar o MS OMS, introduza o seguinte comando:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Indique!!!
O que funciona? O que está em falta? Pessoa que precisa para esta opção para ser úteis para si? Indique em <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Passos Seguintes

 Agora que configurou análise de registos para monitorizar os contentores[ver o dashboard de contentor](../../log-analytics/log-analytics-containers.md).
