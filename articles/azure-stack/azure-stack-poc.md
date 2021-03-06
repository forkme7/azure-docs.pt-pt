---
title: O que é o Azure Stack? | Microsoft Docs
description: Pilha do Azure permite-lhe executar serviços do Azure no seu centro de dados.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: d54f392eddfcca99e7fe0b037b9efd0a4e90433c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-stack"></a>O que é o Azure Stack?

Pilha do Microsoft Azure é uma plataforma de nuvem híbrida que lhe permite fornecer serviços do Azure a partir do Centro de dados da sua organização.  Pilha do Azure foi concebida para ativar novos cenários para as suas aplicações modernas no cenários-chave, como o limite e ambientes desligados ou requisitos de segurança e conformidade específicos no reunião.  Pilha do Azure é oferecida na duas opções de implementação para satisfazer as necessidades.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados do Azure Stack
Pilha do Azure integrados sistemas são fornecidos através de uma parceria da Microsoft e [parceiros de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), criação de uma solução que oferece paced de nuvem inovação balanceado com simplicidade em gestão.  Porque a pilha do Azure é fornecida como um sistema integrado de hardware e software, é-lhe oferecida a quantidade certa de flexibilidade e controlo, enquanto ainda adotar inovação da nuvem.  Sistemas de pilha integrada do Azure no intervalo de tamanho de nós de 4-12 e jointly são suportados pela Microsoft e parceiros de hardware.  Utilize sistemas de pilha do Azure integrado para ativar novos cenários para as cargas de trabalho de produção.    

## <a name="azure-stack-development-kit"></a>Development Kit do Azure Stack
Microsoft [Kit de desenvolvimento de pilha do Azure (ASDK)](.\asdk\asdk-what-is.md) é uma implementação de nó único da pilha do Azure, que pode utilizar para avaliar e obter informações sobre a pilha do Azure.  Também pode utilizar o ASDK como um ambiente de programação, onde pode desenvolver com APIs e ferramentas consistente com o Azure. O ASDK não se destina a ser utilizado como um ambiente de produção.

O ASDK tem as seguintes limitações:
* ASDK está associado um único do Azure Active Directory (Azure AD) ou o fornecedor de identidade de serviços de Federação do Active Directory (AD FS). Pode criar vários utilizadores neste diretório e atribuir as subscrições para cada utilizador.
* Com todos os componentes implementados num único computador, existem limitados recursos físicos disponíveis para os recursos de inquilino. Esta configuração não se destina a avaliação de escala ou desempenho.
* Cenários de redes estão limitados devido ao requisito de anfitrião único/NIC.  

## <a name="next-steps"></a>Passos Seguintes
[Funcionalidades e conceitos principais](azure-stack-key-features.md)

[Pilha do Azure: Uma extensão do Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)

