---
title: Descrição geral do DSC da automatização do Azure
description: Uma descrição geral do Automation DSC do Azure pretendido Estado Configuration (), os termos e problemas conhecidos
keywords: PowerShell dsc, configuração de estado pretendido, azure do powershell dsc
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4282333fc8c6f0835c451b781f70dfaed0697d96
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-automation-dsc-overview"></a>Descrição geral do DSC da automatização do Azure

Automation DSC do Azure é um serviço Azure que permite-lhe escrever, gerir e compilar PowerShell pretendido Estado Configuration (DSC) [configurações](https://msdn.microsoft.com/powershell/dsc/configurations), importar [recursos de DSC](https://msdn.microsoft.com/powershell/dsc/resources)e atribuir as configurações para nós de destino, todos na nuvem.

## <a name="why-use-azure-automation-dsc"></a>Porquê utilizar o DSC de automatização do Azure

Automation DSC do Azure fornece várias vantagens relativamente ao utilizar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de solicitação incorporadas

A automatização do Azure fornece um [servidor de solicitação do DSC](https://msdn.microsoft.com/powershell/dsc/pullserver) para que nós de destino recebem automaticamente as configurações, está em conformidade com o estado pretendido e devolver relatórios sobre a sua conformidade.
O servidor de solicitação incorporada na automatização do Azure elimina a necessidade de configurar e manter o seu próprio servidor de solicitação.
A automatização do Azure pode visar virtuais ou físicas Windows ou Linux máquinas, na nuvem ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos de DSC

Automation DSC do Azure oferece a mesma camada de gestão para [configuração de estado pretendido do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) como automatização do Azure oferece para processamento de scripts do PowerShell.

O portal do Azure ou a partir do PowerShell, pode gerir todos os seus DSC configurações, recursos e nós de destino.

![Captura de ecrã do painel de automatização do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importar dados de relatórios para análise de registos

Nós que são geridos com o Automation DSC do Azure enviarem dados de estado de relatórios detalhados para o servidor de solicitação incorporada.
Pode configurar o DSC de automatização do Azure para enviar estes dados para a sua área de trabalho de análise de registos.
Para saber como enviar dados de estado de DSC para a sua área de trabalho de análise de registos, consulte [reencaminhar Automation DSC do Azure dados de relatórios ao Log Analytics](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Vídeo de introdução

Prefere ver do que ler? Tem a ver o seguinte vídeo de Maio de 2015, quando Automation DSC do Azure foi anunciada pela primeira vez.

>[!NOTE]
>Apesar dos conceitos e o ciclo de vida descritos neste vídeo estarem corretos, DSC de automatização do Azure tem progredido muito desde que o vídeo foi gravado.
>Está agora disponível normalmente, tem uma IU muito mais extensa no portal do Azure e suporta muitas funcionalidades adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Passos Seguintes

* Para saber como nós de carregar para serem geridos pelo Automation DSC do Azure, consulte [máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md)
* Para começar a utilizar o DSC de automatização do Azure, consulte o artigo [introdução ao Azure Automation DSC](automation-dsc-getting-started.md)
* Para saber mais sobre como compilar as configurações de DSC para que pode atribuir-lhes para nós de destino, consulte o artigo [compilar configurações de DSC de automatização do Azure](automation-dsc-compile.md)
* Para referência de cmdlet do PowerShell para o Automation DSC do Azure, consulte [cmdlets do Automation DSC do Azure](/powershell/module/azurerm.automation/#automation)
* Para obter informações sobre preços, consulte [preços do Automation DSC do Azure](https://azure.microsoft.com/pricing/details/automation/)
* Para ver um exemplo de utilização do Azure Automation DSC num pipeline a implementação contínua, consulte [a implementação contínua para IaaS VMs utilizando o Automation DSC do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
