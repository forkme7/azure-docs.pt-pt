---
title: Executar um teste de recuperação após desastre para máquinas no local para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre como executar um teste de recuperação após desastre no local para o Azure com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 2ac15e4da411efa6f018a3e3fb620023bc8964cc
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Executar um teste de recuperação após desastre para o Azure

Este tutorial mostra como executar um teste de recuperação após desastre para uma máquina no local para o Azure com uma ativação pós-falha de teste. Um teste valida a estratégia de replicação sem perda de dados. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar uma rede isolada para ativação pós-falha de teste
> * Preparar a ligação para a VM do Azure após a ativação pós-falha
> * Executar uma ativação pós-falha de teste para uma única máquina

Este é o quarto tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware no local](tutorial-prepare-on-premises-vmware.md)
3. [Configurar a recuperação após desastre](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar uma ativação pós-falha de teste, verifique as propriedades da VM e certifique-se de que o Hyper-V VM[hyper-v-azure-support-matrix.md#replicated-vms], [a VM VMware ou o servidor físico](vmware-physical-azure-support-matrix.md#replicated-machines) estão em conformidade com os requisitos do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. Em **Computação e Rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as definições do disco gerido.
   
      >[!NOTE]
      Atualmente, não é suportada a reativação pós-falha para máquinas Hyper-V no local a partir de VMs do Azure com discos geridos. Só deve utilizar a opção de discos geridos para a ativação pós-falha se estiver a planear migrar VMs no local para o Azure, sem reativá-las.
   
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-test-failover-for-a-single-vm"></a>Executar uma ativação pós-falha de teste para uma única VM

Quando executa uma ativação pós-falha de teste, ocorre o seguinte:

1. É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
2. A ativação pós-falha processa os dados, para que possa ser criada uma VM do Azure. Se for selecionado o último ponto de recuperação, é criado um ponto de recuperação a partir dos dados.
3. É criada uma VM do Azure com base nos dados processados no passo anterior.

Execute a ativação pós-falha de teste da seguinte forma:

1. Em **Definições** > **Itens Replicados**, clique na VM > **+Testar Ativação Pós-falha**.
2. Selecione o ponto de recuperação **Processado mais recentemente** para este tutorial. Este procedimento efetua a ativação pós-falha da VM para o ponto no tempo mais recente disponível. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
3. Em **Ativação Pós-Falha de Teste**, selecione a rede do Azure de destino para as VMs do Azure que serão ligadas após a ocorrência da ativação pós-falha.
4. Clique em **OK** para iniciar a ativação pós-falha. Pode clicar na VM e abrir as respetivas propriedades para acompanhar o progresso. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** >
   **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique que a VM tem o tamanho adequado, está ligada à rede certa e está em execução.
6. Deverá conseguir ligar-se à VM replicada no Azure agora.
7. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Poderá reparar em tempos de ativação pós-falha superiores para máquinas do Linux VMware, VMs VMware que não têm o serviço DHCP ativado e VMs VMware que não têm os controladores de arranque storvsc, vmbus, storflt, intelide e atapi.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha e uma reativação pós-falha para VMs VMware no local](vmware-azure-tutorial-failover-failback.md).
