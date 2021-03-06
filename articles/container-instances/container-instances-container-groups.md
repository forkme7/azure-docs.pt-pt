---
title: Grupos de contentor de instâncias de contentor do Azure
description: Compreender como funcionam os grupos de contentor em instâncias de contentor do Azure
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: cb3d8c27a82c7dfc5fd71c1c7d589e81890e5cfb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contentor em instâncias de contentor do Azure

O recurso de nível superior em instâncias de contentor do Azure está a *grupo contentor*. Este artigo descreve quais são os grupos de contentor e os tipos de cenários que ativar.

## <a name="how-a-container-group-works"></a>Como funciona um grupo contentor

Um grupo contentor é uma coleção de contentores que obter agendada no mesmo computador anfitrião. Os contentores de um grupo contentor partilham um ciclo de vida, rede local e volumes de armazenamento. É semelhante no conceito a um *pod* no [Kubernetes] [ kubernetes-pod] e [DC/SO][dcos-pod].

O diagrama seguinte mostra um exemplo de um grupo contentor, que inclui vários contentores:

![Diagrama de grupos de contentor][container-groups-example]

Este grupo de contentor de exemplo:

* Está agendada uma máquina de anfitrião único.
* É atribuído uma etiqueta de nome DNS.
* Expõe um único endereço IP público, com uma porta exposto.
* É composta por dois contentores. Um contentor escuta na porta 80, enquanto o outro escuta na porta 5000.
* Inclui duas partilham de ficheiros do Azure como volume monta e cada contentor monta uma das partilhas localmente.

> [!NOTE]
> Os grupos de contentor multi são atualmente restritos para contentores de Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades de contentores do Windows, pode encontrar as diferenças da plataforma atual em [Quotas e disponibilidade das regiões do Azure Container Instances](container-instances-quotas.md).

## <a name="deployment"></a>Implementação

Contentor *grupos* tem uma alocação de recursos mínimo de 1 vCPU e 1 GB de memória. Individuais *contentores* num contentor, grupo pode ser aprovisionado com menos de 1 vCPU e 1 GB de memória. Dentro de um grupo contentor, a distribuição de recursos pode ser personalizada para vários contentores dentro dos limites de estabelecer o nível de grupo de contentor. Por exemplo, dois contentores cada com vCPU 0.5 que reside num grupo de contentor que atribuiu 1 vCPU.

## <a name="networking"></a>Redes

Grupos de contentor partilham um endereço IP e um espaço de nomes de porta nesse endereço IP. Para permitir aos clientes externos atingir um contentor dentro do grupo, tem de expor a porta no endereço IP de e para o contentor. Porque contentores dentro do grupo de partilham de um espaço de nomes de porta, o mapeamento de porta não é suportado. Contentores dentro de um grupo podem aceder entre si através de localhost nas portas que possam tem expostos, mesmo que essas portas não são expostas externamente no endereço IP do grupo.

## <a name="storage"></a>Armazenamento

Pode especificar volumes externos para montar dentro de um grupo contentor. Pode mapear esses volumes em caminhos específicos dentro os contentores individuais num grupo.

## <a name="common-scenarios"></a>Cenários comuns

Os grupos de contentor multi são útil nos casos em que pretende dividir uma única tarefa funcional num pequeno número de imagens do contentor. Estas imagens, em seguida, podem ser fornecidas por equipas diferentes e têm requisitos de recursos separado.

Exemplo de utilização pode incluir:

* Um contentor de aplicação e um contentor de registo. O contentor de registo recolhe a saída de registos e as métricas pela aplicação principal e escreve-as para armazenamento de longa duração.
* Um contentor de aplicação e um contentor de monitorização. O contentor de monitorização periodicamente faz um pedido para a aplicação para se certificar de que está em execução e corretamente a responder e gera um alerta se não estiver.
* Um contentor que serve uma aplicação web e um contentor, a extrair o conteúdo mais recente do controlo de origem.

## <a name="next-steps"></a>Passos Seguintes

Saiba como implementar um grupo contentor multi contentor com um modelo Azure Resource Manager:

> [!div class="nextstepaction"]
> [Implementar um grupo contentor](container-instances-multi-container-group.md)

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
