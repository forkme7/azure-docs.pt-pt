---
title: Implementar uma aplicação Spring Boot para o Azure Service Fabric | Microsoft Docs
description: Neste início rápido, vai implementar uma aplicação Spring Boot para o Azure Service Fabric, utilizando um exemplo de aplicação Spring Boot.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: e41a7754e6e170dda7818bceadab7858a9d9fa76
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-azure"></a>Início Rápido: implementar uma Aplicação Spring Boot em Java no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores. 

Este guia de início rápido mostra como implementar uma aplicação Spring Boot no Service Fabric. Este guia de início rápido utiliza o exemplo da [Introdução](https://spring.io/guides/gs/spring-boot/) do site de Spring. Através de ferramentas de linha de comandos familiares, este guia de início rápido irá guiá-lo através da implementação do exemplo de Spring Boot como uma aplicação de Service Fabric. Quando tiver terminado, terá o exemplo da Introdução ao Spring Boot a trabalhar no Service Fabric. 

![Captura de Ecrã da Aplicação](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Neste início rápido, vai aprender a:

* Implementar uma Aplicação Spring Boot no Service Fabric
* Implementar a aplicação no seu cluster local 
* Implementar a aplicação num cluster no Azure
* Escalar horizontalmente a aplicação em vários nós
* Efetuar ativação pós-falha do seu serviço com nenhum resultado de disponibilidade

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido:
1. [Instalar o SDK do Service Fabric e Interface de Linha de Comandos (CLI) do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instale o Git](https://git-scm.com/).
3. [Instalar o Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Configurar o Ambiente de Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Transferir o exemplo
Numa janela de terminal, execute o seguinte comando para clonar a aplicação de exemplo da Introdução ao Spring Boot para o seu computador local.
```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Empacotar a aplicação Spring Boot 
1. Dentro do diretório `gs-spring-boot` no clone, execute o comando `yo azuresfguest`. 

2. Introduza os detalhes seguintes para cada pedido. 

    ![Entradas de Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. No pasta `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, crie um ficheiro chamado `entryPoint.sh`. Adicione o seguinte ao ficheiro. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

Nesta fase, já criou uma aplicação de Service Fabric para o exemplo da Introdução ao Spring Boot que pode implementar no Service Fabric.

## <a name="run-the-application-locally"></a>Executar a aplicação localmente
1. Inicie o seu cluster local com o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    O arranque do cluster local demora algum tempo. Para confirmar que o cluster está totalmente operacional, aceda ao Service Fabric Explorer em **http://localhost:19080**. Os cinco nós em bom estado indicam que o cluster local está a funcionar. 
    
    ![Cluster local em bom estado de funcionamento](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Navegue para a pasta `gs-spring-boot/SpringServiceFabric`.
3. Execute o seguinte comando para ligar ao cluster local. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Execute o script `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Abra o browser favorito e aceda à aplicação em **http://localhost:8080**. 

    ![Front-end da aplicação Local](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Agora pode aceder à aplicação de Spring Boot que foi implementada num cluster do Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurar o Cluster do Azure Service Fabric
Para implementar a aplicação num cluster no Azure, crie o seu próprio cluster.

Os clusters comemorativos são clusters do Service Fabric gratuitos e de tempo limitado, alojados no Azure e executados pela equipa do Service Fabric. Pode utilizar os clusters comemorativos para implementar aplicações e obter informações sobre a plataforma. O cluster utiliza um certificado autoassinado para a segurança de "nó para nó" e de "cliente para nó".

Inicie sessão e adira a um [cluster do Linux](http://aka.ms/tryservicefabric). Transfira o certificado PFX para o seu computador ao clicar na ligação **PFX**. Clique na hiperligação **Leia-me** para localizar a palavra-passe do certificado e instruções sobre como configurar vários ambientes para utilizar o certificado. Mantenha as páginas de **Boas-vindas** e **Leia-me** abertas, irá utilizar algumas das instruções nos passos seguintes. 

> [!Note]
> Há um número limitado de clusters comemorativos por hora. Se obtiver uma mensagem um erro ao tentar inscrever-se num cluster comemorativo, pode ter de aguardar algum tempo e tentar novamente ou pode seguir estes passos em [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) para criar um cluster na sua subscrição. 
>
> O serviço de Spring Boot está configurado para escutar tráfego de entrada na porta 8080. Certifique-se de que a porta está aberta no seu cluster. Se estiver a utilizar um Cluster de Grupo, esta porta estará aberta.
>

O Service Fabric fornece várias ferramentas que pode utilizar para gerir um cluster e as respetivas aplicações:

- O Service Fabric Explorer, uma ferramenta baseada no browser.
- A CLI (Interface de Linha de Comandos) do Service Fabric, que é executada na CLI 2.0 do Azure.
- Comandos do PowerShell. 

Neste início rápido,vai utilizar a CLI do Service Fabric e o Service Fabric Explorer. 

Para utilizar a CLI, terá de criar um ficheiro PEM com base no ficheiro PFX que transferiu. Para converter o ficheiro, utilize o seguinte comando. (Para os clusters comemorativos, pode copiar um comando específico para o ficheiro PFX, a partir das instruções da página **Leia-me**.)

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

De modo a utilizar o Service Fabric Explorer, tem de importar o ficheiro PFX de certificado que transferiu do site do Cluster Comemorativo para o seu arquivo de certificados (Windows ou Mac) ou para o próprio browser (Ubuntu). Precisará da palavra-passe da chave privada do PFX, que pode obter na página **Leia-me**.

Utilize o método que preferir para importar o certificado para o seu sistema. Por exemplo:

- No Windows: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado no seu arquivo pessoal, `Certificates - Current User\Personal\Certificates`. Em alternativa, pode utilizar o comando do PowerShell das instruções **Leia-me**.
- No Mac: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado na sua Keychain.
- No Ubuntu: o Mozilla Firefox é o browser predefinido do Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu browser e, em seguida, clique em **Opções**. Na página **Preferências**, utilize a caixa de pesquisa para procurar "certificados". Clique em **Ver Certificados**, selecione o separador **Os Seus Certificados**, clique em **Importar** e siga as instruções para importar o certificado.
 
   ![Instalar o certificado no Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png) 


### <a name="deploy-the-application-using-cli"></a>Implementar a aplicação com CLI
Agora que a aplicação e o cluster estão prontos, pode implementá-los num cluster diretamente a partir da linha de comandos.

1. Navegue para a pasta `gs-spring-boot/SpringServiceFabric`.
2. Execute o seguinte comando para ligar ao cluster do Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Se o cluster estiver protegido com um certificado autoassinado, execute o comando: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Execute o script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Abra o browser e aceda à aplicação em: **http://\<ConnectionIPOrUrl>:8080**. 

    ![Front-end da aplicação Local](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Agora pode aceder à aplicação de Spring Boot em execução num cluster do Service Fabric no Azure.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicações e serviços num cluster
Os serviços podem ser facilmente dimensionados num cluster para se prepararem para alterações à carga nos serviços. Para dimensionar um serviço, tem de alterar o número de instâncias em execução no cluster. Existem várias formas de dimensionar os seus serviços, por exemplo, pode utilizar scripts ou comandos da CLI do Service Fabric (sfctl). Nos passos seguintes, vai utilizar o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acedido num browser, navegando para a porta (19080) de gestão HTTP dos clusters; por exemplo `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://localhost:19080`.
2. Clique nas reticências (três pontos) junto ao nó **fabric:/SpringServiceFabric/SpringGettingStarted**, na vista de árvore, e escolha**Dimensionar Serviço**.

    ![Dimensionar Serviço no Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Agora, pode optar por dimensionar o número de instâncias do serviço.

3. Altere o número para **3** e clique em **Dimensionar Serviço**.

    Segue-se uma maneira alternativa de dimensionar o serviço utilizando a linha de comandos.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Clique no nó **fabric:/SpringServiceFabric/SpringGettingStarted**, na vista de árvore, e expanda o nó de partição (representado por uma GUID).

    ![Dimensionar Serviço no Service Fabric Explorer Completo](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    O serviço tem três instâncias e a vista de árvore mostra em que nós as instâncias são executadas.

Através desta simples tarefa de gestão, duplicou os recursos disponíveis para o serviço de front-end processar a carga de utilizador. É importante compreender que não precisa de várias instâncias de um serviço para que o mesmo seja executado de forma fiável. Se um serviço falhar, o Service Fabric certifica-se de que uma nova instância do serviço é executada no cluster.

## <a name="fail-over-services-in-a-cluster"></a>Serviços de ativação pós-falha num cluster 
Para demonstrar a ativação pós-falha do serviço, simula-se um reinício do nó utilizando o Service Fabric Explorer. Certifique-se de que apenas uma instância do seu serviço está em execução. 

1. Abra o Service Fabric Explorer no seu cluster - por exemplo, `http://localhost:19080`.
2. Clique no botão de reticências (três pontos) junto ao nó a executar a instância do seu serviço e Reinicie o nó. 

    ![Nó de Reinício do Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. A instância do seu serviço é movida para um nó diferente e a sua aplicação não tem períodos de indisponibilidade. 

    ![Nó de Reinício do Service Fabric Explorer com Êxito](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a:

* Implementar uma Aplicação Spring Boot no Service Fabric
* Implementar a aplicação no seu cluster local 
* Implementar a aplicação num cluster no Azure
* Escalar horizontalmente a aplicação em vários nós
* Efetuar ativação pós-falha do seu serviço com nenhum resultado de disponibilidade

Para saber mais sobre como trabalhar com aplicações Java no Service Fabric, avance para o tutorial para aplicações Java.

> [!div class="nextstepaction"]
> [Implementar uma aplicação Java](./service-fabric-tutorial-create-java-app.md)
