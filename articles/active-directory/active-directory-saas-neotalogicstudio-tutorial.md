---
title: "Tutorial: Integração do Azure Active Directory com Neota lógica Studio | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Neota lógica Studio."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 842605e6-a91d-42cc-a0bb-e23e67173ae2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: jeedes
ms.openlocfilehash: 839a4ed8bb3facbfd41f8a9ca2849b475e03ffc1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-neota-logic-studio"></a>Tutorial: Integração do Azure Active Directory com Neota lógica Studio

Neste tutorial, irá aprender a integrar Neota lógica Studio com o Azure Active Directory (Azure AD).

Integrar Neota lógica Studio com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Neota lógica Studio
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Neota lógica Studio (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. [O que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Neota lógica Studio, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Neota lógica Studio início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Neota lógica Studio a partir da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-neota-logic-studio-from-the-gallery"></a>Adicionar Neota lógica Studio a partir da galeria do

Para configurar a integração do Neota lógica Studio com o Azure AD, tem de adicionar Neota lógica Studio na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Neota lógica Studio a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Neota lógica Studio**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_neotalogicstudio_search.png)

5. No painel de resultados, selecione **Neota lógica Studio**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_neotalogicstudio_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-

Nesta secção, configure e teste do Azure AD-início de sessão único com Neota lógica Studio com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Neota lógica Studio é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Neota lógica Studio tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Neota lógica Studio.

Para configurar e testar o Azure AD-início de sessão único com Neota lógica Studio, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Neota lógica Studio](#creating-a-neota-logic-studio-test-user)**  - para ter um homólogo de Britta Simon Neota lógica Studio que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Neota lógica Studio.

**Para configurar o Azure AD-início de sessão único com Neota lógica Studio, execute os seguintes passos:**

1. No portal do Azure, no **Neota lógica Studio** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_neotalogicstudio_samlbase.png)

3. No **Neota lógica Studio domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_neotalogicstudio_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<sub domain>.neotalogic.com/a/<sub application>`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<sub domain>.neotalogic.com/wb`

    > [!NOTE] 
    > Estes valores não estiverem a real. Atualize estes valores com o URL de início de sessão & Identificador real. Aqui sugerimos que utilize o valor de cadeia exclusivo no identificador de. Contacte [equipa de suporte de cliente do Neota lógica Studio](https://www.neotalogic.com/contact-us/) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_neotalogicstudio_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_400.png)

6. Para obter SSO configurado para a sua aplicação, contacte [suporte Neota lógica Studio](https://www.neotalogic.com/contact-us/) equipa e forneça-los com transferido **XML de metadados** ficheiro.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-neotalogicstudio-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-neotalogicstudio-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-neotalogicstudio-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-neotalogicstudio-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-neota-logic-studio-test-user"></a>Criar um utilizador de teste Neota lógica Studio

Nesta secção, vai criar um utilizador chamado Britta Simon no Neota lógica Studio. trabalhar com [equipa de suporte de cliente do Neota lógica Studio](https://www.neotalogic.com/contact-us/) para adicionar os utilizadores na plataforma Neota lógica Studio. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Neota lógica Studio.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Neota lógica Studio, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Neota lógica Studio**.

    ![Configurar o início de sessão único](./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_neotalogicstudio_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Clique no mosaico Neota lógica Studio no painel de acesso, será redirecionado para a página de início de sessão na organização. Após o início de sessão com êxito, é irá ser com sessão iniciada para a aplicação de Neota lógica Studio. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-neotalogicstudio-tutorial/tutorial_general_203.png

