---
title: O meu primeiro runbook do PowerShell na automatização do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook do PowerShell simples.
keywords: Azure powershell, tutorial de script do powershell, automatização de powershell
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 76d14b0d9bf14c6b9f342b0aae8fd42e871ea18d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2018
---
# <a name="my-first-powershell-runbook"></a>O meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [ runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automatização do Azure. Começar com um runbook simples que testar e publicar enquanto Saiba como monitorizar o estado da tarefa de runbook. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, pode tornar o runbook mais robusto ao adicionar parâmetros do runbook.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. O utilizador para e inicia esta máquina, pelo que não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Passo 1 – criar um novo runbook
Começar através da criação de um runbook simples que produz o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.

   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).

1. Clique em **Runbooks** em **automatização de processos** para abrir a lista de runbooks.
2. Criar um runbook novo ao clicar no **+ adicionar um runbook** botão e, em seguida, **criar um novo runbook**.
3. Atribua ao runbook o nome *MyFirstRunbook PowerShell*.
4. Neste caso, vai criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) , por isso, selecione **Powershell** para **tipo de Runbook**.
5. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook
Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Nestas instruções, escrever diretamente no runbook.

1. O runbook está atualmente vazio, tipo *Write-Output "Olá mundo".* no corpo do script.<br><br> ![Olá Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook
Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
3. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.

   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. É movido para *inicial* quando uma função de trabalho a tarefa e, em seguida, *executar* quando o runbook, na verdade, entra em execução.  

1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No seu caso, deverá ver *Olá, mundo*.<br><br> ![Resultado do Painel de Teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook
O runbook que criou ainda está no modo de Rascunho. Tem de publicá-lo antes de pode executá-lo na produção.  Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
2. Se se deslocar para a esquerda para ver o runbook no **Runbooks** painel,-mostra agora um **criação estado** de **publicada**.
3. Desloque-se para trás para a direita para ver o painel de **MyFirstRunbook-PowerShell**.  
   As opções na parte superior permitem-nos iniciar o runbook, ver o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
4. Pretende iniciar o runbook, por isso, clique em **iniciar** e, em seguida, clique em **Ok** quando abre a página Iniciar Runbook.
5. Uma página de tarefa é aberta para a tarefa de runbook que criou. Pode fechar este painel, mas neste caso, deixá-lo aberto para vermos o progresso da tarefa.
6. O estado da tarefa é mostrado na **resumo da tarefa** e corresponde aos Estados que vimos quando testar o runbook.<br><br> ![Resumo da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Uma vez o estado de runbook mostrar *concluído*, em **descrição geral** clique **saída**. É aberto o painel de resultados e pode ver o *Olá, mundo*.<br><br> ![Resultado da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Feche a página de saída.
9. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Apenas deve conseguir ver *Hello World* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.<br><br> ![Todos os Registos](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. Feche a página de fluxos e a página de tarefas para regressar à página MyFirstRunbook-PowerShell.
11. Em **detalhes**, clique em **tarefas** para abrir o painel de tarefas para este runbook. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.<br><br> ![Lista de Tarefas](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure
Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Não é possível fazê-lo embora, a menos que tenha seja autenticado utilizando as credenciais referidas na [pré-requisitos](#prerequisites). Pode fazê-lo com o **Connect-AzureRmAccount** cmdlet.

1. Abra o editor de texto clicando **editar** na página de MyFirstRunbook-PowerShell.
2. Não precisa de **Write-Output** linha, por isso, para avançar e eliminá-lo.
3. Escreva ou copie e cole o seguinte código que processa a autenticação com a conta da Execução da Automatização como:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Clique em **painel de teste** para que possa testar o runbook.
5. Clique em **Iniciar** para iniciar o teste. Depois de terminar, deverá receber um resultado parecido ao seguinte que mostra informações básicas da sua conta. Isto confirma que a credencial é válida.<br><br> ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual
Agora que o runbook está a autenticar para a sua subscrição do Azure, pode gerir os recursos. Adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição do Azure e para, agora que codifique os de nome do runbook.

1. Depois de *Connect-AzureRmAccount*, tipo *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* fornecer o nome e o nome do grupo de recursos da máquina virtual para iniciar.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Guarde o runbook e, em seguida, clique em **painel de teste** , de modo a que poder testá-lo.
3. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passo 7 – adicionar um parâmetro de entrada ao runbook
Runbook inicia atualmente a máquina virtual que codificado no runbook, mas seria mais útil se especificar a máquina virtual quando o runbook é iniciado. Adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros *VMName* e *ResourceGroupName* ao runbook e utilize estas variáveis com o **Start-AzureRmVM** cmdlet como no exemplo seguinte.

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Guarde o runbook e abra o painel de Teste. Agora pode fornecer valores para as duas variáveis de entrada que são utilizadas no teste.
2. Feche o painel de Teste.
3. Clique em **Publicar** para publicar a nova versão do runbook.
4. Pare a máquina virtual que iniciou no passo anterior.
5. Clique em **OK** para iniciar o runbook. Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.<br><br> ![Transmitir Parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.

## <a name="differences-from-powershell-workflow"></a>Diferenças do Fluxo de Trabalho do PowerShell
Os runbooks do PowerShell têm o mesmo ciclo de vida, capacidades e gestão que os runbooks do Fluxo de trabalho do PowerShell, mas existem algumas diferenças e limitações:

1. Os runbooks do PowerShell são executados rapidamente em comparação com os runbooks do Fluxo de Trabalho do PowerShell, uma vez que não têm o passo de compilação.
2. Os runbooks do Fluxo de Trabalho do PowerShell suportam pontos de verificação. Através dos pontos de verificação, os runbooks do Fluxo de Trabalho do PowerShell podem retomar a partir de qualquer ponto no runbook, ao passo que os runbooks do PowerShell só podem retomar a partir do início.
3. Os runbooks do Fluxo de Trabalho do PowerShell suportam a execução paralela e em série, enquanto os runbooks do PowerShell só podem executar comandos serialmente.
4. Num runbook de Fluxo de Trabalho do PowerShell, uma atividade, um comando ou um bloco de scripts podem ter o seu próprio espaço de execução, ao passo que num runbook do PowerShell, tudo num script é executado num único espaço de execução. Também existem algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook de PowerShell nativo e um runbook de Fluxo de Trabalho do PowerShell.

## <a name="next-steps"></a>Passos Seguintes
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

