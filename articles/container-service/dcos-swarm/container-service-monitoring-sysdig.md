---
title: Monitorizar um cluster do Azure Container Service com o Sysdig
description: Monitorizar um cluster do Azure Container Service com o Sysdig.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 0c0f4fd1f3a8242061e198d7b5447656f9008e96
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Monitorizar um cluster do Azure Container Service com o Sysdig

Neste artigo, vamos implementar agentes do Sysdig em todos os nós de agentes no seu cluster do Azure Container Service. Para esta configuração, é necessária uma conta do Sysdig. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Aceda a [ http://app.sysdigcloud.com ](http://app.sysdigcloud.com) para configurar uma conta de nuvem Sysdig. 

## <a name="sysdig"></a>Sysdig
O Sysdig é um serviço de monitorização que lhe permite monitorizar os seus contentores no cluster. O Sysdig é conhecido por ajudá-lo em resolução de problemas, mas também tem as suas métricas de monitorização básicas para CPU, Redes, Memória e I/O. O Sysdig torna mais fácil ver que contentores se esforçam mais ou utilizam mais memória e CPU. Esta vista encontra-se na secção "Descrição Geral", que atualmente se encontra em fase beta. 

![IU do Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurar uma implementação do Sysdig com o Marathon
Estes passos mostram-lhe como configurar e implementar aplicações do Sysdig no seu cluster com o Marathon. 

Aceder à IU do DC/OS, através de [ http://localhost:80/ ](http://localhost:80/) uma vez na IU do DC/OS, navegue para "Universo", que se encontre na parte inferior esquerda e, em seguida, procure "Sysdig."

![Sysdig no DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

Agora, para completar a configuração, precisa de uma conta na nuvem do Sysdig ou de uma conta de avaliação gratuita. Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Em seguida, introduza a sua Chave de Acesso à configuração do Sysdig no DC/OS Universe. 

![Configuração do Sysdig no DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Agora, defina as ocorrências para 10000000. Assim, sempre que um novo nó for adicionado ao cluster, o Sysdig irá automaticamente implementar um agente nesse novo nó. Trata-se de uma solução provisória para garantir que o Sysdig é implementado para todos os novos agentes no cluster. 

![Configuração do Sysdig em ocorrências do DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig4.png)

Após instalar o pacote, regresse à IU do Sysdig e poderá explorar as diferentes métricas de utilização para contentores no seu cluster. 

Também pode instalar dasboards específicos do Mesos e do Marathon através do [assistente de dashboard novo](https://app.sysdigcloud.com/#/dashboards/new).
