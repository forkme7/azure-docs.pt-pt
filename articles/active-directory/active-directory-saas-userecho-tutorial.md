---
title: "Tutorial: Integração do Azure Active Directory com UserEcho | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e UserEcho."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a3e2d6263b475ca72df93f0f20874971409e4c54
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: Integração do Azure Active Directory com UserEcho

Neste tutorial, irá aprender a integrar UserEcho com o Azure Active Directory (Azure AD).

Integrar UserEcho com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao UserEcho
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para UserEcho (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com UserEcho, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um UserEcho-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar UserEcho a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-userecho-from-the-gallery"></a>Adicionar UserEcho a partir da Galeria
Para configurar a integração de UserEcho com o Azure AD, terá de adicionar UserEcho a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar UserEcho a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **UserEcho**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. No painel de resultados, selecione **UserEcho**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com UserEcho com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no UserEcho é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no UserEcho tem de ser estabelecida.

No UserEcho, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com UserEcho, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste UserEcho](#creating-a-userecho-test-user)**  - para ter um homólogo de Britta Simon UserEcho que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação UserEcho.

**Para configurar o Azure AD-início de sessão único com UserEcho, execute os seguintes passos:**

1. No portal do Azure, no **UserEcho** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. No **UserEcho domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.userecho.com/`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente UserEcho](https://feedback.userecho.com/) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. No **UserEcho configuração** secção, clique em **configurar UserEcho** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. Na outra janela do browser, inicie sessão site da sua empresa UserEcho como administrador.

8. Na barra de ferramentas na parte superior, clique em seu nome de utilizador para expandir o menu e, em seguida, clique em **configuração**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. Clique em **integrações**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. Clique em **Web site**e, em seguida, clique em **único início de sessão (SAML2)**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. No **único início de sessão (SAML)** página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. Como **ativada de SAML**, selecione **Sim**.
    
    b. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **SAML SSO URL** caixa de texto.
    
    c. Colar **Sign-Out URL**, que copiou do portal do Azure para o **logoout remoto URL** caixa de texto.
    
    d. Abra o seu certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-o para o **certificado x. 509** caixa de texto.
    
    e. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-userecho-test-user"></a>Criar um utilizador de teste UserEcho

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon UserEcho.

**Para criar um utilizador chamado Britta Simon UserEcho, execute os seguintes passos:**

1. Início de sessão no site da sua empresa UserEcho como administrador.

2. Na barra de ferramentas na parte superior, clique em seu nome de utilizador para expandir o menu e, em seguida, clique em **configuração**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. Clique em **utilizadores**, para expandir o **utilizadores** secção.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. Clique em **Utilizadores**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. Clique em **convidar um novo utilizador**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. No **convidar um novo utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. No **nome** caixa de texto, nome de tipo do utilizador como Britta Simon.
    
    b.  No **E-Mail** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador Brittasimon@contoso.com.
    
    c. Clique em **convidar**.

É enviado um convite para Britta, que permite-lhe começar a utilizar UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para UserEcho.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a UserEcho, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **UserEcho**.

    ![Configurar o início de sessão único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.  

Quando clica no mosaico UserEcho no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de UserEcho.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

