---
title: Criar, alterar ou eliminar um endereço IP público do Azure | Microsoft Docs
description: Saiba como criar, alterar ou eliminar um endereço IP público.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 7319fea96f0592b9a20a591ad603d575e440704d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2018
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Criar, alterar ou eliminar um endereço IP público

Saiba mais sobre um endereço IP público e como criar, alterar e eliminar um. Um endereço IP público é um recurso com as suas próprias definições configuráveis. Atribuir um endereço IP público a outros recursos do Azure permite:
- Entrada de ligação à Internet para recursos, tais como máquinas virtuais do Azure, conjuntos de dimensionamento de Máquina Virtual do Azure, o Gateway de VPN do Azure, gateways de aplicação e Balanceadores de carga do Azure para a Internet. Recursos do Azure não consegue receber comunicação de entrada a partir da Internet sem um endereço IP público atribuído. Embora alguns recursos do Azure são inerentemente acessíveis através de endereços IP públicos, outros recursos tem de ter os endereços IP públicos atribuídos esteja acessível a partir da Internet.
- Conectividade de saída à Internet através de um endereço IP previsível. Por exemplo, uma máquina virtual podem comunicar atribuída saída à Internet sem um endereço IP público, mas o respetivo endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível. Atribuir um endereço IP público para um recurso permite-lhe saber qual o endereço IP é utilizado para a ligação de saída. Embora previsível, pode alterar o endereço, consoante o método de atribuição escolhido. Para obter mais informações, consulte [criar um endereço IP público](#create-a-public-ip-address). Para saber mais sobre ligações de saída a partir dos recursos do Azure, leia o [compreender ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo.

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Se ainda não tiver uma conta do Azure, inscreva-se um [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar o portal, abra https://portal.azure.come inicie sessão com a sua conta do Azure.
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, quer executar os comandos [Shell de nuvem do Azure](https://shell.azure.com/powershell), ou através da execução do PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Este tutorial requer o Azure PowerShell versão do módulo 5.2.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzureRmAccount` para criar uma ligação com o Azure.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, quer executar os comandos [Shell de nuvem do Azure](https://shell.azure.com/bash), ou executando a CLI do seu computador. Este tutorial requer a CLI do Azure versão 2.0.26 ou posterior. Execute `az --version` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). Se estiver a executar localmente a CLI do Azure, também terá de executar `az login` para criar uma ligação com o Azure.

Endereços IP públicos têm uma cobrança nominal. Para ver os preços, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. 

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *endereço ip público*. Quando **endereços IP públicos** é apresentado nos resultados da pesquisa, clique no mesmo.
2. Clique em **+ adicionar** no **endereço IP público** painel que aparece.
3. Introduza ou selecione os valores para as seguintes definições no **Criar endereço IP público** painel apresentado, clique em **criar**:

    |Definição|Necessário?|Detalhes|
    |---|---|---|
    |SKU|Sim|Os endereços IP públicos todos os criado antes da introdução de SKUs **básico** os endereços IP públicos do SKU.  Não é possível alterar o SKU depois de criado o endereço IP público. A máquina virtual autónoma, máquinas virtuais dentro de um conjunto de disponibilidade ou conjuntos de dimensionamento de máquina virtual pode utilizar básico ou padrão SKUs.  Não é permitida a mistura de SKUs entre máquinas virtuais dentro de conjuntos de disponibilidade ou conjuntos de dimensionamento. **Básico** SKU: Se estiver a criar um endereço IP público numa região que suporte zonas de disponibilidade, o **zona disponibilidade** definição está definida como *nenhum* por predefinição. Pode optar por selecionar uma zona de disponibilidade para garantir uma zona específica para o seu endereço IP público. **Standard** SKU: A Standard SKU um IP público pode ser associado a uma máquina virtual ou uma carga balanceador front-end. Se estiver a criar um endereço IP público numa região que suporte zonas de disponibilidade, o **zona disponibilidade** definição está definida como *zona redundante* por predefinição. Para mais informações sobre as zonas de disponibilidade, consulte o **zona disponibilidade** definição. O SKU standard é necessário se associar o endereço para um balanceador de carga padrão. Para obter mais informações sobre balanceadores de carga padrão, consulte o artigo [Balanceador de carga do Azure standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.|
    |Nome|Sim|O nome tem de ser exclusivo no grupo de recursos que selecionar.|
    |Versão do IP|Sim| Selecione IPv4 ou IPv6. Enquanto endereços IPv4 públicos podem ser atribuídos a vários recursos do Azure, um endereço IP público IPv6 só pode ser atribuído a um balanceador de carga para a Internet. O Balanceador de carga pode equilibrar o tráfego de IPv6 para máquinas virtuais do Azure. Saiba mais sobre [tráfego de IPv6 para máquinas virtuais de balanceamento de carga](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se tiver selecionado o **Standard SKU**, não tem a opção para selecionar *IPv6*. Só pode criar um endereço IPv4 ao utilizar o **Standard SKU**.|
    |Atribuição de endereços IP|Sim|**Dynamic:** endereços dinâmicos são atribuídos apenas depois do endereço IP público está associado a uma interface de rede ligada a uma máquina virtual e a máquina virtual é iniciada pela primeira vez. Podem alterar a endereços dinâmicos se a máquina virtual, que a interface de rede está ligada a está parada (desalocada). O endereço permanece o mesmo se a máquina virtual é reiniciada ou parada (mas não desalocada). **Estática:** endereços estáticos são atribuídos quando for criado o endereço IP público. Endereços estáticos não são alterados, mesmo se a máquina virtual é colocada no estado parado (desalocado). O endereço é libertado apenas quando a interface de rede é eliminada. Pode alterar o método de atribuição depois de criar a interface de rede. Se selecionar *IPv6* para o **versão do IP**, o método de atribuição é *dinâmica*. Se selecionar *padrão* para **SKU**, o método de atribuição é *estático*.|
    |Tempo limite de inatividade (minutos)|Não|O número de minutos para manter uma ligação de TCP ou HTTP aberta sem depender de clientes para enviar mensagens keep-alive. Se selecionar IPv6 para **IP versão**, este valor não pode ser alterado. |
    |Etiqueta de nome DNS|Não|Tem de ser exclusivos numa localização do Azure que crie o nome de no (em todas as subscrições e todos os clientes). Azure regista automaticamente o nome e endereço IP no seu DNS para que possa ligar a um recurso com o nome. Azure acrescenta uma sub-rede predefinida como *location.cloudapp.azure.com* (em que a localização é a localização que selecionar) para o nome de fornecer, para criar o nome DNS completamente qualificado. Se optar por criar ambas as versões de endereço, é atribuído o mesmo nome DNS para endereços IPv4 e IPv6. Predefinição do Azure DNS contém registos de nome de um de IPv4 e IPv6 AAAA e responde com ambos os registos quando o nome DNS está a ser consultado. O cliente escolhe qual o endereço (IPv4 ou IPv6) para comunicar com. Em vez de, ou além de, utilizar a etiqueta de nome DNS com o sufixo predefinido, pode utilizar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que é resolvido para o endereço IP público. Para obter mais informações, veja [Utilizar o DNS do Azure com um endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Criar um endereço IPv6 (ou IPv4)|Não| Indica se é apresentado o IPv6 ou IPv4 está dependente aquilo que selecionar para **IP versão**. Por exemplo, se selecionar **IPv4** para **IP versão**, **IPv6** é apresentado aqui. Se selecionar *padrão* para **SKU**, não tem a opção para criar um endereço IPv6.
    |Nome (apenas visível se tiver selecionado o **criar um endereço IPv6 (ou IPv4)** caixa de verificação)|Sim, se selecionar a **criar IPv6** (ou IPv4) caixa de verificação.|O nome tem de ser diferente do nome introduzido para o primeiro **nome** nesta lista. Se optar por criar um endereço IPv6 e IPv4, o portal cria dois separados endereço recursos do IP público, uma com cada versão do endereço IP atribuído ao mesmo.|
    |Atribuição de endereços IP (apenas visível se tiver selecionado o **criar um endereço IPv6 (ou IPv4)** caixa de verificação)|Sim, se selecionar a **criar IPv6** (ou IPv4) caixa de verificação.|Se a caixa de verificação indica **criar um endereço IPv4**, pode selecionar um método de atribuição. Se a caixa de verificação indica **criar um endereço IPv6**, não é possível selecionar um método de atribuição, como deve ser **dinâmica**.|
    |Subscrição|Sim|Tem de existir na mesma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) como recurso que pretende associar o endereço IP público para.|
    |Grupo de recursos|Sim|Pode existir no mesmo ou outro, [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) como recurso que pretende associar o endereço IP público para.|
    |Localização|Sim|Tem de existir na mesma [localização](https://azure.microsoft.com/regions), também referido como região, como o recurso que pretende associar o IP público de endereços para.|
    |Zona de disponibilidade| Não | Esta definição só é apresentada se selecionar uma localização suportada. Para obter uma lista de localizações suportadas, consulte [descrição geral de zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se tiver selecionado o **básico** SKU, *nenhum* é selecionado automaticamente para si. Se preferir garantir uma zona específica, pode selecionar uma zona específica. A opção não é com redundância de zona. Se tiver selecionado o **padrão** SKU: com redundância de zona é selecionado automaticamente para si e faz com que o caminho de dados resilientes a falhas de zona. Se preferir garantir uma zona específica, que não seja resistente a falhas de zona, pode selecionar uma zona específica.
  

**Comandos**

Embora o portal fornece a opção para criar dois endereços recursos do IP público (IPv4 e um IPv6), os seguintes comandos do PowerShell e CLI criam um recurso com um endereço para uma versão IP ou o outro. Se pretender que os dois endereços recursos do IP público, uma para cada versão do IP, tem de executar duas vezes, o comando especificando diferentes nomes e versões para os recursos de endereço IP públicos. 

|Ferramenta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Ver, alterar as definições ou eliminar um endereço IP público

1. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *endereço ip público*. Quando **endereços IP públicos** é apresentado nos resultados da pesquisa, clique no mesmo.
2. No **endereços IP públicos** painel apresentado, clique no nome do endereço IP público que pretende ver, alterar as definições ou eliminar.
3. No painel que aparece para o endereço IP público, conclua uma das seguintes opções consoante se pretende ver, eliminar ou alterar o endereço IP público.
    - **Vista**: O **descrição geral** secção do painel mostra as definições de chaves para o endereço IP público, como a interface de rede que está associado (se o endereço está associado a uma interface de rede). O portal não apresenta a versão do endereço (IPv4 ou IPv6). Para ver as informações de versão, utilize o comando do PowerShell ou a CLI para ver o endereço IP público. Se a versão do endereço IP for IPv6, o endereço atribuído não é apresentado no portal, PowerShell ou a CLI. 
    - **Eliminar**: para eliminar o endereço IP público, clique em **eliminar** no **descrição geral** secção do painel. Se o endereço está atualmente associado a uma configuração de IP, não pode ser eliminada. Se o endereço está atualmente associado uma configuração, clique em **desassocie** ao desassociar o endereço da configuração de IP.
    - **Alteração**: clique em **configuração**. Alterar as definições utilizando as informações no passo 4 do [criar um endereço IP público](#create-a-public-ip-address) secção deste artigo. Para alterar a atribuição de para um endereço IPv4 estático para dinâmica, primeiro tem de dissociá o endereço IPv4 público da configuração de IP que está associado. Em seguida, pode alterar o método de atribuição para dinâmica e clique em **associar** para associar o IP de endereço para a mesma configuração de IP, uma configuração diferente ou pode deixar desassociada. Ao desassociar a um endereço IP público, no **descrição geral** secção, clique em **desassocie**.

>[!WARNING]
>Quando alterar o método de atribuição de estático em dinâmico, perderá o endereço IP que foi atribuído para o endereço IP público. Embora os servidores DNS públicos do Azure manter um mapeamento entre os endereços estáticos ou dinâmicos e qualquer etiqueta de nome DNS (se for definido um), um endereço IP dinâmico pode ser alteradas quando a máquina virtual é iniciada após no estado parado (desalocado). Para impedir que o endereço de alteração, atribua um endereço IP estático.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[lista de ip público de rede AZ](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_list) para lista os endereços IP públicos, [az rede pública-ip-mostrar](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_show) para mostrar as definições; [atualização de ip público de rede az](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_update) atualizar; [az público-ip da rede eliminar](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_delete) eliminar|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter um objeto de endereço IP público e ver as respetivas definições, [conjunto AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) ao atualizar as definições; [Remover AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) eliminar|

## <a name="next-steps"></a>Passos Seguintes
Atribua endereços IP públicos quando criar os seguintes recursos do Azure:

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais
- [Balanceador de carga do Azure para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de aplicação do Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ligação site a site utilizando um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de dimensionamento da Máquina Virtual do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
