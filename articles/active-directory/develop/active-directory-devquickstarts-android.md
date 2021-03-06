---
title: Azure AD Android, introdução | Microsoft Docs
description: Como criar uma aplicação Android que se integra com o Azure AD para início de sessão e chamadas do Azure AD protegido APIs OAuth2.0 a utilizar.
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 25a908c542bf8fdd8008841a1865cdfb40d847fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android, introdução
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Se estiver a desenvolver uma aplicação de ambiente de trabalho, o Azure Active Directory (Azure AD) faz com que simples e fácil para si autenticar os utilizadores através das respetivas contas do Active Directory no local. Também permite que a aplicação de forma segura consumir qualquer API web protegida pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

Para Android clientes que necessitam de aceder a recursos protegidos, o Azure AD fornece o Active Directory Authentication Library (ADAL). É o único objetivo da ADAL facilitar a sua aplicação obter os tokens de acesso. Para demonstrar como é fácil, iremos irá criar uma aplicação Android lista de tarefas que:

* Obtém acesso para chamar uma API da lista de tarefas utilizando os tokens [protocolo de autenticação OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
* Obtém a lista de tarefas de um utilizador.
* Inicia os utilizadores.

Para começar a utilizar, precisa de um inquilino do Azure AD, onde pode criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Passo 1: Transferir e executar o servidor de exemplo TODO de API de REST do Node.js
O exemplo de TODO de API de REST do Node.js é escrito especificamente para funcionar com o nosso exemplo existente para criar um API de REST de itens pendentes de único inquilino do Azure AD. Este é um pré-requisito para o início rápido.

Para obter informações sobre como configurar esta opção, consulte nossos exemplos existentes no [Service do Azure Active Directory exemplo REST API para Node.js](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>Passo 2: Registar a API web no inquilino do Azure AD
Active Directory suporta a adição de dois tipos de aplicações:

- As APIs que oferecem serviços a utilizadores Web
- As aplicações (executado na web ou num dispositivo) que aceder às APIs web

Neste passo, que está a registar a API web que está a executar localmente para este exemplo de teste. Normalmente, esta API web é um serviço REST que é a funcionalidade que pretende que uma aplicação para aceder à oferta. Azure AD pode ajudar a proteger qualquer ponto final.

Partimos do princípio que está a registar a API de REST de TODO referenciado anteriormente. Mas isto funciona para qualquer API web que pretende que o Azure Active Directory para ajudar a proteger.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. No **diretório** lista, escolha o inquilino do Azure AD, onde é necessário registar a sua aplicação.
3. Clique em **todos os serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Introduza um nome amigável para a aplicação (por exemplo, **TodoListService**), selecione **aplicação Web e/ou Web API**e clique em **seguinte**.
6. Para o URL de início de sessão, introduza o URL de base para o exemplo. Por predefinição, este é `https://localhost:8080`.
7. Clique em **OK** para concluir o registo.
8. Enquanto ainda está no portal do Azure, aceda à sua página de aplicações, localize o valor de ID de aplicação e copiá-lo. Irá precisar deste mais tarde quando configurar a sua aplicação.
9. Do **definições** -> **propriedades** página, atualizar a aplicação ID URI - introduza `https://<your_tenant_name>/TodoListService`. Substitua `<your_tenant_name>` com o nome do seu inquilino do Azure AD.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Passo 3: Registar a aplicação de cliente nativo Android de exemplo
Tem de registar a aplicação web neste exemplo. Isto permite que a aplicação comunicar com a API web just-registado. Será refuse do Azure AD para o mesmo que a sua aplicação e peça para início de sessão, a menos que está registado. Que faz parte de segurança do modelo.

Partimos do princípio que está a registar a aplicação de exemplo referenciada anteriormente. Mas este procedimento funciona para qualquer aplicação que estiver a desenvolver.

> [!NOTE]
> Poderá wonder por que motivo está a colocar uma aplicação e uma API web no um inquilino. Como o poderá ter adivinhado, pode criar uma aplicação que acede a uma API externa que está registada no Azure AD a partir de outro inquilino. Se, fazê-lo, os seus clientes á consentimento para a utilização da API na aplicação. Biblioteca de autenticação do Active Directory para iOS encarrega-se deste consentimento para si. Como podemos explorar as funcionalidades mais avançadas, verá que se trata de uma parte importante do trabalho necessário para aceder ao conjunto de APIs do Microsoft Azure e Office, bem como qualquer outro fornecedor de serviços. Por agora, porque registado web API e a aplicação sob o mesmo inquilino, não verão qualquer pede consentimento. Isto é normalmente o caso se estiver a desenvolver uma aplicação apenas para a sua própria empresa a utilizar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. No **diretório** lista, escolha o inquilino do Azure AD, onde é necessário registar a sua aplicação.
3. Clique em **todos os serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Introduza um nome amigável para a aplicação (por exemplo, **TodoListClient Android**), selecione **aplicação cliente nativa**e clique em **seguinte**.
6. Para o URI de redirecionamento, introduza `http://TodoListClient`. Clique em **Concluir**.
7. A página de aplicações, localize o valor de ID de aplicação e copie-o. Irá precisar deste mais tarde quando configurar a sua aplicação.
8. Do **definições** página, selecione **permissões obrigatórias** e selecione **adicionar**.  Localize e selecione TodoListService, adicione o **acesso TodoListService** permissão em **permissões delegadas**e clique em **feito**.

Para criar com o Maven, pode utilizar pom.xml no nível superior:

1. Este repositório do clone para um diretório da sua preferência:

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. Siga os passos a [pré-requisitos para configurar o ambiente de Maven para Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven).
3. Configure o emulador com 19 de SDK.
4. Aceda à pasta raiz onde clonou o repositório.
5. Execute este comando: `mvn clean install`
6. Altere o diretório para o exemplo de início rápido: `cd samples\hello`
7. Execute este comando: `mvn android:deploy android:run`

   Deverá ver a aplicação iniciar.
8. Introduza as credenciais de utilizador de teste para tentar.

Pacotes JAR serão submetidas ao lado de pacote AAR.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Passo 4: Transferir a ADAL Android e adicione-o à sua área de trabalho do Eclipse
Que fizemos seja mais fácil têm várias opções para utilizar a ADAL no seu projeto Android:

* Pode utilizar o código de origem para importar esta biblioteca para o Eclipse e ligar à sua aplicação.
* Se estiver a utilizar o Android Studio, pode utilizar o formato do pacote AAR e os binários de referência.

### <a name="option-1-source-zip"></a>Opção 1: Origem Zip
Para transferir uma cópia do código fonte, clique em **transferir ZIP** no lado direito da página. Ou pode [transferir a partir do GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases).

### <a name="option-2-source-via-git"></a>Opção 2: Origem via Git
Para obter o código de origem do SDK através de Git, escreva:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>Opção 3: Binários através do Gradle
Pode obter os binários do repositório central Maven. O pacote de AAR pode ser incluído forma no seu projeto no Android Studio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>Opção 4: AAR através do Maven
Se estiver a utilizar o plug-in de M2Eclipse, pode especificar a dependência no seu ficheiro pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>Opção 5: Pacote JAR nessa pasta libs
Pode obter o ficheiro JAR do repositório Maven e removê-la para o **libs** pasta no seu projeto. Tem de copiar os recursos necessários ao seu projeto, porque os pacotes JAR não incluem-los.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>Passo 5: Adicionar referências a Android ADAL ao seu projeto
1. Adicione uma referência ao seu projeto e especifique-o como uma biblioteca do Android. Se estiver incerto como fazê-lo, pode obter mais informações o [site do Android Studio](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Adicione a dependência de projeto para a depuração para as definições do projeto.
3. Atualize o ficheiro de AndroidManifest.xml do seu projeto para incluir:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. Crie uma instância de AuthenticationContext a atividade principal. Os detalhes desta chamada ultrapassam o âmbito deste tópico, mas pode obter um bom ponto de partida ao observar o [exemplo Android Native Client](https://github.com/AzureAD/azure-activedirectory-library-for-android). No exemplo seguinte, SharedPreferences é a cache de predefinição, não sendo autoridade sob a forma de `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Copie este bloco de código para processar o fim do AuthenticationActivity depois do utilizador introduz credenciais e recebe um código de autorização:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Para pedir um token, defina uma chamada de retorno:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Por fim, voltar a pedir um token utilizando esse chamada de retorno:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

Eis uma explicação dos parâmetros:

* *recurso* é necessário e é o recurso que está a tentar aceder.
* *ClientID* é necessário e provém do Azure AD.
* *RedirectUri* não é necessária para a chamada de acquireToken devem ser fornecidas. Pode configurar, como o nome do pacote.
* *PromptBehavior* ajuda a pedir credenciais ignorar a cache e o cookie.
* *chamada de retorno* for chamado depois do código de autorização é trocado para obter um token. Tem um objeto do AuthenticationResult, que tem um token de acesso, data expirou e ID as informações do token.
* *acquireTokenSilent* é opcional. Pode chamá-lo ao identificador de colocação em cache e atualize token. Também fornece a versão da sincronização. Aceita *userId* como parâmetro.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Ao utilizar estas instruções, deverá ter o que precisa integrar com êxito com o Azure Active Directory. Para obter mais exemplos de como este trabalhar, visite o AzureADSamples / repositório no GitHub.

## <a name="important-information"></a>Informações importantes

### <a name="broker"></a>Mediador
A aplicação Portal da empresa do Intune ou o Microsoft Authenticator fornece o componente do Mediador. A conta é criada no AccountManager. O tipo de conta é "workaccount." AccountManager permite apenas uma única conta SSO. Cria um cookie SSO para o utilizador depois de concluir o desafio de dispositivo para uma das aplicações.

Para obter mais informações sobre como configurar a utilizar um mediador, consulte o [artigo do wiki do Mediador](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker). 

### <a name="authority-url-and-ad-fs"></a>URL de autoridade e o AD FS
Serviços de Federação do Active Directory (AD FS) não é reconhecido como produção STS, por isso terá de ativar a deteção de instância e passar falso no construtor AuthenticationContext.

O URL de autoridade necessita de uma instância de STS e um [nome do inquilino](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Consultar os itens da cache
ADAL fornece uma cache de predefinição na SharedPreferences com a cache de algumas simple funções de consulta. Pode aproveitar a cache atual AuthenticationContext utilizando:

    ITokenCacheStore cache = mContext.getCache();

Também pode fornecer a implementação de cache, se pretender personalizá-lo.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Comportamento de aviso
ADAL fornece uma opção para especificar o comportamento de linha de comandos. PromptBehavior.Auto irá mostrar a IU se o token de atualização é inválido e são necessárias credenciais de utilizador. PromptBehavior.Always irá ignorar a utilização de cache e Mostrar sempre a IU.

### <a name="silent-token-request-from-cache-and-refresh"></a>Pedido de token automática da cache e atualizar
Um pedido de token automática não utilizar a IU do pop-up e não necessita de uma atividade. Devolve um token da cache se estiver disponível. Se o token expirou, este método tenta atualizá-lo. Se o token de atualização está expirado ou falha, devolve AuthenticationException.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Também pode efetuar uma sincronização chamar através deste método. Pode definir um nulo a chamada de retorno ou utilizar acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnóstico
Estes são os principais fontes de informação para diagnosticar problemas:

* Exceções
* Registos
* Rastreios de rede

Tenha em atenção que os IDs de correlação são centrais para o diagnóstico na biblioteca. Pode definir a correlação IDs numa base por pedido, se pretender correlacionar um ADAL pedem com outras operações no seu código. Se não definir um ID de correlação, ADAL irá gerar um aleatório. Registar todas as mensagens e chamadas de rede, em seguida, irão ser marcadas com o ID de correlação. As alterações de ID geradas automaticamente em cada pedido.

#### <a name="errors--exceptions"></a>Erros e exceções
Exceções são o diagnóstico primeiro. Vamos tentar fornecer mensagens de erro úteis. Se encontrar um que não seja útil, ficheiro, um problema e informe-nos. Inclua informações de dispositivo como modelo e número SDK.

Para obter mais informações sobre erros de que a aplicação deve processar, consulte o [melhores práticas de processamento de erros](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-adal-error-handling). 

#### <a name="logs"></a>Registos
Pode configurar a biblioteca a gerar mensagens de registo que pode utilizar para ajudar a diagnosticar problemas. Configurar o registo ao efetuar a chamada seguinte para configurar uma chamada de retorno ADAL utilizará a manualmente desativar cada mensagem do registo como é gerada.

Para ativar o registo, consulte o [registo artigo wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging).

### <a name="session-cookies-in-webview"></a>Cookies de sessão no WebView
Android WebView não limpa cookies de sessão após a aplicação está fechada. Pode processar que ao utilizar este código de exemplo:

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

Para obter detalhes sobre os cookies, consulte o [CookieSyncManager informações no site do Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="ntlm-dialog-box"></a>Caixa de diálogo NTLM
Versão da ADAL 1.1.0 suporta uma caixa de diálogo NTLM é processada através do evento onReceivedHttpAuthRequest de WebViewClient. Pode personalizar o esquema e cadeias para a caixa de diálogo.

### <a name="cross-app-sso"></a>SSO em várias aplicações
Saiba [como ativar a SSO em várias aplicações no Android utilizando ADAL](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
