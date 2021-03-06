---
title: Criar uma tarefa do Stream Analytics com o portal do Azure | Microsoft Docs
description: Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics, configurar entradas e saídas, e definir uma consulta.
services: stream-analytics
keywords: Stream analytics, tarefas na Cloud, portal do Azure, entrada da tarefa, saída da tarefa, transformação da tarefa
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: c421ab96585da011cdaef9933ceb8a78ffe356a9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure

Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics. Neste início rápido, vai definir uma tarefa do Stream Analytics que lê dados do sensor de exemplo e filtra as linhas que têm uma temperatura média superior a 100 a cada 30 segundos. Neste artigo, lê os dados a partir do armazenamento de blobs, transforma os dados e escreve-os num contentor diferente no mesmo armazenamento de blobs.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir a tarefa do Stream Analytics, deve preparar os dados configurados como entrada da tarefa. Execute os seguintes passos para preparar os dados de entrada necessários para a tarefa:

1. Transfira os [dados do sensor de exemplo](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) a partir do GitHub. Os dados de exemplo contêm informações do sensor no seguinte formato JSON:  

   ```json
   {
     "time": "2016-01-26T21:18:52.0000000",
     "dspl": "sensorC",
     "temp": 87,
     "hmdt": 44
   }
   ```
2. Iniciar sessão no portal do Azure  

3. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**. Preencha o painel de tarefas da Conta de armazenamento com **Nome** definido como "myasastorageaccount", **Localização** definido como "EUA Oeste 2", **Grupo de recursos** definido como "MyRG" (anfitrião da conta de armazenamento no mesmo grupo de recursos da tarefa de Transmissão em Fluxo para um melhor desempenho). As restantes definições podem ser mantidas nos respetivos valores predefinidos.  

   ![Criar conta de armazenamento](./media/stream-analytics-quick-create-portal/create-a-storage-account.png)

4. No painel **Todos os recursos**, localize a conta de armazenamento que criou no passo anterior. Abra o painel **Descrição geral** e, em seguida, o mosaico **Blobs**.  

5. No painel **Serviço Blob**, selecione **Contentor**, forneça um **Nome** para o contentor, como *container1* e altere o **Nível de acesso público** para Blob (acesso de leitura anónimo apenas para blobs) > selecione **OK**.  

   ![Criar um contentor](./media/stream-analytics-quick-create-portal/create-a-storage-container.png)

6. Aceda ao contentor que criou no passo anterior, selecione **Carregar** e carregue os dados do sensor que obteve no passo 1.  

   ![Carregar dados de exemplo para o blob](./media/stream-analytics-quick-create-portal/upload-sample-data-to-blob.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Iniciar sessão no portal do Azure  

2. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.  

3. Selecione **Dados+Analytics** > **Tarefa do Stream Analytics** na lista de resultados.  

4. Preencha o painel da tarefa do Stream Analytics com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Nome da tarefa   |  myJob   |   Introduza um nome para identificar a tarefa do Stream Analytics. O nome da tarefa do Stream Analytics só pode conter carateres alfanuméricos, hífenes e carateres de sublinhado e tem de ter entre 3 a 63 carateres. |
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que quer utilizar para esta tarefa. |
   |Grupo de recursos   |   myResourceGroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Localização  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione a localização geográfica onde pode alojar a tarefa do Stream Analytics. Utilize a localização mais próxima dos seus utilizadores para um melhor desempenho e reduzir os custos de transferência de dados. |
   |Unidades de transmissão em fluxo  | 1  |   As unidades de transmissão em fluxo representam os recursos informáticos que são necessários para executar uma tarefa. Por predefinição, este valor está definido como 1. Para saber mais sobre o dimensionamento de unidades de transmissão em fluxo, veja o artigo [Compreender e ajustar as unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).   |
   |Ambiente de alojamento  |  Nuvem  |   As tarefas do Stream Analytics podem ser implementadas na cloud ou no Edge. A cloud permite-lhe implementar no Azure Cloud e o Edge permite-lhe implementar num dispositivo do IoT Edge. |

   ![Criar tarefa](./media/stream-analytics-quick-create-portal/create-job.png)

5. Selecione a caixa **Afixar ao dashboard** para colocar a tarefa no seu dashboard e, em seguida, selecione **Criar**.  

6. Deverá ver a mensagem "Implementação em curso..." apresentada na parte superior direita da janela do browser. 

## <a name="configure-input-to-the-job"></a>Configurar a entrada da tarefa

Nesta secção, irá configurar o armazenamento de blobs como entrada da tarefa do Stream Analytics. Antes de configurar a entrada, crie uma conta de armazenamento de blobs.  

### <a name="add-the-input"></a>Adicionar a entrada 

1. Navegue para a tarefa do Stream Analytics.  

2. Selecione **Entradas** > **Adicionar entrada de fluxo** > **Armazenamento de blobs**.  

3. Preencha o painel **armazenamento de blobs** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  BlobInput   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento  |  myasastorageaccount |  Escolha ou introduza o nome da conta de armazenamento. Os nomes de contas de armazenamento são detetados automaticamente se forem criados na mesma subscrição. |
   |Contentor  | container1 | Escolha o nome do contentor que tem dados de exemplo. Os nomes de contentores são detetados automaticamente se forem criados na mesma subscrição. |

4. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar dados de entrada](./media/stream-analytics-quick-create-portal/configure-input.png)
 
## <a name="configure-output-to-the-job"></a>Configurar a saída da tarefa

1. Navegue para a tarefa do Stream Analytics que criou anteriormente.  

2. Selecione **Saídas > Adicionar > Armazenamento de blobs**.  

3. Preencha o painel **armazenamento de blobs** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída |   BlobOutput   |   Introduza um nome para identificar a saída da tarefa. |
   |Subscrição  |  \<A sua subscrição\>  |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento |  myasastorageaccount |   Escolha ou introduza o nome da conta de armazenamento. Os nomes de contas de armazenamento são detetados automaticamente se forem criados na mesma subscrição.       |
   |Contentor |   container2  |  Crie um novo contentor na mesma conta de armazenamento que utilizou para a entrada.   |

4. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar a saída](./media/stream-analytics-quick-create-portal/configure-output.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Navegue para a tarefa do Stream Analytics que criou anteriormente.  

2. Selecione **Consulta** e atualize a consulta da seguinte forma:  

   ```sql
   SELECT 
   System.Timestamp AS OutputTime,
   dspl AS SensorName,
   Avg(temp) AS AvgTemperature
   INTO
     MyBlobOutput
   FROM
     MyBlobInput TIMESTAMP BY time
   GROUP BY TumblingWindow(second,30),dspl
   HAVING Avg(temp)>100
   ```

3. Neste exemplo, a consulta lê os dados do blob e copia-os para um novo ficheiro no blob. Em seguida, selecione **Guardar**.  

   ![Configurar a transformação da tarefa](./media/stream-analytics-quick-create-portal/configure-job-transformation.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Regresse ao painel de descrição geral da tarefa e selecione **Iniciar**  

2. Em **Iniciar tarefa**, selecione **Personalizado** para o campo **Hora de início**. Selecione um dia antes do momento em que carregou o ficheiro para o armazenamento de blobs, uma vez que a hora de carregamento do ficheiro é anterior à hora atual. Quando terminar, selecione **Iniciar**.  

   ![Iniciar a tarefa](./media/stream-analytics-quick-create-portal/start-the-job.png)

3. Após alguns minutos, no portal, localize a conta de armazenamento e o contentor que configurou como saída da tarefa. Agora, pode ver o ficheiro de saída no contentor. A tarefa demora alguns minutos a iniciar pela primeira vez. Depois de ser iniciada, continuará a ser executada à medida que os dados são recebidos.  

   ![Saída transformada](./media/stream-analytics-quick-create-portal/transformed-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma tarefa do Stream Analytics simples. Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)

