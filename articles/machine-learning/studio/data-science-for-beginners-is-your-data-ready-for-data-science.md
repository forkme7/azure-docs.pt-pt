---
title: Os seus dados estão prontos para a ciência de dados? Avaliação de dados - Azure Machine Learning | Microsoft Docs
description: Critérios de quatro estes têm de cumprir para ser preparado para ciência de dados. Este vídeo tem concretos exemplos para ajudar a avaliação de dados básicos.
keywords: dados relevantes, avaliar dados, preparar dados, os critérios de dados, dados prontos
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 2d9c66d89b82c63561b147f3d2537ba6ad07c511
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="is-your-data-ready-for-data-science"></a>Os seus dados estão prontos para a ciência de dados?
## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Ciência de dados para a série de principiantes
Saiba como avaliar os dados para se certificar de que cumpre os critérios de básicos para estar preparado para ciência de dados.

Para tirar o máximo partido da série, veja todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Dados de ciência para principiantes* é uma introdução rápida aos ciência de dados em cinco vídeos curtos.

* Vídeo 1: [a 5 questões de respostas de ciência de dados](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg.)*
* Vídeo de 2: Está pronta para ser ciência de dados os dados?
* Vídeo 3: [fazer uma pergunta, poderá responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg.)*
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg.)*
* Vídeo 5: [copiar trabalho outras pessoas fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcript: Está pronto para ciência de dados os dados?
Bem-vindo ao "É os dados prontos para ciência de dados?" o vídeo segundo da série *dados de ciência para principiantes*.  

Antes de ciência de dados pode dar-lhe as respostas que pretende, terá de atribuir-lhe algumas materiais raw de alta qualidade para trabalhar com. Tal como efetuar uma pizza, melhor ingredients que começar com melhor produto final. 

## <a name="criteria-for-data"></a>Critérios de dados
Ciência de dados, há determinadas ingredients que tem de ser solicitados em conjunto, incluindo:

* Relevantes
* Ligado
* Preciso
* Espaço suficiente para trabalhar com o

## <a name="is-your-data-relevant"></a>Os dados são relevantes?
Por isso ingredient primeiro - terá de dados que são relevantes.

![Dados relevantes vs dados irrelevantes - avaliar dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

À esquerda, a tabela apresenta o nível de alcohol blood de sete pessoas testado fora de uma barra Boston, a média de batting Sox vermelho no último vencem o jogo e o preço de milk no arquivo de conveniência mais próximo.

Esta é a todos os dados perfeitamente legítimos. Falhas apenas é que não é relevante. Não há nenhuma relação óbvias entre estes números. Se alguém lhe forneceu o preço atual milk e a média de batting Sox vermelho, não é possível foi adivinhar o conteúdo de alcohol blood.

Agora ver a tabela à direita. Desta vez o corpo de cada pessoa em massa foi medida, bem como o número de drinks já tinham.  Os números em cada linha agora são relevantes para si. Se posso lhe forneceu os meus corpo em massa e o número de Margaritas posso ter tido, pode efetuar um estimado no meu blood alcohol conteúdo.

## <a name="do-you-have-connected-data"></a>Se ligou dados?
A seguinte ingredient é dados ligada.

![Ligado a dados vs dados desligados - critérios de dados, dados prontos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Eis alguns dados relevantes a qualidade hamburgers: grill temperatura, patty ponderação e classificação no prato local magazine. Mas tenha em atenção as lacunas na tabela à esquerda.

A maioria dos conjuntos de dados estão em falta alguns valores. É comum para holes como esta, e existem formas de trabalhar em torno deles. Mas se há demasiado em falta, os dados começa a aspeto UE cheese.

Se observar a tabela à esquerda, há pelo volume de dados em falta, que é difícil de pensar com qualquer tipo de relação entre grill relativos à temperatura e patty ponderação. Este exemplo mostra dados desligados.

A tabela à direita, no entanto, está cheia e concluir - um exemplo de dados ligada.

## <a name="is-your-data-accurate"></a>Os dados é preciso?
A seguinte ingredient é precisão. Seguem-se quatro destinos para acessos.

![Dados exatos vs. dados incorretos - critérios de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Observe o destino no canto superior direito. Não há um agrupamento sólido à direita em torno os olhos bulls. Que, obviamente, é preciso. Oddly, o idioma de ciência de dados, desempenho, à direita de destino abaixo do mesmo é também considerado preciso.

Se mapear o Centro destas setas, verá que estiver muito próximo os olhos bulls. As setas são distribuir todos em torno do destino, que está a consideradas imprecisa, mas que está a centra-se olho bulls, pelo que está a consideradas exata.

Observe agora o destino do canto superior esquerdo. Aqui as setas muito fechar em conjunto, atingiu um agrupamento sólido. Estiverem exatos, mas está incorretas porque o Centro de forma desativar os olhos bulls. As setas no destino da parte inferior esquerda estão incorretas e imprecisa. Este archer tem mais prática.

## <a name="do-you-have-enough-data-to-work-with"></a>Tem dados suficientes para trabalhar com?
Por fim, ingredient #4 é dados suficientes.

![Tem dados suficientes para análise? Avaliação de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Considere cada ponto de dados na tabela como sendo um stroke de pincel num desenho. Se tiver apenas alguns dos mesmos, o desenho pode ser difusa - é difícil de saber o que está.

Se adicionar alguns traços Pincel mais, em seguida, o desenho começa a obter um pouco sharper.

Quando tiver barely suficiente traços, apenas verá suficiente para tomar algumas decisões abrangentes. É algures que poderá pretender visite? Procura bright, parece máximo limpo – Sim, que é onde vou de férias.

Como adicionar mais dados, a imagem torna-se mais clara e possa tomar decisões mais detalhadas. Agora pode examinar os três hotéis no banco esquerdo. Pode reparar as funcionalidades da arquitetura de um em primeiro plano. Ainda pode optar por permaneça o terceiro piso devido à vista.

Com os dados que são relevantes, ligado, exata e suficiente, tem todas as ingredients necessários para alguns ciência de dados de alta qualidade.

Lembre-se de que veja os vídeos de quatro em *dados de ciência para principiantes* do Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Passos Seguintes
* [Tente uma primeira experimentação de ciência de dados com Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](what-is-machine-learning.md)
