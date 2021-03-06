---
title: Permissões no Centro de segurança do Azure | Microsoft Docs
description: Este artigo explica como o Centro de segurança do Azure utiliza o controlo de acesso baseado em funções para atribuir permissões a utilizadores e identifica as ações permitidas para cada função.
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: f85f49bd54eacbca67143b35eaf555cfb744a41d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="permissions-in-azure-security-center"></a>Permissões no Centro de segurança do Azure

Centro de segurança do Azure utiliza [controlo de acesso baseado em funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídos a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, verá apenas informações relacionadas com a um recurso quando são atribuídas a função de proprietário, Contribuidor ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Além destas funções, há duas funções específicas do Centro de Segurança:

* **Leitor de segurança**: um utilizador que pertence a esta função tem de ver direitos ao centro de segurança. O utilizador pode ver as recomendações, alertas, uma política de segurança e Estados de segurança, mas não é possível efetuar alterações.
* **Administrador de segurança**: um utilizador que pertence a esta função tem os mesmos direitos como o leitor de segurança e também pode atualizar a política de segurança e dispensar alertas e recomendações.

> [!NOTE]
> As funções de segurança, segurança leitor e administrador de segurança, tem acesso apenas no Centro de segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure como armazenamento, Web & Mobile ou Internet das coisas.
>
>

## <a name="roles-and-allowed-actions"></a>Funções e as ações permitidas

A tabela seguinte apresenta as funções e permitido ações no Centro de segurança. Um X indica que a ação é permitida para essa função.

| Função | Editar política de segurança | Aplicar as recomendações de segurança para um recurso | Dispensar alertas e recomendações | Ver alertas e recomendações |
|:--- |:---:|:---:|:---:|:---:|
| Proprietário da subscrição | X | X | X | X |
| Contribuinte de subscrição | X | X | X | X |
| Proprietário do grupo de recursos | -- | X | -- | X |
| Grupo de recursos contribuinte | -- | X | -- | X |
| Leitor | -- | -- | -- | X |
| Administrador de Segurança | X | -- | X | X |
| Leitor de Segurança | -- | -- | -- | X |

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribua a função de leitor para utilizadores que apenas necessitam para ver informações sobre o estado de funcionamento de segurança de um recurso, mas não tomar medidas, tal como aplicar recomendações ou editar políticas.
>
>

## <a name="next-steps"></a>Passos Seguintes
Este artigo explicado como o Centro de segurança utiliza o RBAC para atribuir permissões a utilizadores e identificado as ações permitidas para cada função. Agora que está familiarizado com as atribuições de função necessárias para monitorizar o estado de segurança da sua subscrição, edite as políticas de segurança e aplicar recomendações, saiba como:

- [Definir políticas de segurança no Centro de segurança](security-center-policies.md)
- [Gerir recomendações de segurança no Centro de segurança](security-center-recommendations.md)
- [Monitorizar o estado de funcionamento de segurança dos seus recursos do Azure](security-center-monitoring.md)
- [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
- [Monitorizar soluções de segurança de parceiros](security-center-partner-solutions.md)
