---
title: 'Azure B2C do Active Directory: mudar para um inquilino B2C | Microsoft Docs'
description: Como mudar para o contexto do inquilino do Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/13/2017
ms.author: davidmu
ms.openlocfilehash: 6469636ee05c2b28a24b1d559f0ee7569e784393
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Mudar para o inquilino do Azure AD B2C

Para configurar o Azure AD B2C, terá de estar no contexto do inquilino do Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Iniciar sessão no inquilino do Azure AD B2C

Para navegar para o inquilino do Azure AD B2C, deve ter sessão iniciada no portal do Azure como administrador global do inquilino do Azure AD B2C.

1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
1. Altere os inquilinos ao clicar no endereço de correio eletrónico ou na imagem no canto superior direito.
1. Na lista `Directory` apresentada, selecione o inquilino do Azure AD B2C que pretende gerir.

O portal do Azure é atualizado.  Agora a sessão iniciada no Portal do Azure no contexto do inquilino do Azure AD B2C está iniciada.

## <a name="navigate-to-the-b2c-features-pane"></a>Navegar para o painel de funcionalidades do B2C

1. Clique em **Procurar** na navegação do lado esquerdo.
1. Clique em **Todos os serviços** e, em seguida, procure `Azure AD B2C` no painel de navegação à esquerda.  (Para afixar ao Startboard do lado esquerdo, clique na estrela à esquerda de Azure AD B2C)
1. Clique em **Azure AD B2C** para aceder ao painel de funcionalidades B2C.
   
    ![Captura de ecrã da Procura do painel de funcionalidades do B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Precisa de ser um Administrador Global de inquilino do B2C para poder aceder ao painel de funcionalidades do B2C. Um Administrador Global de qualquer outro inquilino ou um utilizador de qualquer outro inquilino não poderá aceder.  Pode mudar para o seu inquilino B2C ao utilizar o comutador do inquilino no canto superior direito do portal do Azure.
