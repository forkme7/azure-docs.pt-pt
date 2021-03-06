---
title: Configurar um servidor de processos no Azure para a VM de VMware e reativação pós-falha do servidor físico com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar um servidor de processos no Azure, a reativação pós-falha VMs do Azure para VMware.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 7bbe690e749680edde08facadf6d5910d7896f7e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor de processos no Azure para reativação pós-falha

Após a ativação pós-falha de VMs de VMware ou servidores físicos a utilização do Azure [recuperação de Site](site-recovery-overview.md), pode recuperá-los para o site no local quando for novamente em funcionamento. Para voltar a falhar, terá de configurar um servidor de processo temporário no Azure, para lidar com a replicação a partir do Azure no local. É possível eliminar esta VM após a conclusão da reativação pós-falha.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o [só](vmware-azure-reprotect.md) e [reativação pós-falha](vmware-azure-failback.md) processo.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processos no Azure

1. No cofre > **infraestrutura de recuperação de Site**> **gerir** > **servidores de configuração**, selecione o servidor de configuração.
2. Na página do servidor, clique em **+ o servidor de processos**
3. No **Adicionar servidor de processos** página e selecione para implementar o servidor de processos no Azure.
4. Especifique as definições do Azure, incluindo a subscrição utilizada para ativação pós-falha, um grupo de recursos, a região do Azure utilizados para a ativação pós-falha e a rede virtual no qual estão localizadas as VMs do Azure. Se utilizou várias redes do Azure, é necessário um servidor de processos em cada um deles.
5. No **nome do servidor**, **nome de utilizador**, e **palavra-passe**, especifique um nome para o servidor de processos e as credenciais que serão atribuídas permissões de administrador no servidor.
6. Especifique uma conta de armazenamento a serem utilizadas para os discos VM do servidor, a sub-rede na qual o servidor de processos VM estarão localizado e o endereço IP do servidor que será atribuído quando a VM entrar.
7. Clique em **OK** botão para iniciar a implementação de VM do servidor de processos.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registar o servidor de processos (em execução no Azure) para um servidor de configuração (em execução no local)

Depois do servidor de processos VM está a funcionar, tem de registá-lo com o servidor de configuração no local, da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


