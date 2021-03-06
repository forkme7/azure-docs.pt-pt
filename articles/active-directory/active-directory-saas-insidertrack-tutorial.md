---
title: "Tutorial: Integração do Azure Active Directory com Insider controlar | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e controlar internas."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 53b6a928-e8a4-4cf1-9952-50cd3f013b7c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: jeedes
ms.openlocfilehash: 02e6bb0c4bf8ab32f9cfb1481b77a4268af52b18
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-insider-track"></a>Tutorial: Integração do Azure Active Directory com controlar internas

Neste tutorial, irá aprender a integrar Insider controlar com o Azure Active Directory (Azure AD).

Integrar Insider controlar com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao controlar internas.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para controlar internas (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com controlar internas, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma utilização Track-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de controlar internas da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-insider-track-from-the-gallery"></a>A adição de controlar internas da galeria do
Para configurar a integração de controlar Insider com o Azure AD, tem de adicionar controlar internas da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar controlar internas da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Insider controlar**, selecione **Insider controlar** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Controlar internas na lista de resultados](./media/active-directory-saas-insidertrack-tutorial/tutorial_insidertrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com controlar internas, com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no internas controlar é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no internas controlar tem de ser estabelecida.

Controlar internas, atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com controlar internas, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de controlar Insider](#create-an-insider-track-test-user)**  - para ter um homólogo de Britta Simon Insider controlar que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de controlar internas.

**Para configurar o Azure AD-início de sessão único com Insider controlar, execute os seguintes passos:**

1. No portal do Azure, no **Insider controlar** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-insidertrack-tutorial/tutorial_insidertrack_samlbase.png)

3. No **Insider controlar domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio de controlar internas e URLs único informações de início de sessão](./media/active-directory-saas-insidertrack-tutorial/tutorial_insidertrack_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte: `https://<companyname>/InsiderTrack.Portal.<companyname>/Sso/`

    > [!NOTE] 
    > Início de sessão do valor de URL não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte internas controlar cliente](https://cytecsolutions.com/contact/) para obter este valor.

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-insidertrack-tutorial/tutorial_insidertrack_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-insidertrack-tutorial/tutorial_general_400.png)

6. No **Insider controlar configuração** secção, clique em **configurar controlar de Insider** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de controlar internas](./media/active-directory-saas-insidertrack-tutorial/tutorial_insidertrack_configure.png) 

7. Para configurar o início de sessão único em **Insider controlar** lado, terá de enviar o transferido **XML de metadados, Sign-Out URL, ID de entidade de SAML,** e **único início de sessão no URL do serviço SAML** para [Equipa de suporte internas controlar](https://cytecsolutions.com/contact/). Se definir esta definição para que a ligação de SAML SSO corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-insidertrack-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-insidertrack-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-insidertrack-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-insidertrack-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-insider-track-test-user"></a>Criar um utilizador de teste de controlar internas

Nesta secção, vai criar um utilizador chamado Britta Simon controlar internas. Trabalhar com [equipa de suporte internas controlar](https://cytecsolutions.com/contact/) para adicionar os utilizadores na plataforma controlar internas. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para controlar internas.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon para controlar internas, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Insider controlar**.

    ![A ligação de controlar internas na lista de aplicações](./media/active-directory-saas-insidertrack-tutorial/tutorial_insidertrack_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de controlar Insider no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de controlar internas.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-insidertrack-tutorial/tutorial_general_203.png

