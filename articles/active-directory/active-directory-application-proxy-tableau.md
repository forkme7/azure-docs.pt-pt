---
title: Proxy de aplicações do Azure Active Directory e o Tableau | Microsoft Docs
description: Saiba como utilizar o Proxy de aplicações do Azure Active Directory (Azure AD) para fornecer acesso remoto para a sua implementação Tableau.  .
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 480e1cd72bcb42d7f39a92fc49e7c3d66ecf05f8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy de aplicações do Azure Active Directory e o Tableau 

Proxy de aplicações do Active Directory do Azure e o Tableau tem parcerias para se certificar de que é facilmente capazes de utilizar o Proxy de aplicações para fornecer acesso remoto para a sua implementação Tableau. Este artigo explica como configurar este cenário.  

## <a name="prerequisites"></a>Pré-requisitos 

O cenário neste artigo pressupõe que tem:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#reverse-proxy-server) configurado. 

- Um [conector do Proxy de aplicação](active-directory-application-proxy-enable.md) instalado. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Ativar o Proxy da aplicação para Tableau 

Se pretender utilizar o Proxy de aplicações para o Tableau, terá de enviar um e-mail para [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) para obter este cenário ativado.
No seu correio eletrónico:

-   Utilizar ativar Proxy de aplicações para Tableau como requerente
-   Incluir o ID de inquilino no corpo do    

Receberá uma confirmação quando estiver pronto a utilizar a aplicação. Normalmente, isto demora uma semana. No entanto, pode concluir as configurações ao aguardar a confirmação.


 

## <a name="publish-your-applications-in-azure"></a>Publicar as suas aplicações no Azure 

Para publicar o Tableau, terá de publicar uma aplicação no Portal do Azure.

Para:

- Detalhadas instruções dos passos 1-8, consulte [publicar aplicações através do Proxy de aplicações do Azure AD](application-proxy-publish-azure-portal.md). 
- Informações sobre como determinar os valores de Tableau para os campos de Proxy de aplicações, consulte a documentação de Tableau.  

**Para publicar a aplicação**: 


1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global. 

2. Selecione **do Azure Active Directory > aplicações da empresa**. 

3. Selecione **adicionar** na parte superior do painel. 

4. Selecione **no local aplicação**. 

5. Preencha os campos obrigatórios, com informações sobre a nova aplicação. Utilize as seguintes orientações para as definições: 

    - **URL interno**: esta aplicação deve ter um URL interno que é o URL de Tableau próprio. Por exemplo, `https://adventure-works.tableau.com`. 

    - **Método de pré-autenticação**: Azure Active Directory (recomendado mas não obrigatório). 

6. Selecione **adicionar** na parte superior do painel. A aplicação é adicionada e abre o menu de início rápido. 

7. No menu de início rápido, selecione **atribuir um utilizador de teste**, e adicione, pelo menos, um utilizador para a aplicação. Certifique-se que esta conta de teste tem acesso à aplicação no local. 

8. Selecione **atribuir** para guardar a atribuição de utilizador de teste. 

9. (Opcional) Na página de gestão de aplicações, selecione **de sessão único-**. Escolha **autenticação integrada do Windows** do menu pendente e preenchimento enviados os campos obrigatórios, com base na sua configuração Tableau. Selecione **Guardar**. 

 

## <a name="testing"></a>Testes 

A aplicação está agora pronta para testar. O URL externo que utilizou para publicar o Tableau, de acesso e inicie sessão como um utilizador atribuído a ambas as aplicações.



## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre o Proxy de aplicações do Azure AD, consulte [como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).

