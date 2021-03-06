---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com o Azure PowerShell | Microsoft Docs
description: Neste início rápido, irá utilizar o Azure PowerShell no armazenamento de objetos (Blobs). Em seguida, utilize o PowerShell para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: f028d37a98cecf14706773a2eb7cb601481435d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>Início Rápido: carregar, transferir e listar blobs através do Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia fornece detalhes sobre a utilização do PowerShell para transferir ficheiros entre o disco locais e o armazenamento de Blobs do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer a versão 3.6 ou posterior do módulo Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de blobs, à semelhança de como organiza os ficheiros em pastas no seu computador.

Defina o nome do contentor, crie o contentor com o comando [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) e defina as permissões para "blob" para permitir o acesso público dos ficheiros. O nome do contentor neste exemplo é *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os ficheiros VHD utilizados nas cópias de segurança de VMs IaaS são blobs de páginas. Os blobs de acréscimo são utilizados para registo, como quando quer escrever num ficheiro e continuar a adicionar mais informações. A maioria dos ficheiros guardados no armazenamento de Blobs são blobs de blocos. 

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e uma referência de blob de blocos nesse contentor. Assim que tiver a referência de blob, pode carregar dados para o mesmo com o comando [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Esta operação cria o blob, caso este ainda não exista, ou substitui o mesmo se já existir.

Os exemplos seguintes carregam Image001.jpg e Image002.png da pasta D:\\_TestImages no disco local para o contentor que criou.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Carregue os ficheiros que quiser antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obtenha uma lista de blobs no contentor com o comando [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Este exemplo mostra apenas os nomes dos blobs carregados.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Transferir blobs

Transfira os blobs para o disco local. Para cada blob ser transferido, defina o nome e chame [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) para transferir o blob.

Este exemplo transfere os blobs para D:\\_TestImages\Downloads no disco local. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é outra opção para a transferência de dados de scripts de elevado desempenho para o Armazenamento do Azure. Pode utilizar o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas.

Como exemplo rápido, eis o comando do AzCopy para carregar um ficheiro denominado *myfile.txt* para o contentor *mystoragecontainer* a partir de uma janela do PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Remova todos os recursos que criou. A forma mais fácil de o fazer é eliminar o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Neste caso, remove a conta de armazenamento e o grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a transferir ficheiros entre o disco local e o armazenamento de Blobs do Azure. Para saber mais sobre a utilização do armazenamento de Blobs, avance para os procedimentos do armazenamento de blobs.

> [!div class="nextstepaction"]
> [Procedimentos de Operações de Armazenamento de Blobs](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referência de cmdlets do Armazenamento do Microsoft Azure PowerShell
* [Cmdlets do Armazenamento do PowerShell](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure
* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.