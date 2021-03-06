---
title: Editar textual runbooks na automatização do Azure
description: Este artigo fornece procedimentos diferentes para trabalhar com runbooks do PowerShell e o fluxo de trabalho do PowerShell na automatização do Azure utilizando o editor de texto.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 055bd2a7607b8cab9c7ca417c7c3f57c2e569f77
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2018
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Editar textual runbooks na automatização do Azure

O editor de texto na automatização do Azure pode ser utilizado para editar [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks do fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Isto tem as funcionalidades típicas de outros editores de código como o intellisense e cor codificação com funcionalidades especiais adicionais para ajudá-lo a aceder a recursos comuns a runbooks. Este artigo fornece os passos detalhados para executar diferentes funções deste editor.

O editor de texto inclui uma funcionalidade para inserir o código para cmdlets, recursos e runbooks subordinados num runbook. Em vez de digitar o código por si, pode selecionar a partir de uma lista de recursos disponíveis e o código correto inserido no runbook.

Cada runbook na automatização do Azure tem duas versões, rascunho e a publicada. Editar a versão de rascunho do runbook e, em seguida, publica-pode ser executado. Não é possível editar a versão publicada. Consulte [publicar um runbook](automation-creating-importing-runbook.md#publishing-a-runbook) para obter mais informações.

Trabalhar com [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks), consulte [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure

Utilize o procedimento seguinte para abrir um runbook para o editor de texto de edição.

1. No portal do Azure, selecione a sua conta de automatização.
2. Em **AUTOMATIZAÇÃO de processos**, selecione **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook que pretende editar e, em seguida, clique em de **editar** botão.
4. Efetue a edição necessária.
5. Clique em **guardar** quando as edições que efectuou estiverem concluídas.
6. Clique em **publicar** se pretender que a versão mais recente de rascunho do runbook a ser publicado.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>A inserir um cmdlet num runbook

1. Na tela do editor de texto, posicione o cursor onde pretende colocar o cmdlet.
2. Expanda o **Cmdlets** nó no controlo da biblioteca.
3. Expanda o módulo que contém o cmdlet que pretende utilizar.
4. Clique com o botão direito do rato no cmdlet para inserir e selecione **adicionar à tela**. Se o cmdlet tem mais do que um conjunto de parâmetros, será possível adicionar o conjunto predefinido. Também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferentes.
5. O código para o cmdlet é inserido com a lista completa de parâmetros.
6. Forneça um valor adequado em vez do tipo de dados rodeado por chavetas <> para os parâmetros necessários. Remova quaisquer parâmetros, não precisa.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir o código para um runbook subordinado num runbook

1. Na tela do editor de texto, posicione o cursor onde pretende colocar o código para o [runbook subordinado](automation-child-runbooks.md).
2. Expanda o **Runbooks** nó no controlo da biblioteca.
3. Clique com o botão direito do rato no runbook a inserir e selecione **adicionar à tela**.
4. O código para o runbook subordinado é inserido com quaisquer marcadores de posição para quaisquer parâmetros do runbook.
5. Substitua os marcadores de posição com os valores adequados para cada parâmetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>A inserir um elemento num runbook

1. Na tela do editor de texto, posicione o cursor onde pretende colocar o código para o runbook subordinado.
2. Expanda o **ativos** nó no controlo da biblioteca.
3. Expanda o nó para o tipo de recurso que pretende.
4. Clique com o botão direito do rato em Insira e selecione o elemento **adicionar à tela**. Para [recursos de variável](automation-variables.md), selecione **adicionar "Obter variável" à tela** ou **adicionar "Definir variável" à tela** dependendo se pretende obter ou definir a variável.
5. O código para o elemento é inserido no runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar um runbook de automatização do Azure com o Windows PowerShell

Para editar um runbook com o Windows PowerShell, utilize um editor à sua escolha e guarde-o num ficheiro. ps1. Pode utilizar o [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) cmdlet para obter os conteúdos do runbook e, em seguida, [conjunto AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) cmdlet para substituir a existente rascunho de runbook pelo modificado.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para obter os conteúdos de um Runbook com o Windows PowerShell

Os comandos de exemplo seguintes mostram como obter o script de um runbook e guardá-lo para um ficheiro de script. Neste exemplo, é obtida a versão de rascunho. Também é possível obter a versão publicada do runbook, apesar de não pode ser alterada nesta versão.

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

$runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
$runbookContent = $runbookDefinition.Content

Out-File -InputObject $runbookContent -FilePath $scriptPath
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para alterar o conteúdo de um Runbook com o Windows PowerShell

Os comandos de exemplo seguintes mostram como substituir o conteúdo existente de um runbook com o conteúdo de um ficheiro de script. Tenha em atenção que este é o mesmo procedimento de exemplo do [para importar um runbook a partir de um ficheiro de script com o Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName
```

## <a name="related-articles"></a>Artigos relacionados

* [Criar ou importar um runbook na automatização do Azure](automation-creating-importing-runbook.md)
* [Fluxo de trabalho do PowerShell de aprendizagem](automation-powershell-workflow.md)
* [Gráfico de criação na automatização do Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Ligações](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)
