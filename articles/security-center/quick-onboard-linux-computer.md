---
title: "Início Rápido do Centro de Segurança do Azure - Carregar os computadores Linux para o Centro de Segurança | Microsoft Docs"
description: "Este início rápido mostra como carregar os computadores Linux para o Centro de Segurança."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 05e4bed0f9b4dfb6d1879408085447ef53db8655
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Início Rápido: Carregar computadores Linux para o Centro de Segurança do Azure
Depois de carregar as subscrições do Azure, pode ativar o Centro de Segurança para os recursos Linux em execução fora do Azure, por exemplo, no local ou noutras clouds, ao aprovisionar o Agente para Linux.

Este início rápido mostra como instalar o Agente para Linux num computador Linux.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Tem de estar no escalão de preço Standard do Centro de Segurança antes de começar este início rápido. Veja [Carregar uma subscrição do Azure para o Centro de Segurança Standard](security-center-get-started.md) para obter instruções sobre a atualização de versão. Pode experimentar o escalão Standard do Centro de Segurança sem custos durante os primeiros 60 dias.

## <a name="add-new-linux-computer"></a>Adicionar um novo computador Linux

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

 ![Descrição geral do Centro de Segurança][2]

3. No menu principal do Centro de Segurança, selecione **Inclusão de segurança avançada**.
4. Selecione **Quer adicionar computadores que não pertencem ao Azure**.
   ![Inclusão de segurança avançada][3]

5. Em **Adicionar novos computadores não Azure**, é apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou outra área de trabalho que pretenda utilizar.

    ![Adicionar computador não pertencente ao Azure][4]

6.  Na página **Agente Direto**, em **TRANSFERIR E CARREGAR AGENTE PARA LINUX**, selecione o botão **copiar** para copiar o comando *wget*.

7.  Abra o Bloco de notas e cole este comando. Guarde este ficheiro numa localização acessível a partir do seu computador Linux.

## <a name="install-the-agent"></a>Instalar o agente

1.  No computador Linux, abra o ficheiro que foi guardado anteriormente. Selecione todo o conteúdo, copie, abra uma consola de terminal e cole o comando.
2.  Depois de concluída a instalação, pode validar a instalação do *omsagent*, executando o comando *pgrep*. O comando irá devolver o PID (ID de processo) *omsagent*, conforme apresentado abaixo:

  ![Instalar o agente][5]

Os registos para o Agente do Centro de Segurança para Linux podem ser encontrados em: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Registos para o agente][6]

Após algum tempo, pode demorar até 30 minutos, o novo computador Linux será apresentado no Centro de Segurança.

Agora, pode monitorizar as VMs do Azure e os computadores não pertencentes ao Azure num único local. Em **Computação**, tem uma descrição geral de todas as VMs e computadores, juntamente com recomendações. Cada coluna representa um conjunto de recomendações. A cor representa o estado de segurança atual da VM ou do computador para essa recomendação. O Centro de Segurança também apresenta quaisquer deteções destes computadores nos alertas de Segurança.

  ![Painel Computação][7] Estão representados dois tipos de ícones no painel **Computação**:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computador não pertencente ao Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) VM do Azure

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, pode remover o agente do computador Linux.

Para remover o agente:

1. Transfira o [script universal](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) do agente para Linux para o computador.
2. Execute o ficheiro .sh do pacote com o argumento *--purge* no computador, o que remove completamente o agente e a respetiva configuração.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprovisionou o agente num computador Linux. Para saber mais sobre como utilizar o Centro de Segurança, avance para o tutorial para configurar uma política de segurança e avaliar a segurança dos seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: definir e avaliar as políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
