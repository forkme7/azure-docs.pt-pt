---
title: Tutorial - gerir os custos com o Azure Cost Management | Microsoft Docs
description: Neste tutorial, saiba com efetuar a gestão dos custos com relatórios de alocação, análise de custos e estorno.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 16f86eace9b5848f263e0d0772db441a123f21ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-manage-costs-by-using-azure-cost-management"></a>Tutorial: gerir os custos através do Azure Cost Management

Pode gerir os custos e produzir relatórios de análise de custos no Azure Cost Management ao alocar os custos com base em etiquetas. O processo de alocação de custos atribui os custos aos recursos de cloud consumidos. Os custos estão totalmente alocados quando todos os recursos estiverem categorizados com etiquetas. Após a alocação dos custos, pode fornecer análise de custos ou estorno aos seus utilizadores através de dashboards e relatórios. No entanto, muitos recursos podem não estar marcados quando começar a utilizar o Cost Management.

Por exemplo, pode querer ser reembolsado relativamente aos custos de engenharia. Tem de conseguir mostrar à sua equipa de engenharia que precisa de um montante específico, com base nos custos de recursos. Pode mostrar-lhes um relatório para todos os recursos consumidos marcados como *engenharia*.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar etiquetas personalizadas para alocar custos.
> * Criar relatórios de análise de custos e estorno.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma conta do Azure.
- Tem de ter um registo de avaliação ou uma subscrição paga do Azure Cost Management.

## <a name="use-custom-tags-to-allocate-costs"></a>Utilizar etiquetas personalizadas para alocar custos

Quando inicia a alocação de custos, a primeira coisa a fizer é definir o âmbito através de um modelo de custos. O modelo de custos não altera os custos, distribui-os. Quando criar um modelo de custos, segmenta os dados por entidade de custo, conta ou subscrição e por múltiplas etiquetas. Etiquetas de exemplo comuns podem incluir um código de faturação, centro de custos ou nome do grupo. As etiquetas também ajudam a efetuar a análise de custos ou o estorno para outras partes da sua organização.

Para criar um modelo de alocação de custos personalizado, selecione **Custo** &gt; **Cost Management** &gt; **Cost Allocation 360** no menu do relatório.

![Seleção do Cost Allocation 360](./media/tutorial-manage-costs/cost-allocation-360.png)

Na página **Cost Allocation 360**, selecione **Adicionar** e, em seguida, introduza um nome e uma descrição para o modelo de custos. Selecione todas as contas ou contas individuais. Se quiser utilizar contas individuais, pode selecionar múltiplas contas de vários fornecedores de serviços cloud. Em seguida, clique em **Categorização** para escolher as etiquetas detetadas que categorizam os dados de custo. Escolha as etiquetas (categorias) que quer incluir no modelo. No exemplo seguinte, a etiqueta **Unidade** está selecionada.

![Categorização do modelo de custos de exemplo](./media/tutorial-manage-costs/cost-model01.png)



O exemplo mostra que o valor 14 444 $ não está categorizado (sem etiquetas).

Em seguida, selecione **Recursos Não Categorizados** e selecione os serviços com custos não alocados. Em seguida, defina regras de alocação de custos.

Por exemplo, pode querer distribuir os custos de armazenamento do Azure igualmente para as máquinas virtuais (VMs) do Azure. Para tal, selecione o serviço **Azure/Armazenamento**, selecione **Proporcional ao Categorizado** e, em seguida, selecione **Azure/VM**. Em seguida, selecione **Criar**.

![Regra de alocação do modelo de custos de exemplo para distribuição igual](./media/tutorial-manage-costs/cost-model02.png)



Num exemplo diferente, pode querer alocar todos os custos de rede do Azure a uma unidade de negócio específica na sua organização. Para tal, selecione o serviço **Azure/Rede** e, em seguida, selecione **Distribuição Explícita**. Em seguida, defina a percentagem de distribuição para 100 e selecione a unidade de negócio **G&amp;A** na imagem seguinte:

![Regra de alocação do modelo de custos de exemplo para uma unidade de negócio específica](./media/tutorial-manage-costs/cost-model03.png)



Para todos os recursos não categorizados restantes, crie regras de alocação adicionais.

Se tiver instâncias reservadas do Amazon Web Services (AWS) não alocadas, pode atribui-las a categorias marcadas como **Instâncias Reservadas**.

Para ver informações sobre as escolhas que efetuou para alocar os custos, selecione **Resumo**. Para guardar as informações e continuar a trabalhar nas regras adicionais mais tarde, selecione **Guardar Como Rascunho**. Ou, para guardar as informações e o Cloudyn iniciar o processamento do modelo de alocação de custos, selecione **Guardar e Ativar**.

A lista de modelos de custos mostra o novo modelo de custos com **Estado de processamento**. Pode demorar algum tempo até a base de dados do Cloudyn estar atualizada com o modelo de custos. Quando o processamento estiver concluído, o estado é atualizado para **Concluído**. Em seguida, pode ver os dados do modelo de custos no relatório Análise de Custos em **Filtros Expandidos** &gt; **Modelo de Custos**.

### <a name="category-manager"></a>Category Manager

O Category Manager é uma ferramenta de limpeza de dados que o ajuda a unir os valores de múltiplas categorias (etiquetas) para criar categorias novas. É uma ferramenta simples baseada em regras onde pode selecionar uma categoria e criar regras para unir os valores existentes. Por exemplo, pode ter categorias existentes para **R&amp;D** e **dev** em que ambos representam o grupo de desenvolvimento.

No portal do Cloudyn, clique no símbolo de engrenagem na parte superior direita e selecione **Category Manager**. Para criar uma nova categoria, selecione o símbolo de adição (**+**). Introduza um nome para a categoria e, em seguida, em **Chaves**, introduza as chaves de categoria que quer incluir na nova categoria.

Quando definir uma regra, pode adicionar múltiplos valores com uma condição OU. Também pode efetuar algumas operações básicas de cadeia. Para ambos os casos, clique no símbolo de reticências (**...**) à direita da **Regra**.

Para definir uma nova regra, na área **Regras**, crie uma nova regra. Por exemplo, introduza **dev** em **Regras** e, em seguida, introduza **R&amp;D** em **Ações**. Quando tiver terminado, guarde a nova categoria.

A imagem seguinte mostra um exemplo de regras criadas para uma nova categoria designada **Carga de Trabalho**:

![Categoria de exemplo](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Relatórios e origens de etiquetas

Os dados de etiquetas que vê nos relatórios do Cloudyn têm origem em três locais:

- APIs de recursos do fornecedor de cloud
- APIs de faturação do fornecedor de cloud
- Etiquetas criadas manualmente a partir das seguintes origens:
    - Etiquetas de entidades do Cloudyn – metadados definidos pelo utilizador aplicados às entidades do Cloudyn
    - Category Manager – ferramenta de limpeza de dados que cria novas etiquetas com base em regras aplicadas às etiquetas existentes

Para ver as etiquetas do fornecedor de cloud em relatórios de custos do Cloudyn, tem de criar um modelo de alocação de custos personalizado através do Cost Allocation 360. Para tal, aceda a **Custo** > **Cost Management** > **Cost Allocation 360**, selecione as etiquetas pretendidas e, em seguida, defina as regras para processar custos não marcados. Em seguida, crie um novo modelo de custos. Posteriormente, pode ver os relatórios na Análise de Alocação de Custos para ver, filtrar e ordenar com base nas etiquetas de recursos do Azure.

As etiquetas de recursos do Azure só aparecem nos relatórios **Análise de Alocação de Custos**.

As etiquetas de faturação do fornecedor de cloud aparecem em todos os relatórios de custos.

As etiquetas de entidades do Cloudyn e as etiquetas criadas manualmente aparecem em todos os relatórios de custos.


## <a name="create-showback-and-chargeback-reports"></a>Criar relatórios de análise de custos e estorno

O método que as organizações utilizam para efetuar uma análise de custos e estorno varia bastante. No entanto, pode utilizar qualquer um dos dashboards e relatórios no portal do Cloudyn como base para qualquer finalidade. Pode fornecer acesso de utilizador a qualquer pessoa na sua organização para que seja possível ver dashboards e relatórios a pedido. Todos os relatórios de Análise de Custos suportam a análise de custos, porque mostram aos utilizadores os recursos que consumiram. Além disso, permitem aos utilizadores explorar os dados de custos ou utilização específicos do respetivo grupo na sua organização.

Para ver os resultados da alocação de custos, abra o relatório Análise de Custos e selecione o modelo de custos que criou. Em seguida, adicione um agrupamento de uma ou mais das etiquetas selecionadas no modelo de custos.

![Relatório Análise de Custos](./media/tutorial-manage-costs/cost-analysis.png)

Pode facilmente criar e guardar relatórios que se concentrem em serviços específicos consumidos por grupos específicos. Por exemplo, pode ter um departamento que utiliza extensivamente VMs do Azure. Pode criar um relatório que seja filtrado nas VMs do Azure para mostrar o consumo e os custos.

Se precisar de fornecer dados de instantâneos a outras equipas, pode exportar qualquer relatório no formato PDF ou CSV.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar etiquetas personalizadas para alocar custos.
> * Criar relatórios de análise de custos e estorno.



Avance para o próximo tutorial para saber mais sobre como controlar o acesso a dados.

> [!div class="nextstepaction"]
> [Control access to data](tutorial-user-access.md) (Controlar o acesso a dados)
