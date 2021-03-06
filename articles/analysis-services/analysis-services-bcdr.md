---
title: Disponibilidade elevada de Analysis Services do Azure | Microsoft Docs
description: Assuring Azure Analysis Services elevada disponibilidade.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f8252e47224b96d43551ad389148a1ce4349c308
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="analysis-services-high-availability"></a>Disponibilidade elevada do Analysis Services
Este artigo descreve assuring elevada disponibilidade para servidores do Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Assuring elevada disponibilidade durante uma interrupção do serviço
Embora seja raro, um centro de dados do Azure pode ter uma falha. Quando ocorre uma falha, faz com que uma interrupção de negócios que poderá última alguns minutos ou pode última durante as horas. Elevada disponibilidade é normalmente conseguida com redundância de servidor. Com o Azure Analysis Services, pode conseguir redundância através da criação de servidores adicionais, secundárias num ou mais regiões. Quando criar servidores redundantes, para assegurar que os dados e os metadados nesses servidores na sincronização com o servidor numa região que ficou offline, pode:

* Implemente modelos servidores redundantes noutras regiões. Este método requer que dados de processamento no seu servidor primário e de servidores redundantes em paralelo, assuring todos os servidores estão em sincronização.

* [Cópia de segurança](analysis-services-backup.md) bases de dados do seu servidor primário e de restauro em servidores redundantes. Por exemplo, pode automatizar as cópias de segurança noturna ao armazenamento do Azure e restaurar para outros servidores redundantes noutras regiões. 

Em ambos os casos, se o servidor primário sofre uma falha, tem de alterar as cadeias de ligação no Reporting Services que os clientes ligar ao servidor no Centro de dados regional diferente. Esta alteração deve ser considerada último recurso e ocorre apenas se uma falha do Centro de dados regionais catastrófica. É mais provável, uma falha do Centro de dados que aloja o servidor primário seria fique novamente online antes de lhe foi possível atualizar ligações em todos os clientes. 

Para evitar a necessidade de alterar cadeias de ligação nos relatórios de clientes, pode criar um servidor [alias](analysis-services-server-alias.md) para o servidor primário. Se o servidor primário ficar inativo, pode alterar o alias para apontar para um servidor redundante noutra região. Pode automatizar alias para o nome do servidor por codificação uma verificação de estado de funcionamento do ponto final no servidor primário. Se falhar a verificação de estado de funcionamento, pode direcionar o mesmo ponto de final para um servidor redundante noutra região. 

## <a name="related-information"></a>Informações relacionadas
[Cópia de segurança e restauro](analysis-services-backup.md)   
[Gerir serviços de análise do Azure](analysis-services-manage.md)   
[Nomes de alias de servidor](analysis-services-server-alias.md) 

