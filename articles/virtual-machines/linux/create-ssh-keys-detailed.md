---
title: Os passos detalhados - SSH par de chaves para VMs do Linux do Azure | Microsoft Docs
description: Saiba mais passos detalhados sobre como criar e gerir um par de chaves público e privado SSH para VMs com Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2018
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Passos de detalhado: criar e gerir chaves SSH para a autenticação para uma VM com Linux no Azure 
Com um par de chaves secure shell (SSH), pode criar uma máquina virtual Linux no Azure que será assumida a utilizar chaves SSH para a autenticação, eliminando a necessidade de palavras-passe iniciar sessão. As VMs criadas com o portal do Azure, CLI do Azure, o Gestor de recursos, modelos ou outras ferramentas podem incluir a chave pública SSH como parte da implementação, que configura a autenticação de chave SSH para ligações SSH. 

Este artigo fornece o fundo detalhado e os passos para criar e gerir um par de ficheiro de chaves públicas-privadas SSH RSA SSH para ligações de cliente. Se pretender que os comandos rápidos, consulte [como criar um par de chaves públicas-privadas SSH para VMs com Linux no Azure](mac-create-ssh-keys.md).

Para obter formas adicionais gerar e utilizar chaves SSH num computador Windows, consulte [das chaves de como utilizar o SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Frase de acesso de chave privada
A chave SSH privada deve ter uma frase de acesso muito segura para protegê-lo. Esta frase de acesso é simplesmente para aceder ao ficheiro de chave SSH privado e *não é* a palavra-passe da conta de utilizador. Quando adiciona uma frase de acesso para a chave SSH, encripta a chave privada utilizando AES de 128 bits, para que a chave privada é inútil sem o frase de acesso para desencriptá-lo. Se um atacante stole a chave privada e essa chave não tinha uma frase de acesso, deverá conseguir utilizar essa chave privada para iniciar sessão todos os servidores que têm a chave pública correspondente. Se uma chave privada está protegida por uma frase de acesso, não pode ser utilizado por esse atacante, fornecendo uma camada adicional de segurança para a sua infraestrutura no Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Utilização e vantagens das chaves SSH

Quando cria uma VM do Azure, especificando a chave pública, Azure copia a chave pública (no `.pub` formato) para o `~/.ssh/authorized_keys` pasta na VM. As chaves SSH no `~/.ssh/authorized_keys` são utilizadas para desafiar o cliente para corresponder a chave privada correspondente numa ligação de início de sessão SSH. No VM Linux do Azure que utiliza as chaves SSH para a autenticação, o Azure configura o servidor SSHD para não permitir inícios de sessão de palavra-passe, apenas as chaves SSH. Por conseguinte, ao criar uma VM com Linux do Azure com chaves SSH, pode ajudar a proteger a implementação da VM e guardar si próprio o passo de configuração pós-implementação típica da desativação de palavras-passe no `sshd_config` ficheiro.

Se não pretender utilizar chaves SSH, pode configurar a VM com Linux para utilizar a autenticação de palavra-passe. Se a VM não está exposta à Internet, a utilização de palavras-passe pode ser suficiente. No entanto, ainda tem de gerir as palavras-passe para cada VM com Linux e manter políticas de palavra-passe bom estado de funcionamento e práticas, como o comprimento mínimo da palavra-passe e atualizações regulares. Utilizar chaves SSH reduz a complexidade da gestão de credenciais individuais através de várias VMs.

## <a name="generate-keys-with-ssh-keygen"></a>Gerar chaves de ssh-keygen

Para criar as chaves, é um comando preferencial `ssh-keygen`, que está disponível com OpenSSH utilitários numa Shell de nuvem do Azure, macOS ou anfitrião Linux, o [subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/about)e outras ferramentas. `ssh-keygen` pede-lhe uma série de perguntas e, em seguida, escreve uma chave privada e uma chave pública correspondente. 

Por predefinição, as chaves SSH são mantidas no diretório `~/.ssh`.  Se não tiver um diretório `~/.ssh`, o comando `ssh-keygen` cria-o por si com as permissões corretas.

### <a name="basic-example"></a>Exemplo básico

O seguinte `ssh-keygen` comandos gera 2048 bits SSH RSA públicos e privados ficheiros de chave por predefinição no `~/.ssh` diretório. Se um par de chaves SSH existe na localização atual, esses ficheiros são substituídos.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Exemplo de detalhado
O exemplo seguinte mostra as opções de comandos adicionais para criar um par de chaves SSH RSA. Se um par de chaves SSH existe na localização atual, esses ficheiros são substituídos. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Comando explicado**

`ssh-keygen` = o programa utilizado para criar as chaves

`-t rsa` = o tipo de chave para criar, neste caso, no formato RSA

`-b 4096` = o número de bits de chave, neste caso, 4096

`-C "azureuser@myserver"` = um comentário acrescentado ao final do ficheiro da chave pública para o identificar facilmente. Normalmente, um endereço de e-mail é utilizado como o comentário, mas utilize que funciona melhor para a sua infraestrutura.

`-f ~/.ssh/mykeys/myprivatekey` = o nome de ficheiro do ficheiro de chave privada, se optar por não utilizar o nome predefinido. Um ficheiro de chave pública correspondente anexado com `.pub` é gerado no mesmo diretório. O diretório tem de existir.

`-N mypassphrase` = uma frase de acesso adicional, utilizado para aceder ao ficheiro de chave privado. 

### <a name="example-of-ssh-keygen"></a>Exemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>Ficheiros de chave guardados

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

O nome do par de chaves para este artigo. Ter um par de chaves com o nome `id_rsa` é a predefinição; algumas ferramentas podem contar o `id_rsa` nome de ficheiro de chave privada, pelo que é uma boa ideia. O diretório `~/.ssh/` é a localização predefinida de todos os pares de chaves SSH e do ficheiro de configuração SSH. Se não for especificado com um caminho completo, a `ssh-keygen` cria as chaves no atual diretório de trabalho, não a predefinição `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista do `~/.ssh` diretório

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Frase de acesso de chave

`Enter passphrase (empty for no passphrase):`

É *vivamente* recomendado para adicionar uma frase de acesso à sua chave privada. Sem uma frase de acesso para proteger o ficheiro de chave, qualquer pessoa com o ficheiro pode utilizá-lo para iniciar sessão qualquer servidor que tenha a chave pública correspondente. Adicionar uma frase de acesso oferece mais proteção no caso de alguém conseguir obter acesso ao seu ficheiro de chave privada, dar tempo para alterar as chaves.

## <a name="generate-keys-automatically-during-deployment"></a>Gerar chaves automaticamente durante a implementação

Se utilizar o [Azure CLI 2.0](/cli/azure) para criar a VM, opcionalmente, pode gerar ficheiros de chaves públicos e privados SSH executando o [az vm criar](/cli/azure/vm#az_vm_create) comando com o `--generate-ssh-keys` opção. As chaves são armazenadas no diretório ~/.ssh. Tenha em atenção que esta opção de comando não substituir chaves se estas já existem nessa localização.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Forneça a chave pública SSH ao implementar uma VM

Para criar uma VM com Linux que utilizar chaves SSH para a autenticação, forneça a chave pública SSH ao criar a VM com o portal do Azure, CLI, modelos do Resource Manager ou outros métodos. Quando utilizar o portal, introduza a chave pública em si. Se utilizar o [Azure CLI 2.0](/cli/azure) para criar a VM com uma chave pública existente, especifique o valor ou a localização desta chave pública, executando o [az vm criar](/cli/azure/vm#az_vm_create) comando com o `--ssh-key-value` opção. 

Se não estiver familiarizado com o formato de uma chave pública SSH, pode ver a chave pública executando `cat` seguinte, substituindo `~/.ssh/id_rsa.pub` com a sua própria localização do ficheiro de chave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

O resultado é semelhante à seguinte (aqui redacted):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Se copiar e colar o conteúdo do ficheiro de chave pública no portal do Azure ou um modelo do Resource Manager, certifique-se de não copiar quaisquer espaços em branco adicionais ou introduzir linebreaks adicionais. Por exemplo, se utilizar macOS, pode encaminhar o ficheiro de chave pública (por predefinição, `~/.ssh/id_rsa.pub`) para **pbcopy** para copiar o conteúdo (existem outros programas de Linux que efetuar a mesma coisa, tais como **xclip**).

Se preferir utilizar uma chave pública que está num formato multilinha, pode gerar uma chave formatada RFC4716 num contentor pem de chave pública que criou anteriormente.

Para criar uma chave formatada RFC4716 a partir de uma chave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH para a VM com um cliente SSH
Com a chave pública implementado na sua VM do Azure e a chave privada no seu sistema local, o SSH para a VM com o endereço IP ou nome DNS da sua VM. Substitua *azureuser* e *myvm.westus.cloudapp.azure.com* no seguinte comando com o nome de utilizador de administrador e o nome de domínio completamente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se tiver indicado uma frase de acesso quando criou o par de chaves, introduza-a quando lhe for pedido durante o processo de início de sessão. (O servidor é adicionado à pasta `~/.ssh/known_hosts` e não lhe será pedido para ligar novamente até a chave pública na VM do Azure ser alterada ou o nome do servidor ser removido do `~/.ssh/known_hosts`.)

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Utilizar o ssh-agent para armazenar a frase de acesso de chave privada

Para evitar escrever a frase de acesso do ficheiro de chave privada com cada início de sessão SSH, pode utilizar `ssh-agent` para colocar em cache a frase de acesso do ficheiro de chave privada. Se estiver a utilizar um Mac, o macOS Keychain armazena a frase de acesso de chave privada em segurança quando invocar `ssh-agent`.

Certifique-se e utilizar `ssh-agent` e `ssh-add` para informar o sistema SSH sobre os ficheiros de chave para que não terá de utilizar o frase de acesso de forma interativa.

```bash
eval "$(ssh-agent -s)"
```

Em seguida, adicione a chave privada a `ssh-agent` utilizando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A frase de acesso de chave privada está agora armazenado numa `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Utilizar o ssh-cópia-id para copiar a chave para uma VM existente
Se já tiver criado uma VM, pode instalar a nova chave pública SSH para a VM com Linux com um comando semelhante ao seguinte:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um ficheiro de configuração SSH

Pode criar e configurar um ficheiro de configuração SSH (`~/.ssh/config`) para acelerar a inícios de registo e para otimizar o comportamento do cliente SSH. 

O exemplo seguinte mostra uma configuração simple que pode utilizar para rapidamente iniciar sessão como um utilizador para uma VM específica utilizando a chave privada SSH do predefinido. 

### <a name="create-the-file"></a>Criar o ficheiro

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite o ficheiro para adicionar a nova configuração de SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Configuração de exemplo

Adicione as definições de configuração adequadas para o anfitrião VM.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Pode adicionar configurações para anfitriões adicionais para que cada um utilizar o seu próprio par de chaves dedicado. Consulte [ficheiro de configuração SSH](https://www.ssh.com/ssh/config/) mais opções de configuração avançadas.

Agora que tem um par de chaves SSH e um ficheiro de configuração SSH configurado, pode iniciar sessão na VM do Linux de maneira rápida e segura. Quando executar o seguinte comando, o SSH localiza e carrega todas as definições do `Host myvm` bloquear no ficheiro de configuração SSH.

```bash
ssh myvm
```

A primeira vez que iniciar sessão para um servidor com uma chave SSH, os pedidos de comando a frase de acesso para esse ficheiro de chave.

## <a name="next-steps"></a>Passos Seguintes

O passo seguinte consiste na criação de VMs com Linux do Azure utilizando a nova chave pública SSH. As VMs do Azure criadas com uma chave pública SSH como início de sessão estão mais protegidas do que as criadas com as palavras-passe, o método de início de sessão predefinido.

* [Criar uma máquina virtual Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com a CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux utilizando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
