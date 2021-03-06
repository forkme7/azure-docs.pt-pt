---
title: "Diferenças e as considerações para máquinas virtuais na pilha do Azure | Microsoft Docs"
description: "Saiba mais sobre as diferenças e as considerações ao trabalhar com máquinas virtuais na pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: brenduns
ms.openlocfilehash: 50c0f293ac669ade4e45a5f45b0adf9a7c4b6c36
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/17/2018
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Considerações para máquinas virtuais na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Máquinas virtuais são uma a pedido, dimensionáveis recursos informáticos disponibilizadas pela pilha de Azure. Quando utilizar máquinas virtuais, tem de compreender que existem diferenças entre as funcionalidades que estão disponíveis no Azure e pilha do Azure. Este artigo fornece uma descrição geral das considerações de exclusivas para máquinas virtuais e as respetivas funcionalidades na pilha do Azure. Para saber mais sobre das principais diferenças entre a pilha do Azure e do Azure, consulte o [chave considerações](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-virtual-machine-differences"></a>Cheat folha: diferenças de Máquina Virtual

| Funcionalidade | Azure (global) | Azure Stack |
| --- | --- | --- |
| Imagens da máquina virtual | O Azure Marketplace contém imagens que pode utilizar para criar uma máquina virtual. Consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) página para ver a lista de imagens que estão disponíveis no Azure Marketplace. | Por predefinição, não sabemos de quaisquer imagens disponíveis no mercado pilha do Azure. O administrador da nuvem do Azure pilha deve publicar ou transferir imagens no Marketplace de pilha do Azure antes dos utilizadores podem utilizá-los. |
| Tamanhos de máquinas virtuais | Azure suporta uma grande variedade de tamanhos de máquinas virtuais. Para saber mais sobre as opções e tamanhos disponíveis, consulte o [tamanhos de máquinas virtuais do Windows](../../virtual-machines/virtual-machines-windows-sizes.md) e [tamanhos de máquinas virtuais do Linux](../../virtual-machines/linux/sizes.md) tópicos. | Pilha do Azure suporta um subconjunto de tamanhos de máquinas virtuais que estão disponíveis no Azure. Para ver a lista de tamanhos suportados, consulte o [tamanhos de máquina virtual](#virtual-machine-sizes) secção deste artigo. |
| Quotas de máquina virtual | [Limites de quota](../../azure-subscription-service-limits.md#service-specific-limits) estão definidas pela Microsoft | O administrador da nuvem do Azure pilha tem de atribuir quotas para máquinas virtuais oferecem aos respetivos utilizadores. |
| Extensões da máquina virtual |Azure suporta uma grande variedade de extensões de máquina virtual. Para saber mais sobre as extensões disponíveis, consulte o [extensões de máquina virtual e funcionalidades](../../virtual-machines/windows/extensions-features.md) artigo.| Pilha do Azure suporta um subconjunto de extensões que estão disponíveis no Azure e cada da extensão têm versões específicas. O administrador da nuvem do Azure pilha pode escolher quais as extensões para ficarem disponíveis para os seus utilizadores. Para ver a lista de extensões suportadas, consulte o [extensões de máquina virtual](#virtual-machine-extensions) secção deste artigo. |
| Rede de máquinas virtuais | Endereços IP públicos atribuídos à máquina virtual de inquilino estejam acessíveis através da Internet.<br><br><br>Máquinas virtuais do Azure com um nome DNS fixo | Endereços IP públicos atribuídos a uma máquina virtual inquilina estão acessíveis apenas o ambiente de Kit de desenvolvimento de pilha do Azure. Um utilizador tem de ter acesso para o Kit de desenvolvimento de pilha do Azure através de [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para ligar a uma máquina virtual que é criada na pilha do Azure.<br><br>Máquinas virtuais criadas dentro de uma instância específica de pilha do Azure tem um nome DNS com base no valor que é configurado pelo administrador da nuvem. |
| Armazenamento de máquina virtual | Suporta [discos geridos pelo.](../../virtual-machines/windows/managed-disks-overview.md) | Discos geridos não são suportados ainda na pilha do Azure. |
| Versões da API | O Azure tem sempre as versões de API mais recentes para todas as funcionalidades de máquina virtual. | Pilha do Azure suporta serviços específicos do Azure e versões de API específicas para estes serviços. Para ver a lista de versões de API suportadas, consulte o [versões de API](#api-versions) secção deste artigo. |
|Conjuntos de disponibilidade de máquina virtual|Vários domínios de falhas (2 ou 3 por região)<br>Vários domínios de atualização<br>Gerido suporte de disco|Vários domínios de falhas (2 ou 3 por região)<br>Vários domínios de atualização (até 20)<br>Sem suporte de disco gerido|
|Conjuntos de dimensionamento de máquinas virtuais|Escala automática suportada|Não suportado dimensionamento automático.<br>Adicione mais instâncias a uma escala definida utilizando o portal, modelos do Resource Manager ou PowerShell.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquinas virtuais
Azure impõe limites de recursos de várias formas para evitar overconsumption de recursos (servidor local e o nível de serviço). Sem colocar alguns limites num consumo de inquilinos de recurso, a experiência de inquilino pode ser afetado e quando um vizinho inúteis overconsumes recursos. 
- Para a saída de rede da VM, existem caps de largura de banda no local. Caps na pilha de Azure correspondem caps no Azure.  
- Para recursos de armazenamento, a pilha do Azure implementa limites de IOPs de armazenamento para evitar overconsumption básico dos recursos por inquilinos para acesso de armazenamento. 
- Para VMs com vários discos de dados anexado, o débito máximo de cada disco de dados individuais é 500 IOPS para HHDs e 2300 IOPS para SSDs.

A tabela seguinte lista as VMs que são suportadas na pilha do Azure, juntamente com a respetiva configuração:

| Tipo           | Tamanho          | Intervalo de tamanhos suportados |
| ---------------| ------------- | ------------------------ |
|Fins gerais |Básico A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Fins gerais |Um padrão     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Fins gerais |Série D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Fins gerais |Série Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Fins gerais |Série DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Fins gerais |Série DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Com otimização de memória|Série D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Com otimização de memória|Série DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Com otimização de memória|Série Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Com otimização de memória|Série de série DSv2-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Tamanhos de máquina virtual e as quantidades de recursos associados são consistentes entre pilha do Azure e o Azure. Por exemplo, este consistência inclui a quantidade de memória, número de núcleos de e/tamanho do número de discos de dados que podem ser criadas. No entanto, o desempenho do mesmo tamanho VM na pilha do Azure depende as características subjacentes de um determinado ambiente de pilha do Azure.

## <a name="virtual-machine-extensions"></a>Extensões da máquina virtual

 A pilha de Azure inclui um pequeno conjunto de extensões. Atualizações e extensões adicionais e estão disponíveis através de sindicação do Marketplace.

Utilize o seguinte script do PowerShell para obter a lista de extensões de máquina virtual que estão disponíveis no seu ambiente de pilha do Azure:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versões da API

Funcionalidades de máquina virtual na pilha do Azure suportam as seguintes versões de API:

![Tipos de recurso VM](media/azure-stack-vm-considerations/vm-resoource-types.png)

Pode utilizar o seguinte script do PowerShell para obter as versões de API para as funcionalidades de máquina virtual que estão disponíveis no seu ambiente de pilha do Azure:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
A lista de tipos de recurso suportados e versões de API pode variar devido às se o operador da nuvem atualiza o seu ambiente de pilha do Azure para uma versão mais recente.

## <a name="next-steps"></a>Passos Seguintes

[Criar uma máquina virtual do Windows com o PowerShell na pilha do Azure](azure-stack-quick-create-vm-windows-powershell.md)
