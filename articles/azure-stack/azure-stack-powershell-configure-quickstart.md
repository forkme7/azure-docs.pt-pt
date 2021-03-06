---
title: Instalar e configurar o PowerShell para início rápido do Azure pilha | Microsoft Docs
description: Saiba mais sobre como instalar e configurar o PowerShell para a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: 6846791b50140f849217fe7071cfde58159e0bf8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Começar a trabalhar com o PowerShell na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Este guia de introdução ajuda-o a instalar e configurar um ambiente de pilha do Azure com o PowerShell. O script que fornecemos neste artigo tem um âmbito para o **operador de pilha do Azure** apenas.

Este artigo é uma versão condensed dos passos descritos a [instale o PowerShell]( azure-stack-powershell-install.md), [descarregar as ferramentas de]( azure-stack-powershell-download.md), e [configurar o ambiente de PowerShell o operador de pilha do Azure]( azure-stack-powershell-configure-admin.md) artigos. Ao utilizar os scripts neste tópico, pode configurar o PowerShell para ambientes de pilha do Azure que são implementadas com o Azure Active Directory ou os serviços de Federação do Active Directory (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurar o PowerShell para implementações baseadas no Azure Active Directory

Inicie sessão no seu Kit de desenvolvimento de pilha do Azure ou um cliente externo baseado em Windows se estiver ligado através de VPN. Abra uma sessão elevada do ISE do PowerShell e, em seguida, execute o seguinte script. Certifique-se de que atualiza o **TenantName**, **ArmEndpoint**, e **GraphAudience** variáveis conforme necessário para a configuração do seu ambiente:

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurar o PowerShell para implementações do AD FS com base em

Pode utilizar o seguinte script se estiver a utilizar o Azure pilha quando ligado à internet. No entanto se estiver a utilizar o Azure pilha sem conectividade à internet, utilize o [desligado forma de instalar o PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) e os cmdlets para configurar o PowerShell permanecerá igual à mostrada neste script. Inicie sessão no seu Kit de desenvolvimento de pilha do Azure ou um cliente externo baseado em Windows se estiver ligado através de VPN. Abra uma sessão elevada do ISE do PowerShell e, em seguida, execute o seguinte script. Certifique-se de que atualiza o **ArmEndpoint** e **GraphAudience** variáveis conforme necessário para a configuração do seu ambiente:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que configurou o PowerShell, pode testar a configuração através da criação de um grupo de recursos:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Para especificar um grupo de recursos, terá de ter um grupo de recursos na sua subscrição. Para mais informações sobre as subscrições, consulte [descrição geral do plano, oferta, quota e subscrição](azure-stack-plan-offer-quota-overview.md)

Depois de criar o grupo de recursos, o **estado de aprovisionamento** propriedade está definida como **com êxito**.

## <a name="next-steps"></a>Passos Seguintes

* [Instalar e configurar a CLI](azure-stack-connect-cli.md)

* [Desenvolver modelos](user/azure-stack-develop-templates.md)
