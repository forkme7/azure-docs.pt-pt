---
title: Do Azure Machine Learning pré-visualização de 2017 FAQ | Microsoft Docs
description: 'Este artigo contém perguntas mais comuns e funcionalidades de pré-visualização do Azure Machine Learning: respostas'
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 864e9ff5551637a93b957e5011a8857072c6eaaf
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2018
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Perguntas mais frequentes sobre o Azure Machine Learning

Azure Machine Learning é um serviço do Azure completamente gerido que lhe permite criar, testar, gerir e implementar modelos de AI e de aprendizagem. Os nossos serviços e aplicações transferível oferecem uma abordagem de código primeiro que tira partido da nuvem, no local e limite para fornecer a formação, implementar, gerir e monitorizar modelos com capacidade, a velocidade e a flexibilidade. Em alternativa, o Azure Machine Learning Studio oferece um baseada no browser, visual arrastar e largar criação ambiente em que nenhum codificação é necessária. 

## <a name="general-product-questions"></a>Perguntas gerais do produto

**Que outros serviços do Azure são necessários?**

Armazenamento de Blobs do Azure e de registo de contentor do Azure são utilizadas pelo Azure Machine Learning. Além disso, terá de aprovisionar os recursos de computação, tais como um cluster de VM de ciência de dados ou o HDInsight. Computação e de alojamento também são necessários quando implementar os serviços web, tal como [serviço de contentor Azure](https://docs.microsoft.com/azure/aks).

**Como é que Azure Machine Learning se relacionam com serviços do Microsoft Machine Learning no SQL Server 2017?**   

Serviços de Machine Learning no SQL Server 2017 é uma plataforma extensível, dimensionável para integrar tarefas de machine learning em fluxos de trabalho de base de dados. É uma opção perfeita para cenários onde é necessária, por exemplo, onde o movimento de dados é dispendioso ou untenable uma solução no local. Em contrapartida, as cargas de trabalho de nuvem ou híbrida são uma excelente opção para a nossa novos serviços do Azure. 

**Suportam Python e R? O que sobre outras linguagens de programação, como do C++**

Iremos suporta atualmente o Python apenas. Estamos a trabalhar para integração de R e esperar para disponível em breve. 

**Como Azure Machine Learning relacionadas com a Microsoft Machine Learning para Spark?**

MMLSpark fornece learning profunda e ferramentas de ciência de dados para o Apache Spark com ênfase na produtividade, facilitam de experimentação e algoritmos de-de-última geração. MMLSpark oferece integração de pipelines de Spark Machine Learning com o Toolkit de cognitivos e OpenCV. Pode criar poderosos e altamente dimensionáveis preditivos e analíticos modelos de dados de imagem e texto. MMLSpark está disponível com uma licença de open source e está incluído no AML Workbench como um conjunto de algoritmos e consumíveis modelos. Para obter mais informações sobre MMLSpark, visite a nossa documentação de produto. 

**Quais as versões do Spark são suportadas pelas novas ferramentas e serviços?**

Atualmente, o Workbench inclui e suporta MMLSpark versão 0.8, que é compatível com o Apache Spark 2.1. Tem também uma opção para utilizar a imagem de Docker preparados para a GPU do MMLSpark 0.8 em máquinas virtuais do Linux.

## <a name="experimentation-service"></a>Experimentação de serviço

**O que é o serviço de experimentação do Azure Machine Learning?**

O serviço de experimentação é um serviço gerido do Azure que demora a experimentação do machine learning para o próximo nível. Podem ser criadas experimentações localmente ou na nuvem. Rapidamente protótipo num ambiente de trabalho, em seguida, dimensionamento de máquinas virtuais ou os clusters do Spark. As VMs do Azure com a tecnologia GPU mais recente permitem-lhe participem na aprendizagem profunda rapidamente e eficaz. Também iremos tiver incluídas profunda integração com o Git, para que pode ligue facilmente fluxos de trabalho existentes para controlo de código, configuração e colaboração. 

**Como será posso cobrado para o serviço de experimentação?**

Os primeiros dois utilizadores associados com o serviço de experimentação do Azure Machine Learning são gratuitos. Os utilizadores adicionais serão cobrados à taxa de pré-visualização pública de $50/mês. Para obter mais informações sobre preços e faturação, visite a nossa página de preços.

**Será posso cobrado com base nas experimentações quantos posso executar?**

Não, o serviço de experimentação permite experimentações tantos conforme necessário e os encargos apenas com base no número de utilizadores. Os recursos de computação de Experimentação são cobrados em separado. Aconselhamo-lo para efetuar várias experimentações para que possa encontrar o melhor ajuste o modelo para a sua solução.   

**Os tipos específicos de recursos de armazenamento e computação posso utilizar?**

O serviço de experimentação pode executar das suas experimentações no locais máquinas (diretas ou baseada em Docker), [Virtual Machines do Azure](https://azure.microsoft.com/services/virtual-machines/), e [HDInsight](https://azure.microsoft.com/services/hdinsight/). O serviço também acede um [Storage do Azure](https://azure.microsoft.com/services/storage/) conta para armazenar saídas de execução e pode tirar partido uma [serviço de equipa do Visual Studio](https://azure.microsoft.com/services/visual-studio-team-services/) conta para o controlo de versão e o armazenamento de Git. Tenha em atenção que será faturado independentemente de quaisquer computação foram consumida e de recursos de armazenamento, com base no respetivo preços individuais.  


## <a name="model-management"></a>Gestão de Modelos

**O que é a gestão de modelo do Azure Machine Learning?**

O Azure Machine Learning modelo Management é um serviço gerido do Azure que permite que as equipas de cientistas e dev ops dados implementar modelos preditivos de forma fiável para uma grande variedade de ambientes. Repositórios de Git e contentores de Docker fornecem traceability e repetibilidade. Modelos podem ser implementados de forma fiável na nuvem, no local ou edge. Uma vez na produção, pode gerir o desempenho do modelo, em seguida, proativamente reparametrização dos se degrada o desempenho. Pode implementar modelos de máquinas locais, a [VMs do Azure](https://azure.microsoft.com/services/virtual-machines/), Spark no [HDInsight](https://azure.microsoft.com/services/hdinsight/) ou orquestradas de Kubernetes [serviço de contentor Azure](https://azure.microsoft.com/services/container-service/) clusters.  

**O que é um "modelo"?**

Um modelo é o resultado de uma experimentação executar que tem sido promovido para a gestão de modelo. Um modelo que está registado na conta de alojamento é contabilizado contra o seu plano, incluindo modelos atualizados através da iteração reparametrização ou versão.

**O que é um "modelo gerido?"**

Um modelo é o resultado de um processo de formação e a aplicação de um algoritmo de machine learning para dados de formação. Gestão de modelo permite-lhe implementar modelos como serviços web, várias versões dos seus modelos de gerir e monitorizar as métricas e desempenho. Foram registados modelos "Gerido" com uma conta de gestão de modelo do Azure Machine Learning. Como exemplo, considere um cenário em que está a tentar prever vendas. Durante a fase de experimentação, gerar muitos modelos utilizando conjuntos de dados diferentes ou algoritmos. Ter gerado quatro modelos com accuracies variando mas optar por registar apenas o modelo com a precisão máxima. O modelo que está registado torna-se o primeiro modelo gerido.
 
**O que é uma "a implementação?"**

Gestão de modelo permite-lhe implementar modelos como contentores do serviço web em pacote no Azure. Estes serviços web podem ser invocados utilizando REST APIs. Cada serviço web é contabilizado como uma implementação única e o número total de implementações ativas é contado para o seu plano. O plano de vendas previsão de exemplo, quando implementar o seu melhor desempenho modelo a utilizar, é incrementado por uma implementação. Se, em seguida, reparametrização dos e implementar a outra versão, tem duas implementações. Se determinar que o modelo mais recente é melhor e este é debitado por um eliminar original, a contagem de implementações.  

**Que recursos de computação específicos estão disponíveis para os meus implementações?** 

Gestão de modelo pode executar as implementações, como contentores de Docker registado para [serviço de contentor Azure](https://azure.microsoft.com/services/container-service/), como [Virtual Machines do Azure](https://azure.microsoft.com/services/virtual-machines/), ou em máquinas locais. Destinos de implementação adicionais serão adicionados em breve. Tenha em atenção que será faturado independentemente de quaisquer recursos de computação foram consumidos, com base no respetivo preços individuais.     

**Pode utilizar a gestão de modelo do Azure Machine Learning para implementar modelos criados utilizando ferramentas que não seja o serviço de experimentação?**

Obter a melhor experiência quando implementar modelos criados utilizando o serviço de experimentação. No entanto, pode implementar modelos criados com outras estruturas e ferramentas. Iremos suportar um leque de modelos, incluindo MMLSpark, TensorFlow, Microsoft cognitivos Toolkit, scikit-saber, Keras, etc. 

**Posso utilizar o meus próprio recursos do Azure?**

Sim, o serviço de experimentação e gestão de modelo de trabalhar em conjunto com vários arquivos de dados do Azure, as cargas de trabalho e outros serviços de computação. Consulte a nossa documentação técnica para obter mais detalhes sobre os serviços do Azure necessários.

**Suporte no local e na nuvem cenários de implementação?**

Sim. Iremos suportar no local e na nuvem cenários de implementação através de contentores de Docker. Incluem destinos de execução local: implementações de Docker de nó único, [Microsoft SQL Server com os serviços de ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop ou Spark. Também é suportada a nuvem implementações através de Docker, incluindo: agrupados implementações através do serviço de contentor do Azure e Kubernetes, HDInsight ou Spark clusters. Cenários de limite são suportados através do Azure IOT Edge e contentores de Docker. 

**Pode executar uma imagem de Docker que foi criada utilizando a CLI do Azure Machine Learning no outro anfitrião?**

Sim. Pode utilizar a imagem como um serviço web em qualquer anfitrião do docker desde que o anfitrião tem recursos de computação suficientes para alojar a imagem de docker.

**Suporta reparametrização dos modelos implementados?**

Sim, pode implementar várias versões do mesmo modelo. Gestão de modelo irá suportar as atualizações de serviço para atualizado todos os modelos e as imagens.

## <a name="workbench"></a>Workbench

**O que é o Workbench do Azure Machine Learning?**

O Workbench do Azure Machine Learning é uma aplicação de complementar incorporada para cientistas de dados profissionais. Disponível para o Windows e Mac, o Workbench do Machine Learning fornece descrição geral, gestão e o controlo de soluções do machine learning. O Workbench do Machine Learning inclui acesso para estruturas de AI inovador da Microsoft e a Comunidade de código aberto. Iremos tiver incluído os toolkits de ciência de dados mais populares, incluindo TensorFlow, Microsoft cognitivos Toolkit, Spark ML, scikit-saiba e muito mais. Podemos também ativar a integração com a ciência de dados populares IDEs como blocos de notas do Jupyter, PyCharm e Visual Studio Code. O Workbench do Machine Learning tem capacidades de preparação de dados incorporados rapidamente de exemplo, compreender e preparar dados, se estruturados ou não estruturados. A nossa nova ferramenta de preparação do dados chamada [PROSE](https://microsoft.github.io/prose/), baseia-se na tecnologia inovadores da Microsoft Research.  

**É Workbench um IDE?**

Não. O Workbench do Machine Learning tem foi concebido como um complemento para IDEs populares, tais como blocos de notas do Jupyter, o Visual Studio Code e PyCharm mas não é um IDE totalmente funcional. O Workbench do Machine Learning oferece algumas texto básica capacidades de edição, mas a depuração, intellisense e outras normalmente utilizadas capacidades IDE não são suportadas. Recomendamos que utilize o IDE favorito para o desenvolvimento de código, editar e depuração. Também pode pretender tente [as ferramentas de código do Visual Studio para AI](https://www.visualstudio.com/downloads/ai-tools-vscode).

**É cobrada uma taxa para utilizar o Workbench do Azure Machine Learning?**

Não. Azure Workbench de aprendizagem máquina é uma aplicação gratuita. Pode transferi-lo para qualquer número de máquinas e de utilizadores, conforme necessitar. Para utilizar o Azure Machine Learning Workbench, precisa de uma conta de Experimentação. .  

**Suportam as capacidades de linha de comandos?**

Sim, o Azure Machine Learning oferece uma interface completa de CLI. A CLI do Machine Learning é instalada por predefinição com o Workbench do Azure Machine Learning. Também é fornecido como parte da máquina virtual Linux ciência de dados no Azure e irá ser integrada no [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Pode utilizar blocos de notas do Jupyter com Workbench?**

Sim! Pode executar blocos de notas do Jupyter no Workbench, com o Workbench como a aplicação de alojamento de cliente, tal como pretende utilizar um browser como um cliente. 

**Kernels que notas do Jupyter são suportados?**

A versão atual do Jupyter incluído com o Workbench inicia um kernel do Python 3 e um kernel adicional para cada ficheiro de "runconfig" na sua pasta aml_config. Configurações suportadas incluem:
- Local Python
- Python no local ou remoto Docker

## <a name="data-formats-and-capabilities"></a>Os formatos de dados e capacidades

**Os formatos de ficheiro são atualmente suportados para ingestão de dados no Workbench?**

As ferramentas de preparação de dados no Workbench suportam atualmente ingestão dos seguintes formatos: 
- Ficheiros delimitados como CSV, TSV, etc.  
- Ficheiros de largura fixa
- Ficheiros de texto simples
- Excel (.xls/xlsx)
- Ficheiros JSON
- Ficheiros de parquet 
- Ficheiros personalizados (scripts), se a sua solução requer a ingestão de dados de fontes adicionais, código de Python pode ser utilizado para... 

**As localizações de armazenamento de dados são atualmente suportadas?**

Para a pré-visualização pública, Workbench suporta ingestão de dados a partir de: 
- Disco rígido local ou localização de armazenamento de rede mapeadas
- BLOB do Azure ou de armazenamento do Azure (requer uma subscrição do Azure)
- Azure SQL Server
- Microsoft SQL Server


**Os tipos de dados wrangling, preparação e transformações estão disponíveis?**

Para pré-visualização pública, Workbench suporta "Deriva coluna por exemplo", "Dividir coluna por exemplo", "Texto Clustering", "Processar valores em falta" e muitas outras.  Workbench também suporta a conversão do tipo de dados, agregação de dados (CONTAGEM, média, VARIÂNCIA, etc.) e as associações de dados complexas. Para obter uma lista completa das capacidades suportadas, visite a nossa documentação de produto. 

**Existem os limites de tamanho de dados impostos pelo Azure Machine Learning Workbench, experimentação ou modelo de gestão?**

Não, os novos serviços não impõem quaisquer limitações de dados. No entanto, existem limitações introduzidas pelo ambiente em que estiver a efetuar a preparação de dados, formação de modelo, experimentação ou implementação. Por exemplo, se estiver a filtrar um ambiente local para formação, são limitado pelo espaço disponível no disco rígido. Em alternativa, se tiver como objetivo o HDInsight, são limitados por qualquer dimensão associada ou restraints de computação. 

## <a name="algorithms-and-libraries"></a>Algoritmos e bibliotecas

**Os algoritmos são suportados no Azure Machine Learning Workbench?**

Os nossos produtos de pré-visualização e serviços incluem o melhor da Comunidade do código aberto. Suportamos uma vasta gama de algoritmos e bibliotecas, incluindo TensorFlow, scikit-saber mais, o Apache Spark e o Toolkit de cognitivos. O Workbench do Azure Machine Learning também pacotes a [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) pacote.

**Azure Machine Learning a inter-relação entre o Toolkit de cognitivos?**

O [Toolkit de cognitivos](https://www.microsoft.com/cognitive-toolkit/) é uma das muitas estruturas suportadas pela nossa novas ferramentas e serviços. O Toolkit cognitivos é um toolkit avançada learning unificada que permite-lhe consumir e combinar populares modelos de machine learning, incluindo o reencaminhamento de Feed as redes Neurais profundas, Convolutional redes, sequência de sequência e redes periódica. Para obter mais informações sobre o Toolkit de cognitivos, visite a nossa [documentação do produto](https://docs.microsoft.com/cognitive-toolkit/). 