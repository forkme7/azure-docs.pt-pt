---
title: Do Azure Machine Learning a implementação de serviço Web do modelo gestão | Microsoft Docs
description: Este documento descreve os passos envolvidos na implementação de um modelo de machine learning com o Azure Machine Learning modelo de gestão.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 5211fa29af1d8cba17049b69974189990d30f34a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Implementar um modelo de Machine Learning como um serviço web

O Azure Machine Learning modelo Management fornece interfaces para implementar modelos como de serviços web baseados em Docker. Pode implementar modelos criados com estruturas, tais como o Spark, a Microsoft cognitivos Toolkit (CNTK), Keras, Tensorflow e Python. 

Este documento aborda os passos para implementar os seus modelos como serviços web através da interface de linha de comandos (CLI) de gestão de modelo do Azure Machine Learning.

## <a name="what-you-need-to-get-started"></a>O que precisa de começar

Para tirar o máximo partido deste guia, deve ter acesso de contribuinte a um grupo de recursos que pode implementar os seus modelos para ou a uma subscrição do Azure.
A CLI é previamente instalada o Workbench do Azure Machine Learning e no [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).  Também pode ser instalado como um pacote autónomo.

Além disso, um ambiente de conta e a implementação da gestão de modelo tem já de ser definido.  Para obter mais informações sobre como configurar a conta de gestão de modelo e o ambiente para o local e a implementação de cluster, consulte [configuração de gestão de modelo](deployment-setup-configuration.md).

## <a name="deploying-web-services"></a>Implementar serviços web
Utilizar os CLIs, pode implementar serviços web para ser executado no computador local ou num cluster.

Recomendamos que comece com uma implementação local. Primeiro a validar que o modelo e o código de trabalho, em seguida, implementar o serviço web para um cluster para utilização de escala de produção.

Seguem-se os passos de implementação:
1. Utilizar o modelo de Machine Learning guardado, preparado,
2. Criar um esquema de entrada do seu serviço web e dados de saída
3. Criar uma imagem com base em Docker contentor
4. Criar e implementar o serviço web

### <a name="1-save-your-model"></a>1. Guardar o seu modelo
Começar a utilizar o ficheiro de modelo treinado guardados, por exemplo, mymodel.pkl. A extensão de ficheiro varia consoante a plataforma que utilizar para formação e guarde o modelo. 

Por exemplo, pode utilizar a biblioteca de Pickle do Python para guardar um modelo preparado para um ficheiro. Eis um [exemplo](http://scikit-learn.org/stable/modules/model_persistence.html) utilizando o conjunto de dados Iris:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Criar um ficheiro de schema.json

Enquanto a geração de esquema é opcional, é altamente recomendado para definir o formato da variável pedido e a entrada para processar melhor.

Crie um esquema para validar automaticamente a entrada e saída do seu serviço web. Os CLIs também utilizam o esquema para gerar um documento Swagger para o seu serviço web.

Para criar o esquema, importe as bibliotecas seguintes:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Em seguida, defina as variáveis de entrada como um Spark dataframe, Pandas dataframe ou NumPy matriz. O exemplo seguinte utiliza uma matriz de Numpy:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
O exemplo seguinte utiliza uma dataframe Spark:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

O exemplo seguinte utiliza uma dataframe PANDAS:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

O exemplo seguinte utiliza um formato JSON genérico:

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Criar um ficheiro de score.py
Fornecer um ficheiro de score.py, que carrega o seu modelo e devolve o result(s) predição utilizando o modelo.

O ficheiro deve incluir duas funções: init e executar.

Adicione o seguinte código na parte superior do ficheiro score.py para ativar a funcionalidade de recolha de dados que o ajuda a recolher dados de entrada e de previsão do modelo

```python
from azureml.datacollector import ModelDataCollector
```

Verifique [recolha de dados de modelo](how-to-use-model-data-collection.md) secção para obter mais detalhes sobre como utilizar esta funcionalidade.

#### <a name="init-function"></a>Função init
Utilize a função de init para carregar o modelo guardado.

Exemplo de uma função de init simples carregar o modelo:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Executar a função
A função de execução utiliza o modelo e os dados de entrada para devolver uma predição.

Exemplo de uma simples executar a função de processar a entrada e devolvendo o resultado de predição:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Registar um modelo
Os seguintes comandos é utilizado para registar um modelo que criou no passo 1 acima,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Crie o manifesto.
Os seguintes comandos ajuda a criar um manifesto para o modelo

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
Pode adicionar um modelo anteriormente registado para o manifesto utilizando argumento `--model-id` ou `-i` no comando mostrado acima. É possível especificar vários modelos adicionais com -i argumentos.

### <a name="6-create-an-image"></a>6. Criar uma imagem 
Pode criar uma imagem com a opção de ter criado o manifesto antes. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>Também pode utilizar um único comando para efetuar a criação de registo, o manifesto e o modelo do modelo. Utilize -h com o serviço criar comando para obter mais detalhes.

Como alternativa, há um único comando para registar um modelo, crie um manifesto e criar uma imagem (mas não criar e implementar o serviço web, ainda) como um passo da seguinte forma.

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Para obter mais detalhes sobre os parâmetros de comando, tipo -h no final do comando imagem do ml az por exemplo, crie -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Criar e implementar o serviço web
Implemente o serviço utilizando o seguinte comando:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Também pode utilizar um único comando para efetuar todas as 4 passos anteriores. Utilize -h com o serviço criar comando para obter mais detalhes.

Como alternativa, há um único comando para registar um modelo, crie um manifesto, criar uma imagem, bem como, criar e implementar o serviço Web, como um passo da seguinte forma.

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8. O serviço de teste
Utilize o seguinte comando para obter informações sobre como chamar o serviço:

```
az ml service usage realtime -i <service id>
```

Chame o serviço utilizando a função de execução da linha de comandos:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

O seguinte exemplo chama um serviço web de Iris de exemplo:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Passos Seguintes
Agora que tiver testado o seu serviço web para ser executada localmente, pode implementá-la para um cluster para utilização em grande escala. Para obter mais informações sobre como configurar um cluster para implementação do serviço web, consulte [configuração de gestão de modelo](deployment-setup-configuration.md). 
