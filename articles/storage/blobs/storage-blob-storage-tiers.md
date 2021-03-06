---
title: Armazenamento frequente, esporádico e de arquivos para blobs | Microsoft Docs
description: Armazenamento frequente, esporádico e de arquivos para contas de Armazenamento do Azure.
services: storage
documentationcenter: ''
author: kuhussai
manager: jwillis
editor: ''
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: c62f3a92e6199f6467556054c9f58c20b6ceba2c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Armazenamento de Blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivos

## <a name="overview"></a>Descrição geral

O Armazenamento do Azure disponibiliza três camadas de armazenamento para o Armazenamento de objetos de blobs, para que possa armazenar os seus dados de forma mais económica consoante o modo como os utiliza. A **camada de armazenamento de acesso frequente** do Azure está otimizada para armazenar dados que são acedidos com frequência. A **camada de armazenamento de acesso esporádico** do Azure está otimizada para armazenar dados que são acedidos com pouca frequência e armazenados durante 30 dias, pelo menos. A **camada de armazenamento de arquivos** do Azure está otimizada para armazenar dados que são raramente acedidos e armazenados durante, pelo menos, 180 dias, com requisitos de latência flexíveis (na ordem das horas). Este armazenamento só está disponível ao nível do blob e não ao nível da conta de armazenamento. Os dados na camada de armazenamento de acesso esporádico podem tolerar disponibilidade ligeiramente inferior, mas ainda requerem uma durabilidade elevada, bem como tempo de acesso e características de débito semelhantes aos dados de acesso frequente. Para os dados de acesso esporádico, um SLA de disponibilidade ligeiramente inferior e custos de acesso superiores comparados com frequentes são compromissos aceitáveis em troca de custos de armazenamento inferiores. O armazenamento de arquivos está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais elevados. Apenas os níveis de armazenamento frequente e esporádico (não arquivo) podem ser definidos ao nível de conta. Todos os três níveis podem ser definidos ao nível de objeto.

Atualmente, os dados armazenados na nuvem estão a crescer a um ritmo exponencial. Para gerir os custos das suas necessidades de armazenamento em expansão, é recomendável organizar os dados com base em atributos como a frequência de acesso e o período de retenção planeado para otimizar os custos. Os dados armazenados na nuvem podem ser diferentes em termos da forma como são gerados, processados e acedidos ao longo da respetiva duração. Alguns dados são ativamente acedidos e modificados durante o seu ciclo de vida. Alguns dados são acedidos frequentemente no início da sua vida, mas o acesso diminui significativamente à medida que a sua idade aumenta. Alguns dados permanecem inativos na nuvem e são raramente acedidos após serem armazenados ou não são acedidos de todo.

Cada um destes cenários de acesso a dados beneficia de uma camada de armazenamento diferente, otimizada para um padrão de acesso específico. Com as camadas de armazenamento frequente, esporádico e de arquivo, o armazenamento de Blobs do Azure dá resposta à necessidade de ter camadas de armazenamento diferenciadas com modelos de preços distintos.

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que suportam camadas

Só pode incluir os seus dados de armazenamento de objetos à camada frequente, esporádica ou de arquivos em contas de Armazenamento de Blobs ou de Fins Gerais v2 (GPv2). As contas de Fins Gerais (GPv1) não suportam as camadas. No entanto, os clientes podem converter facilmente as respetivas contas GPv1 ou de Armazenamento de Blobs existentes em contas GPv2 através de um processo de um só clique no portal do Azure. GPv2 proporciona uma nova estrutura de preço para blobs, ficheiros e filas, bem como acesso a uma variedade de outras funcionalidades de armazenamento novas. Além disso, no futuro, algumas funcionalidades e reduções de preços só estarão disponíveis nas contas GPv2. Por este motivo, os clientes devem considerar a utilização destas contas, mas utilizá-las apenas depois de analisarem os preços de todos os serviços, pois algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para saber mais, veja [Azure storage account options](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Opções de contas de armazenamento do Azure).

As contas GPv2 e de Armazenamento de Blobs expõem o atributo **Access Tier** (Camada de Acesso) ao nível da conta, que lhe permite especificar a camada de armazenamento predefinida como frequente ou esporádica para qualquer blob na conta de armazenamento que não tenha a camada definida ao nível do objeto. Relativamente aos objetos cuja camada está definida ao nível do objeto, a camada da conta não se aplicará. A camada de arquivo só pode ser aplicada ao nível do blob. Pode alternar entre estas camadas de armazenamento em qualquer altura.

## <a name="hot-access-tier"></a>Escalão de acesso frequente

Os custos de armazenamento do armazenamento frequente são superiores aos dos armazenamentos de arquivo e esporádico, mas os custos de acesso são mais baixos. Os exemplos de cenários de utilização da camada de armazenamento frequente incluem:

* Dados que estão a ser utilizados ativamente ou que deverão ser acedidos (para operações de leitura e escrita) com frequência.
* Dados preparados para processamento e eventual migração para a camada de armazenamento de acesso esporádico.

## <a name="cool-access-tier"></a>Escalão de acesso esporádico

A camada de armazenamento esporádico tem custos de armazenamento inferiores e custos de acesso superiores em comparação com armazenamento frequente. Esta camada destina-se a dados que permanecem na camada de acesso esporádico durante, pelo menos, 30 dias. Os exemplos de cenários de utilização da camada de armazenamento esporádico incluem:

* Conjuntos de dados de cópia de segurança e recuperação após desastre de curto prazo.
* Conteúdo de multimédia mais antigo que já não é visualizado com frequência, mas que deverá estar disponível de imediato quando acedido.
* Grandes conjuntos de dados que devem ser armazenados de forma económica enquanto são recolhidos mais dados para processamento futuro. (*Por exemplo,*, armazenamento a longo prazo de dados científicos, dados de telemetria não processados de uma instalação de fabrico)

## <a name="archive-access-tier"></a>Camada de acesso de arquivo

O armazenamento de arquivo tem o custo de armazenamento mais baixo e os custos de obtenção de dados mais altos em comparação com o armazenamento frequente e esporádico. Esta camada destina-se a dados que podem tolerar várias horas de latência de obtenção e que vão permanecer na camada de arquivo, pelo menos, 180 dias.

Enquanto um blob está num armazenamento de arquivo, está offline e não pode ser lido (exceto os metadados, que estão online e disponíveis), copiado, substituído ou modificado. Do mesmo modo, também não pode criar instantâneos de blobs no armazenamento de arquivo. Contudo, pode utilizar operações existentes para eliminar, listar, obter propriedades/metadados de blobs ou alterar a camada dos seus blobs.

Os exemplos de cenários de utilização da camada de armazenamento de arquivo incluem:

* Conjuntos de dados de arquivo, cópia de segurança secundária e cópia de segurança a longo prazo
* Dados originais (não processados) que têm de ser preservados, mesmo depois de terem sido processados para a forma utilizável final. (*Por exemplo,*, ficheiros de multimédia não processados após a transcodificação noutros formatos)
* Dados de conformidade e arquivo que têm ser armazenados durante muito tempo e que raramente são acedidos. (*Por exemplo,*, filmagens de câmaras de segurança, raios X/RMs antigos para organizações de cuidados de saúde, gravações de áudio e transcrições de chamadas dos clientes para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blobs
Para ler os dados no armazenamento de arquivo, tem de, primeiro, alterar a camada do blob para frequente ou esporádica. Este processo é conhecido como “reidratação” e pode demorar até 15 horas a ser concluído. Recomendam-se vivamente tamanhos de blobs grandes para um desempenho ótimo. “Reidratar” vários pequenos blobs em simultâneo pode provocar mais atrasos.

Durante a “reidratação”, pode verificar a propriedade **Archive Status** do blob para confirmar se a camada foi alterada. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “archive status” do blob é removida e a propriedade **Access Tier** reflete a camada frequente ou esporádica nova.  

## <a name="blob-level-tiering"></a>Camadas ao nível do blob

As camadas ao nível do blob permitem-lhe alterar a camada dos seus dados ao nível do objeto através de uma operação individual, chamada [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Pode alterar facilmente a camada de acesso de um blob de entre as camadas frequente, esporádica ou de arquivo à medida que os padrões de utilização se modificam, sem ter de mover dados entre contas. Todas as alterações às camadas entram em vigor imediatamente, exceto se um blob estiver a ser reidratado do arquivo, o que pode demorar várias horas. A hora da última alteração da camada de blob é exposta através do atributo **Access Tier Change Time** (Tempo de Alteração da Camada de Acesso) nas propriedades do blob. Se um blob estiver na camada de arquivo, não pode ser substituído e, por conseguinte, não é permitido carregar o mesmo blob neste cenário. Pode substituir um blob no armazenamento frequente e esporádico e, neste caso, o blob novo herda a camada do blob antigo que foi substituído.

Podem coexistir na mesma conta blobs nas três camadas de armazenamento. Um blob que não tenha uma camada atribuída explicitamente infere a camada da definição da camada de acesso da conta. Se a camada de acesso for inferida a partir da conta, pode ver que a propriedade **Access Tier Inferred** (Camada de Acesso Inferida) do blob está definida como "true" e que a propriedade **Access Tier** do blob corresponde à camada da conta. No portal do Azure, a propriedade da camada de acesso inferido é apresentada com a camada de acesso do blob (por exemplo, acesso frequente (inferido) ou esporádico (inferido)).

> [!NOTE]
> O armazenamento de arquivo e a criação de camadas ao nível de blobs suportam apenas blobs de blocos. Também não é possível alterar a camada de um blob de bloco que tem instantâneos.

### <a name="blob-level-tiering-billing"></a>Faturação da criação de camadas ao nível de blobs

Quando um blob é movido para uma camada mais esporádica (frequente->esporádica, frequente->arquivos ou esporádica->arquivos), a operação é faturada como uma operação de escrita da camada de destino na qual a operação de escrita (por 10 000) e os encargos de escrita de dados (por GB) da camada de destino são aplicáveis. Quando um blob é movido para uma camada mais frequente (arquivo->esporádica, arquivo->frequente ou esporádica->frequente), a operação é faturada como uma leitura da camada de origem onde a operação de leitura (por 10 000) e os encargos de obtenção de dados (por GB) da camada de origem são aplicáveis.

Se mudar a camada da conta de frequente para esporádica, são-lhe cobradas operações de escrita (por 10 000) para todos os blobs que não tenham uma camada definida em contas GPv2 apenas. Estas cobranças não acontecem nas contas de Armazenamento de Blobs. Se mudar a conta GPv2 ou de Armazenamento de Blobs de esporádica para frequente, são-lhe cobradas as operações de escrita (por 10 000) e obtenção de dados (por GB). Podem também aplicar-se cobranças com deteções precoces para qualquer blob que seja retirado da camada esporádica ou de arquivo.

### <a name="cool-and-archive-early-deletion"></a>Eliminação precoce de blobs de acesso esporádico e de arquivo

Para além dos custos por mês por GB, qualquer blob que seja movido para a camada esporádica (apenas contas GPv2) está sujeito a um período de 30 dias de eliminação precoce esporádica e qualquer blob que seja incluído na camada de arquivos está sujeito a um período de 180 dias de eliminação precoce de arquivos. Estes custos são rateados. Por exemplo, se um blob for movido para arquivo e, em seguida, for eliminado ou movido para a camada frequente após 45 dias, é-lhe cobrada uma tarifa de eliminação precoce equivalente a 135 (180 menos 45) dias de armazenamento desse blob no arquivo.

## <a name="comparison-of-the-storage-tiers"></a>Comparação das camadas de armazenamento

A tabela seguinte mostra uma comparação das camadas de armazenamento frequente, esporádico e de arquivos.

| | **Camada de armazenamento frequente** | **Camada de armazenamento esporádico** | **Camada de armazenamento de arquivo**
| ---- | ----- | ----- | ----- |
| **Disponibilidade** | 99,9% | 99% | N/D |
| **Disponibilidade** <br> **(leituras RA-GRS)**| 99,99% | 99,9% | N/D |
| **Custos de utilização** | Custos de armazenamento superiores, custos de acesso e transação mais baixos | Custos de armazenamento inferiores, custos de acesso e transação superiores | Custos de armazenamento inferiores, custos de acesso e transação superiores |
| **Tamanho mínimo do objeto** | N/D | N/D | N/D |
| **Duração mínima do armazenamento** | N/D | 30 dias (GPv2 apenas) | 180 dias
| **Latência** <br> **(Tempo até ao primeiro byte)** | milissegundos | milissegundos | < 15 hrs
| **Metas de escalabilidade e desempenho** | Igual às contas do Storage para fins gerais | Igual às contas do Storage para fins gerais | Igual às contas do Storage para fins gerais |

> [!NOTE]
> As contas do Armazenamento de Blobs suportam as mesmas metas de desempenho e escalabilidade que as contas do Storage para fins gerais. Consulte [Metas de Desempenho e Escalabilidade do Storage do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações.

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta secção, são demonstrados os seguintes cenários através do portal do Azure:

* Como alterar a camada de acesso predefinida de uma conta de Armazenamento de Blobs ou GPv2.
* Como alterar a camada de um blob numa conta de Armazenamento de Blobs ou GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso predefinida de uma conta GPv2 ou de Armazenamento de Blobs

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Para navegar para a sua conta de armazenamento, selecione Todos os Recursos, em seguida, selecione a sua conta de armazenamento.

3. No painel Definições, clique em**Configuração** para ver e/ou alterar a configuração da conta.

4. Selecione a camada de armazenamento correta para as suas necessidades: defina a **Camada de acesso** como **Esporádica** ou **Frequente**.

5. Clique em Guardar na parte superior do painel.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar a camada de um blob numa conta GPv2 ou de Armazenamento de Blobs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Para navegar para o blob na conta de armazenamento, selecione Todos os Recursos, selecione a conta de armazenamento, selecione o contentor e, em seguida, selecione o blob.

3. No painel de propriedades do Blob, clique no menu pendente **Camada de acesso** para selecionar a camada de armazenamento **Frequente**, **Esporádica** ou **Arquivo**.

5. Clique em Guardar na parte superior do painel.

## <a name="faq"></a>FAQ

**Posso utilizar contas de Armazenamento de Blobs ou GPv2 se quiser adicionar os meus dados a uma camada?**

Para adicionar a camadas, recomendamos que utilize uma conta GPv2 em vez de uma conta de Armazenamento de Blobs. As contas GPv2 suportam todas as funcionalidades que as contas de Armazenamento de Blobs suportam e muitas mais. Os preços entre ambas são quase idênticos, mas algumas funcionalidades e reduções de preços só estarão disponíveis nas GPv2. As contas GPv1 não suportam as camadas.

A estrutura de preços entre as contas GPv1 e GPv2 são diferentes e os clientes devem analisá-las cuidadosamente antes de optarem pelas GPv2. Pode converter facilmente uma conta de Armazenamento de Blobs ou GPv1 existente em GPv2 através de um processo de um só clique simples. Para saber mais, veja [Azure storage account options](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Opções de contas de armazenamento do Azure).

**Posso armazenar objetos nas três camadas de armazenamento (frequente, esporádica e de arquivo) na mesma conta?**

Sim. O atributo **Access Tier** definido ao nível de uma conta é a camada predefinida que se aplica a todos os objetos nessa conta sem uma camada de conjunto explícita. No entanto, a criação de camadas ao nível do blob permite-lhe definir a camada de acesso ao nível do objeto, independentemente de qual seja a definição de camada de acesso da conta. Os blobs em qualquer uma das três camadas de armazenamento (frequente, esporádico ou arquivo) podem existir na mesma conta.

**Posso alterar a camada de armazenamento da minha conta de Armazenamento de Blobs ou GPv2?**

Sim, pode definir o atributo **Access Tier** na conta de armazenamento para alterar a camada de armazenamento predefinida. A alteração da camada de armazenamento aplica-se a todos os objetos armazenados na conta que não têm uma camada explícita definida. A alteração da camada de armazenamento de frequente para esporádica implica operações de escrita (por 10 000) para todos os blobs sem uma camada definida apenas em contas GPv2 e a alteração de esporádica para frequente implica custos de operações de leitura (por 10 000) e obtenção de dados (por GB) para todos os blobs em contas de Armazenamento de Blobs e GPv2.

**Posso definir a camada de acesso de conta predefinida como arquivo?**

Não. Apenas as camadas de armazenamento frequente e esporádico podem ser definidas como a camada de acesso de conta predefinida. A camada de arquivo só pode ser definida ao nível do objeto.

**Em que regiões estão disponíveis as camadas de armazenamento frequente, esporádico e de arquivo?**

As camadas de armazenamento frequente e esporádico, juntamente com a atribuição de camadas ao nível de blobs, estão disponíveis em todas as regiões. Inicialmente, o armazenamento de arquivo só estará disponível em determinadas regiões. Para obter uma lista completa, veja [Produtos do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).

**O comportamento dos blobs na camada de armazenamento esporádico é diferente dos blobs na camada de armazenamento frequente?**

Os blobs na camada de armazenamento frequente têm a mesma latência dos blobs nas contas de Armazenamento de Blobs, GPv1 e GPv2. Os blobs na camada de armazenamento esporádico têm uma latência semelhante (em milissegundos) aos blobs nas contas de Armazenamento de Blobs, GPv1 e GPv2. Os blobs na camada de armazenamento de arquivo têm várias horas latência nas contas de Armazenamento de Blobs, GPv1 e GPv2.

Os blobs na camada de armazenamento esporádico têm um nível de serviço (SLA) de disponibilidade ligeiramente inferior ao dos blobs armazenados na camada de armazenamento frequente. Para obter mais detalhes, consulte [SLA para o armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádica e de arquivo são iguais?**

Sim. Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações de arquivos válidas, incluindo “delete”, “list”, “get blob properties/metadata” e “set blob tier”, são 100% consistentes com as camadas frequente e esporádica. Os blobs não podem ser lidos nem modificados enquanto estiverem na camada de arquivo.

**Como posso saber que a reidratação de um blob a partir da camada de arquivo para a camada frequente ou esporádica foi concluída?**

Durante a reidratação, pode utilizar a operação “get blob properties” para consultar o atributo **Archive Status** e verificar se a alteração de camada já está concluída. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade “archive status” do blob é removida e a propriedade **Access Tier** reflete a camada frequente ou esporádica nova.  

**Depois de definir a camada de um blob, quando é que me começa a ser faturada a taxa adequada?**

Cada blob é sempre faturado de acordo com a camada indicada pela propriedade **Access Tier**. Ao definir uma camada nova num blob, a propriedade **Access Tier** refletirá imediatamente a camada nova para todas as transições, exceto em caso de “desidratação” de um blob da camada de arquivo para frequente ou esporádica, o que pode demorar várias horas. Neste caso, continua a ser faturado às tarifas de arquivo até que a “reidratação” esteja concluída, altura em que **Access Tier** passará a refletir a camada nova. Só então começa a ser faturado à tarifa nova de camada frequente ou esporádica.

**Como posso saber se me vai ser faturada uma cobrança de eliminação precoce quando eliminar ou retirar um blob da camada esporádica ou de arquivo?**

Qualquer blob que seja eliminado ou retirado da camada de acesso esporádico (contas GPv2 apenas) ou de arquivo antes de terem decorrido 30 e 180 dias, respetivamente, irá incorrer numa cobrança por eliminação precoce rateada. Para saber há quanto tempo é que um blob está na camada esporádica ou de arquivo, veja a respetiva propriedade **Access Tier Time** (Tempo na Camada de Acesso), que mostra um carimbo de data/hora da última alteração à camada. Veja a secção [Cool and archive early deletion](#cool-and-archive-early-deletion) (Eliminação precoce na camada esporádica e de arquivo) para obter mais detalhes.

**Que ferramentas e SDKs do Azure suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivo?**

O portal, o PowerShell e as ferramentas da CLI do Azure e as bibliotecas de.NET, Java, Python e Node.js suportam a atribuição de camadas ao nível do blob e o armazenamento de arquivos.  

**Quantos dados posso armazenar nas camadas frequente, esporádica e de arquivo?**

O armazenamento de dados, juntamente com outros limites, é definido ao nível da conta e não de camada de armazenamento. Por conseguinte, pode optar por utilizar todo o seu limite numa só camada ou nas três. Consulte [Metas de Desempenho e Escalabilidade do Storage do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Avaliar as camadas frequente, esporádica e de arquivo nas contas GPv2 e de Armazenamento de Blobs

[Verificar a disponibilidade das camadas frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)

[Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
