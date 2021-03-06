---
title: Atribuições de elegíveis e visibilidade de recursos do Azure no Privileged Identity Management | Microsoft Docs
description: Descreve como atribuir membros como elegível para funções de recurso ao utilizar o PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4804d930a98192d64245784058920eeba7d30212
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Atribuições de elegíveis e visibilidade do recurso Privileged Identity Management

Privileged Identity Management (PIM) para funções de recursos do Azure fornece segurança melhorada para as organizações que tenham recursos do Azure críticos. Os administradores de recursos podem utilizar o PIM atribuir membros como elegível para funções de recursos. Saiba mais sobre os tipos de atribuição diferentes e Estados de atribuição de funções de recursos do Azure nas secções seguintes. 

## <a name="assignment-types"></a>Tipos de atribuição

PIM para recursos do Azure fornece dois tipos de atribuição distintas:

- Elegível
- Ativa

Atribuições de elegíveis requerem o membro da função efetuar uma ação para utilizar a função. As ações podem incluir ter êxito uma verificação de autenticação multifator, fornecendo uma justificação de negócio ou solicitar a aprovação de aprovadores designados.

Atribuições de Active Directory não necessitam de membro efetuar qualquer ação para utilizar a função. Os membros atribuídos como ativo tem os privilégios atribuídos à função de todas as vezes.

## <a name="assignment-duration"></a>Duração de atribuição

Os administradores de recursos podem escolher entre duas opções para cada tipo de atribuição quando configurarem PIM as definições para uma função. Estas opções tornar-se a duração máxima de predefinição quando um membro é atribuído à função no PIM. 

Um administrador pode escolher um dos seguintes tipos de atribuição:

- Permitir que a atribuição de elegível permanente
- Permitir atribuição permanente de Active Directory

Em alternativa, um administrador pode escolher um dos seguintes tipos de atribuição:

- Expirar atribuições elegíveis após
- Atribuições de Active Directory depois de expirar

Se escolher um administrador de recursos **permitir atribuição elegível permanente** ou **permitir atribuição permanente de Active Directory**, todos os administradores que atribuir membros para o recurso podem atribuir permanente associações.

Se escolher um administrador de recursos **expirar atribuições elegíveis após** ou **expirar atribuições de Active Directory após**, o administrador de recursos controla o ciclo de vida de atribuição, exigindo que todos os atribuições de tem uma data de início e de fim especificada.

> [!NOTE] 
> Todas as atribuições com uma data de fim especificada podem ser renovadas por administradores de recursos. Além disso, os membros podem iniciar pedidos de self-service para [expandir ou renovar atribuições](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Estados de atribuição

PIM para recursos do Azure tem dois Estados distintos atribuição que aparecem no **funções ativas** separador o **as minhas funções**, **funções**, e **membros**vistas do PIM. Estes Estados permitidos são:

- Atribuído
- Ativado

Quando visualiza uma associação que está listada no **funções ativas**, pode utilizar o valor no **estado** coluna para diferenciar entre os utilizadores que são **atribuído** como Active Directory e os utilizadores que **ativado** uma atribuição elegível e tem agora Active Directory.

## <a name="next-steps"></a>Passos Seguintes

[Atribuir funções no Privileged Identity Manager](pim-resource-roles-assign-roles.md)
