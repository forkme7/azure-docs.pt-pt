---
title: Copiar dados do SQL Server para o Armazenamento de Blobs com o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de um arquivo de dados no local para a cloud mediante a utilização de um runtime de integração autoalojado no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: e21c08d418022430400ff14baedc1759d2d16069
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copiar dados de uma base de dados do SQL Server no local para o armazenamento de Blobs do Azure
Neste tutorial, vai utilizar a interface de utilizador (IU) do Azure Data Factory para criar um pipeline de fábrica de dados que copia dados de uma base de dados do SQL Server no local para o armazenamento de Blobs do Azure. Vai criar e utilizar um runtime de integração autoalojado, que move dados entre arquivos de dados no local e na cloud.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Azure Data Factory, que está atualmente em pré-visualização. Se utiliza a versão 1 do Data Factory, que está disponível em geral, veja [Documentação da versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Este artigo não disponibiliza uma introdução detalhada do Data Factory. Para obter mais informações, veja [Introdução ao Data Factory](introduction.md). 

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure. 
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Subscrição do Azure
Antes de começar, se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de fábricas de dados, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ter atribuída a função *contribuidor* ou *proprietário* ou ser *administradora* da subscrição do Azure. 

Para ver as permissões que tem na subscrição, aceda ao portal do Azure. No canto superior direito, selecione o nome de utilizador e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de *origem*. O pipeline da fábrica de dados que vai criar neste tutorial copia dados desta base de dados do SQL Server no local (origem) para o Armazenamento de blobs (sink). Vai criar uma tabela com o nome **emp** na sua base de dados do SQL Server e inserir algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado no seu computador, aceda a [Transferir o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Utilize as suas credenciais para se ligar à sua instância do SQL Server. 

3. Crie uma base de dados de exemplo. Na vista de árvore, clique com o botão direito do rato em **Bases de Dados** e selecione **Nova Base de Dados**. 
4. Na janela **Nova Base de Dados**, introduza um nome para a base de dados e selecione **OK**. 

5. Para criar a tabela **emp** e inserir alguns dados de exemplo na mesma, execute o script de consulta seguinte na base de dados:

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. Na vista de árvore, clique com o botão direito do rato na base de dados que criou e selecione **Nova Consulta**.

### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Neste tutorial, utiliza uma conta de armazenamento do Azure para fins gerais (mais concretamente, o Armazenamento de blobs) como arquivo de dados de destino/sink. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account). O pipeline da fábrica de dados que vai criar neste tutorial copia dados desta base de dados do SQL Server no local (origem) para o armazenamento de Blobs (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, siga os passos seguintes: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com o seu nome de utilizador e a palavra-passe do Azure. 

2. No painel esquerdo, selecione **Mais serviços**. Utilize a palavra-chave **Armazenamento** para filtrar e selecione **Contas de armazenamento**.

    ![Procurar conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Na lista de contas de armazenamento, filtre para encontrar a sua conta de armazenamento, se necessário. Em seguida, selecione a sua conta de armazenamento. 

4. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Chaves de acesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e cole-os no Bloco de notas ou noutro editor, para utilizar mais adiante no tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contentor adftutorial 
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no seu Armazenamento de blobs. 

1. Na janela **Conta de armazenamento**, vá para **Descrição Geral** e selecione **Blobs**. 

    ![Selecionar a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Na janela **Serviço de Blob**, selecione **Contentor**. 

    ![Botão de contentor](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Na janela **Novo contentor**, em **Nome**, introduza **adftutorial**. Em seguida, selecione **OK**. 

    ![Janela Novo contentor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Na lista de contentores, clique em **adftutorial**.

    ![Seleção de contentor](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenha a janela do **contentor** de **adftutorial** aberta. Vai utilizá-la para verificar o resultado no final deste tutorial. O Data Factory cria automaticamente a pasta de saída neste contentor, pelo que não precisa de a criar.

    ![Janela do contentor](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. No menu da esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**.
   
   ![Criação de nova fábrica de dados](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**. 
      
     ![Página Nova fábrica de dados](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   O nome da fábrica de dados tem de ser *globalmente exclusivo*. Se vir a seguinte mensagem de erro no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
  
   ![Nome da nova fábrica de dados](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
4. Selecione a **subscrição** do Azure na qual quer criar a fábrica de dados.
5. Em **Grupo de Recursos**, efetue um destes passos:
     
      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).
6. Em **Versão**, selecione **V2 (Pré-visualização)**.
7. Em **Localização**, selecione a localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que o Data Factory utiliza podem estar noutras regiões.
8. Selecione **Afixar ao dashboard**. 
9. Selecione **Criar**.
10. No dashboard, é apresentado o mosaico seguinte, com o estado **A Implementar o Data Factory**:

    ![Mosaico A Implementar o Data Factory](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
11. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem:
   
    ![Home page da fábrica de dados](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
12. Selecione o mosaico **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte. 


## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página **Vamos começar**, selecione **Criar pipeline**. É criado um pipeline automaticamente por si. Verá o pipeline na vista de árvore e o respetivo editor aberto. 

   ![Página Vamos começar](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. No separador **Geral**, na parte inferior da janela **Propriedades**, em **Nome**, introduza **SQLServerToBlobPipeline**.

   ![Nome do pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
3. Na caixa de ferramentas **Atividades**, expanda **DataFlow**. Arraste e largue a atividade **Copiar** para a superfície de desenho do pipeline. Defina o nome da atividade como **CopySqlServerToAzureBlobActivity**.

   ![Nome da atividade](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
4. Na janela **Propriedades**, vá para o separador **Origem** e selecione **+ Novo**.

   ![Separador Origem](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
5. Na janela **Novo Conjunto de Dados**, procure **SQL Server**. Selecione **SQL Server** e, em seguida, selecione **Concluir**. Verá um separador novo com o nome **SqlServerTable1**. Verá também o conjunto de dados **SqlServerTable1** na vista de árvore, do lado esquerdo. 

   ![Seleção do SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
6. No separador **Geral**, na parte inferior da janela **Propriedades**, em **Nome**, introduza **SqlServerDataset**.
    
   ![Nome do conjunto de dados de origem](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
7. Vá para o separador **Ligação** e selecione **+ Novo**. Neste passo, vai criar uma ligação para o arquivo de dados de origem (base de dados do SQL Server). 

   ![Conjunto de dados de ligação – origem](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
8. Na janela **Novo Serviço Ligado**, selecione **Novo Runtime de Integração**. Nesta secção, vai criar um integration runtime autoalojado e vai associá-lo a um computador no local com a base de dados do SQL Server. O runtime de integração autoalojado é o componente que copia os dados da base de dados do SQL Server no seu computador para o armazenamento de Blobs. 

   ![Novo runtime de integração](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
9. Na janela **Configuração do Runtime de Integração**, selecione **Rede Privada** e **Seguinte**. 

   ![Seleção de rede privada](./media/tutorial-hybrid-copy-portal/select-private-network.png)
10. Introduza um nome para o runtime de integração e selecione **Seguinte**.
    
    ![Nome do runtime de integração](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
11. Em **Opção 1: configuração rápida**, selecione **Clique aqui para iniciar a configuração rápida neste computador**. 

    ![Ligação de configuração rápida](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
12. Na janela **Configuração Rápida do Runtime de Integração (Autoalojado)**, selecione **Fechar**. 

    ![Configuração rápida do runtime de integração (autoalojado)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
13. No browser, na janela **Configuração do Runtime de Integração**, selecione **Concluir**. 

    ![Configuração do runtime de integração](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
14. Na janela **Novo Serviço Ligado**, siga os passos seguintes:

    a. Em **Nome**, introduza **SqlServerLinkedService**.

    b. Em **Ligar através do runtime de integração**, confirme que aparece o runtime de integração autoalojado que criou anteriormente.

    c. Em **Nome do servidor**, introduza o nome da instância do SQL Server. 

    d. Em **Nome da base de dados**, introduza o nome da base de dados com a tabela **emp**.

    e. Em **Tipo de autenticação**, selecione o tipo de autenticação adequado que o Data Factory deverá utilizar para se ligar à sua base de dados do SQL Server.

    f. Em **Nome de utilizador** e **Palavra-passe**, introduza o nome de utilizador e a palavra-passe. Se precisar de utilizar um caráter de barra invertida (\\) no nome da sua conta de utilizador ou no nome do seu servidor, utilize o caráter de escape (\\) como prefixo. Por exemplo, utilize *omeudominion\\\\omeuutilizador*.

    g. Selecione **Testar ligação**. Execute este passo para confirmar que o Data Factory se consegue ligar à sua base de dados do SQL Server com o runtime de integração autoalojado que criou.

    h. Para guardar o serviço ligado, selecione **Guardar**.

       ![Definições do novo serviço ligado](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
15. Deverá regressar à janela com o conjunto de dados de origem aberto. No separador **Ligação** da janela **Propriedades**, siga os passos abaixo: 

    a. Em **Serviço ligado**, confirme que vê **SqlServerLinkedService**.

    b. Em **Tabela**, selecione **[dbo].[emp]**.

    ![Informações da ligação do conjunto de dados de origem](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
16. Vá para o separador com **SQLServerToBlobPipeline** ou selecione **SQLServerToBlobPipeline** na vista de árvore. 

    ![Separador do pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
17. Vá para o separador **Sink**, na parte inferior da janela **Propriedades**, e selecione **+ Novo**. 

    ![Separador Sink](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
18. Na janela **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure**. Em seguida, selecione **Concluir**. Verá um novo separador aberto para o conjunto de dados. Também verá o conjunto de dados na vista de árvore. 

    ![Seleção do Armazenamento de blobs](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
19. Em **Nome**, introduza **AzureBlobDataset**.

    ![Nome do conjunto de dados de sink](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
20. Vá para o separador **Ligação** na parte inferior da janela **Propriedades**. Junto a **Serviço ligado**, selecione **+ Novo**. 

    ![Botão Novo serviço ligado](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
21. Na janela **Novo Serviço Ligado**, siga os passos seguintes:

    a. Em **Nome**, introduza **AzureStorageLinkedService**.

    b. Em **Nome da conta de armazenamento**, selecione a conta de armazenamento.

    c. Para testar a ligação à conta de armazenamento, selecione **Testar ligação**.

    d. Selecione **Guardar**.

    ![Definições do serviço ligado do armazenamento](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
22.  Deverá regressar à janela com o conjunto de dados de sink aberto. No separador **Ligação**, siga os passos abaixo: 

        a. Em **Serviço ligado**, confirme que **AzureStorageLinkedService** está selecionado.

        b. Na parte **folder** do **Caminho do ficheiro**, introduza **adftutorial/fromonprem**. Se a pasta de saída não existir no contentor adftutorial, o Data Factory cria-a automaticamente.

        c. Para a parte **file name** do **Caminho do ficheiro**, introduza `@CONCAT(pipeline().RunId, '.txt')`.

     ![Conjunto de dados de ligação – sink](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
23. Vá para o separador com o pipeline aberto ou selecione o pipeline na vista de árvore. Em **Conjunto de Dados de Sink**, confirme que **AzureBlobDataset** está selecionado. 

    ![Conjunto de dados de sink selecionado](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
24. Para validar as definições do pipeline, selecione **Validar** na barra de ferramentas do mesmo. Para fechar o **Relatório de Validação do Pipeline**, selecione **Fechar**. 

    ![Validar o pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
25. Para publicar as entidades que criou no Data Factory, selecione **Publicar Tudo**.

    ![Botão Publicar](./media/tutorial-hybrid-copy-portal/publish-button.png)
26. Aguarde até ver a mensagem **Publicação com êxito**. Para ver o estado da publicação, selecione a ligação **Mostrar Notificações**, no lado esquerdo. Para fechar a janela de notificação, selecione **Fechar**. 

    ![Publicação com êxito](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline
Selecione **Acionar** na barra de ferramentas do pipeline e, em seguida, selecione **Acionar Agora**.

![Acionar a execução do pipeline](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Vá para o separador **Monitorizar**. Verá o pipeline que acionou manualmente no passo anterior. 

    ![Monitorizar execuções de pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Vai ver apenas execuções de atividades, porque só existe uma atividade no pipeline. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna **Ações**. Para regressar à vista **Execuções do Pipeline**, selecione **Pipelines** na parte superior.

    ![Monitorização de execuções de atividade](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída com o nome *fromonprem* no contentor de blobs `adftutorial`. Confirme que consegue ver o ficheiro *dbo.emp.txt* na pasta de saída. 

1. No portal do Azure, na janela do contentor **adftutorial**, selecione **Atualizar** para ver a pasta de saída.

    ![Pasta de saída criada](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Selecione `fromonprem` na lista de pastas. 
3. Confirme que vê um ficheiro com o nome `dbo.emp.txt`.

    ![Ficheiro de saída](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização no amazenamento de Blobs. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento. 
> * Criar conjuntos de dados do SQL Server e do armazenamento de Blobs.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Para obter uma lista dos arquivos de dados que o Data Factory suporta, veja [Arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber como copiar dados em massa de uma origem para um destino, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
