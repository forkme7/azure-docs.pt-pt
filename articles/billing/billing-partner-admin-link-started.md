---
title: Associar uma conta do Azure para as parcerias ID | Microsoft Docs
description: Controlar as ações de envolvimento com clientes do Azure, ligando os ID de parceiro de conta de utilizador que utilizar para gerir os recursos do cliente.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: abab0e63f91ad34d2671c37773d47c31eeeb8339
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>ID de parceiro de ligação às suas contas do Azure 
Como parceiro, pode controlar o impacto nas ações de envolvimento cliente ao ligar o seu ID de parceiro às contas utilizadas para gerir recursos do cliente.

Esta funcionalidade está disponível uma versão de pré-visualização pública. 

## <a name="get-access-from-your-customer"></a>Obtenha o acesso do seu cliente 
Antes de ligar o seu ID de parceiro, o cliente deve conceder-lhe acesso aos respetivos recursos do Azure ao utilizar uma das seguintes opções:

- **Utilizador convidado:** seu cliente pode adicioná-o como um utilizador convidado e atribuir quaisquer funções RBAC. Para obter mais informações, consulte [adicionar os utilizadores convidados a partir de outro diretório](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Conta de diretório:** seu cliente pode criar um novo utilizador da sua organização no seu diretório e atribuir qualquer função RBAC. 

- **Principal de serviço:** seu cliente pode adicionar uma aplicação ou um script da sua organização no seu diretório e atribuir qualquer função RBAC. A identidade da aplicação ou script é conhecida como principal de serviço.

## <a name="link-partner-id"></a>ID de parceiro de ligação
Quando tiver acesso a recursos do cliente, utilize o PowerShell ou o CLI para ligar o seu ID de rede de parceiros Microsoft (MPN ID) para o ID de utilizador ou o principal de serviço. Tem de associar o ID de parceiro para cada inquilino de cliente. 

### <a name="use-powershell-to-link-new-partner-id"></a>Utilize o PowerShell para ligar o novo ID de parceiro

1. Instalar o [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) módulo do PowerShell.

2. Inicie sessão no inquilino do cliente com a conta de utilizador ou principal de serviço, para obter mais informações, consulte [início de sessão com o Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Ligue o novo ID de parceiro. O parceiro ID é o [Network(MPN) de parceiro da Microsoft](https://partner.microsoft.com/) ID da sua organização.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Obter o ID de parceiro ligado
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Atualizar o ID de parceiro ligado
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Eliminar o ID de parceiro ligado
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Utilizar a CLI para ligar o novo ID de parceiro
1.  Instale a extensão do CLI.

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Inicie sessão no inquilino do cliente com a conta de utilizador ou principal de serviço. Para obter mais informações, consulte [iniciar sessão com o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Ligue o novo ID de parceiro. O parceiro ID é o [Network(MPN) de parceiro da Microsoft](https://partner.microsoft.com/) ID da sua organização.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obter o ID de parceiro ligado
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Atualizar o ID de parceiro ligado
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Eliminar o ID de parceiro ligado
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

**Quem pode ligar o ID de parceiro?**

Qualquer utilizador da organização do parceiro de que está a gerir recursos do cliente pode ligar o ID de parceiro de conta.
  

**Assim que está ligado um ID de parceiro pode-ser alterada?**

Sim, ID de parceiro ligado pode ser alterado, adicionada ou removida.

**E se um utilizador tiver uma conta em vários inquilinos do cliente?**

A ligação entre o ID de parceiro e a conta é feita para cada inquilino de cliente.  Tem de associar o ID de parceiro cada inquilino de cliente.

**Pode outro parceiro ou cliente editar ou remover a ligação para o ID de parceiro?**

A ligação está associada ao nível da conta. Apenas pode editar ou remover a ligação para o ID de parceiro. O cliente e o outro parceiro não é possível alterar a ligação para o ID de parceiro. 


