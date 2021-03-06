---
title: Vincular um certificado SSL personalizado já existente às Aplicações Web do Azure | Microsoft Docs
description: Saiba como vincular um certificado SSL personalizado a aplicações Web, ao back-end de aplicações móveis ou a aplicações API no Serviço de Aplicações do Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5a6fd54e4d20e55116bc0fa771e039e5ea2bb30b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Tutorial: Vincular um certificado SSL personalizado já existente às Aplicações Web do Azure

As Aplicações Web do Azure dispõem de um serviço de alojamento na Web, altamente dimensionável e com correção automática. Este tutorial mostra-lhe como vincular um certificado SSL personalizado comprado numa autoridade de certificação fidedigna às [Aplicações Web do Azure](app-service-web-overview.md). Quando tiver terminado, poderá aceder à sua aplicação Web no ponto final HTTPS do seu domínio DNS personalizado.

![Aplicação Web com certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Vincular o certificado SSL personalizado ao Serviço de Aplicações
> * Impor HTTPS para a sua aplicação
> * Automatizar o enlace de certificados SSL com scripts

> [!NOTE]
> Se precisar de obter um certificado SSL personalizado, pode obtê-lo no portal do Azure diretamente e vinculá-lo à sua aplicação Web. Siga o [tutorial App Service Certificates tutorial](web-sites-purchase-ssl-web-site.md) (Certificados do Serviço de Aplicações).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapear um nome DNS personalizado à sua aplicação Web](app-service-web-tutorial-custom-domain.md)
- Adquirir um certificado SSL numa autoridade de certificação fidedigna
- Ter a chave privada que utilizou para assinar o pedido de certificado SSL

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos do certificado SSL

Para utilizar um certificado no Serviço de Aplicações, aquele tem de cumprir os seguintes requisitos:

* Ser assinado por uma autoridade de certificação fidedigna
* Ser exportado como um ficheiro PFX protegido por palavra-passe
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

## <a name="prepare-your-web-app"></a>Preparar a sua aplicação Web

Para vincular um certificado SSL personalizado à sua aplicação Web, o [plano do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/) tem de estar no escalão **Básico**, **Standard** ou**Premium**. Neste passo, vai confirmar que a aplicação Web está no escalão de preço suportado.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Abra o [Portal do Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navegue até à sua aplicação Web

No menu à esquerda, clique em **Serviços de Aplicações** e clique no nome da sua aplicação Web.

![Selecionar a aplicação Web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Chegou à página de gestão da sua aplicação Web.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação Web, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)**.

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Confirme que a aplicação Web não está no escalão **Gratuito** ou **Partilhado**. O escalão atual da aplicação é realçado com uma caixa azul-escura.

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

O SSL personalizado não é suportado nos escalões **Gratuito** e **Partilhado**. Se precisar de aumentar verticalmente, siga os passos na secção seguinte. Caso contrário, feche a página **Escolher o escalão de preço** e avance para [Carregar e vincular o certificado SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Aumentar verticalmente o seu plano do Serviço de Aplicações

Selecione um dos escalões **Básico**, **Standard** ou **Premium**.

Clique em **Selecionar**.

![Escolher um escalão de preço](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Notificação para “aumentar verticalmente”](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Vincular o seu certificado SSL

Está pronto para carregar o certificado SSL para a sua aplicação Web.

### <a name="merge-intermediate-certificates"></a>Intercalar certificados intermédios

Se a sua autoridade de certificação lhe der vários certificados na cadeia de certificados, terá de intercalá-los por ordem. 

Para tal, abra cada certificado recebido num editor de texto. 

Crie um ficheiro para o certificado intercalado, denominado _mergedcertificate.crt_. Num editor de texto, copie o conteúdo de cada certificado para este ficheiro. A ordem dos seus certificados deve seguir a ordem na cadeia de certificados, a começar no seu certificado e a terminar no certificado de raiz. O aspeto é igual ao do exemplo abaixo:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado SSL intercalado com a chave privada com que o pedido de certificado foi gerado.

Se tiver gerado o pedido de certificado com OpenSSL, significa que criou um ficheiro de chave privada. Para exportar o certificado para PFX, execute o seguinte comando. Substitua os marcadores de posição  _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a chave privada e o ficheiro de certificado intercalado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Vai utilizar esta palavra-passe ao carregar o certificado SSL para o Serviço de Aplicações mais tarde.

Se tiver utilizado o IIS ou _Certreq.exe_ para gerar o pedido de certificado, instale o certificado no seu computador local e [exporte-o para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Carregar o seu certificado SSL

Para carregar o certificado SSL, clique em **definições SSL**, na navegação da esquerda da sua aplicação Web.

Clique em **Carregar Certificado**. 

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX.

Clique em **Carregar**.

![Carregar certificado](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Quando o Serviço de Aplicações concluir o carregamento do certificado, o mesmo aparece na página **definições SSL**.

![Certificado carregado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Vincular o seu certificado SSL

Na secção **Enlaces de SSL**, clique em **Adicionar enlace**.

Na página **Adicionar Enlace de SSL**, utilize os menus pendentes para selecionar o nome de domínio que vai ser protegido e o certificado que vai ser utilizado.

> [!NOTE]
> Se tiver carregado o certificado, mas não conseguir ver os nomes de domínio no menu pendente **Hostname** (Nome de anfitrião), experimente atualizar a página do browser.
>
>

Em **Tipo de SSL**, selecione se vai utilizar **[SSL baseado em Indicação do Nome de Servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou baseado em IP.

- **SSL baseado em SNI** - podem ser adicionados vários enlaces de SSL baseado em SNI. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os browsers mais modernos (incluindo o Internet Explorer, o Chrome, o Firefox e o Opera) suportam SNI (encontre informações mais abrangentes sobre o suporte de browsers em [Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication) [Indicação do Nome de Servidor]).
- **SSL baseado em IP** - só pode ser adicionado um enlace SSL baseado em IP. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Para proteger vários domínios, tem de protegê-los a todos com o mesmo certificado SSL. Esta é a opção tradicional para o enlace de SSL.

Clique em **Adicionar Enlace**.

![Vincular o certificado SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Quando o Serviço de Aplicações concluir o carregamento do certificado, o mesmo aparece nas secções **Enlaces de SSL**.

![Certificado vinculado à aplicação Web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registo A para SSL IP

Se não utilizar o SSL baseado em IP na sua aplicação Web, avance para [Testar HTTPS para o seu domínio personalizado](#test).

Por predefinição, a sua aplicação Web utiliza um endereço IP público partilhado. Quando vincula um certificado com SSL baseado em IP, o Serviço de Aplicações cria um endereço IP dedicado novo para a sua aplicação Web.

Se tiver mapeado um registo A para a aplicação Web, atualize o registo do seu domínio com esse endereço IP dedicado novo.

A página **Domínio personalizado** da aplicação Web é atualizada com o endereço IP dedicado novo. [Copie este endereço IP](app-service-web-tutorial-custom-domain.md#info) e [remapeie o registo A ](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este endereço IP novo.

<a name="test"></a>

## <a name="test-https"></a>Tester HTTPS

Agora, só falta confirmar que HTTPS funciona no seu domínio personalizado. Em vários browsers, navegue até `https://<your.custom.domain>` para ver que o mesmo serve a sua aplicação Web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se a sua aplicação Web der erros de validação do certificado, é provável que esteja a utilizar um certificado autoassinado.
>
> Se não for esse o caso, poderá ter deixou de fora certificados intermédios quando exportou o certificado para o ficheiro PFX.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Impor HTTPS

Por predefinição, qualquer pessoa pode continuar a aceder à sua aplicação Web através de HTTP. Pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na página da aplicação Web, na navegação do lado esquerdo, selecione **definições SSL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Quando a operação for concluída, navegue para um dos URLs HTTP que apontam para a sua aplicação. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-1112"></a>Impor TLS 1.1/1.2

A aplicação permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 por predefinição, o que é já não é considerado seguro pelas normas do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS superiores, siga estes passos:

Na página da aplicação Web, na navegação do lado esquerdo, selecione **definições SSL**. Em seguida, na **versão do TLS**, selecione a versão mínima do TLS que pretende.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Quando a operação for concluída, a sua aplicação rejeita todas as ligações com versões do TLS inferiores.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar os enlaces de SSL para a sua aplicação Web com scripts através da [CLI do Azure](/cli/azure/install-azure-cli) ou do [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte carrega um ficheiro PFX exportado e obtém o thumbprint.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

O comando seguinte utiliza o thumbprint do comando anterior para adicionar um enlace SSL baseado em SNI.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

O comando seguinte carrega um ficheiro PFX exportado e adiciona um enlace SSL baseado em SNI.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Certificados Públicos (opcional)
Pode carregar [certificados públicos](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) para a sua aplicação Web. Também pode utilizar certificados públicos em aplicações de Ambientes de Serviço de Aplicações. Se tiver de armazenar o certificado no arquivo de certificados LocalMachine, terá de utilizar uma aplicação Web no Ambiente de Serviço de Aplicações. Para obter mais informações, veja [How to configure public certificates to your Web App](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer) (Como configurar certificados públicos para a sua Aplicação Web).

![Carregar o Certificado Público](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Vincular o certificado SSL personalizado ao Serviço de Aplicações
> * Impor HTTPS para a sua aplicação
> * Automatizar o enlace de certificados SSL com scripts

Avance para o próximo tutorial para saber como utilizar a Rede de Entrega de Conteúdos do Azure.

> [!div class="nextstepaction"]
> [Add a Content Delivery Network (CDN) to an Azure App Service](app-service-web-tutorial-content-delivery-network.md) (Adicionar uma Rede de Entrega de Conteúdos (CDN) ao Serviço de Aplicações do Azure)

Para obter mais informações, veja [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (Utilizar um certificado SSL no código da sua aplicação no Serviço de Aplicações do Azure).