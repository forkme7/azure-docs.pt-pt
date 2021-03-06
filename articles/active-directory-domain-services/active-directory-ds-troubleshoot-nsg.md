---
title: 'Serviços do Azure Active Directory domínio: Configuração do grupo de segurança de rede de resolução de problemas | Microsoft Docs'
description: Resolver problemas de configuração de NSG para serviços de domínio do Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: ce03ee0e0936cea4b96e48fbc949f40ee0fe83a0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Resolver problemas de configuração de rede inválida para o seu domínio gerido
Este artigo ajuda-o a resolver erros de configuração relacionadas com a rede que resultam a seguinte mensagem de alerta:

## <a name="alert-aadds104-network-error"></a>AADDS104 alerta: Erro de rede
**Mensagem de alerta:** *Microsoft é não é possível aceder aos controladores de domínio para este domínio gerido. Isto pode acontecer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloqueia o acesso a domínio gerido. Outra razão possível é se houver uma rota definida pelo utilizador que bloqueia o tráfego de entrada da internet.*

Configurações de NSG inválidas são a causa mais comum de erros de rede para serviços de domínio do Azure AD. O grupo de segurança de rede (NSG) configurado para a rede virtual tem de permitir acesso ao [portas específicas](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Se estas portas são bloqueadas, a Microsoft não é possível monitorizar ou atualizar o seu domínio gerido. Além disso, a sincronização entre o diretório do Azure AD e o seu domínio gerido é afetada. Ao criar o NSG, mantém estas portas abertas para evitar a interrupção do serviço.


## <a name="sample-nsg"></a>exemplo NSG
A tabela seguinte ilustra um NSG manteria seu domínio gerido segura ao permitir que a Microsoft para monitorizar, gerir e atualizar as informações de exemplo.

![exemplo NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Serviços de domínio do AD do Azure requer acesso sem restrições de saída da rede virtual. Recomendamos que não para criar qualquer regra NSG adicional que restringe o acesso de saída para a rede virtual.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Adicionar uma regra a um grupo de segurança de rede através do portal do Azure
Se não pretender utilizar o PowerShell, pode adicionar manualmente regras único para os NSGs no portal do Azure. Para criar regras no seu grupo de segurança de rede, execute os seguintes passos:

1. Navegue para o [grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) página no portal do Azure
2. A partir da tabela, escolha o NSG associado à sub-rede na qual o seu domínio gerido está ativado.
3. Em **definições** no painel esquerdo, clique em **regras de segurança de entrada** ou **regras de segurança de saída**.
4. Criar a regra clicando **adicionar** e preencher as informações. Clique em **OK**.
5. Certifique-se de que a regra foi criada através da localização-lo na tabela de regras.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Criar um NSG para serviços de domínio do Azure AD através do PowerShell
Este NSG está configurado para permitir tráfego de entrada para as portas necessárias pelos serviços de domínio do Azure AD, ao negar qualquer outro acesso de entrada indesejável.

**Pré-requisitos: Instalar e configurar o Azure PowerShell** siga as instruções para [instalar o módulo Azure PowerShell e ligue à sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Recomendamos que utilize a versão mais recente do módulo do PowerShell do Azure. Se já tiver uma versão mais antiga do módulo Azure PowerShell instalada, atualize para a versão mais recente.
>

Utilize os seguintes passos para criar um novo NSG através do PowerShell.
1. Inicie sessão sua subscrição do Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Connect-AzureRmAccount
  ```

2. Crie um NSG com três regras. O script seguinte define três regras para o NSG que permite o acesso para as portas necessárias para executar os serviços de domínio do Azure AD. Em seguida, o script cria um novo NSG contém essas regras. Utilize o mesmo formato para adicionar regras adicionais que permitem que o restante tráfego de entrada, se necessário por cargas de trabalho implementadas na rede virtual.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. Por último, associe o NSG a vnet e sub-rede à escolha.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Total de script para criar e aplicar um NSG para serviços de domínio do Azure AD
>[!TIP]
> Recomendamos que utilize a versão mais recente do módulo do PowerShell do Azure. Se já tiver uma versão mais antiga do módulo Azure PowerShell instalada, atualize para a versão mais recente.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Connect-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>Precisa de ajuda?
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para suporte](active-directory-ds-contact-us.md).
