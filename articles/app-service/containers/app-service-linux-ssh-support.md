---
title: Suporte SSH para o serviço de aplicações do Azure no Linux | Microsoft Docs
description: Saiba como utilizar o SSH com o serviço de aplicações do Azure no Linux.
keywords: serviço de aplicações do Azure, aplicação web, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 905c257ab40057f05081e54e8680bd818023d886
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Suporte SSH para o serviço de aplicações do Azure no Linux

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) é um protocolo de rede criptográficos por utilizar os serviços de rede em segurança. É frequentemente utilizado para iniciar sessão num sistema remotamente em segurança de uma linha de comandos e executar remotamente comandos administrativos.

Serviço de aplicações no Linux fornece suporte SSH para o contentor de aplicação com cada uma das imagens de Docker incorporadas utilizadas para a pilha de tempo de execução de aplicações web novas.

![Pilhas de tempo de execução](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Também pode utilizar o SSH com as imagens de Docker personalizadas, incluindo o servidor SSH como parte da imagem e configurá-lo, tal como descrito neste artigo.

## <a name="making-a-client-connection"></a>Efetuar uma ligação de cliente

Para efetuar uma ligação de cliente SSH, o site principal tem de ser iniciado.

Cole o ponto final de gestão de controlo de origem (SCM) para a sua aplicação web no seu browser utilizando o seguinte formato:

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Se não está já autenticado, é necessário para autenticar com a sua subscrição do Azure para ligar.

![Ligação de SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>Suporte SSH com imagens personalizadas do Docker

Ordem de uma imagem personalizada do Docker suportar a comunicação de SSH entre o contentor e o cliente no portal do Azure, execute os seguintes passos para a imagem de Docker.

Estes passos são apresentados no repositório de App Service do Azure como [um exemplo](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Incluir o `openssh-server` instalação no [ `RUN` instrução](https://docs.docker.com/engine/reference/builder/#run) no Dockerfile para a imagem e defina a palavra-passe para a raiz da conta para `"Docker!"`.

    > [!NOTE]
    > Esta configuração não permite ligações externas ao contentor. SSH só pode ser acedido através do Kudu / SCM Site, o que é autenticado utilizando as credenciais de publicação.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Adicionar um [ `COPY` instrução](https://docs.docker.com/engine/reference/builder/#copy) para Dockerfile para copiar uma [sshd_config](http://man.openbsd.org/sshd_config) do ficheiro para o */etc/ssh/* diretório. O ficheiro de configuração deve ser baseado no ficheiro sshd_config no repositório de GitHub do serviço de aplicações do Azure [aqui](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > O *sshd_config* ficheiros têm de incluir o seguinte procedimento ou a ligação falhar: 
    > * `Ciphers` tem de incluir, pelo menos, um dos seguintes: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` tem de incluir, pelo menos, um dos seguintes: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Incluir porta 2222 no [ `EXPOSE` instrução](https://docs.docker.com/engine/reference/builder/#expose) para o Dockerfile. Embora a palavra-passe de raiz seja conhecida, não é possível aceder à porta 2222 a partir da Internet. É uma porta única interna acessível apenas por contentores dentro da rede de bridge de uma rede privada virtual.

    ```docker
    EXPOSE 2222 80
    ```

1. Certifique-se iniciar o serviço SSH utilizando um script de shell (consulte exemplo em [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

O Dockerfile utiliza o [ `ENTRYPOINT` instrução](https://docs.docker.com/engine/reference/builder/#entrypoint) para executar o script.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="next-steps"></a>Passos Seguintes

Pode publicar perguntas e problemas no [fórum do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Para obter mais informações sobre a aplicação Web para contentores, consulte:

* [Como utilizar uma imagem personalizada do Docker para as Aplicações Web para Contentores](quickstart-docker-go.md)
* [Utilizar o .NET Core no Serviço de Aplicações do Azure no Linux](quickstart-dotnetcore.md)
* [Utilizar o Ruby no Serviço de Aplicações do Azure no Linux](quickstart-ruby.md)
* [FAQ sobre a Aplicação Web do Serviço de Aplicações do Azure para Contentores](app-service-linux-faq.md)