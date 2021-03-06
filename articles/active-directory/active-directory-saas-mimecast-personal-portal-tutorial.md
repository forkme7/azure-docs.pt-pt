---
title: "Tutorial: Integração do Azure Active Directory com o Portal pessoais Mimecast | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Portal pessoais Mimecast."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jeedes
ms.openlocfilehash: 4f2c5f7323d9d10b6a784da8f45577ccf774b78f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integração do Azure Active Directory com o Portal pessoais Mimecast

Neste tutorial, irá aprender a integrar Portal pessoais Mimecast com o Azure Active Directory (Azure AD).

Integrar Portal pessoais Mimecast com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Portal pessoais Mimecast.
- Pode permitir aos utilizadores automaticamente obter assinado no Mimecast pessoais Portal (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Portal pessoais Mimecast, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Portal pessoais Mimecast-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adição do Portal de pessoais Mimecast na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Adição do Portal de pessoais Mimecast na galeria do
Para configurar a integração do Portal pessoais Mimecast com o Azure AD, tem de Adicionar Portal pessoais Mimecast na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Portal pessoais Mimecast na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Portal pessoais Mimecast**, selecione **Portal pessoais Mimecast** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Mimecast Portal pessoais na lista de resultados](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Portal pessoais Mimecast com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Portal pessoais Mimecast é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Portal pessoais Mimecast tem de ser estabelecida.

No Portal de pessoais Mimecast, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Portal pessoais Mimecast, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Portal pessoais Mimecast](#create-a-mimecast-personal-portal-test-user)**  - para ter um homólogo de Britta Simon no Portal de pessoais Mimecast que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de Portal pessoais Mimecast.

**Para configurar o Azure AD-início de sessão único com o Portal pessoais Mimecast, execute os seguintes passos:**

1. No portal do Azure, no **Portal pessoais Mimecast** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. No **Mimecast pessoais Portal domínio e os URLs** secção, execute os seguintes passos:

    ![Mimecast pessoais Portal domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|
    
    > [!NOTE] 
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contacte [equipa de suporte de cliente de Portal pessoais Mimecast](http://www.mimecast.com/customer-success/technical-support/) para obter o valor. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. No **Mimecast pessoais Portal configuração** secção, clique em **configurar Portal de pessoal de Mimecast** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do Portal Mimecast pessoal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no seu Portal pessoais Mimecast como administrador.

8. Aceda a **serviços \> aplicações**.
   
    ![Aplicações](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "aplicações")

9. Clique em **autenticação perfis**.
   
    ![Perfis de autenticação](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "perfis de autenticação")

10. Clique em **novo perfil de autenticação**.
   
    ![Novo perfil de autenticação](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "novo perfil de autenticação")

11. No **perfil autenticação** secção, execute os seguintes passos:
   
    ![Perfil de autenticação](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "perfil de autenticação")
   
    a. No **Descrição** caixa de texto, escreva um nome para a sua configuração.
   
    b. Selecione **impôr a autenticação de SAML do Portal de pessoal de Mimecast**.
   
    c. Como **fornecedor**, selecione **do Azure Active Directory**.
   
    d. No **URL do emissor** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.
   
    e. No **URL de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.
   
    f. No **URL de fim de sessão** caixa de texto, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.

    g. Abra o **base-64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **o certificado de fornecedor de identidade (metadados)** caixa de texto.

    h. Selecione **Permitir início de sessão único em**.
   
    posso. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Criar um utilizador de teste do Portal pessoais Mimecast

Para permitir que os utilizadores do Azure AD inicie sessão no Portal pessoais Mimecast, tem de ser aprovisionados no Portal pessoais Mimecast. No caso do Portal de pessoais Mimecast, o aprovisionamento é numa tarefa manual.

Tem de registar um domínio, antes de poder criar utilizadores.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Portal pessoais Mimecast** como administrador.

2. Aceda a **diretórios \> interno**.
   
    ![Diretórios](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "diretórios")

3. Clique em **registar o novo domínio**.
   
    ![Registar o novo domínio](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "registar o novo domínio")

4. Depois de ter sido criado o novo domínio, clique em **novo endereço**.
   
    ![Novo endereço](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "novo endereço")

5. Na caixa de diálogo novo do endereço, execute os seguintes passos de um Azure válido que pretende aprovisionar de conta do AD:
   
    ![Guardar](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "guardar")
   
    a. No **endereço de correio eletrónico** caixa de texto, tipo **endereço de correio eletrónico** do utilizador como  **BrittaSimon@contoso.com** .
    
    b. No **nome Global** caixa de texto, tipo de **username** como **BrittaSimon**.

    c. No **palavra-passe**, e **Confirmar palavra-passe** caixas de texto, tipo de **palavra-passe** do utilizador.
   
    b. Clique em **Guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Portal pessoais Mimecast ou APIs fornecidas pelo Portal pessoais Mimecast aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao Portal pessoais Mimecast.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon portal pessoais Mimecast, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Portal pessoais Mimecast**.

    ![A ligação de Portal pessoais Mimecast na lista de aplicações](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de Portal pessoais Mimecast no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Portal pessoais Mimecast.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

