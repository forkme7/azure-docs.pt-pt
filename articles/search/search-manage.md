---
title: Administração de serviço para a Azure Search no portal do Azure
description: Gerir a Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure, utilizando o portal do Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 896a12db1ac196b6de1e57dde9b5910e11dcc8c7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2018
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração de serviço para a Azure Search no portal do Azure
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

A pesquisa do Azure é um serviço completamente gerido, baseado na nuvem de pesquisa utilizado para criar uma experiência de pesquisa avançada em aplicações personalizadas. Este artigo abrange o *service administração* tarefas que pode efetuar no [portal do Azure](https://portal.azure.com) para um serviço de pesquisa tenha sido já aprovisionada. *Serviço de administração* é simples por predefinição, limitada até as seguintes tarefas:

* Gerir e proteger o acesso ao *chaves de api* utilizado para leitura ou de acesso de escrita ao seu serviço.
* Ajuste a capacidade de serviço alterando a atribuição de partições e réplicas.
* Monitorizar a utilização de recursos, relativo aos limites máximos de camada de serviços.

Repare que *atualizar* não está indicada como uma tarefa administrativa. Porque os recursos são atribuídos quando o serviço é aprovisionado, a mudança para uma camada diferente exige um novo serviço. Para obter mais informações, consulte [criar um serviço da Azure Search](search-create-service-portal.md).

> [!Tip]
> Está à procura de ajuda sobre como analisar o desempenho de tráfego ou consulta de pesquisa? Ganhos aprofundadas volume de consulta, quais as pessoas de termos de pesquisa para, e os resultados da pesquisa como êxito são ao servir pelos clientes a documentos específicos no seu índice. Para obter orientações, consulte [análise de tráfego de pesquisa do Azure Search](search-traffic-analytics.md), [monitorizar métricas de utilização e a consulta](search-monitor-usage.md), e [desempenho e a otimização de](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
Aprovisionamento ou desativar o próprio serviço pode ser efetuada por um administrador de subscrição do Azure ou coadministrador.

Dentro de um serviço, qualquer pessoa com acesso para o URL do serviço e uma chave de api de administrador tem acesso de leitura / escrita para o serviço. Acesso de leitura e escrita fornece a capacidade de adicionar, eliminar ou modificar objetos de servidor, incluindo as chaves de api, índices, indexadores, origens de dados, as agendas e atribuições de funções conforme implementado através do [funções do RBAC](search-security-rbac.md).

Todos os interação do utilizador com a Azure Search fica dentro destes modos: acesso de leitura / escrita para o serviço (direitos de administrador) ou acesso só de leitura para o serviço (direitos de consulta). Para obter mais informações, consulte [gerir as chaves de api](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Informações de registo e sistema
A pesquisa do Azure não expõe os ficheiros de registo para um serviço individual ou através do portal ou interfaces programáticas. Na camada básica e superior, Microsoft monitoriza todos os serviços de pesquisa do Azure para disponibilidade de 99,9% por contratos de nível de serviço (SLA). Se o serviço estiver lento ou débito de pedidos está abaixo de limiares de SLA, as equipas de suporte rever os ficheiros de registo disponíveis e resolver o problema.

Em termos de informações gerais sobre o serviço, pode obter as informações das seguintes formas:

* No portal, no dashboard do serviço, através de notificações, propriedades e as mensagens de estado.
* Utilizar [PowerShell](search-manage-powershell.md) ou [API de REST de gestão](https://docs.microsoft.com/rest/api/searchmanagement/) para [obter propriedades de serviço](https://docs.microsoft.com/rest/api/searchmanagement/services), ou o estado na utilização dos recursos de índice.
* Através de [pesquisar a análise de tráfego](search-traffic-analytics.md), conforme indicado anteriormente.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorizar a utilização de recursos
No dashboard de monitorização de recursos está limitada às informações mostradas no dashboard do serviço e métricas alguns que pode obter consultando o serviço. No dashboard do serviço, na secção de utilização, pode determinar rapidamente se os níveis de recursos de partição são adequadas para a sua aplicação.

Utilizando a API de REST do serviço de pesquisa, pode obter uma contagem em índices e documentos através de programação: 

* [Obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contagem de documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Falhas de serviço e de recuperação de desastre

Embora iremos pode salvage os dados, da Azure Search não fornece instantânea ativação pós-falha do serviço se houver uma falha ao nível do centro do cluster ou de dados. Se falhar um cluster no Centro de dados, a equipa de operações irá detetar e de trabalho para restaurar o serviço. Irá ocorrer um período de indisponibilidade durante o restauro de serviço. Pode pedir os créditos de serviço para compensar para a indisponibilidade do serviço pelo [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se for necessária em caso de falhas catastrófica fora do controlo da Microsoft contínua do serviço, foi [aprovisionar um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de georreplicação para garantir que os índices são totalmente redundantes em todos os serviços.

Os clientes que utilizam [indexadores](search-indexer-overview.md) preencher e atualizar os índices pode processar a recuperação após desastre através de indexadores georreplicação específicos tirar partido da mesma origem de dados. Dois serviços em regiões diferentes, cada um indexador em execução foi índice da mesma origem de dados para alcançar a redundância geográfica. Se estiver a indexação de origens de dados que também são georredundante, lembre-se de que os indexadores de pesquisa do Azure só podem efetuar a indexação incremental de réplicas primárias. Um evento de ativação pós-falha, lembre-se de que aponte novamente o indexador para a nova réplica primária. 

Se não utilizar indexadores, utilizaria dados nos serviços de pesquisa diferentes e de código da aplicação para objetos de push em paralelo. Para obter mais informações, consulte [desempenho e a Otimização da Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Porque a Azure Search não é uma solução de armazenamento de dados principal, não podemos fornecer um mecanismo formal self-service a cópia de segurança e restauro. O código da aplicação utilizado para criar e preencher um índice é a opção de restauro de facto, se eliminar um índice por engano. 

Para reconstruir um índice, teria de eliminá-lo (partindo do princípio de que existe), recrie o índice no serviço e recarregar ao obter dados a partir do seu arquivo de dados principal. Em alternativa, pode aceder à [suporte ao cliente]() para índices residual se existir uma falha regional.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente
Cada serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se iniciou a cópia de segurança para um [escalão que fornece recursos dedicados](search-limits-quotas-capacity.md), clique em de **escala** mosaico no dashboard de serviço para ajustar a utilização de recursos.

Quando adicionar capacidade através de qualquer um dos recursos, o serviço de as utilizar automaticamente. Não é necessária nenhuma ação adicional da sua parte, mas não existe um ligeiro atraso antes do impacto do novo recurso é realizado. Pode demorar 15 minutos ou mais para aprovisionar os recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
Aumentar consultas por segundo (QPS) ou alcançar a elevada disponibilidade é feito adicionando as réplicas. Cada réplica tem uma cópia de um índice, para que adicionar uma réplica mais traduz-se um índice mais disponível para processar pedidos de consulta do serviço. Um mínimo de 3 réplicas são necessários para elevada disponibilidade (consulte [planeamento de capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa ter mais de réplicas pode carregar saldo os pedidos de consulta através de um grande número de índices. Tendo em conta um nível de volume de consulta, débito de consulta vai ser mais rápida quando existirem mais cópias do índice disponível para processar o pedido. Se ocorrerem latência de consulta, que pode esperar um impacto positivo no desempenho, uma vez que as réplicas adicionais estão online.

Embora o débito de consulta fica cópias de segurança à medida que adiciona réplicas, não precisamente duplo ou tripla à medida que as réplicas que adiciona ao seu serviço. Todas as aplicações de pesquisa estão sujeitos a fatores externos que podem impinge no desempenho das consultas. Consultas complexas e a latência de rede são dois fatores contribuem para variações no tempo de resposta de consulta.

### <a name="add-partitions"></a>Adicionar partições
A maioria das aplicações de serviço têm uma necessidade incorporada de mais réplicas em vez de partições. Para os casos em que é necessária uma contagem de documentos maior, pode adicionar partições se se inscreveu no serviço padrão. O escalão básico fornecem para partições adicionais.

No escalão Standard, as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefacto de fragmentação. É criado um índice em 12 shards, que podem todos ser armazenadas na partição de 1 ou igualmente divididos em 2, 3, 4, 6 ou 12 partições (um ID de partição horizontal por partição).

### <a name="remove-replicas"></a>Remover as réplicas
Após os períodos de volumes de consulta elevado, pode reduzir as réplicas depois de cargas de consulta de pesquisa tem normalizado (por exemplo, depois de vendas feriado são).

Para tal, regresse o controlo de deslize de réplica para um número inferior. Não existem não existem passos adicionais necessários da sua parte. Reduzir a contagem de réplica relinquishes máquinas virtuais no Centro de dados. As operações de ingestão de dados e a consulta irão agora ser executada em VMs menos que antes. O limite mínimo é uma réplica.

### <a name="remove-partitions"></a>Remova partições
Em contraste com réplicas, a remover que não necessita de nenhum esforço adicional da sua parte, poderá ter alguns cálculos para fazer se estiver a utilizar o armazenamento mais do que pode ser reduzido. Por exemplo, se a sua solução utilizar três partições, downsizing para uma ou duas partições irá gerar um erro se o novo espaço de armazenamento é menor que o necessário. Como seria de esperar, as suas escolhas estão a eliminar índices ou documentos dentro de um índice associado para libertar espaço ou manter a configuração atual.

Não há nenhum método de deteção que indica que shards índice são armazenados em partições específicas. Cada partição fornece cerca de 25 GB no armazenamento, por isso terá de reduzir o armazenamento para um tamanho que pode ser acomodado pelo número de partições que tem. Se pretender reverter para uma partição, todos os 12 shards terá de ajustar.

Para ajudar a planear futuros, pode querer verifique armazenamento (utilizando [obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver a quantidade realmente utilizado. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Melhores práticas em escala e implementação
Este vídeo de 30 minutos revê as melhores práticas para cenários de implementação avançadas, incluindo geo-distribuição cargas de trabalho. Também pode ver [desempenho e a Otimização da Azure Search](search-performance-optimization.md) para páginas de ajuda que abrangem os mesmos pontos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passos Seguintes
Assim que compreender os conceitos atrás de administração do serviço, considere a utilização de [PowerShell](search-manage-powershell.md) para automatizar tarefas.

Também recomendamos que reveja o [desempenho e a otimização de artigo](search-performance-optimization.md).

Outra recomenda-se ver o vídeo indicado na secção anterior. Fornece mais profunda cobertura de técnicas mencionados nesta secção.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



