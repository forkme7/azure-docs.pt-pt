---
title: 'Passo 6: Aceder ao serviço da Web do Machine Learning | Microsoft Docs'
description: 'Passo 6 da desenvolver uma solução preditiva explicação passo a passo: aceder a um serviço Web do Azure Machine Learning Active Directory.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 82e068ca3656b28e5e8dad19a31d6e5ff9c6f8b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Passo 6 das Instruções: Aceder ao serviço Web do Azure Machine Learning

Este é o último passo de instruções, [desenvolver uma solução de Análise Preditiva no Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](walkthrough-2-upload-data.md)
3. [Criar uma nova experimentação](walkthrough-3-create-new-experiment.md)
4. [Dar formação e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementar o serviço Web](walkthrough-5-publish-web-service.md)
6. **Aceder ao serviço Web**

- - -
No passo anterior nestas instruções Implementámos um serviço web que utiliza o nosso modelo de previsão do risco de crédito. Agora os utilizadores têm capacidade para enviar dados para a mesma e receber os resultados. 

O serviço Web é um serviço web do Azure que pode receber e devolver dados através de REST APIs de uma das seguintes formas:  

* **Pedido/resposta** - o utilizador envia uma ou mais linhas de dados de crédito para o serviço utilizando um protocolo HTTP e o serviço responde com uma ou mais conjuntos de resultados.
* **Execução de lote** - o utilizador armazena um ou mais linhas de dados de crédito num Azure blob e, em seguida, envia a localização de blob para o serviço. O serviço pontuações todas as linhas de dados no blob de entrada, armazena os resultados num blob de outra e devolve o URL do contentor.  

A forma mais rápida e mais fácil de aceder a um serviço de web clássico é efetuada através de [do Azure ML resposta-pedido Web do App Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) ou [modelo de aplicação ao Web do serviço de execução do Azure ML Batch](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Estes modelos de aplicação web podem criar uma aplicação web personalizada que sabe o seu serviço web dados de entrada e o que irá devolver. Tudo o que precisa de fazer é fornecer acesso ao seu serviço web e os dados, e o modelo não o resto.

Para obter mais informações sobre como utilizar os modelos de aplicação web, consulte [consumir um serviço Web do Azure Machine Learning com um modelo de aplicação web](consume-web-service-with-web-app-template.md).

Também pode desenvolver uma aplicação personalizada para aceder ao serviço web com o código de arranque fornecido por si no R, c# e linguagens de programação do Python.

Pode encontrar detalhes completos sobre no [como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

