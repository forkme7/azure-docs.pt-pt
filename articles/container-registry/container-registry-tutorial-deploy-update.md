---
title: Tutorial do Azure Container Registry - Enviar uma imagem atualizada para implementações regionais
description: Envie uma imagem de Docker modificada para o seu Azure Container Registry georreplicado e veja as alterações automaticamente implementadas nas aplicações Web executadas em múltiplas regiões. Parte três de uma série com três partes.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 2e9a46f2a99bc9b530ac5859068bde58bf5b5098
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-push-an-updated-image-to-regional-deployments"></a>Tutorial: enviar uma imagem atualizada para implementações regionais

Esta é a parte três de um tutorial de três partes. No [tutorial anterior](container-registry-tutorial-deploy-app.md), a georreplicação foi configurada para duas implementações regionais de Aplicações Web. Neste tutorial, comece por modificar a aplicação e, em seguida, crie uma nova imagem de contentor e envie-a para o seu registo georreplicado. Finalmente, verá a alteração, implementada automaticamente pelos webhooks do Azure Container Registry, em ambas as instâncias de Aplicações Web.

Neste tutorial, a última parte da série:

> [!div class="checklist"]
> * Modificar o HTML da aplicação Web
> * Criar e etiquetar a imagem do Docker
> * Enviar a alteração para o Azure Container Registry
> * Ver a aplicação atualizada em duas regiões diferentes

Se ainda não configurou as duas implementações regionais da *Aplicação Web para Contentores*, regresse ao tutorial anterior na série, [Implementar a aplicação Web a partir do Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modificar a aplicação Web

Neste passo, efetue uma alteração à aplicação Web que será altamente visível quando enviar a imagem de contentor atualizada para o Azure Container Registry.

Localize o ficheiro `AcrHelloworld/Views/Home/Index.cshtml` na origem de aplicação que [clonou do GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) num tutorial anterior e abra-o no seu editor de texto favorito. Adicione a seguinte linha abaixo da linha `<h1>` existente:

```html
<h1>MODIFIED</h1>
```

O seu `Index.cshtml` modificado deverá ter um aspeto semelhante a:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Recriar a imagem

Agora que atualizou a aplicação Web, recrie a respetiva imagem de contentor. Conforme anteriormente, utilize o nome de imagem totalmente qualificado, incluindo o URL do servidor de início de sessão, para a etiqueta:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagens para o Azure Container Registry

Agora, envie a imagem de contentor *acr-helloworld* atualizada para o seu registo georreplicado. Aqui, vai executar um único comando `docker push` para implementar a imagem atualizada nas réplicas de registo nas regiões *E.U.A. Oeste* e *E.U.A. Leste*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

O resultado deverá ser semelhante ao seguinte:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Ver os registos de webhooks

Enquanto a imagem está a ser replicada, pode ver os webhooks do Azure Container Registry a serem acionados.

Para ver os webhooks regionais que foram criados quando implementou o container nas *Aplicações Web para Contentores* num tutorial anterior, navegue para o seu registo de contentor no portal do Azure e, em seguida, selecione **Webhooks** em **SERVIÇOS**.

![Webhooks de registo de contentor no portal do Azure][tutorial-portal-01]

Selecione cada Webhook para ver o histórico de chamadas e respostas do mesmo. Deverá ver uma linha para a ação de **envio** nos registos de ambos os Webhooks. Neste caso, o registo do Webhook localizado na região *E.U.A. Oeste* mostra a ação de **envio** acionada pelo `docker push` no passo anterior:

![Registo de Webhooks de registo de contentor no portal do Azure (E.U.A. Oeste)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Ver a aplicação Web atualizada

Os Webhooks notificam as Aplicações Web de que uma nova imagem foi enviada para o registo, o que implementa automaticamente o contentor atualizado para duas aplicações Web regionais.

Certifique-se de que a aplicação foi atualizada em ambas as implementações ao navegar para ambas as implementações de Aplicações Web regionais no seu browser. Lembre-se de que pode encontrar o URL da aplicação Web implementada no canto superior direito de cada separador de descrição geral do Serviço de Aplicações do Azure.

![Descrição geral do Serviço de Aplicações do Azure no portal do Azure][tutorial-portal-03]

Para ver a atualização aplicada, selecione a ligação na descrição geral do Serviço de Aplicações do Azure. Eis uma vista de exemplo da aplicação executada na região *E.U.A. Oeste*:

![Vista de browser da aplicação Web modificada executada na região E.U.A. Oeste][deployed-app-westus-modified]

Verifique se a imagem de contentor atualizada também foi implementada na implementação *E.U.A. Leste* ao vê-la no seu browser.

![Vista de browser da aplicação Web modificada executada na região E.U.A. Leste][deployed-app-eastus-modified]

Com um único `docker push`, atualizou ambas as implementações de Aplicações Web regionais e o Azure Container Registry apresentou ao contentor imagens dos repositórios sem rede.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou e enviou uma nova versão do contentor de aplicações Web para o seu registo georreplicado. Os webooks no Azure Container Registry notificaram as Aplicações Web para Contentores da atualização, o que acionou uma solicitação local das réplicas de registo.

Neste tutorial, a última parte da série, o utilizador:

> [!div class="checklist"]
> * Atualizou o HTML da aplicação Web
> * Criou e etiquetou a imagem do Docker
> * Publicou a alteração no Azure Container Registry
> * Visualizou a aplicação atualizada em duas regiões diferentes

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png