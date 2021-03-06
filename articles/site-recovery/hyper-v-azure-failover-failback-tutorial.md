---
title: "Ativação pós-falha e falhar fazer uma cópia de VMs de Hyper-V replicado para o Azure com a recuperação de Site | Microsoft Docs"
description: "Saiba como efetuar a ativação pós-falha de VMs de Hyper-V para o Azure e haja reativação para o site no local, com o Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Ativação pós-falha e falhar fazer uma cópia de VMs de Hyper-V replicado para o Azure

Este tutorial descreve como efetuar a ativação pós-falha de uma VM de Hyper-V para o Azure. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Certifique-se de que as propriedades de VM de Hyper-V para verificar em conformidade com os requisitos do Azure
> * Executar uma ativação pós-falha para o Azure
> * Voltar a proteger as VMs do Azure para o site no local
> * Falhar novamente a partir do Azure no local
> * Voltar a proteger VMs no local, para iniciar a replicação para o Azure novamente

Este é o quinto tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware no local](tutorial-prepare-on-premises-hyper-v.md)
3. Configurar a recuperação após desastre para [VMs de Hyper-V](tutorial-hyper-v-to-azure.md), ou para [VMs de Hyper-V geridas em nuvens VMM do System Center](tutorial-hyper-v-vmm-to-azure.md)
4. [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparar para ativação pós-falha e a reativação pós-falha

Certifique-se de que existem não existem instantâneos da VM e que a VM no local é desativada durante só. Desta forma, garante-se a consistência dos dados durante a replicação. Não ative a VM após a conclusão da nova proteção. 

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Fazer a ativação pós-falha para o Azure**: fazer a ativação pós-falha de máquinas do site no local para o Azure.
2. **Proteja as VMs do Azure**: Proteja as VMs do Azure, para que possam iniciar replicar as VMs de Hyper-V no local.
3. **Efetuar a ativação pós-falha no local**: executar uma ativação pós-falha do Azure para o seu site no local, quando estiver disponível.
4. **Reproteção no local VMs**: depois de dados falhou novamente, voltar a proteger as VMs no local para iniciar a replicação-los para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Verifique as propriedades da VM e certifique-se de que a VM está em conformidade com [requisitos do Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. No **itens protegidos**, clique em **itens replicados** >< VM-name >.

2. No **replicados item** painel, reveja as informações de VM, o estado de funcionamento e os pontos de recuperação disponível mais recentes. Clique em **Propriedades** para ver mais detalhes.
     - No **computação e rede**, pode modificar as definições de VM e as definições, incluindo a rede/sub-rede na qual de rede VM do Azure. Discos geridos não são suportados para reativação pós-falha a partir do Azure para o Hyper-V.
   estarão localizadas após a ativação pós-falha e o endereço IP que será atribuído ao mesmo.
    - Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="fail-over-to-azure"></a>Efetuar a ativação pós-falha para o Azure

1. Em **Definições** > **Itens replicados** clique na VM > **Ativação Pós-falha**.
2. No **ativação pós-falha** selecionar o **mais recente** ponto de recuperação. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. Recuperação de site tenta efetuar um encerramento de VMs de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Tarefas**.
4. Depois de verificar a ativação pós-falha, clique em **consolidar**. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar em curso, deixa de ativação pós-falha, mas a VM não irá replicar novamente.

## <a name="reprotect-azure-vms"></a>Voltar a proteger VMs do Azure

1. No **AzureVMVault** > **replicado itens**, a VM que foi efetuada a ativação pós-falha com o botão direito e selecione **voltar a proteger**.
2. Certifique-se de que a direção de proteção está definida como **do Azure para o local**.
3. A VM no local é desativada durante a que, para ajudar a garantir a consistência dos dados. Não ativá-la após a conclusão da só.
4. Depois do processo de a VM começa a replicar a partir do Azure para o site no local.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Ativação pós-falha do Azure e voltar a proteger a VM no local

Ativação pós-falha do Azure para o site no local e inicie a replicar VMs a partir do site no local para o Azure.

1. No **definições** > **replicado itens**, clique na VM > **ativação pós-falha planeada**.
2. No **confirmar a ativação de pós-falha planeadas**, certifique-se a direção de ativação pós-falha (a partir do Azure) e selecione as localizações de origem e de destino.
3. Selecione **sincronizar os dados antes da ativação pós-falha (sincronizar apenas alterações de delta)**. Esta opção minimiza o tempo de inatividade VM porque sincroniza-se sem encerrar a VM.
4. Inicie a ativação pós-falha. Pode seguir o progresso de ativação pós-falha no **tarefas** separador.
5. Depois dos dados iniciais é efetuada a sincronização e estará pronto encerrar o VMs do Azure, clique em **tarefas** > a ser planeada a ativação pós-falha-tarefa-nome-> **concluir a ativação pós-falha**. Isto será encerrado a VM do Azure, transfere a mais recente alterações no local e começa a VM no local.
6. Inicie sessão para a VM no local para verificar que se está disponível como esperado.
7. A VM no local agora está a ser um **consolidar pendente** estado. Clique em **consolidar**. Esta ação elimina as VMs do Azure e os respetivos discos e prepara a VM no local para a replicação inversa.
Iniciar a replicação da VM no local para o Azure, ativar **inverso replicar**. Isto aciona a replicação das alterações de delta que ocorreram desde que a VM do Azure foi desativada.  
