---
title: "Descubra que software está instalado nos seus computadores com a Automatização do Azure | Microsoft Docs"
description: "Utilize o Inventário para descobrir que software está instalado nos computadores em todo o ambiente."
services: automation
keywords: "inventário, automatização,alteração, controlo"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/28/2018
ms.topic: tutorial
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 97cd2c91ca2c70b044518c43d49356918202d5ff
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Descobrir que software está instalado nos seus computadores do Azure e não Azure

Neste tutorial, irá aprender a descobrir que software está instalado no seu ambiente. Pode recolher e ver o inventário do software, dos ficheiros, dos daemons do Linux, dos Serviços do Windows e das chaves do Registo do Windows nos seus computadores. Controlar as configurações dos seus computadores pode ajudar a identificar problemas operacionais em todo o ambiente e compreender melhor o estado dos mesmos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Carregar uma VM para Controlo de alterações e Inventário
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um [conta de Automatização](automation-offering-get-started.md) para reter os runbooks de observador e ação e a Tarefa de Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de alterações e Inventário

Primeiro, tem de ativar o Controlo de alterações e Inventário para a VM para este tutorial. Se ativou anteriormente a solução de **Controlo de Alterações** para a VM, este passo não é necessário.

1. No menu da esquerda, selecione **Máquinas virtuais** e selecione uma VM da lista
2. No menu da esquerda, na secção **Operações**, clique em **Inventário**. É aberta a página **Ativar o Controlo de alterações e Inventário**.

![Faixa de configuração do carregamento de Inventário](./media/automation-tutorial-installed-software/enableinventory.png)

Para ativar a solução, configure a localização, a área de trabalho e a conta de Automatização do Log Analytics a utilizar e clique em **Ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

A área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) serve para recolher dados gerados pelas funcionalidades e serviços, como o Inventário.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.
Após a ativação da solução, as informações sobre o software instalado e as alterações efetuadas na VM são transmitidas para o Log Analytics.
Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="view-installed-software"></a>Ver software instalado

Depois de ativar a solução Controlo de alterações e Inventário, pode ver os resultados na página **Inventário**.

Na sua VM, selecione **Inventário** em **OPERAÇÕES**.

Na página **Inventário**, clique no separador **Software**.

No separador **Software**, existe uma tabela que lista o software que foi encontrado. O software é agrupado por nome de software e versão.

Os detalhes de alto nível para cada registo de software são visíveis na tabela. Estes detalhes incluem o nome do software, a versão, o editor, a hora da última atualização (a hora de atualização mais recente comunicada por um computador no grupo) e os computadores (a contagem de computadores com esse software).

![Inventário de software](./media/automation-tutorial-installed-software/inventory-software.png)

Clique numa linha para ver as propriedades do registo de software e os nomes dos computadores com esse software.

Para pesquisar um software específico ou um grupo de software, pode pesquisar na caixa de texto diretamente acima da lista de software.
O filtro permite-lhe pesquisar com base no nome do software, na versão ou no editor.

Por exemplo, a pesquisa de "Contoso" devolve todos os softwares com um nome, editor ou versão que contêm "Contoso".

## <a name="search-inventory-logs-for-installed-software"></a>Pesquisar registos de inventário do software instalado

O Inventário gera dados de registo que são enviados para o Log Analytics. Para pesquisar os registos através da execução de consultas, selecione **Log Analytics** na parte superior da janela **Inventário**.

Os dados do Inventário são armazenados abaixo do tipo **ConfigurationData**.
A consulta do Log Analytics de exemplo seguinte devolve os Editores que contêm "Microsoft" e o número de registos de Software (agrupados por SoftwareName e Computador) para cada Editor.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo no Log Analytics, consulte [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventário de computador único

Para ver o inventário de software de um único computador, pode aceder ao Inventário a partir da página de recurso da VM do Azure ou utilizar o Log Analytics para filtrar o computador correspondente. A consulta do Log Analytics de exemplo seguinte devolve a lista de software de um computador com o nome ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu como ver o inventário de software, como:

> [!div class="checklist"]
> * Carregar uma VM para Controlo de alterações e Inventário
> * Ver software instalado
> * Pesquisar registos de inventário do software instalado

Avance para a descrição geral da solução Controlo de alterações e Inventário para saber mais acerca dela.

> [!div class="nextstepaction"]
> [Solução Gestão de mudança e Inventário](automation-change-tracking.md)