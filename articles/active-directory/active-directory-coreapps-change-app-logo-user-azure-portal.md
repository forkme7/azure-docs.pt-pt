---
title: Alterar o nome ou logótipo de uma aplicação empresarial no Azure Active Directory | Microsoft Docs
description: Como alterar o nome ou logótipo para uma aplicação empresarial personalizada no Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 18e5555d4eaf49fceb5aec673eedce8ed90d46a3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2018
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Alterar o nome ou logótipo de uma aplicação empresarial no Azure Active Directory
É fácil de alterar o nome ou logótipo para uma aplicação empresarial personalizada no Azure Active Directory (Azure AD). Tem de ter as permissões adequadas para efetuar estas alterações, e tem de ser o criador da aplicação personalizada.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Como alterar nome ou logótipo de uma aplicação empresarial?
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **todos os serviços**, introduza **do Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
3. No **Azure Active Directory - *directoryname***  painel (ou seja, o Azure AD para o diretório que está a gerir), selecione **aplicações empresariais**.

    ![Aplicações da empresa ao abrir](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. No **aplicações empresariais** painel, selecione **todas as aplicações**. É apresentada uma lista das aplicações que pode gerir.
5. No **aplicações da empresa - todas as aplicações** painel, selecione uma aplicação.
6. No ***appname*** painel (ou seja, o painel com o nome da aplicação selecionada no título), selecione **propriedades**.

    ![Selecionar o comando Propriedades](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. No ***appname*** **-propriedades** painel, procure um ficheiro para utilizar como um logótipo de novo, ou editar o nome da aplicação, ou ambos.

    ![Alterar o comando de logótipo ou nameproperties de aplicação](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Selecione o **guardar** comando.

## <a name="next-steps"></a>Passos Seguintes
* [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)
* [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Desativar o utilizador inícios de sessão para uma aplicação empresarial](active-directory-coreapps-disable-app-azure-portal.md)
