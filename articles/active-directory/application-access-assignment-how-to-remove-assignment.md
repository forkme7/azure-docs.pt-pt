---
title: "Como remover o acesso de um utilizador a uma aplicação | Microsoft Docs"
description: "Compreender como remover o acesso de um utilizador a uma aplicação"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: aec49b108c21b97646e622c47bf69728a81d6d33
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Como remover o acesso de um utilizador a uma aplicação

Este artigo ajuda-o a compreender como remover o acesso de um utilizador a uma aplicação.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Pretender remover um utilizador específico ou do grupo de atribuição para uma aplicação

Para remover um utilizador ou a atribuição de grupo para uma aplicação, siga os passos apresentados no [remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artigo.

. # # pretendo desativar todos os acessos a uma aplicação para cada utilizador

Para desativar a todos os utilizadores inícios de sessão a uma aplicação, siga os passos apresentados no [desativar utilizador inícios de sessão para uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artigo.

## <a name="i-want-to-delete-an-application-entirely"></a>Pretender eliminar uma aplicação totalmente

Para **eliminar uma aplicação**, siga estas instruções:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **todos os serviços** no topo do menu de navegação esquerdo principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todos os Aplicações.**

6.  Selecione a aplicação que pretende eliminar.

7.  Quando carrega a aplicação, clique em **eliminar** ícone da aplicação superior **descrição geral** painel.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as operações de consentimento do utilizador futura para qualquer aplicação

Desativar o consentimento do utilizador para o seu diretório todo impedir que os utilizadores finais consenting para qualquer aplicação. Os administradores podem ainda consentimento no behalves do utilizador. Para saber mais sobre o consentimento de aplicação e, por isso poderá ou poderá pretender fazê-lo, ler [utilizador compreender e administração consentimento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Para **desativar todas as operações de consentimento do utilizador futuras no seu diretório todo**, siga estas instruções:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **todos os serviços** no topo do menu de navegação esquerdo principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **as definições de utilizador**.

6.  Desative todas as operações de consentimento do utilizador futuras definindo a **utilizadores podem permitir que as aplicações aceder aos respetivos dados** alternar para **não** e clique em de **guardar** botão.


# <a name="next-steps"></a>Passos Seguintes
[Gerir o acesso a aplicações](active-directory-managing-access-to-apps.md)
