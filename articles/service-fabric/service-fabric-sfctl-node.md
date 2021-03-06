---
title: Azure Service Fabric CLI sfctl nós | Microsoft Docs
description: Descreve os comandos de nó de sfctl CLI de recursos de infraestrutura de serviço.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 50c7fe38d8bf7b14adf437f85c758e465e7d231d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-node"></a>sfctl node
Gerir os nós que formam um cluster.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
|    desactivar       | Desative um nó de cluster do Service Fabric com a intenção de Desativação especificada.|
|    ativar        | Ative um nó de cluster do Service Fabric, que está atualmente desativado.|
|    estado de funcionamento        | Obtém o estado de funcionamento de um nó de Service Fabric.|
|    informações          | Obtém as informações sobre um nó específico no cluster de Service Fabric.|
|    lista          | Obtém a lista de nós no cluster de Service Fabric.|
|    carregar          | Obtém as informações de carga de um nó de Service Fabric.|
|    remoção de estado  | Notifica o Service Fabric que o estado persistente num nó foi permanentemente removido ou perdido.|
|    report-health | Envia um relatório de estado de funcionamento no nó de Service Fabric.|
|    Reiniciar       | Reinicia um nó de cluster do Service Fabric.|
|    transição    | Inicia ou para um nó de cluster.|
|    Estado de transição| Obtém o progresso de uma operação iniciado utilizando StartNodeTransition.|


## <a name="sfctl-node-disable"></a>desativação do nó de sfctl
Desative um nó de cluster do Service Fabric com a intenção de Desativação especificada.

Desative um nó de cluster do Service Fabric com a intenção de Desativação especificada. Assim que a desativação estiver em curso, a intenção de Desativação pode ser aumentada, mas não diminuída (por exemplo, um nó que está desativado com a tentativa de colocar em pausa pode ser desativado ainda mais com reinício, mas não o inverso. Podem ser reativados nós utilizando a ativar uma operação de nó qualquer altura depois de estes são desativadas. Se a desativação não for concluída, isto cancela a desativação. Um nó que fica inativo e volta a funcionar enquanto desativada ainda tem de ser reativado antes de serviços podem ser colocados nesse nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| --deactivation-intent | Descreve o motivo para desativar o nó ou intenção. |
| tempo limite – -t       | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug            | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h          | Mostra esta mensagem de ajuda e saída.|
| --o de saída        | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta            | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose          | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-enable"></a>Ativar de nó sfctl
Ative um nó de cluster do Service Fabric que está atualmente desativado.

Ativa um nó de cluster do Service Fabric que está atualmente desativado. Depois de ativado, o nó novamente torna-se um destino viável para colocação de novo réplicas, e quaisquer réplicas desativadas restante no nó são reativadas.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| tempo limite – -t       | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug            | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h          | Mostra esta mensagem de ajuda e saída.|
| --o de saída        | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta            | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose          | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-health"></a>Estado de funcionamento de nó de sfctl
Obtém o estado de funcionamento de um nó de Service Fabric.

Obtém o estado de funcionamento de um nó de Service Fabric. Utilize EventsHealthStateFilter para filtrar a recolha de eventos de estado de funcionamento comunicado no nó com base no estado de funcionamento. Se o nó que especificou, o nome não existe no arquivo de estado de funcionamento, esta ação devolve um erro.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| --events-health-state-filter| Permite a filtragem na coleção de objetos de HealthEvent devolvido com base no estado de funcionamento. Os valores possíveis para este parâmetro incluem o valor de número inteiro de um dos seguintes Estados de funcionamento. Apenas os eventos que correspondem ao filtro são devolvidos. Todos os eventos são utilizados para avaliar o estado de funcionamento agregada. Se não for especificado, são devolvidas todas as entradas. Os valores de estado são enumeração baseada no sinalizador de, pelo que o valor pode ser uma combinação destes valores obtidos através de operador de 'Ou' bit a bit. Por exemplo, se o valor fornecido é 6, em seguida, todos os eventos com o valor HealthState OK (2) e de aviso (4) são devolvidos. -Predefinição - valor predefinido. Corresponde a qualquer HealthState. O valor é zero. -Nenhuma - filtro que não corresponde a qualquer valor HealthState. Utilizado para não devolver resultados numa coleção especificada dos Estados. O valor é 1. -Ok - filtre que corresponde de entrada com o valor de HealthState Ok. O valor é 2. -Aviso - filtro que corresponde à entrada com HealthState valor aviso. O valor é 4. -Erro - filtro que corresponda à entrada com o valor de HealthState erro. O valor é 8. -Todos os - filtro que corresponda à entrada com qualquer valor HealthState. O valor é a 65535.|
| tempo limite – -t             | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                  | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                | Mostra esta mensagem de ajuda e saída.|
| --o de saída              | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta                  | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-info"></a>informações do nó de sfctl
Obtém as informações sobre um nó específico no cluster de Service Fabric.

Obtém as informações sobre um nó específico no Cluster.The de recursos de infraestrutura do serviço de resposta incluir o nome, o estado, o id, o estado de funcionamento, o tempo de atividade e o outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| tempo limite – -t       | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug            | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h          | Mostra esta mensagem de ajuda e saída.|
| --o de saída        | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta            | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose          | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-list"></a>lista de nós sfctl
Obtém a lista de nós no cluster de Service Fabric.

Obtém a lista de nós no cluster de Service Fabric. A resposta inclui o nome, o estado, o id, o estado de funcionamento, o tempo de atividade e o outros detalhes sobre o nó.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| -token de continuação| O parâmetro de token de continuação é utilizado para obter o seguinte conjunto de resultados. Um token de continuação com um valor não vazio está incluído na resposta da API de quando os resultados do sistema não se enquadram numa única resposta.      Quando este valor é transmitido para a próxima chamada de API, a API devolve o seguinte conjunto de resultados. Se não existirem resultados adicionais, em seguida, o token de continuação não contém um valor. O valor deste parâmetro não deve ser o URL, codificado.|
| --node-status-filter| Permite a filtragem de nós com base na NodeStatus. Apenas os nós que o valor do filtro especificado correspondentes são devolvidos. O valor do filtro pode ser um dos seguintes procedimentos. Predefinição: predefinido.|
| tempo limite – -t     | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug          | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h        | Mostra esta mensagem de ajuda e saída.|
| --o de saída      | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta          | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose        | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-load"></a>carga de nó sfctl
Obtém as informações de carga de um nó de Service Fabric.

Obtém as informações de carga de um nó de Service Fabric para todas as métricas que têm de carga ou a capacidade definido.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| tempo limite – -t       | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug            | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h          | Mostra esta mensagem de ajuda e saída.|
| --o de saída        | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta            | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose          | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-restart"></a>reiniciar o nó sfctl
Reinicia um nó de cluster do Service Fabric.

Reinicia um nó de cluster do Service Fabric já foi iniciado.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --nome do nó [necessário]| O nome do nó.|
| -criar-recursos de infraestrutura-captura  | Especifica verdadeiro para criar uma captura do processo de nó de recursos de infraestrutura. Esta é a maiúsculas e minúsculas.  Predefinição: False.|
| --node-instance-id | O ID de instância do nó de destino. Se o ID de instância for especificado o nó é reiniciado apenas se corresponder com a instância atual do nó. Um valor predefinido de "0" corresponderia a qualquer ID de instância. O ID de instância pode ser obtido utilizando a consulta do nó de get.  Predefinição: 0.|
| tempo limite – -t       | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug            | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h          | Mostra esta mensagem de ajuda e saída.|
| --o de saída        | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.  Predefinição: json.|
| – consulta            | Cadeia de consulta JMESPath. Para obter mais informações e exemplos, consulte http://jmespath.org/.|
| -verbose          | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="sfctl-node-transition"></a>transição de nó sfctl
Inicia ou para um nó de cluster.

Inicia ou para um nó de cluster.  Um nó de cluster é um processo, não a instância de SO em si.
Para iniciar um nó, transmita "Start" para o parâmetro NodeTransitionType. Para parar um nó, transmita "Parar de" para o parâmetro NodeTransitionType. Esta API começa a operação - quando a API devolve que o nó poderá não terminar em transição ainda. Chame GetNodeTransitionProgress com o mesmo OperationId para obter o progresso da operação. 

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| – id de instância de nó [necessário]| O ID de instância de nó do nó de destino. Isto pode ser determinado através da API de GetNodeInfo.|
| --nome do nó [necessário]| O nome do nó.|
| -tipo de transição de nó [necessário]| Indica o tipo de transição para executar.                       NodeTransitionType.Start inicia um nó de paragem.                       NodeTransitionType. Parar deixa de um nó que está a funcionar. |
| – id de operação [necessário]| Um GUID que identifica uma chamada desta API.  Isto é transmitido para a API de GetProgress correspondente.|
| -stop-duração-na-segundos [necessário]| A duração, em segundos, para manter o nó parada.  O valor mínimo é 600, o número máximo é 14400. Após este período de tempo expira, o nó automaticamente volta a funcionar.|
| tempo limite – -t                      | Tempo limite do servidor em segundos.  Predefinição: 60.|

### <a name="global-arguments"></a>Argumentos global

|Argumento|Descrição|
| --- | --- |
| -debug                           | Aumente a verbosidade do registo para mostrar que todos os registos de depuração.|
| -ajudar -h                         | Mostra esta mensagem de ajuda e saída.|
| --o de saída                       | Formato de saída.  Valores permitidos: json, jsonc, tabela, tsv.                       Predefinição: json.|
| – consulta                           | Cadeia de consulta JMESPath. Consulte http://jmespath.org/ para obter mais informações e exemplos.|
| -verbose                         | Aumente a verbosidade do registo. Utilize - a depuração para os registos de depuração completas.|

## <a name="next-steps"></a>Passos Seguintes
- [A configuração](service-fabric-cli.md) os recursos de infraestrutura do serviço CLI.
- Saiba como utilizar a CLI de recursos de infraestrutura de serviço utilizando o [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).