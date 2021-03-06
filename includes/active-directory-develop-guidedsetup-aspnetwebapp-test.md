---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7e9518f8a90faa0566b96d58992b01e4b0a642f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
## <a name="test-your-code"></a>Testar o seu código

Para testar a aplicação no Visual Studio, prima **F5** para executar o projeto. O browser abre o http://<span></span>localhost: localização {porta} e ver o **iniciar sessão com a Microsoft** botão. Selecione o botão para iniciar o processo de início de sessão.

Quando estiver pronto para executar o teste, utilize uma conta do Microsoft Azure Active Directory (Azure AD) (conta escolar ou profissional) ou uma conta Microsoft pessoal (<span>em direto.</span> com ou <span>outlook.</span> com) para iniciar sessão.

![Inicie sessão com a Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Inicie sessão na sua conta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Ver os resultados da aplicação
Depois de iniciar sessão, o utilizador é redirecionado para a home page do seu site. A página inicial é o URL HTTPS que é especificado nas suas informações de registo de aplicação no Portal de registo de aplicações do Microsoft. A home page inclui uma mensagem de boas-vindas *"Hello \<utilizador >,"* uma hiperligação para terminar sessão e uma ligação para ver as afirmações do utilizador. A ligação para afirmações do utilizador navega até à *afirmações* controlador que criou anteriormente.

### <a name="browse-to-see-the-users-claims"></a>Procurar para ver as afirmações do utilizador
Para ver as afirmações do utilizador, selecione a ligação para procurar a vista de controlador que só está disponível para utilizadores autenticados.

#### <a name="view-the-claims-results"></a>Ver os resultados de afirmações
Depois de procura para a vista de controlador, deverá ver uma tabela que contém as propriedades básicas para o utilizador:

|Propriedade |Valor |Descrição |
|---|---|---|
|**Nome** |Nome completo do utilizador | O utilizador do primeiro e último nome.
|**Nome de Utilizador** |utilizador<span>@domain.com</span> | O nome de utilizador que é utilizado para identificar o utilizador.
|**Assunto** |Assunto |Uma cadeia que identifica exclusivamente o utilizador em web.|
|**ID do inquilino** |GUID | A **guid** que exclusivamente representa a organização do utilizador do Azure AD.|

Além disso, deverá ver uma tabela de todas as afirmações que estão no pedido de autenticação. Para obter mais informações, consulte o [lista de afirmações são um Token de ID do Azure AD de](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Teste o acesso a um método que tem um atributo de autorizar (opcional)
Para testar o acesso como um utilizador anónimo para um controlador protegido com o `Authorize` atributo, siga estes passos:
1. Selecione a ligação para terminar sessão de utilizador e concluir o processo de início de sessão.
2. No seu browser, escreva http://<span></span>localhost: {porta} / afirmações para aceder ao seu controlador de que está protegido com o `Authorize` atributo.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados depois de acesso a um controlador protegido
Lhe for pedida a autenticação para utilizar a vista de controlador protegido.

## <a name="advanced-options"></a>Opções avançadas

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteger o seu Web site completo
Para proteger o seu Web site todo, do **global. asax** ficheiro, adicione o `AuthorizeAttribute` atributo para o `GlobalFilters` filtrar no `Application_Start` método:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode iniciar sessão sua aplicação
Por predefinição quando criar a aplicação criada por este guia, a aplicação irá aceitar sessão ins do pessoal contas (incluindo outlook.com, live.com e outros), bem como funcionam contas escolares ou profissionais de qualquer da empresa ou organização que tem integrado com o Azure Active Directory. Esta é uma opção recomendada para aplicações SaaS.

Para restringir o início de sessão de acesso de utilizadores para a sua aplicação, estão disponíveis várias opções:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restringir os utilizadores da instância do Active Directory da organização apenas um para iniciar sessão sua aplicação (único inquilino)

Esta opção é um cenário comum para *aplicações LOB*: Se pretender que a aplicação para aceitar inícios de sessão apenas a partir de contas que pertençam a uma instância específica do Azure Active Directory (incluindo *as contas de convidados*dessa instância) faça o seguinte:

1. No **Web. config** de ficheiros, altere o valor para o `Tenant` parâmetro de `Common` para o nome do inquilino da organização, tais como `contoso.onmicrosoft.com`.
2. No seu [classe de Startup da OWIN](#configure-the-authentication-pipeline), defina o `ValidateIssuer` argumento `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opção 2: Restringir o acesso à sua aplicação para os utilizadores numa lista específica de organizações

Pode restringir o acesso de início de sessão para contas de utilizador único que estão numa organização do Azure AD que se encontra na lista das organizações permitidas:
1. No seu [classe de Startup da OWIN](#configure-the-authentication-pipeline), defina o `ValidateIssuer` argumento `true`.
2. Defina o valor da `ValidIssuers` parâmetro à lista de permitidos organizações.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Utilize um método personalizado para validar emissores
Pode implementar um método personalizado para validar os emissores utilizando o **IssuerValidator** parâmetro. Para obter mais informações sobre como utilizar este parâmetro, leia sobre o [TokenValidationParameters classe](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) no MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
