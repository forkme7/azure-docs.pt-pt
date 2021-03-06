---
title: Saiba como fornecer afirmações opcionais para a aplicação do Azure AD | Microsoft Docs
description: Um guia para adicionar afirmações personalizadas ou adicionais para os tokens de SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo Azure Active Directory.
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: f87487c4ee56ae90eb5825b0e77610fac73bd3fa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="optional-claims-in-azure-ad-preview"></a>Afirmações opcionais no Azure AD (pré-visualização)

Esta funcionalidade é utilizada pelos programadores de aplicações para especificar quais as afirmações que pretendem em tokens enviados para a respetiva aplicação. Pode utilizar as afirmações opcionais para:
-   Selecione afirmações adicionais a incluir nos tokens para a sua aplicação.
-   Altere o comportamento de determinadas afirmações que devolve do Azure AD em tokens.
-   Adicionar e afirmações personalizadas para a sua aplicação de acesso. 

> [!Note]
> Esta capacidade está atualmente em pré-visualização pública. Esteja preparado para reverter ou remover quaisquer alterações. A funcionalidade está disponível em qualquer subscrição do Azure AD durante a pré-visualização pública. No entanto, quando a funcionalidade torna-se geralmente disponível, alguns aspetos da funcionalidade poderão necessitar de uma subscrição do Azure AD premium.

Para obter a lista de afirmações padrão e como são utilizados em tokens, consulte o [Noções básicas de tokens emitidos pelo Azure AD](active-directory-token-and-claims.md). 

Um dos objetivos do [ponto final v 2.0 do Azure AD](active-directory-appmodel-v2-overview.md) é mais pequenos tamanhos de token para garantir um desempenho ideal pelos clientes.  Como resultado, várias afirmações incluídas anteriormente o acesso e tokens de ID já não estão presentes em tokens de v 2.0 e devem ser-lhe pedidas para especificamente numa base por aplicação.  

**Tabela 1: aplicabilidade**

| Tipo de conta | Ponto final de v 1.0                      | Ponto final v 2.0  |
|--------------|------------------------------------|----------------|
| MSA          | N/d - RPS pedidos são utilizados em vez disso, | Suporte do |
| AAD          | Suportadas                          | Suportadas      |

## <a name="standard-optional-claims-set"></a>Conjunto de afirmações opcional padrão
O conjunto de afirmações opcionais disponíveis por predefinição para as aplicações utilizem estão listados abaixo.  Para adicionar afirmações opcionais personalizadas para a sua aplicação, consulte [extensões de diretórios](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions), abaixo. 

> [!Note]
>A maioria destas afirmações pode ser incluída no JWTs, mas não os tokens SAML, exceto se encontra onde indicado na coluna de tipo de Token.  Além disso, enquanto opcionais afirmações só são suportadas para utilizadores AAD atualmente, está a ser adicionado suporte MSA.  Quando MSA tem afirmações opcionais suporta o ponto final v 2.0, a coluna de tipo de utilizador será denota se uma afirmação está disponível para um utilizador AAD ou MSA.  

**Tabela 2: Conjunto de afirmações opcional padrão**

| Nome                     | Descrição                                                                                                                                                                                     | Tipo de token | Tipo de Utilizador | Notas                                                                                                                                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `auth_time`                | Hora quando o utilizador autenticado pela última vez.  A especificação de OpenID Connect consulte.                                                                                                                                | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_region_scope`      | Região do inquilino de recursos                                                                                                                                                                   | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `signin_state`             | Inicie sessão no estado de afirmação                                                                                                                                                                             | JWT        |           | 6 devolver valores, como sinalizadores:<br> "dvc_mngd": o dispositivo é gerido<br> "dvc_cmp": dispositivo está em conformidade<br> "dvc_dmjd": o dispositivo está associado a um domínio<br> "dvc_mngd_app": o dispositivo é gerido através de MDM<br> "inknownntwk": o dispositivo está dentro de uma rede conhecida.<br> "kmsi": mantenha-Me assinado foi utilizado. <br> |
| `controls`                 | Multivalue de afirmação que contém os controlos de sessão impostos por políticas de acesso condicional.                                                                                                       | JWT        |           | 3 valores:<br> "app_res": A aplicação precisa de impor restrições mais granulares. <br> "ca_enf": imposição do acesso condicional foi adiada tendo ainda é necessária. <br> "no_cookie": Este token é insuficiente para exchange para um cookie no browser. <br>                              |
| `home_oid`                 | Para os utilizadores convidados, o ID de objeto do utilizador no inquilino doméstica do utilizador.                                                                                                                           | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `sid`                      | ID de sessão utilizado para a sessão-signout de utilizador.                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `platf`                    | Plataforma de dispositivos                                                                                                                                                                                 | JWT        |           | Restringe-se aos dispositivos geridos, que podem verificar o tipo de dispositivo.                                                                                                                                                                                                                              |
| `verified_primary_email`   | Tenham origem a partir PrimaryAuthoritativeEmail do utilizador                                                                                                                                               | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `verified_secondary_email` | Tenham origem a partir SecondaryAuthoritativeEmail do utilizador                                                                                                                                             | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `enfpolids`                | Os IDs de política aplicada. Uma lista de política de IDs, que foram considerados para o utilizador atual.                                                                                                         | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `vnet`                     | Informações do especificador VNET.                                                                                                                                                                     | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `fwd`                      | Endereço IP.  Adiciona o endereço IPv4 original do cliente pedir (quando dentro de uma VNET)                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `ctry`                     | País do utilizador                                                                                                                                                                                  | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `tenant_ctry`              | País do inquilino de recursos                                                                                                                                                                       | JWT        |           |                                                                                                                                                                                                                                                                                         |
| `acct`    | Estado da conta de utilizadores no inquilino.  Se o utilizador for um membro do inquilino, o valor é `0`.  Se forem um convidado, o valor é `1`.  | JWT, SAML | | |
| `upn`                      | UserPrincipalName afirmação.  Embora esta afirmação é automaticamente incluída, pode especificá-la como uma afirmação opcional para anexar propriedades adicionais para modificar o respetivo comportamento no caso de utilizador convidado. | JWT, SAML  |           | Propriedades adicionais: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash`                                                                                                                                                                 |

### <a name="v20-optional-claims"></a>Afirmações opcionais v 2.0
Estas afirmações sempre estão incluídas no v 1.0 tokens, mas são removidas de tokens de v 2.0, a menos que o pedido.  Estas afirmações só são aplicáveis para JWTs (tokens de identidade e Tokens de acesso).  

**Tabela 3: Apenas de v 2.0 afirmações opcionais**

| JWT afirmação     | Nome                            | Descrição                                                                                                                    | Notas |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP, o cliente de a sessão.                                                                                      |       |
| `onprem_sid`  | Identificador de segurança no local |                                                                                                                                |       |
| `pwd_exp`     | Hora de expiração de palavra-passe        | A datetime no qual a palavra-passe expira.                                                                                    |       |
| `pwd_url`     | URL de palavra-passe de alteração             | Um URL que os utilizadores podem visitar para alterar a palavra-passe.                                                                        |       |
| `in_corp`     | Dentro da rede empresarial        | Sinais se o cliente está a iniciar sessão da rede empresarial. Se não estiverem, a afirmação não está incluída                     |       |
| `nickname`    | Alcunha                        | Um nome adicional para o utilizador, separado do nome do primeiro ou último.                                                             |       |                                                                                                                |       |
| `family_name` | Apelido                       | Fornece o último nome, apelido ou nome de família do utilizador, tal como definido no objeto de utilizador do Azure AD. <br>"family_name": "Santos" |       |
| `given_name`  | Nome próprio                      | Fornece o primeiro ou "fornecido" nome do utilizador, conforme definido no objeto de utilizador do Azure AD.<br>"given_name": "Francisco"                   |       |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de afirmações opcionais

Alguns afirmações opcionais podem ser configuradas para alterar a forma como a afirmação é devolvida.  Estas propriedades adicionais são utilizadas principalmente para ajudar a migração das aplicações no local com as expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda-o com os clientes que não podem processar hashmarks (`#`) no UPN)

**Tabela 4: Valores para configurar afirmações opcionais padrão**

| Nome da propriedade                                     | Nome de propriedade adicionais                                                                                                             | Descrição |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |             |
| | `include_externally_authenticated_upn`              | Inclui o convidado UPN conforme armazenado no inquilino do recurso.  Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com`                            |             
| | `include_externally_authenticated_upn_without_hash` | Mesmo que acima, exceto que a hashmarks (`#`) são substituídos por carateres de sublinhado (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |             

> [!Note]
>Especificar que a afirmação opcional upn sem uma propriedade adicional não altera nenhum comportamento – para ver uma nova afirmação emitida no token, pelo menos uma das propriedades adicionais têm de ser adicionada. 


#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Este objeto OptionalClaims faz com que o token de ID devolvido ao cliente para incluir o upn de outro com o inquilino de raiz adicional e informações de inquilino de recursos.  

## <a name="configuring-optional-claims"></a>Configurar afirmações opcionais

Pode configurar afirmações opcionais para a sua aplicação ao modificar o manifesto da aplicação (consulte exemplo abaixo). Para obter mais informações, consulte o [compreender o artigo de manifesto de aplicação do Azure AD](active-directory-application-manifest.md).

**Esquema de exemplo:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "upn", 
                   "essential": false, 
                   "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ],
 "accessToken": [ 
             {
                    "name": "auth_time", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Tipo de OptionalClaims

Declara as afirmações opcionais, pedidas por uma aplicação. Uma aplicação pode configurar afirmações opcionais devem ser devolvidos em cada um dos três tipos de tokens (ID token, token, SAML 2 token de acesso) pode receber do serviço de token de segurança. A aplicação pode configurar um conjunto diferente de afirmações opcionais devem ser devolvidos em cada tipo de token. A propriedade OptionalClaims a entidade de aplicação é um objeto de OptionalClaims.

**Tabela 5: Propriedades do tipo de OptionalClaims**

| Nome        | Tipo                       | Descrição                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As afirmações opcionais devolvidas no token JWT ID.     |
| `accessToken` | Coleção (OptionalClaim) | As afirmações opcionais devolvidas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As afirmações opcionais devolvidas no SAML token.       |


### <a name="optionalclaim-type"></a>Tipo de OptionalClaim

Contém uma afirmação opcional associada uma aplicação ou um principal de serviço. As propriedades idToken, accessToken e saml2Token do [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) tipo é uma coleção de OptionalClaim.
Caso seja suportado por uma afirmação específica, também pode modificar o comportamento de OptionalClaim utilizando o campo de AdditionalProperties.

**Tabela 6: Propriedades do tipo de OptionalClaim**

| Nome                 | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da afirmação opcional.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A origem (objeto de diretório) da afirmação. Existem afirmações predefinidas e afirmações definido pelo utilizador a partir das propriedades de extensão. Se o valor de origem é nulo, a afirmação é uma afirmação opcional predefinida. Se o valor de origem for o utilizador, o valor na propriedade nome é propriedade de extensão do objeto user. |
| `essential`            | Edm.Boolean             | Se o valor for VERDADEIRO, a afirmação especificada pelo cliente é necessária garantir uma experiência de autorização suave da tarefa específica solicitada pelo utilizador final. O valor predefinido é falso.                                                                                                                 |
| `additionalProperties` | Coleção (EDM) | Propriedades adicionais de afirmação. Se existir uma propriedade nesta coleção, modifica o comportamento da afirmação opcional especificado na propriedade nome.                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>Configurar afirmações personalizadas através de extensões de diretórios

Para o conjunto de afirmações opcional padrão, além de tokens também podem ser configurados para incluir extensões de esquema de diretório (consulte o [artigo de extensões de esquema de diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) para obter mais informações).  Esta funcionalidade é útil para anexar informações de utilizador adicionais que pode utilizar a aplicação – por exemplo, um identificador adicional ou a opção de configuração importantes que o utilizador tem de definir. 

> [!Note]
> Extensões de esquema de diretório são uma funcionalidade como só de AAD, pelo que o se a sua aplicação manifesto pedidos uma extensão personalizada e um utilizador MSA nos registos ou na sua aplicação, estas extensões não serão devolvidas. 

### <a name="values-for-configuring-additional-optional-claims"></a>Valores para configurar afirmações opcionais adicionais 

Para os atributos de extensão, utilize o nome completo da extensão (no formato: `extension_<appid>_<attributename>`) no manifesto da aplicação. O `<appid>` tem de corresponder ao id da aplicação a pedir a afirmação. 

Dentro do JWT, estas afirmações vai ser emitidas com o seguinte formato de nome: `extn.<attributename>`.

Dentro os tokens SAML, estas afirmações irão ser emitidas com o formato URI seguinte: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="optional-claims-example"></a>Exemplo de afirmações opcional

Nesta secção, pode percorrer um cenário para ver como pode utilizar a funcionalidade de afirmações opcional para a sua aplicação.
Existem várias opções disponíveis para atualizar as propriedades na configuração da identidade de uma aplicação para ativar e configurar afirmações opcionais:
-   Pode modificar o manifesto da aplicação. O exemplo abaixo irá utilizar este método para efetuar a configuração. Leia o [compreender o documento de manifesto de aplicação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) primeiro para uma introdução para o manifesto.
-   Também é possível escrever uma aplicação que utiliza o [Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar a sua aplicação. O [entidade e referência de tipo complexo](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) a referência da Graph API guia pode ajudá-lo com a configuração de afirmações opcionais.

**Exemplo:** no exemplo abaixo, vai modificar o manifesto de uma aplicação para adicionar afirmações para acesso, o ID e SAML tokens pretendidos para a aplicação.
1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Depois de ter autenticado, escolha o seu inquilino do Azure AD ao selecioná-la a partir do canto superior direito da página.
3.  Selecione **extensão do Azure AD** do painel de navegação esquerdo e clique em **registos de aplicação**.
4.  Localize a aplicação que pretende configurar afirmações opcionais para na lista e clique na mesma.
5.  Na página da aplicação, clique em **manifesto** para abrir o editor de manifesto inline. 
6.  Pode editar diretamente o manifesto através deste editor. O manifesto segue o esquema para o [entidade aplicação](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)e uma vez guardado o manifesto de formatos de automática. Novo elemets serão adicionadas ao `OptionalClaims` propriedade.

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      Neste caso, foram adicionadas afirmações opcionais diferentes para cada tipo de token de que a aplicação pode receber. Os tokens de ID agora irão conter o UPN para utilizadores federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Os tokens de acesso agora irão receber a afirmação auth_time. Os tokens SAML agora irão conter a extensão de esquema de diretório skypeId (neste exemplo, o ID da aplicação para esta aplicação é ab603c56068041afb2f6832e2a17e237).  Os tokens SAML irão expor o ID de Skype como `extension_skypeId`.

7.  Quando tiver terminado o manifesto de atualização, clique em **guardar** para guardar o manifesto


## <a name="related-content"></a>Conteúdo relacionado
* Saiba mais sobre o [afirmações padrão](active-directory-token-and-claims.md) fornecido pelo Azure AD. 
