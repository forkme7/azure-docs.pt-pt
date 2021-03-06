---
title: Associar o tempo de execução de integração de SSIS do Azure a uma rede virtual | Microsoft Docs
description: Saiba como associar o tempo de execução de integração de SSIS do Azure a uma rede virtual do Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 2372b6bd91dfb1c33456b42e91aa2496532796ef
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Aderir a um tempo de execução de integração do Azure-SSIS a uma rede virtual
Associe o seu tempo de execução de integração do Azure-SSIS (IR) a uma rede virtual do Azure nos seguintes cenários: 

- Estão a alojar o SQL Server Integration Services (SSIS) catálogo de dados no Azure instância SQL Server da base de dados geridos (pré-visualização) numa rede virtual.
- Pretende ligar a arquivos de dados no local a partir de pacotes de SSIS em execução no integration runtime do Azure-SSIS.

 O Azure Data Factory versão 2 (pré-visualização) permite-lhe associar o seu tempo de execução de integração do Azure SSIS para uma rede virtual criadas através do modelo de implementação clássica ou o modelo de implementação Azure Resource Manager. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que se encontra na disponibilidade geral (DG), consulte o [documentação da versão 1 de fábrica de dados](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Acesso aos arquivos de dados no local
Se acedem a pacotes SSIS arquivos de dados de nuvem pública apenas, não precisa de associar a resposta a incidentes SSIS do Azure a uma rede virtual. Se acedem a pacotes SSIS arquivos de dados no local, tem de associar a resposta a incidentes SSIS do Azure a uma rede virtual que está ligada à rede no local. 

Se o catálogo do SSIS é alojado numa instância de SQL Database do Azure que não está na rede virtual, terá de abrir as portas adequadas. 

Se o catálogo SSIS está alojado na instância SQL Server da base de dados geridos (pré-visualização) numa rede virtual, pode associar uma resposta a incidentes SSIS do Azure para:

- A mesma rede virtual.
- Uma rede virtual diferente que tenha uma ligação de rede de rede com um que tenha a instância SQL Server da base de dados geridos (pré-visualização). 

A rede virtual pode ser implementada através do modelo de implementação clássico ou modelo de implementação Azure Resource Manager. Se estiver a planear para associar a resposta a incidentes SSIS do Azure *mesma rede virtual* que tenha a instância SQL Server da base de dados geridos (pré-visualização), certifique-se de que a resposta a incidentes SSIS do Azure está a ser um *outra sub-rede* daquele que tenha o SQL Server Instância gerido da base de dados (pré-visualização).   

As secções seguintes fornecem mais detalhes.

Eis alguns pontos importantes a ter em atenção: 

- Se não houver nenhuma existente rede virtual ligada à sua rede no local, primeiro crie um [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) ou um [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para a integração do Azure-SSIS tempo de execução para associar. Em seguida, configure um site para site [ligação de gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) ou [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) ligação de rede virtual à sua rede no local.
- Se existir um existente do Azure Resource Manager ou a rede virtual clássica ligados à sua rede no local na mesma localização que a sua resposta a incidentes SSIS do Azure, pode associar a IR nessa rede virtual.
- Se existir uma rede virtual clássica existente ligada à sua rede no local numa localização diferente da sua resposta a incidentes SSIS do Azure, pode criar primeiro um [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para a sua resposta a incidentes SSIS do Azure associar. Em seguida, configure um [rede virtual clássica para clássica](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) ligação. Ou pode criar um [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para o tempo de execução de integração do Azure-SSIS associar. Em seguida, configure um [rede virtual do clássico para o Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) ligação.
- Se existir um existente do Azure Resource Manager rede virtual ligada à sua rede no local numa localização diferente da sua resposta a incidentes SSIS do Azure, pode criar primeiro um [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) para sua SSIS do Azure IR para associar. Em seguida, configure uma ligação de rede virtual do Azure Resource Manager para o Azure Resource Manager. Em alternativa, pode criar um [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para a sua resposta a incidentes SSIS do Azure associar. Em seguida, configure um [rede virtual do clássico para o Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) ligação.

## <a name="domain-name-services-server"></a>Servidor de serviços de nome de domínio 
Se precisar de utilizar o seu próprio servidor de serviços de nomes de domínio (DNS) numa rede virtual associada ao seu tempo de execução de integração de SSIS do Azure, siga as instruções na secção "Resolução de nomes que utiliza o seu próprio servidor DNS" do artigo [resolução de nomes máquinas virtuais e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="network-security-group"></a>Grupo de segurança de rede
Se precisar de implementar um grupo de segurança de rede (NSG) numa rede virtual associada ao seu tempo de execução de integração do Azure SSIS, permitir o tráfego de entrada/saída através das seguintes portas:

| Portas | Direção | Protocolo de transporte | Objetivo | Destino origem entrada/saída |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (se a associação a IR para uma rede virtual clássica)<br/><br/>29876, 29877 (se a associação a IR para uma rede virtual do Azure Resource Manager) | Entrada | TCP | Serviços do Azure utilizam estas portas para comunicar com os nós do seu tempo de execução de integração do Azure-SSIS na rede virtual. | Internet | 
| 443 | Saída | TCP | Os nós do seu tempo de execução de integração do Azure-SSIS na rede virtual utilizam esta porta para aceder aos serviços do Azure, como o Storage do Azure e Event Hubs do Azure. | Internet | 
| 1433<br/>11000-11999<br/>14000-14999  | Saída | TCP | Os nós do seu tempo de execução de integração do Azure-SSIS na rede virtual utilizam estas portas para aceder a SSISDB alojada pelo seu servidor de SQL Database do Azure (este objetivo não é aplicável a SSISDB alojada pela instância SQL Server da base de dados geridos (pré-visualização).) | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (IU da fábrica de dados)
Esta secção mostra como associar um tempo de execução do Azure-SSIS existente a uma rede virtual (clássico ou do Azure Resource Manager) utilizando o portal do Azure e a IU da fábrica de dados. Em primeiro lugar, terá de configurar a rede virtual corretamente antes de a associar a sua resposta a incidentes SSIS do Azure ao mesmo. Passar por uma das duas secções seguintes consoante o tipo da sua rede virtual (clássico ou do Azure Resource Manager). Em seguida, prossiga com a secção terceira para associar a sua resposta a incidentes SSIS do Azure para a rede virtual. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Utilizar o portal para configurar uma rede virtual clássica
Terá de configurar uma rede virtual antes de pode associar uma resposta a incidentes SSIS do Azure ao mesmo.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a IU da fábrica de dados é suportada apenas em browsers estes web.
2. Inicie sessão no [portal do Azure](https://portal.azure.com).
3. Selecione **mais serviços**. Filtrar e selecione **redes virtuais (clássicas)**.
4. Filtrar e selecione a rede virtual na lista. 
5. No **rede Virtual (clássica)** página, selecione **propriedades**. 

    ![ID de recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Selecione o botão Copiar para **ID de recurso** para copiar o ID de recurso para a rede clássico para a área de transferência. Guarde o ID da área de transferência no OneNote ou um ficheiro.
6. Selecione **sub-redes** no menu da esquerda. Certifique-se de que o número de **endereços disponíveis** é maior do que os nós no seu tempo de execução de integração de SSIS do Azure.

    ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Associar **MicrosoftAzureBatch** para o **clássico contribuinte de Máquina Virtual** função para a rede virtual.

    a. Selecione **(IAM) do controlo de acesso** no menu à esquerda e selecione **adicionar** na barra de ferramentas. 
       !["Controlo de acesso" e "Adicionar" botões](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. No **adicionar permissões** página, selecione **clássico contribuinte de Máquina Virtual** para **função**. Colar **ddbf3205-c6bd-46ae-8127-60eb93363864** no **selecione** caixa e, em seguida, selecione **do Microsoft Azure Batch** da lista de resultados da pesquisa.   
       ![Resultados da procura na página "Adicionar permissões"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Selecione **guardar** para guardar as definições e fechar a página.  
       ![Guardar as definições de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Confirme que vê **do Microsoft Azure Batch** na lista de contribuintes.  
       ![Confirmar o acesso do Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Certifique-se de que o fornecedor do Azure Batch está registado na subscrição do Azure com a rede virtual. Em alternativa, registe o fornecedor do Azure Batch. Se já tiver uma conta do Azure Batch na sua subscrição, em seguida, a sua subscrição está registada para o Azure Batch.

   a. No portal do Azure, selecione **subscrições** no menu da esquerda.

   b. Selecione a sua subscrição.

   c. Selecione **fornecedores de recursos** à esquerda e confirme que **Microsoft.Batch** é um fornecedor registado.     
      ![Confirmação de estado de "Registada"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **Microsoft.Batch** na lista, para registá-lo, [criar uma conta do Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode eliminá-lo mais tarde. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Utilizar o portal para configurar uma rede virtual do Azure Resource Manager
Terá de configurar uma rede virtual antes de pode associar uma resposta a incidentes SSIS do Azure ao mesmo.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a IU da fábrica de dados só é suportada nesses browsers da web.
2. Inicie sessão no [portal do Azure](https://portal.azure.com).
3. Selecione **mais serviços**. Filtrar e selecione **redes virtuais**.
4. Filtrar e selecione a rede virtual na lista. 
5. No **rede Virtual** página, selecione **propriedades**. 
6. Selecione o botão Copiar para **ID de recurso** para copiar o ID de recurso para a rede virtual para a área de transferência. Guarde o ID da área de transferência no OneNote ou um ficheiro.
7. Selecione **sub-redes** no menu da esquerda. Certifique-se de que o número de **endereços disponíveis** é maior do que os nós no seu tempo de execução de integração de SSIS do Azure.
8. Certifique-se de que o fornecedor do Azure Batch está registado na subscrição do Azure com a rede virtual. Em alternativa, registe o fornecedor do Azure Batch. Se já tiver uma conta do Azure Batch na sua subscrição, em seguida, a sua subscrição está registada para o Azure Batch.

   a. No portal do Azure, selecione **subscrições** no menu da esquerda.

   b. Selecione a sua subscrição. 
   
   c. Selecione **fornecedores de recursos** à esquerda e confirme que **Microsoft.Batch** é um fornecedor registado.     
      ![Confirmação de estado de "Registada"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se não vir **Microsoft.Batch** na lista, para registá-lo, [criar uma conta do Azure Batch vazia](../batch/batch-account-create-portal.md) na sua subscrição. Pode eliminá-lo mais tarde.

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Associar a resposta a incidentes SSIS do Azure a uma rede virtual


1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a IU da fábrica de dados só é suportada nesses browsers da web.
2. No [portal do Azure](https://portal.azure.com), selecione **fábricas de dados** no menu da esquerda. Se não vir **fábricas de dados** no menu, selecione **mais serviços**e selecione **fábricas de dados** no **INTELLIGENCE + análise**secção. 
    
   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Selecione a fábrica de dados com o tempo de execução de integração do Azure-SSIS na lista. Verá a home page da fábrica de dados. Selecione o **autor & implementar** mosaico. Pode ver a IU da fábrica de dados noutro separador. 

   ![Home page da fábrica de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. Na IU de fábrica de dados, mude para o **editar** separador, selecione **ligações**e mude para o **tempos de execução de integração** separador. 

   ![Separador de "Integração tempos de execução"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Se a sua resposta a incidentes SSIS do Azure está em execução, na lista de tempo de execução de integração, selecione o **parar** clique no botão no **ações** coluna para a sua IR. SSIS do Azure Não é possível editar uma resposta a incidentes enquanto não o interrompê-lo. 

   ![Parar a resposta a incidentes](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Na lista de tempo de execução de integração, selecione o **editar** clique no botão no **ações** coluna para a sua IR. SSIS do Azure

   ![Editar o tempo de execução de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. No **definições gerais** página do **a configuração de tempo de execução de integração** janela, selecione **seguinte**. 

   ![Definições gerais para a configuração de resposta a incidentes](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. No **as definições de SQL** página, introduza a palavra-passe de administrador e selecione **seguinte**.

   ![Definições do SQL Server para a configuração de resposta a incidentes](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. No **definições avançadas** página, efetue as seguintes ações: 

   a. Selecione a caixa de verificação **selecionar uma VNet para o tempo de execução de integração do Azure-SSIS associar e permitir que os serviços do Azure para configurar permissões/definições da VNet**.

   b. Para **tipo**, especifique se a rede virtual é uma rede virtual clássica ou uma rede virtual do Azure Resource Manager. 

   c. Para **nome da VNet**, selecione a rede virtual.

   d. Para **nome de sub-rede**, selecione a sub-rede na rede virtual.

   e. Selecione **atualização**. 

   ![Definições avançadas para a configuração de resposta a incidentes](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Agora, pode começar a resposta a incidentes utilizando o **iniciar** clique no botão no **ações** coluna para a sua IR. SSIS do Azure Demora cerca de 20 minutos para iniciar um IR. SSIS do Azure 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual
Terá de configurar uma rede virtual antes de pode associar uma resposta a incidentes SSIS do Azure ao mesmo. Para configurar automaticamente as permissões/definições de rede virtual para o tempo de execução de integração do Azure-SSIS associar a rede virtual, adicione o seguinte script:

```powershell
# Register to the Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar uma resposta a incidentes SSIS do Azure e associar a uma rede virtual
Pode criar uma resposta a incidentes SSIS do Azure e associar a uma rede virtual ao mesmo tempo. Para o script completa e instruções, consulte [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Associar uma resposta a incidentes SSIS do Azure existente a uma rede virtual
O script no [criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md) artigo mostra-lhe como criar uma resposta a incidentes SSIS do Azure e associar a uma rede virtual no mesmo script. Se tiver uma resposta a incidentes SSIS do Azure existente, execute os seguintes passos para associar à rede virtual: 

1. Parar o IR. SSIS do Azure
2. Configure o Azure SSIS IR para associar a rede virtual. 
3. Iniciar o IR. SSIS do Azure 

### <a name="define-the-variables"></a>Definir as variáveis

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

#### <a name="guidelines-for-selecting-a-subnet"></a>Diretrizes para selecionar uma sub-rede
-   Não selecione GatewaySubnet para implementar um tempo de execução para a integração de SSIS do Azure, porque esta se encontra dedicada para gateways de rede virtual.
-   Certifique-se de que a sub-rede que selecionar tem suficiente espaço de endereços disponíveis para IR de SSIS do Azure a utilizar. Mantenha, pelo menos, 2 * número de nós de resposta a incidentes em endereços IP disponíveis. Azure reserva-se alguns endereços IP dentro de cada sub-rede e estes endereços não podem ser utilizados. Os endereços IP primeiro e últimos das sub-redes estão reservados para compatibilidade com o protocolo, juntamente com três endereços mais utilizados para serviços do Azure. Para obter mais informações, consulte [existem restrições sobre como utilizar estas sub-redes de endereços IP?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).


### <a name="stop-the-azure-ssis-ir"></a>Parar a resposta a incidentes SSIS do Azure
Interrompa o tempo de execução de integração do Azure-SSIS antes de a poder associar a uma rede virtual. Este comando disponibiliza todos os respetivos nós e deixa de faturação:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurar definições de rede virtual para o Azure SSIS IR associar
Registar para o fornecedor de recursos do Azure Batch:

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configurar a resposta a incidentes SSIS do Azure
Para configurar o tempo de execução de integração do Azure-SSIS para associar a rede virtual, execute o `Set-AzureRmDataFactoryV2IntegrationRuntime` comando: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Iniciar a resposta a incidentes SSIS do Azure
Para iniciar o tempo de execução de integração de SSIS do Azure, execute o seguinte comando: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Este comando assume 20 a 30 minutos a concluir.

## <a name="use-azure-expressroute-with-the-azure-ssis-ir"></a>Utilizar o ExpressRoute do Azure com a resposta a incidentes SSIS do Azure

Pode ligar um [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuito à sua infraestrutura de rede virtual para expandir a sua rede no local para o Azure. 

Uma configuração comum consiste em utilizar a imposição do túnel (anunciar uma rota BGP, 0.0.0.0/0 para a VNet) que força o tráfego de Internet de saída do fluxo de VNet para o dispositivo de rede no local para inspeção e registo. Este fluxo de tráfego de quebras de conectividade entre o Azure SSIS IR na VNet com os serviços do Azure Data Factory dependentes. A solução é definir um (ou mais) [rotas definidas pelo utilizador (UDRs)](../virtual-network/virtual-networks-udr-overview.md) na sub-rede que contém o IR. SSIS do Azure Um UDR define rotas de sub-rede específica, que são cumpridas em vez da rota BGP.

Se possível, use a seguinte configuração:
-   A configuração do ExpressRoute anuncia 0.0.0.0/0 e por predefinição force-túneis todo o tráfego de saída no local.
-   UDR aplicado à sub-rede que contém a resposta a incidentes Azure SSIS define rota 0.0.0.0/0 com o tipo de próximo salto "Internet".
- 
O efeito combinado destes passos é que o nível de sub-rede UDR tem precedência sobre o ExpressRoute forçado túnel, que garante a saída acesso à Internet do IR. SSIS do Azure

Se estiver preocupados com a perder a capacidade de inspecionar o tráfego de Internet de saída dessa sub-rede, também pode adicionar uma regra NSG na sub-rede para restringir os destinos de saída para [endereços IP do Centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Consulte [este script do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) para obter um exemplo. Tem de executar o script semanalmente a par da lista de endereços IP de centro de dados do Azure.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o tempo de execução de SSIS do Azure, consulte os tópicos seguintes: 

- [Tempo de execução do Azure-SSIS integração](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações concetuais sobre tempos de execução de integração em geral, incluindo o IR. SSIS do Azure 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR. SSIS do Azure Utiliza uma base de dados SQL do Azure para alojar o catálogo SSIS. 
- [Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Criar um runtime de integração Azure-SSIS). Este artigo expande o tutorial e fornece instruções sobre como utilizar o Azure instância SQL Server da base de dados geridos (pré-visualização) e associar a IR para uma rede virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Monitorizar um IR Azure-SSIS) Este artigo mostra como obter informações sobre um IR Azure-SSIS e descrições dos estados nas informações devolvidas. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentar horizontalmente a resposta a incidentes SSIS do Azure ao adicionar nós. 
