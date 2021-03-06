---
title: Criar, ver e gerir alertas utilizando o Monitor do Azure | Microsoft Docs
description: Utilize a nova experiência de unificada alertas do Azure para criar, ver e gerir a métrica e regras de alerta de registo a partir de um local.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: fdb3ebe3820191a642c4503851b04dd5fc5e6048
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Criar, ver e gerir alertas utilizando o Monitor do Azure  

## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar alertas através da nova interface de alertas no interior do portal do Azure. Definição de uma regra de alerta está a ser três partes:
- Destino: Específico recursos do Azure, que está a ser monitorizado
- Critérios de: Condição específica ou lógica que quando é apresentada no sinal, deve acionar ação
- Ação: Chamada específica enviada para um recetor de uma notificação - por e-mail, SMS, webhook etc.

Alertas do Azure também fornece uma vista unificada para todas as suas regras de alertas e capacidade para geri-los de um único local; incluindo a visualização de todos os alertas não resolvidos. Saiba mais sobre as funcionalidades da [alertas do Azure – descrição geral](monitoring-overview-unified-alerts.md).

Alerta utiliza o termo **alertas de registo** para descrever alertas em que o sinal é consulta personalizada em [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Application Insights](../application-insights/app-insights-analytics.md). O [nova capacidade de alerta métrica](monitoring-near-real-time-metric-alerts.md) fornece a capacidade de alerta no [métricas multidimensionais](monitoring-metric-charts.md) para recursos do Azure específicos. Os alertas para esses recursos podem utilizar filtros adicionais em dimensões criar **alertas de métrica de acordo com**.


> [!NOTE]
> Ao alertas do Azure oferece uma experiência de novo e melhorada para a criação de alertas no Azure. Existente [alertas (clássica)](monitoring-overview-alerts.md) experiência permanece utilizável.
>

Detalhadas seguinte é um guia passo a passo para utilizar alertas do Azure.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Criar uma regra de alerta com o portal do Azure
1. No [portal](https://portal.azure.com/), selecione **Monitor** e na secção MONITOR - escolha **alertas**.  
    ![Monitorização](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Selecione o **nova regra de alerta** botão para criar um novo alerta no Azure.
    ![Adicionar o alerta](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. A secção de criar o alerta é apresentada com três partes consiste em: *definir a condição de alerta*, *definir os detalhes do alerta*, e *grupo de ação de definir*.

    ![Criar regra](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definir a condição de alerta utilizando o **selecionar recursos** ligação e a especificação do destino, selecionando um recurso. Filtrar por escolher o *subscrição* , *tipo de recurso* , e, finalmente, selecionar necessário *recursos*.

    >[!NOTE]

    > Antes de continuar, certifique-se a sinais disponíveis para o recurso selecionado.

    ![Selecionar o recurso](./media/monitor-alerts-unified/Alert-SelectResource.png)

 A interface de utilizador permite-lhe criar vários tipos de alertas a partir de um local. Com base no tipo de alerta assim o desejar escolher o passo seguinte como:

    - Para **métrica alertas**: Siga os passos 5 a 7; em seguida, vá diretamente para o passo 11
    - Para **alertas de registo**, avance para o passo 8.

    > [!NOTE]

    > Alertas de registo de atividade são também suportadas, mas estão em pré-visualização. [Saiba mais](monitoring-activity-log-alerts-new-experience.md).

5. *Alertas métricas* : Certifique-se **tipo de recurso** está selecionada com o tipo de sinal como **métrica**, em seguida, uma vez adequado **recursos** é escolhido clique  *Feito* botão para devolver o alerta de criar. Em seguida utilize o **adicionar critérios** botão para escolher o sinal específico da lista de opções de sinal, respetivo serviço de monitorização e o tipo listados - que estão disponíveis para o recurso selecionado anteriormente.

    ![Selecione um recurso](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Todos os [quase em tempo real alertas](monitoring-near-real-time-metric-alerts.md) com capacidade de recursos estão listados com o serviço de monitor **plataforma** e assinalar o tipo como **métrica**

6. *Alertas métricas*: após o sinal estiver selecionada, pode ser indicada lógica para alertas. Para referência, os dados históricos de sinal são apresentados com a opção para otimizar a janela de tempo utilizando **Mostrar histórico**, variando a partir do último seis horas a última semana. Com a visualização no local, **lógica alerta** podem ser selecionadas das opções apresentadas da condição, agregação e, finalmente, limiar. Como pré-visualização da lógica de fornecido, a condição é mostrada na visualização juntamente com o histórico de sinal, para indicar quando seria ter acionado o alerta. Especifique, finalmente, para que duração de tempo, alerta deve procurar a condição especificada, seleccionando o **período** opção juntamente com a frequência de alerta deve executar selecionando **frequência**.

    ![Configurar o sinal lógica para a métrica](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alertas métricas*: sinal se é uma métrica é, em seguida, pode ser filtrada alerta janela utilizando vários pontos de dados ou dimensões para o referida recursos do Azure. 

    a. Escolha uma duração do **Mostrar histórico** pendente para visualizar um período de tempo diferente. Pode escolher dimensões para as métricas suportadas filtrar uma série de tempo; Escolher dimensões é opcional e podem ser utilizadas se a cinco dimensões. 

    b. **Alerta lógica** podem ser selecionados a partir apresentadas opções de *condição*, *agregação* e *limiar*. Como pré-visualização da lógica de fornecido, a condição é mostrada na visualização juntamente com o histórico de sinal, para indicar quando o alerta seria ter sido acionado no passado. 

    c. Para especificar a duração de tempo escolhido **período** juntamente com a frequência deve executar selecionando alerta **frequência**.

    ![Configurar o sinal lógica para a métrica multidimensional](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Registar alertas*: Certifique-se **tipo de recurso** é uma origem de análise como *Log Analytics* ou *Application Insights* e assinalar o tipo como **registo** , em seguida, uma vez adequado **recursos** é escolhido, clique em *feito*. Em seguida utilize o **adicionar critérios** botão para ver a lista das opções de sinal disponíveis para o recurso e a lista de sinal **pesquisa de registo personalizado** opção para monitor de serviço como o registo de escolhida *registo Análise de* ou *Application Insights*.

   ![Selecione um recurso - pesquisa de registo personalizado](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Alertas apresenta uma lista pode importar a consulta de análise como tipo de sinal - **registo (consulta guardada)**, como mostrado na acima ilustração. Para que os utilizadores possam perfect sua consulta na análise e, em seguida, guarde-as para utilização futura alertas - mais detalhes sobre como utilizar a gravação de consulta disponíveis em [utilizando a pesquisa de registo na análise de registos](../log-analytics/log-analytics-log-searches.md) ou [consulta partilhada no application insights análise de](../log-analytics/log-analytics-overview.md). 

9.  *Registar alertas*: depois de selecionada, a consulta para alertar pode ser indicada na **consulta de pesquisa** campo; se a sintaxe de consulta está incorreta, o campo apresenta o erro vermelho. Se a sintaxe da consulta está correta – para referência dados históricos da consulta declarado são mostrados como um gráfico com a opção para otimizar a janela de tempo da última seis horas da última semana.

 ![Configurar a regra de alerta](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Visualização de dados históricos só pode ser apresentada se os resultados da consulta tem detalhes do tempo. Se a consulta resulta em dados resumidos ou valores de coluna específico - mesmo é apresentado como um único do desenho.

    >  Para o tipo de medição de métrica de alertas de registo de utilizar o Application insights, pode especificar que variável específico para agrupar os dados utilizando o **agregado no** opção; conforme ilustrado nas abaixo:

    ![opção de agregação](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Registar alertas*: com a visualização no local, **lógica alerta** podem ser selecionadas das opções apresentadas da condição, agregação e, finalmente, limiar. Por fim, especificar na lógica, o tempo para avaliar a condição especificada, utilizando **período** opção. Juntamente com a frequência de alerta deve executar selecionando **frequência**.
Para **alertas de registo** alertas podem ser baseados em:
   - *Número de registos*: é criado um alerta se a contagem de registos devolvidos pela consulta for maior ou menor do que o valor fornecido.
   - *Medida de métrica*: é criado um alerta se cada *agregar valor* nos resultados da excede o valor de limiar fornecido e é *agrupados por* escolhido valor. O número de falhas de um alerta é o número de vezes que o limiar for excedido no período de tempo escolhido. Pode especificar falhas Total para qualquer combinação de falhas entre o conjunto de resultados ou falhas consecutivas para exigir que as falhas tem de ocorrer amostras consecutivas. Saiba mais sobre [alertas de registo e os respetivos tipos](monitor-alerts-unified-log.md).

    > [!TIP]
    > Atualmente numa alertas - alertas de pesquisa de registo poderá demorar personalizados *período* e *frequência* valor num minuto (s). Valores podem variar entre 5 minutos e 1440 minutos (ou seja) 24 horas. Por isso, se pretender que o período de alerta para ser diga a três horas, convertê-la em minutos - 180 minutos, antes de utilização

11. Como o segundo passo, defina um nome para o alerta no **nome da regra de alerta** campo juntamente com um **Descrição** com detalhes sobre informações específicas para o alerta e **gravidade** valor a partir do opções fornecidas. Estes detalhes são reutilizadas em todos os e-mails de alerta, as notificações ou push efetuada pelo Monitor do Azure. Além disso, o utilizador pode optar por ativar imediatamente na criação de regra de alerta por adequadamente ativando ou desativando **ativar regra após a criação** opção.

    Para **alertas de registo** apenas algumas funcionalidades adicionais estão disponíveis nos detalhes do alerta:

    - **Suprimir os alertas**: ao ativar a supressão para a regra de alerta, ações para a regra estão desativadas por um período de tempo depois de criar um novo alerta definido. A regra está ainda em execução e cria registos de alerta fornecido critérios forem satisfeitos. Permitir que a hora para corrigir o problema sem executar ações duplicadas.

        ![Suprimir os alertas para os alertas de registo](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Como passo final e terceiro, especifique se qualquer **ação grupo** tem de ser acionado para a regra de alerta quando for cumprida a condição de alerta. Pode escolher qualquer grupo de ação existente com o alerta ou criar um novo grupo de ação. Em conformidade com selecionado ação grupo, quando o alerta é o acionador do Azure será: enviar email(s), Enviar SMS(s), chamar Webhook(s), remediar através de Runbooks do Azure, enviar por push para a ferramenta ITSM, etc. Saiba mais sobre [ação grupos](monitoring-action-groups.md).

    Para **alertas de registo** algumas funcionalidades adicionais estão disponíveis para substituir as ações predefinidas:

    - **Notificação por e-mail**: substitui *assunto do correio electrónico* no e-mail de enviada através do grupo de ação; se a ação de e-mail de um ou mais existe no grupo de ação de referida. Não é possível modificar o corpo da mensagem e este campo é **não** para endereço de correio eletrónico.
    - **Incluir payload Json personalizado**: substitui o webhook JSON utilizado por grupos de ação; se existir um ou mais ação de webhook no grupo de ação referida. O utilizador pode specificy formato JSON para ser utilizado para todos os webhooks configurados num grupo de ação associadas; Para obter mais informações sobre os formatos de webhook, consulte [ação do webhook para alertas de registo](monitor-alerts-unified-log-webhook.md). Opção de Webhook de teste é fornecida para verificar o formato e processamento pelo destino utilizando o JSON de exemplo e esta opção como assinaladas como destinam-se apenas **testar** fins.

        ![Substituições de ação para alertas de registo](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Se todos os campos são válidos e com marcas de escala verde a **criar regra de alerta** pode clicar no botão e é criado um alerta no Monitor do Azure - alertas. Os Dashboard de alertas podem ser visualizados todos os alertas.

    ![Criação de regra](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Dentro de alguns minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="view-your-alerts-in-azure-portal"></a>Ver os alertas no portal do Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e na secção MONITOR - escolha **alertas**.  

2. O **alertas Dashboard** é apresentado - wherein todos os alertas do Azure são unified e apresentados num painel único ![Dashboard alerta](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Da parte superior esquerda para a direita, o Dashboard mostra rapidamente, o seguinte -, que pode ser clicado para ver uma lista detalhada:
    - *É desencadeado alertas*: O número de alertas atualmente, tiver cumprido lógica e no desencadeou o Estado
    - *Total de regras de alerta*: O número de regras de alerta criadas e na subtext, o número que estão atualmente ativadas
4. Uma lista de todos os alertas fired é apresentada que o utilizador pode clicar para ver os detalhes
5. Aiding no específicos a localizar os alertas; um pode utilizar as opções de lista pendente na parte superior para filtrar específico *subscrição, grupo de recursos e/ou recurso*. Adicional para qualquer não pode estar resolvido de alertas, um utilize o *alerta filtro* opção para encontrar para fornecido palavra-chave - alertas correspondentes específicos com *nome, alerta critérios, grupo de recursos e o recurso de destino*

## <a name="managing-your-alerts-in-azure-portal"></a>Gerir os alertas no portal do Azure
1. No [portal](https://portal.azure.com/), selecione **Monitor** e na secção MONITOR - escolha **alertas**.  
2. Selecione o **gerir regras** botão na barra superior, para navegar para a secção de gestão de regra - onde estão apresentadas todas as regras de alerta criadas; incluindo alertas que foram desativados.
3. Para localizar de regras de alerta específicas, um pode utilizar os filtros de lista pendente na parte superior, que permitem às regras de alerta shortlist específicos *subscrição, grupos de recursos e/ou recurso*. Em alternativa utilizando a pesquisa painel acima a lista de regra de alerta marcado *filtrar alertas*, um pode fornecer a palavra-chave, o que é comparado com *nome de alerta, a condição e o recurso de destino*; para mostrar apenas as regras de correspondência.
   ![Alerta de gerir as regras](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Para ver ou modificar a regra de alerta específica, clique no respetivo nome que será mostrado como uma ligação clicável.
5. Alerta definida é mostrada - na estrutura de três fase do: grupo de 3) ação de detalhado de 2) o alerta de condição de alerta de 1). **Critérios de destino** podem ser clicados para modificar a lógica de alerta ou uma nova critérios podem ser adicionados depois de utilizar o ícone de reciclagem para eliminar a lógica anterior. Da mesma forma, na secção de detalhes do alerta - **Descrição** e **gravidade** pode ser modificado. E o grupo de ação pode ser alterado ou um novo pode ser crafted para ligar para o alerta utilizando o **novo grupo de ação** botão.

   ![Modificar a regra de alerta](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Utilizar o painel superior, as alterações do alerta podem ser incluindo reflective: **guardar** para consolidar as alterações efetuadas para o alerta, **rejeitar** para voltar atrás sem consolidar as alterações efetuadas ao alerta, **desativar**  para desativar o alerta - após o qual este já não é executado ou aciona qualquer ação. E por fim, **eliminar** para remover permanentemente a regra de alerta toda a partir do Azure.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as novas [quase em tempo real alertas métricas](monitoring-near-real-time-metric-alerts.md)
- Obter um [descrição geral da coleção de métricas](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e reativa.
- Saiba mais sobre [registar alertas nos alertas do Azure](monitor-alerts-unified-log.md)
- [Saiba mais sobre alertas de registo de atividade numa experiência de alertas (pré-visualização)](monitoring-activity-log-alerts-new-experience.md)
