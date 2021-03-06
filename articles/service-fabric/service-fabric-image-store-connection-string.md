---
title: Cadeia do connnection de arquivo de imagem do Azure Service Fabric | Microsoft Docs
description: Compreender a cadeia de ligação do arquivo de imagens
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 3c34a3851dbb5c5258b3dc0cf35a510f62cbe14e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2018
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Compreender a definição de ImageStoreConnectionString

Em algumas das nossa documentação, iremos brevemente mencionar a existência de um parâmetro de "ImageStoreConnectionString" sem que descrevem o que realmente significa. E depois vai através de um artigo como [implementar e remover aplicações utilizando o PowerShell][10], aspeto que tem tudo o que fazer é o valor de copiar/colar como é apresentado no manifesto do cluster do cluster de destino. Para a definição tem de ser configurável por cluster, mas quando criar um cluster através de [portal do Azure][11], não existe nenhuma opção para configurar esta definição e está sempre "fabric: arquivo de imagens". O que é o objetivo, em seguida, esta definição?

![Manifesto do cluster][img_cm]

Service Fabric iniciado como uma plataforma de consumo interno do Microsoft por várias equipas diversificadas, pelo que alguns aspetos do mesmo são altamente personalizáveis - o "arquivo de imagens" é um desse aspeto. Essencialmente, o arquivo de imagens é um repositório incorporável para armazenar pacotes de aplicações. Quando a aplicação for implementada para um nó do cluster, esse nó transfere os conteúdos do seu pacote de aplicação a partir do arquivo de imagens. O ImageStoreConnectionString é uma definição que inclui todas as informações necessárias para clientes e nós localizar o arquivo de imagens correto para um determinado cluster.

Existem três tipos possíveis de fornecedores de arquivo de imagens e os respetivos correspondente cadeias de ligação são os seguintes:

1. Serviço de arquivo de imagens: "fabric: arquivo de imagens"

2. Sistema de ficheiros: "caminho do sistema file:[file]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

O tipo de fornecedor utilizado na produção é o serviço de arquivo de imagem, que é um serviço de sistema persistente com monitorização de estado que pode ver a partir do Explorador de recursos de infraestrutura de serviço. 

![Serviço de arquivo de imagens][img_is]

Que aloja o arquivo de imagens num serviço de sistema do cluster em si elimina dependências externas para o repositório de pacote e nos dá mais controlo sobre a localização de armazenamento. Melhoramentos futuros à volta o arquivo de imagens são provável que o fornecedor de arquivo de imagens de destino, primeiro, caso contrário, exclusivamente. A cadeia de ligação para o fornecedor de serviço de arquivo de imagens não tem qualquer informação exclusiva, uma vez que o cliente já está ligado ao cluster de destino. O cliente só tem de saber que os protocolos direcionada para o serviço de sistema devem ser utilizados.

O fornecedor do sistema de ficheiros é utilizado em vez do serviço de arquivo de imagens para clusters de caixa de um locais, durante o desenvolvimento arranque ligeiramente mais rapidamente o cluster. A diferença é pequena, normalmente, mas é uma otimização útil para a maioria dos utilizadores durante o desenvolvimento. É possível implementar um cluster de caixa de um local com os outros fornecedor tipos de armazenamento bem, mas não existe, normalmente, nenhum motivo para tal, uma vez que o fluxo de trabalho de desenvolver/teste permanece igual independentemente do fornecedor. O fornecedor de armazenamento do Azure só existe para o suporte legacy dos antigo clusters implementados antes do fornecedor de serviço de arquivo de imagens foi introduzido.

Além disso, nem o fornecedor do sistema de ficheiros nem o fornecedor de armazenamento do Azure, deve ser utilizado como um método de partilhar um arquivo de imagens entre vários clusters - esta operação resultará Corrupção de dados de configuração de cluster como cada cluster pode escrever dados em conflito para a Arquivo de imagens. A partilha de pacotes de aplicações aprovisionado entre vários clusters, utilize [sfpkg] [ 12] ficheiros em vez disso, que podem ser também carregadas para qualquer arquivo externo com um URI de transferência.

Por isso, enquanto o ImageStoreConnectionString é configurável, é geralmente apenas utilizar a predefinição. Quando a publicação no Azure através do Visual Studio, o parâmetro é automaticamente definido para si em conformidade. Para a implementação programática para clusters alojado no Azure, a cadeia de ligação é sempre "fabric: arquivo de imagens". Embora quando em dúvida, o valor sempre pode ser verificado ao obter o manifesto do cluster por [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Teste no local e clusters de produção, devem sempre ser configuradas para utilizar o fornecedor de serviço de arquivo de imagens.

### <a name="next-steps"></a>Passos Seguintes
[Implementar e remover aplicações utilizando o PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
