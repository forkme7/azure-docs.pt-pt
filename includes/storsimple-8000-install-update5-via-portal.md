<!--author=alkohli last changed: 08/04/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização a partir do portal do Azure

1. Na página do serviço StorSimple, selecione o seu dispositivo.

    ![Selecione o dispositivo](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Navegue para **definições do dispositivo** > **atualizações ao dispositivo**.

    ![Clique em atualizações ao dispositivo](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. É apresentada uma notificação se estiverem disponíveis novas atualizações. Em alternativa, no **atualizações ao dispositivo** painel, clique em **procurar atualizações**. É criada uma tarefa para procurar as atualizações disponíveis. É notificado quando a tarefa for concluída com êxito.

    ![Clique em atualizações ao dispositivo](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Recomendamos que reveja as notas de versão antes de aplicar uma atualização ao seu dispositivo. Para aplicar atualizações, clique em **instalar atualizações**. No **confirmar atualizações regulares** painel, reveja os pré-requisitos para concluir antes de aplicar as atualizações. Selecione a caixa de verificação para indicar que está pronto para atualizar o dispositivo e, em seguida, clique em **instalar**.

    ![Clique em atualizações ao dispositivo](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Um conjunto de verificações de pré-requisitos é iniciado. Estas verificações incluem:
   
   * **Verificações do estado de funcionamento do controlador** para verificar se os dois controladores do dispositivo estão em bom estado de funcionamento e online.
   * **Verificações do estado de funcionamento do componente de hardware** para verificar se todos os componentes de hardware do seu dispositivo StorSimple estão em bom estado de funcionamento.
   * **Verificações de DATA 0** para verificar se DATA 0 está ativado no seu dispositivo. Se esta interface não estiver ativada, terá de ativá-la e, em seguida, tentar novamente.

    A atualização é transferida e instalada apenas se a todas as verificações forem concluídas com êxito. Será notificado quando as verificações estiverem em curso. Se o prechecks falharem, em seguida, irá ser fornecido com os motivos da falha. Resolva os problemas e, em seguida, repita a operação. Poderá ter de contactar o Suporte da Microsoft se não conseguir resolver estes problemas.

7. Depois dos prechecks forem concluídos com êxito, é criada uma tarefa de atualização. Será notificado quando a tarefa de atualização for criada com êxito.
   
    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    A atualização é então aplicada ao seu dispositivo.

9. A atualização demora algumas horas a ser concluída. Selecione a tarefa de atualização e clique em **Detalhes** para ver os detalhes da tarefa em qualquer altura.

    ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Também pode monitorizar o progresso da tarefa de atualização de **definições do dispositivo > tarefas**. No **tarefas** painel, pode ver o progresso da atualização.

     ![Criação de tarefa de atualização](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Depois da tarefa estiver concluída, navegue para o **definições do dispositivo > atualizações ao dispositivo**. A versão do software agora deve ser atualizada.

