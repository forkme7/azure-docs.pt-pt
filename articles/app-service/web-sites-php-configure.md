---
title: Configurar PHP nas Web Apps do App Service do Azure
description: Saiba como configurar a instalação do PHP predefinidos ou adicione uma instalação personalizada do PHP para Web Apps no App Service do Azure.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 3dbd41756ede8577fee43d9758e39eb36130a6ab
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Configurar PHP nas Web Apps do App Service do Azure

## <a name="introduction"></a>Introdução

Este guia mostra como configurar o tempo de execução do PHP incorporado para Web Apps no [App Service do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), forneça um tempo de execução do PHP personalizado e ativar extensões. Para utilizar o serviço de aplicações, inscrever-se a [avaliação gratuita]. Para aproveitar ao máximo este guia, deve primeiro criar uma aplicação web PHP no App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Como: alterar a versão do PHP incorporada

Por predefinição, PHP 5.6 está instalado e imediatamente disponível para utilização quando criar um aplicação web do app Service. É a melhor forma de ver a revisão de versão disponíveis, a sua configuração predefinida e as extensões ativadas para implementar um script que chama o [phpinfo()] função.

Versões do PHP 7.0 e PHP 7.2 também estão disponíveis, mas não ativado por predefinição. Para atualizar a versão do PHP, siga um dos seguintes métodos:

### <a name="azure-portal"></a>Portal do Azure

1. Navegue até à sua aplicação web no [portal do Azure](https://portal.azure.com) e clique em de **definições** botão.

    ![Definições da aplicação Web][settings-button]
1. Do **definições** painel, selecione **definições da aplicação** e escolha a nova versão do PHP.

    ![Definições da Aplicação][application-settings]
1. Clique em de **guardar** na parte superior da parte a **Web as definições de aplicação** painel.

    ![Guardar definições de configuração][save-button]

### <a name="azure-powershell-windows"></a>O Azure PowerShell (Windows)

1. Abrir Azure PowerShell, início de sessão e à sua conta:

        PS C:\> Connect-AzureRmAccount
1. Defina a versão do PHP para a aplicação web.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
1. A versão do PHP está agora definida. Pode confirmar estas definições:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli-20-linux-mac-windows"></a>CLI do Azure 2.0 (Linux, Mac, Windows)

Para utilizar a Interface de linha de comandos do Azure, terá [instalar o Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) no seu computador.

1. Abra Terminal, início de sessão e à sua conta.

        az login

1. Verifique a lista de tempos de execução suportados.

        az webapp list-runtimes | grep php

1. Defina a versão do PHP para a aplicação web.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

1. A versão do PHP está agora definida. Pode confirmar estas definições:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: alterar as configurações do PHP incorporadas

Para qualquer tempo de execução do PHP incorporado, pode alterar qualquer uma das opções de configuração seguindo estes passos. (Para obter informações sobre as diretivas de php.ini, consulte [lista de diretivas de php.ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Alterar o PHP\_INI\_utilizador, o PHP\_INI\_PERDIR, PHP\_INI\_todas as definições de configuração

1. Adicionar um [. user.ini] ficheiro para o diretório de raiz.
1. Adicionar as definições de configuração para o `.user.ini` ficheiros utilizando a mesma sintaxe que pretende utilizar num `php.ini` ficheiro. Por exemplo, se pretendesse ativar o `display_errors` definição e defina `upload_max_filesize` definição a 10M, o `.user.ini` ficheiros iriam conter este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
1. Implemente a aplicação web.
1. Reinicie a aplicação web. (O reinício é necessário porque a frequência com que PHP lê `.user.ini` ficheiros que são regulados pelo `user_ini.cache_ttl` definição, que é uma definição de nível do sistema e é de 300 segundos (5 minutos) por predefinição. Reiniciar a aplicação web força o PHP para ler as definições de novo no `.user.ini` ficheiro.)

Como alternativa à utilização de um `.user.ini` ficheiro, pode utilizar o [ini_set()] função em scripts para definir as opções de configuração que não são as diretivas de ao nível do sistema.

### <a name="changing-phpinisystem-configuration-settings"></a>Alterar o PHP\_INI\_as definições de configuração do sistema

1. Adicionar uma definição de aplicação para a sua aplicação Web com a chave `PHP_INI_SCAN_DIR` e valor `d:\home\site\ini`
1. Criar um `settings.ini` ficheiros utilizando a consola do Kudu (http://&lt;site-name&gt;. scm.azurewebsite.net) no `d:\home\site\ini` diretório.
1. Adicionar as definições de configuração para o `settings.ini` ficheiros utilizando a mesma sintaxe que utilizaria num ficheiro php.ini. Por exemplo, se pretendesse apontar o `curl.cainfo` definição uma `*.crt` de ficheiros e configure a definição de 'wincache.maxfilesize' como 512K, sua `settings.ini` ficheiros iriam conter este texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Para recarregar as alterações, reinicie a aplicação Web.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: Ativar extensões no tempo de execução do PHP predefinido

Conforme indicado na secção anterior, a melhor forma de ver a versão do PHP predefinido, a sua configuração predefinida e as extensões ativadas é implementar um script que chama [phpinfo()]. Para ativar extensões adicionais, seguindo estes passos:

### <a name="configure-via-ini-settings"></a>Configurar através de definições de ini

1. Adicionar um `ext` diretório para o `d:\home\site` diretório.
1. Colocar `.dll` extensão ficheiros no `ext` diretório (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões são compatíveis com a versão predefinida do PHP e são VC9 e compatível de não-segura para os threads (nts).
1. Adicionar uma definição de aplicação para a sua aplicação Web com a chave `PHP_INI_SCAN_DIR` e valor `d:\home\site\ini`
1. Criar um `ini` ficheiros `d:\home\site\ini` chamado `extensions.ini`.
1. Adicionar as definições de configuração para o `extensions.ini` ficheiros utilizando a mesma sintaxe que utilizaria num ficheiro php.ini. Por exemplo, se pretendesse ativar extensões MongoDB e XDebug, sua `extensions.ini` ficheiros iriam conter este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie a aplicação Web para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar através de definição de aplicação

1. Adicionar um `bin` diretório para o diretório de raiz.
1. Colocar `.dll` extensão ficheiros no `bin` diretório (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões são compatíveis com a versão predefinida do PHP e são VC9 e compatível de não-segura para os threads (nts).
1. Implemente a aplicação web.
1. Navegue até à sua aplicação web no portal do Azure e clique em de **definições** botão.

    ![Definições da aplicação Web][settings-button]
1. Do **definições** painel, selecione **definições da aplicação** e desloque-se para o **as definições de aplicação** secção.
1. No **as definições de aplicação** secção, crie um **PHP_EXTENSIONS** chave. O valor para esta chave de ser um caminho relativo à raiz do Web site: **bin\your-ext-ficheiro**.

    ![Ativar a extensão de definições da aplicação][php-extensions]
1. Clique em de **guardar** na parte superior da parte a **Web as definições de aplicação** painel.

    ![Guardar definições de configuração][save-button]

Zend são também suportadas utilizando um **PHP_ZENDEXTENSIONS** chave. Para ativar várias extensões, incluir uma lista separada por vírgulas de `.dll` ficheiros para o valor de definição de aplicação.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: utilizar um tempo de execução do PHP personalizado

Em vez de tempo de execução do PHP para predefinição, as Web Apps do App Service pode utilizar um tempo de execução do PHP que fornece para executar os scripts PHP. O tempo de execução que fornecer pode ser configurado por um `php.ini` ficheiros que fornecem também. Para utilizar um tempo de execução do PHP personalizado com as Web Apps, siga os passos seguintes.

1. Obter um não-seguro para thread, VC9 ou VC11 versão compatível do PHP para Windows. As versões recentes do PHP para Windows podem ser encontradas aqui: [ http://windows.php.net/download/ ]. Versões mais antigas que podem ser encontradas no arquivo aqui: [ http://windows.php.net/downloads/releases/archives/ ].
1. Modificar o `php.ini` ficheiro para o tempo de execução. As definições de configuração que são as diretivas de sistema nível-só são ignoradas pelos Web Apps. (Para obter informações sobre as diretivas de sistema nível-só, consulte [lista de diretivas de php.ini]).
1. Opcionalmente, adicione as extensões para o tempo de execução do PHP e ativá-los no `php.ini` ficheiro.
1. Adicionar um `bin` diretório para o diretório de raiz e coloque o diretório que contém o tempo de execução do PHP no mesmo (por exemplo, `bin\php`).
1. Implemente a aplicação web.
1. Navegue até à sua aplicação web no portal do Azure e clique em de **definições** botão.

    ![Definições da aplicação Web][settings-button]
1. Do **definições** painel, selecione **definições da aplicação** e desloque-se para o **mapeamentos do processador** secção. Adicionar `*.php` para a extensão de campo e adicione o caminho para o `php-cgi.exe` executável. Se colocar o seu tempo de execução do PHP no `bin` diretório na raiz da sua aplicação, o caminho é `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Especifique o processador no mapeamentos do processador][handler-mappings]
1. Clique em de **guardar** na parte superior da parte a **Web as definições de aplicação** painel.

    ![Guardar definições de configuração][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como: Ativar a compositor de automatização no Azure

Por predefinição, o serviço de aplicações não faz nada com Composer, se tiver uma no seu projeto PHP. Se utilizar [implementação de Git](app-service-deploy-local-git.md), pode ativar Composer processamento durante `git push` ao ativar a extensão de compositor.

> [!NOTE]
> Pode [voto para compositor suporte de primeira classe no App Service aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. No seu PHP web painel da aplicação no [portal do Azure](https://portal.azure.com), clique em **ferramentas** > **extensões**.

    ![Painel de definições do portal do Azure para ativar a compositor de automatização no Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **adicionar**, em seguida, clique em **compositor**.

    ![Adicionar extensão de compositor de ativar a compositor de automatização no Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.

    O **extensões instaladas** painel mostra a extensão de compositor.
    ![Aceite os termos legais de ativar a compositor de automatização no Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, numa janela de terminal no seu computador local, realizar `git add`, `git commit`, e `git push` à sua aplicação Web. Tenha em atenção que o compositor está a instalar dependências definidas no Composer.

    ![Implementação de Git com a automatização de compositor no Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte o [Centro para programadores do PHP](/develop/php/).

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
>

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[lista de diretivas de php.ini]: http://www.php.net/manual/en/ini.list.php
[. user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
