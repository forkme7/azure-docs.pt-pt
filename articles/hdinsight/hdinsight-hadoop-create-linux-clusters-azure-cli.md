---
title: Criar clusters do Hadoop através da linha de comandos-Azure HDInsight | Microsoft Docs
description: Saiba como criar clusters do HDInsight utilizando a CLI do Azure de várias plataformas 1.0.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: e56829c771ae47933f79c519920a20c1308873fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar clusters do HDInsight utilizando a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Os passos destas instruções de documento, criar um cluster de HDInsight 3.5 utilizando a CLI do Azure 1.0.

> [!IMPORTANT]
> Este tópico descreve como utilizar a CLI do Azure 1.0 para criar um cluster do HDInsight. Esta versão da CLI do foi preterido e não foi adicionado suporte para a criação de clusters do HDInsight para o Azure CLI 2.0.
>
> Também pode utilizar o Azure PowerShell para criar e gerir clusters do HDInsight. Para obter mais informações, consulte o [criar clusters do HDInsight com o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) documento.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **CLI do Azure**. Os passos neste documento pela última vez foram testados com a CLI do Azure versão 0.10.14.

    > [!IMPORTANT]
    > Azure CLI 1.0 foi preterido e não foi adicionado suporte para a criação de clusters do HDInsight para o Azure CLI 2.0.

## <a name="log-in-to-your-azure-subscription"></a>Inicie sessão na subscrição do Azure

Siga os passos documentados em [Ligar a uma subscrição do Azure a partir da Interface de Linha de Comandos do Azure (CLI do Azure)](/cli/azure/authenticate-azure-cli) e ligue à sua subscrição utilizando o método de **início de sessão**.

## <a name="create-a-cluster"></a>Criar um cluster

Os seguintes passos devem ser efetuados numa linha de comandos, como o PowerShell ou Bash.

1. Utilize o seguinte comando para autenticar a sua subscrição do Azure:

        azure login

    Lhe for pedido para fornecer o nome e a palavra-passe. Se tiver várias subscrições do Azure, utilize `azure account set <subscriptionname>` para configurar a subscrição que utilizam os comandos da CLI do Azure.

2. Mude para o modo Azure Resource Manager utilizando o seguinte comando:

        azure config mode arm

3. Crie um grupo de recursos. Este grupo de recursos contém o cluster do HDInsight e associado à conta de armazenamento.

        azure group create groupname location

    * Substitua `groupname` com um nome exclusivo para o grupo.

    * Substitua `location` com a região geográfica que pretende criar o grupo no.

       Para obter uma lista de localizações válidas, utilize o `azure location list` comando e, em seguida, utilize uma das localizações do `Name` coluna.

4. Criar uma conta de armazenamento. Esta conta de armazenamento é utilizada como armazenamento de predefinido para o cluster do HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Substitua `groupname` com o nome do grupo criado no passo anterior.

    * Substitua `location` com a mesma localização que utilizou no passo anterior.

    * Substitua `storagename` com um nome exclusivo para a conta de armazenamento.

        > [!NOTE]
        > Para obter mais informações sobre os parâmetros utilizados neste comando, utilize `azure storage account create -h` para ver a ajuda para este comando.

5. Obter a chave utilizada para aceder à conta de armazenamento.

        azure storage account keys list -g groupname storagename

    * Substitua `groupname` com o nome do grupo de recursos.
    * Substitua `storagename` com o nome da conta de armazenamento.

     Os dados que são devolvidos, guarde o `key` valor para `key1`.

6. Crie um cluster do HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua `groupname` com o nome do grupo de recursos.

    * Substitua `Hadoop` com o tipo de cluster que pretende criar. Por exemplo, `Hadoop`, `HBase`, `Kafka`, `Spark`, ou `Storm`.

     > [!IMPORTANT]
     > HDInsight clusters são fornecidos em vários tipos que correspondam à carga de trabalho ou tecnologia que o cluster está otimizado para. Não há nenhum método suportado para criar um cluster que combina vários tipos, por exemplo, Storm e HBase num cluster.

    * Substitua `location` com a mesma localização utilizada nos passos anteriores.

    * Substitua `storagename` com o nome de conta de armazenamento.

    * Substitua `storagekey` com a chave que obteve no passo anterior.

    * Para o `--defaultStorageContainer` parâmetro, utilizar o mesmo nome estiver a utilizar para o cluster.

    * Substitua `admin` e `httppassword` com o nome e a palavra-passe que pretende utilizar quando aceder ao cluster através de HTTPS.

    * Substitua `sshuser` e `sshuserpassword` com o nome de utilizador e palavra-passe que pretende utilizar quando aceder ao cluster através de SSH

    > [!IMPORTANT]
    > Este exemplo cria um cluster de dois nós de trabalho. Também pode alterar o número de nós de trabalho após a criação do cluster por efetuar operações de dimensionamento. Se planeia utilizar mais do que 32 nós de trabalho, tem de selecionar um tamanho de nó principal com pelo menos 8 núcleos e 14 GB de RAM. Pode definir o tamanho de nó principal utilizando o `--headNodeSize` parâmetro durante a criação do cluster.
    >
    > Para obter mais informações sobre os tamanhos de nós e os custos associados, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Pode demorar alguns minutos para que o processo de criação do cluster concluir. Normalmente, cerca de 15.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos Seguintes

Agora que criou com êxito um cluster do HDInsight utilizando a CLI do Azure, utilize o seguinte para saber como trabalhar com o cluster:

### <a name="hadoop-clusters"></a>Clusters do Hadoop

* [Utilizar o Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters de HBase

* [Introdução ao HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para o HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters de Storm

* [Desenvolver topologias de Java de Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilize os componentes de Python Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar as topologias Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
