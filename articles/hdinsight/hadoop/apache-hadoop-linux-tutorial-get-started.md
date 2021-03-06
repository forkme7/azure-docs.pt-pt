---
title: "Introdução ao Hadoop e ao Hive no Azure HDInsight | Microsoft Docs"
description: Saiba como criar clusters do HDInsight e consultar dados com o Hive.
keywords: "guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: 11c0a583ab671b27a5c5a65c4bb36032a8582466
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2018
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Tutorial do Hadoop: Introdução ao Hadoop no HDInsight

Saiba como criar clusters do [Hadoop](http://hadoop.apache.org/) no HDInsight e como executar tarefas do Hive no HDInsight. O [Apache Hive](https://hive.apache.org/) é o componente mais popular do ecossistema Hadoop. Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes elementos:

* **Subscrição do Azure**: para criar uma conta de avaliação gratuita de um mês, aceda a [azure.microsoft.com/free](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Criar cluster

A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Nesta secção, vai criar um cluster do Hadoop no HDInsight com um [modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Não precisa de ter experiência no modelo do Resource Manager para seguir este tutorial. Para conhecer outros métodos de criação de clusters e compreender as propriedades utilizadas neste tutorial, consulte [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

O modelo do Resource Manager utilizado neste tutorial está localizado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza ou selecione os seguintes valores:

    * **Subscrição**: selecione a sua subscrição do Azure.
    * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. 
    * **Localização**: selecione a localização do Azure onde pretende criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. 
    * **Tipo de cluster**: selecione **hadoop** para este tutorial.
    * **Nome do Cluster**: introduza um nome para o cluster do Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode ser composto por um máximo de 59 carateres, incluindo letras, números e hífenes. Note que o primeiro e o último caráter do nome não podem ser hífenes.
    * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é **admin**. A palavra-passe tem de ter no mínimo 10 carateres e tem de conter, pelo menos, um número, uma letra maiúscula e uma letra minúscula, e um caráter não alfanumérico (exceto os carateres ' " `\). 
    * **Nome de utilizador e palavra-passe SSH**: o nome de utilizador predefinido é **sshuser**.  Pode mudar o nome de utilizador do SSH.  A palavra-passe de utilizador do SSH tem os mesmos requisitos que a palavra-passe de início de sessão do cluster.   
   
    ![Introdução ao modelo do Resource Manager no Portal no HDInsight com Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Implementar o cluster do Hadoop no HDInsight com o portal do Azure e um modelo do gestor do grupo de recursos").
     
    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo.
    
    Cada cluster tem uma dependência de [conta do Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou de [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). É designada de conta de armazenamento predefinida. O cluster do HDInsight e a respetiva conta do Storage predefinida devem estar colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento. 
        
    Para obter mais explicações sobre estas propriedades, veja [Criar clusters Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selecione **Concordo com os termos e condições indicados acima** e **Afixar ao dashboard** e, em seguida, clique em **Comprar**. Verá um novo mosaico intitulado **A implementar implementação de Modelo** no dashboard do portal. A criação de um cluster demora cerca de 20 minutos. 

4. Assim que o cluster for criado, a legenda do mosaico é alterada para o nome do grupo de recursos que especificou. Clique no mosaico para ver o cluster e o armazenamento predefinido para o cluster listado.
   
    ![Introdução ao grupo de recursos no HDInsight com Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Grupo de recursos de cluster no Azure HDInsight")

4. Clique no nome do cluster para abrir o cluster.

   ![Introdução às definições do cluster no HDInsight com Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Propriedades do cluster no HDInsight")


## <a name="run-hive-queries"></a>Executar consultas do Hive

O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste tutorial, vai utilizar a vista Ambari Hive do portal. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

1. Para abrir o Ambari, a partir da captura de ecrã anterior, clique em **Dashboard do Cluster** e, em seguida, clique em **Dashboard do Cluster do HDInsight**.  Também pode procurar em **https://&lt;ClusterName>.azurehdinsight.net**, onde &lt;ClusterName> é o cluster que criou na secção anterior.

2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou ao criar o cluster. O nome de utilizador predefinido é **admin**.

3. Abra a **Vista do Hive** conforme mostrado na captura de ecrã seguinte:
   
    ![Selecionar vistas Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu do Ambari Hive Viewer")

4. No separador **CONSULTAS**, cole as seguintes declarações HiveQL na folha de cálculo:
   
        SHOW TABLES;

    ![Vistas do Hive do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor de Consulta do Hive View do HDInsight")
   
   > [!NOTE]
   > O Hive requer a utilização do ponto e vírgula.       
   > 
   > 

5. Clique em **Executar**. O separador **RESULTADOS** aparece por baixo do separador **CONSULTA** e apresenta informações sobre a tarefa. 
   
    Assim que a consulta estiver concluída, o separador **CONSULTA** apresenta os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.
   
    ![Vistas do Hive do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor de Consulta do Hive View do HDInsight")

6. Repita os passos 4 e 5 para executar a seguinte consulta:
   
        SELECT * FROM hivesampletable;
   
7. Também pode guardar os resultados da consulta. Clique no botão de menu à direita e especifique se pretende transferir os resultados como um ficheiro CSV ou armazená-lo na conta de armazenamento associada ao cluster.

    ![Guardar o resultado da consulta do Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Guardar o resultado da consulta do Hive")



Depois de concluir uma tarefa deo Hive, pode [exportar os resultados para a SQL Database do Azure ou a base de dados do SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Do mesmo modo, pode [visualizar os resultados com o Excel](apache-hadoop-connect-excel-power-query.md). Para obter mais informações sobre como utilizar o Hive no HDInsight, consulte [Utilizar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um ficheiro Apache log4j de exemplo](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Limpar o tutorial
Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. 

> [!NOTE]
> Com o [Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md), pode criar clusters do HDInsight a pedido e configurar uma definição de TimeToLive para eliminar os clusters automaticamente. 
> 
> 

**Para eliminar o cluster e/ou a conta de armazenamento predefinida**

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Pode clicar em **Eliminar** se apenas pretender eliminar o cluster, mas quiser manter a conta de armazenamento predefinida:

    ![Eliminação de cluster do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminar cluster do HDInsight")
1. À direita, clique no nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.
2. Clique em **Eliminar grupo de recursos** para eliminar o grupo de recursos que contém o cluster e a conta de Armazenamento predefinida. Em alternativa, clique no nome do cluster no mosaico **Recursos** e, em seguida, clique em **Eliminar**. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um cluster do HDInsight baseado em Linux com um modelo do Resource Manager, bem como a executar consultas básicas do Hive.

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como utilizar o Hive com o HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, veja [Use Hive with HDInsight](hdinsight-use-hive.md) (Utilizar o Hive com o HDInsight).
* Para saber mais sobre o Pig, uma linguagem utilizada para transformar dados, veja [Use Pig with HDInsight](hdinsight-use-pig.md) (Utilizar o Pig com o HDInsight).
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, veja [Use MapReduce with HDInsight](hdinsight-use-mapreduce.md) (Utilizar o MapReduce com o HDInsight).
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md).

Se estiver pronto para começar a trabalhar com os seus próprios dados e precisar de saber mais sobre a forma como o HDInsight armazena os dados ou como carregar dados para o HDInsight, consulte os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).

Se gostaria de saber mais sobre como criar ou gerir um cluster do HDInsight, consulte os seguintes artigos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, consulte [Gerir clusters do HDInsight com o Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md).
* Se estiver familiarizado com o Linux e o Hadoop, mas pretender obter informações específicas sobre o Hadoop no HDInsight, consulte [Trabalhar com o HDInsight no Linux](../hdinsight-hadoop-linux-information.md). Este artigo disponibiliza informações como:
  
  * URLs para serviços alojados no cluster, como Ambari e WebHCat
  * A localização de ficheiros do Hadoop e exemplos no sistema de ficheiros local
  * A utilização do Storage do Azure (WASB) em vez do HDFS como o arquivo de dados predefinido

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


