---
title: Adicionar utilizadores de colaboração do B2B no portal do Azure - Azure Active Directory | Microsoft Docs
description: Mostra como um administrador pode adicionar utilizadores convidados ao seu diretório de uma organização de parceiro a utilizar a colaboração B2B do Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/02/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 34bd5b51089045c4cd20f29d179bb230e5e3fac2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar utilizadores de colaboração do B2B de diretório Active Directory do Azure no portal do Azure

Como um administrador global ou um utilizador a quem é atribuído a qualquer uma das funções de diretório administrador limitado, pode utilizar o portal do Azure para convidar utilizadores de colaboração B2B. Pode convidar os utilizadores convidados para o diretório, a um grupo ou para uma aplicação. Depois de convidar um utilizador através de qualquer um destes métodos, conta de utilizador convidados é adicionada ao Azure Active Directory (Azure AD), com um tipo de utilizador de *convidado*. O utilizador convidado, em seguida, tem resgatar as respetivas convite para aceder a recursos.

## <a name="add-guest-users-to-the-directory"></a>Adicionar utilizadores de convidado para o diretório

Para adicionar utilizadores de colaboração do B2B para o diretório, siga estes passos:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Em **gerir**, selecione **utilizadores e grupos** > **todos os utilizadores**.
4. Selecione **novo utilizador convidado**.

   ![Mostra onde o novo utilizador convidado está na IU](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. Em **convidar um convidado**, introduza o endereço de e-mail do utilizador externo. Opcionalmente, inclua uma mensagem de boas-vindas. Por exemplo:

   ![Mostra onde o novo utilizador convidado está na IU](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. Selecione **convidar** para enviar automaticamente o convite ao utilizador convidado. No **notificação** área, procure um **utilizador com êxito convidado** mensagem. 
 
Depois de enviar o convite, a conta de utilizador é automaticamente adicionada ao diretório como convidado.


![Mostra B2B utilizador com o tipo de utilizador convidado](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar utilizadores convidados a um grupo
Se precisar de adicionar manualmente utilizadores de colaboração B2B a um grupo como um administrador do Azure AD, siga estes passos:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Em **gerir**, selecione **utilizadores e grupos** > **todos os grupos**.
4. Selecione um grupo (ou clique em **novo grupo** para criar um novo). É uma boa ideia para incluir na descrição do grupo que o grupo contém os utilizadores convidados de B2B.
5. Selecione **membros** > **adicionar membros**. 
6. Efetue um dos seguintes:
   - Se o utilizador convidado já existe no diretório, procure o utilizador B2B. Selecione o utilizador > clique **selecione** para adicionar o utilizador ao grupo.
   - Se o utilizador convidado ainda não existir no diretório, selecione **convidar**.
   ![Adicionar o convite botão para adicionar membros de convidado](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      Em **convidar um convidado**, introduza o endereço de correio eletrónico e uma mensagem pessoal opcional > selecione **convidar**. Clique em **selecione** para adicionar o utilizador ao grupo.

      O convite sai automaticamente ao utilizador convidado. No **notificação** área, procure um bem-sucedida **Invited utilizador** mensagem. 

Também pode utilizar grupos dinâmicos com colaboração B2B do Azure AD. Para obter mais informações, consulte [grupos dinâmicos e de colaboração B2B do Azure Active Directory do](active-directory-b2b-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar utilizadores de convidado para uma aplicação

Para adicionar utilizadores de colaboração do B2B a uma aplicação como um administrador do Azure AD, siga estes passos:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Em **gerir**, selecione **aplicações empresariais** > **todas as aplicações**.
4. Selecione a aplicação para o qual pretende adicionar os utilizadores convidados.
5. Em **gerir**, selecione **utilizadores e grupos**.
6. Selecione **adicionar utilizador**.
7. Em **adicionar atribuição**, selecione **grupos de utilizadores e**.
8. Efetue um dos seguintes:
   - Se o utilizador convidado já existe no diretório, procure o utilizador B2B. Selecione o utilizador e, em seguida, clique em **selecione** para adicionar o utilizador para a aplicação.
   - Se o utilizador convidado ainda não existir no diretório, selecione **convidar**.
   ![Adicionar o convite botão para adicionar membros de convidado](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      Em **convidar um convidado**, introduza o endereço de correio eletrónico e uma mensagem pessoal opcional > selecione **convidar**. Clique em **selecione** para adicionar o utilizador para a aplicação.

      O convite sai automaticamente ao utilizador convidado. No **notificação** área, procure um bem-sucedida **Invited utilizador** mensagem.

9. Em **adicionar atribuição**, clique em **selecionar função** > Selecione uma função para aplicar ao utilizador selecionado (se aplicável) > selecione **OK**.
10. Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Volte a enviar convites para os utilizadores convidados

Se um utilizador convidado não tiver resgatadas ainda as respetivas convite, pode reenviar o convite.

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Em **gerir**, selecione **utilizadores e grupos**.
4. Selecione **todos os utilizadores**.
5. Selecione a conta de utilizador.
6. Em **gerir**, selecione **perfil**.
7. Se o utilizador ainda não aceite o convite, um **reenviar o convite** opção está disponível. Selecione este botão para reenviar.

   ![Opção de convite de reenviar no perfil de utilizador](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> Se reenviou um convite que originalmente direcionadas para o utilizador para uma aplicação específica, tenha em atenção que a ligação no novo convite orienta o utilizador para o painel de acesso de nível superior em vez disso.

## <a name="next-steps"></a>Passos Seguintes

- Para saber como admins não do Azure AD pode adicionar utilizadores de convidado de B2B, consulte [como os técnicos de informação adicionar utilizadores de colaboração do B2B?](active-directory-b2b-iw-add-users.md)
- Para obter informações sobre o e-mail de convite, consulte [os elementos do e-mail de convite de colaboração B2B](active-directory-b2b-invitation-email.md).
- Para obter informações sobre o processo de resgate convite, consulte [resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md).


