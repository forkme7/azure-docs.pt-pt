---
title: Criar VMs a executar uma pilha SQL&#92;IIS&#92;.NET no Azure | Microsoft Docs
description: Tutorial - instalar uma pilha do Azure SQL, IIS, .NET em máquinas virtuais do Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ac6038f7600d6eb4c8d021998f9cfc40bd369332
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Install uma pilha SQL&#92;IIS&#92;.NET no Azure

Neste tutorial, vamos instalar a pilha SQL&#92;IIS&#92;.NET com o Azure PowerShell. Este pilha consiste em duas VMs com o Windows Server 2016, uma com o IIS e .NET e a outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma VM 
> * Instalar o IIS e o SDK de .NET Core na VM
> * Criar uma VM a executar o SQL Server
> * Instalar a extensão do SQL Server

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 4.3.1 ou posterior do módulo Azure AzureRM.Compute. Executar `Get-Module -ListAvailable AzureRM.Compute` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Criar uma VM de IIS 

Neste exemplo, utilizamos o cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) no Cloud Shell do PowerShell para criar rapidamente uma VM do Windows Server 2016 e, em seguida, instalar o IIS e o .NET Framework. AS VMs do IIS e SQL partilham um grupo de recursos e a rede virtual, pelo que iremos criar variáveis para esses nomes.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Instale o IIS e o .NET Framework com a extensão de script personalizado.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Criar outra sub-rede

Crie uma segunda sub-rede para a VM do SQL. Obtenha a vNet com [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Crie uma configuração para a sub-rede com [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Atualizar a vNet com as informações da nova sub-rede, utilizando [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>VM do Azure SQL

Utilize uma imagem do Azure Marketplace pré-configurada de um servidor SQL para criar a VM do SQL. Ao criar a VM pela primeira vez, instalamos então a Extensão do SQL Server na VM. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Utilize [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) para adicionar a [extensão do SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) à VM do SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, instalou a pilha SQL&#92;IIS&#92;.NET com o Azure PowerShell. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM 
> * Instalar o IIS e o SDK de .NET Core na VM
> * Criar uma VM a executar o SQL Server
> * Instalar a extensão do SQL Server

Avance para o tutorial seguinte para aprender a proteger o servidor Web IIS com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web IIS com certificados SSL](tutorial-secure-web-server.md)

