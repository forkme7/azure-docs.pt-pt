---
title: Acesso condicional do Azure Active Directory | Microsoft Docs
description: Saiba como o acesso condicional no Azure Active Directory pode ajudá-lo a gerir o controlo de acesso a partir de uma localização central.
services: active-directory
keywords: acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, as políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 94d34df771334319db36009053a7264f0aeacf46
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Acesso condicional no Azure Active Directory

A segurança é uma preocupação superior para organizações com a nuvem. Um aspeto chave da segurança de nuvem é a identidade e acesso quando se trata de gerir os recursos de nuvem. No mundo mobile-primeiro, primeiro de nuvem, os utilizadores podem aceder a recursos da organização utilizando uma variedade de dispositivos e aplicações a partir de qualquer lugar. Como resultado, apenas concentrar-se em quem pode aceder a um recurso já não faz suficiente. Para mestra o equilíbrio entre produtividade e segurança, profissionais de TI tem também de fator de forma a ser aceder a recursos para uma decisão de controlo de acesso. Com o acesso condicional do Azure AD, pode resolver este requisito. Acesso condicional é uma funcionalidade do Azure Active Directory permite-lhe aplicar controlos de acesso a aplicações no seu ambiente com base nas condições específicas de uma localização central. 


![Controlo](./media/active-directory-conditional-access-azure-portal/81.png)

Este artigo fornece uma descrição geral conceptual do acesso condicional no Azure AD.


## <a name="common-scenarios"></a>Cenários comuns

No mundo mobile-primeiro, primeiro de nuvem, o Azure Active Directory permite início de sessão único para dispositivos, aplicações e serviços em qualquer lugar. Com a proliferação de dispositivos (incluindo o BYOD), trabalham em redes empresariais e de terceiros aplicações SaaS, profissionais de TI são confrontados com dois objetivos adversária:

- Capacitar os utilizadores sejam produtivos onde quer que e sempre
- Proteger os ativos da empresa em qualquer altura

Ao utilizar políticas de acesso condicional, pode aplicar os controlos de acesso à direita nas condições necessárias. Acesso condicional do Azure AD fornece maior segurança quando for necessário e mantém-se fora do modo do utilizador quando não se encontra. 

Seguem-se alguns problemas de acesso comuns que acesso condicional pode ajudar com:



- **[Início de sessão risco](active-directory-conditional-access-conditions.md#sign-in-risk)**: Azure AD Identity Protection Deteta riscos de início de sessão. Como restringir acesso se um risco de início de sessão detetado indica um ator incorretos? E se gostaria de obter uma provas mais forte que um início de sessão foi efetuado pelo utilizador legítimo ou os doubts são suficientemente seguras para bloquear o mesmo utilizadores específicos de aceder a uma aplicação?

- **[Localização de rede](active-directory-conditional-access-locations.md)**: do Azure AD é acessível a partir de qualquer lugar. E se uma tentativa de acesso é executada a partir de uma localização de rede que não se encontra sob o controlo do seu departamento de TI? Utilizando uma combinação de nome de utilizador e palavra-passe pode ser suficiente boa como tentativas de uma prova de identidade para o acesso aos recursos da sua rede empresarial. E se a procura uma prova de identidade mais forte para acesso tenta que são iniciadas a partir do outro inesperados países ou regiões do mundo? E se quiser, mesmo bloquear o acesso tenta de determinadas localizações?  

- **[Gestão de dispositivos](active-directory-conditional-access-conditions.md#device-platforms)**: no Azure AD, os utilizadores podem aceder a aplicações na nuvem de uma vasta gama de dispositivos, incluindo mobile bem como dispositivos pessoais. E se é a pedido que tentativas de acesso deve apenas ser efetuada através de dispositivos que são geridos pelo departamento de TI? E se ainda pretende bloquear determinados tipos de dispositivo acedam a aplicações em nuvem no seu ambiente? 

- **[Aplicação de cliente](active-directory-conditional-access-conditions.md#client-apps)**: atualmente, pode aceder a várias aplicações na nuvem através de diferentes tipos de aplicação, tais como aplicações baseadas na web, as aplicações móveis ou aplicações de ambiente de trabalho. E se é efetuada uma tentativa de acesso, utilização de um tipo de aplicação de cliente que provoque problemas conhecidos? E se necessitam de um dispositivo que é gerido pelo departamento de TI para determinados tipos de aplicação? 

Estas perguntas e as respostas relacionadas representam cenários comuns de acesso de acesso condicional do Azure AD. Acesso condicional é uma funcionalidade do Azure Active Directory permite-lhe processar cenários de acesso utilizando uma abordagem baseada em políticas.


## <a name="conditional-access-policies"></a>Políticas de acesso condicional

Uma política de acesso condicional é a definição de um cenário de acesso através do padrão de seguinte:

![Controlo](./media/active-directory-conditional-access-azure-portal/10.png)

**Em seguida, fazer isto** define a resposta da sua política. É importante salientar que o objetivo da política de acesso condicional não conceder acesso a uma aplicação de nuvem. No Azure AD, conceder acesso a aplicações em nuvem é requerente atribuições de utilizador. Com a política de acesso condicional, controla utilizadores autorizados como (utilizadores que tenham sido concedidos acesso a uma aplicação de nuvem) podem aceder a aplicações na nuvem em condições específicas. Na sua resposta impor requisitos adicionais, tais como a autenticação multifator, um dispositivo gerido e outras pessoas. No contexto do acesso condicional do Azure AD, os requisitos que da política impõe são denominados controlos de acesso. O formato mais restritivas, a política pode bloquear o acesso. Para obter mais informações, consulte [aceder controlos de acesso condicional do Azure Active Directory](active-directory-conditional-access-controls.md).
     

**Quando isto acontece** define o motivo para acionar a política. Esta razão é caracterizada por um grupo de condições que têm foram satisfeitos. No acesso condicional do Azure AD, as condições de atribuição de dois desempenham uma função especial:

- **[Os utilizadores](active-directory-conditional-access-conditions.md#users-and-groups)**: os utilizadores executar uma tentativa de acesso (**quem**). 

- **[Aplicações em nuvem](active-directory-conditional-access-conditions.md#cloud-apps)**: os destinos de uma tentativa de acesso (**que**).    

Estas duas condições são obrigatórias numa política de acesso condicional. Para além das duas condições obrigatórias, também pode incluir condições adicionais que descrevem como é efetuada a tentativa de acesso. Exemplos comuns estiver a utilizar dispositivos móveis ou localizações que pertencem à sua rede empresarial. Para obter mais informações, consulte [condições de acesso condicional do Azure Active Directory](active-directory-conditional-access-conditions.md).   

A combinação de condições com os controlos de acesso representa uma política de acesso condicional. 

![Controlo](./media/active-directory-conditional-access-azure-portal/51.png)

Com o acesso condicional do Azure AD, pode controlar os utilizadores autorizados como pode aceder às suas aplicações em nuvem. O objetivo da política de acesso condicional é impor controlos de acesso adicionais numa tentativa de acesso a uma aplicação de nuvem é suscitada pelo departamento pela forma como é efetuada uma tentativa de acesso.

Uma vantagem de utilizar uma abordagem baseada em políticas para proteger o acesso às suas aplicações em nuvem é que pode iniciar drafting os requisitos da política para o seu ambiente utilizando a estrutura descrita neste artigo, sem ter de se preocupar sobre a implementação técnica. 

## <a name="what-you-need-to-know"></a>O que precisa de saber

### <a name="general-requirements-for-using-conditional-access"></a>Requisitos gerais para utilizar o acesso condicional

Pode utilizar o acesso condicional do Azure AD para proteger as aplicações na nuvem quando uma tentativa de autenticação é proveniente de:

- Um browser

- Uma aplicação de cliente que utiliza a autenticação moderna

- Exchange ActiveSync

Para obter mais informações, consulte [aplicações de cliente](active-directory-conditional-access-conditions.md#client-apps).

Alguns [aplicações em nuvem](active-directory-conditional-access-conditions.md#cloud-apps) também suporta os protocolos de autenticação existente. Isto aplica-se, por exemplo, ao SharePoint Online e Exchange Online. Quando uma aplicação de cliente pode utilizar um protocolo de autenticação existente para aceder uma aplicação de nuvem, o Azure AD não é possível impor uma política de acesso condicional esta tentativa de acesso. Para impedir que uma aplicação de cliente de ignorar a imposição de políticas, deve verificar se é possível ativar apenas a autenticação moderna nas aplicações cloud afetados.

Exemplos de cliente de acesso condicional de aplicações não é aplicável a são:

- Office 2010 e anterior

- Office 2013 quando a autenticação moderna não está ativada

Para obter mais informações, consulte [configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md).


### <a name="license-requirements-for-using-conditional-access"></a>Requisitos de licença para utilizar o acesso condicional

Utilizar o acesso condicional é necessária uma licença do Azure AD Premium. Para obter a licença adequada para os seus requisitos, consulte [comparar geralmente disponíveis funcionalidades das edições gratuito, Basic e Premium](https://azure.microsoft.com/pricing/details/active-directory/).


## <a name="next-steps"></a>Passos Seguintes

- Se pretender saber mais sobre:
    - Condições, consulte [condições de acesso condicional do Azure Active Directory](active-directory-conditional-access-conditions.md).

    - Controlos de acesso, consulte [aceder controlos de acesso condicional do Azure Active Directory](active-directory-conditional-access-controls.md).

- Se pretender obter alguma experiência com a configuração de políticas de acesso condicional, consulte [introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 
