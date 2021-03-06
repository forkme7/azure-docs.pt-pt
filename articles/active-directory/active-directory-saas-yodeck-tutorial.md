---
title: 'Tutorial: Integração do Azure Active Directory com Yodeck | Microsoft Docs'
description: Saiba como configurar o início de sessão entre o Azure Active Directory e Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 550eb3f25a1b17600a5c4eee51ff1867d2f1fb34
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Tutorial: Integração do Azure Active Directory com Yodeck

Neste tutorial, irá aprender a integrar Yodeck com o Azure Active Directory (Azure AD).

Integrar Yodeck com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Yodeck.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Yodeck (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Yodeck, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Yodeck-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Yodeck a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-yodeck-from-the-gallery"></a>Adicionar Yodeck a partir da Galeria
Para configurar a integração de Yodeck com o Azure AD, terá de adicionar Yodeck a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Yodeck a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Yodeck**, selecione **Yodeck** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Yodeck na lista de resultados](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Yodeck com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Yodeck é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Yodeck tem de ser estabelecida.

Para configurar e testar o Azure AD-início de sessão único com Yodeck, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Yodeck](#create-a-yodeck-test-user)**  - para ter um homólogo de Britta Simon Yodeck que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Yodeck.

**Para configurar o Azure AD-início de sessão único com Yodeck, execute os seguintes passos:**

1. No portal do Azure, no **Yodeck** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. No **Yodeck domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Domínio Yodeck e os URLs únicos de informações de início de sessão](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url.png)

    No **identificador (ID de entidade)** caixa de texto, escreva o URL: `https://app.yodeck.com/api/v1/account/metadata/`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Domínio Yodeck e os URLs únicos de informações de início de sessão](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url1.png)

    No **URL de início de sessão** caixa de texto, escreva o URL: `https://app.yodeck.com/login`

5. No **certificado de assinatura de SAML** secção, clique no botão Copiar para copiar **Url de metadados de Federação de aplicação** e cole-o bloco de notas.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-yodeck-tutorial/tutorial_general_400.png)
    
7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Yodeck como administrador.

8. Clique em **as definições de utilizador** opção formulário canto superior direito da página e selecione **as definições da conta**.

    ![Configuração de Yodeck](./media/active-directory-saas-yodeck-tutorial/configure1.png)

9. Selecione **SAML** e execute os seguintes passos:

    ![Configuração de Yodeck](./media/active-directory-saas-yodeck-tutorial/configure2.png)

    a. Selecione **importar do URL**.

    b. No **URL** caixa de texto, cole o **Url de metadados de Federação de aplicação** valor que copiou do portal do Azure e clique em **importação**.
    
    c. Depois de importar **Url de metadados de Federação de aplicação**, os campos restantes preencher automaticamente.

    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-yodeck-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-yodeck-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-yodeck-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-yodeck-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-yodeck-test-user"></a>Criar um utilizador de teste Yodeck

Para permitir que os utilizadores do Azure AD iniciem sessão nos Yodeck, têm de ser aprovisionados para Yodeck.
No caso de Yodeck, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Yodeck como administrador.

2. Clique em **as definições de utilizador** opção formulário canto superior direito da página e selecione **utilizadores**.

    ![Adicionar empregado](./media/active-directory-saas-yodeck-tutorial/user1.png)

3. Clique em **+ utilizador** para abrir o **detalhes de utilizador** separador.

    ![Adicionar empregado](./media/active-directory-saas-yodeck-tutorial/user2.png)

4. No **detalhes de utilizador** diálogo página, execute os seguintes passos:

    ![Adicionar empregado](./media/active-directory-saas-yodeck-tutorial/user3.png)

    a. No **nome próprio** caixa de texto, como o tipo do nome próprio do utilizador **Britta**.

    b. No **Apelido** caixa de texto, como o tipo o último nome de utilizador **Simon**.

    c. No **E-Mail** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador **brittasimon@contoso.com**.

    d. Selecione adequado **permissões de conta** opção de acordo com os requisitos organizacionais.
    
    e. Clique em **Guardar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Yodeck.

![Atribuir a função de utilizador][200]

**Para atribuir Britta Simon a Yodeck, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201]

2. Na lista de aplicações, selecione **Yodeck**.

    ![A ligação de Yodeck na lista de aplicações](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Yodeck no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Yodeck.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_203.png

