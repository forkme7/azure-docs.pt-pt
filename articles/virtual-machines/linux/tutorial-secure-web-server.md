---
title: Proteger um servidor Web com certificados SSL no Azure | Microsoft Docs
description: Saiba como proteger o servidor Web NGINX com certificados SSL numa VM com Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 02118533c4ab552f81157f644bb794e68fbc4ce3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2018
---
# <a name="secure-a-web-server-with-ssl-certificates-on-a-linux-virtual-machine-in-azure"></a>Proteger um servidor Web com certificados SSL numa máquina virtual com Linux no Azure
Para proteger servidores Web, pode ser utilizado um certificado SSL (Secure Sockets Layer) para encriptar o tráfego da Web. Estes certificados SSL podem ser armazenados no Azure Key Vault e permitir implementações seguras de certificados para máquinas virtuais (VMs) com Linux no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um Azure Key Vault
> * Gerar ou carregar um certificado para o Key Vault
> * Criar uma VM e instalar o servidor Web NGINX
> * Inserir o certificado na VM e configurar o NGINX com um enlace SSL

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.22 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).  


## <a name="overview"></a>Descrição geral
O Azure Key Vault salvaguarda as chaves criptográficas e os segredos, como certificados ou palavras-passe. O Key Vault ajuda a simplificar o processo de gestão de chaves e permite-lhe manter o controlo das chaves que acedem a esses certificados. Pode criar um certificado autoassinado no Key Vault ou carregar um certificado fidedigno que já possui.

Em vez de utilizar uma imagem de VM personalizada, que inclua certificados integrados, pode inserir certificados numa VM em execução. Este processo garante que são instalados os certificados mais atualizados num servidor Web durante a implementação. Se renovar ou substituir um certificado, também não tem de criar uma nova imagem de VM personalizada. Os certificados mais recentes são inseridos automaticamente à medida que cria VMs adicionais. Durante todo o processo, os certificados nunca saem da plataforma do Azure nem são expostos num script, histórico de linha de comandos ou modelo.


## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault
Para poder criar um Key Vault e certificados, crie primeiro um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroupSecureWeb* na localização *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Em seguida, crie um Key Vault com [az keyvault create](/cli/azure/keyvault#az_keyvault_create) e ative-o para utilização quando implementar uma VM. Cada Key Vault requer um nome exclusivo com todas as letras minúsculas. Substitua *<mykeyvault>* no exemplo seguinte pelo seu próprio nome exclusivo do Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazená-lo no Key Vault
Para efeitos de produção, deve importar um certificado válido assinado por um fornecedor fidedigno com [az keyvault certificate import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). Para este tutorial, o exemplo seguinte mostra como pode gerar um certificado autoassinado com [az keyvault certificate create](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) que utiliza a política de certificado predefinida:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Preparar um certificado para utilização numa VM
Para utilizar o certificado durante o processo de criação da VM, obtenha o ID do certificado com [az keyvault secret list-versions](/cli/azure/keyvault/secret#az_keyvault_secret_list_versions). Converta o certificado com [az vm format-secret](/cli/azure/vm#az_vm_format_secret). O exemplo seguinte atribui o resultado destes comandos a variáveis para uma utilização mais fácil nos passos que se seguem:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Criar uma configuração cloud-init para proteger o NGINX
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é executado durante o processo de arranque inicial, não existem passos adicionais ou agentes necessários a aplicar à configuração.

Quando criar uma VM, os certificados e as chaves são armazenados no diretório */var/lib/waagent/* protegido. Para automatizar a adição do certificado à VM e configurar o servidor Web, utilize o cloud-init. Neste exemplo, vai instalar e configurar o servidor Web NGINX. Pode utilizar o mesmo processo para instalar e configurar o Apache. 

Crie um ficheiro com o nome *cloud-init-web-server.txt* e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Criar uma VM segura
Agora, crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). Os dados do certificado são inseridos a partir do Key Vault com o parâmetro `--secrets`. Transmite a configuração de cloud-init com o parâmetro `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Demora alguns minutos até a VM ser criada, os pacotes serem instalados e a aplicação ser iniciada. Quando a VM tiver sido criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder ao seu site num browser.

Para permitir que o tráfego da Web seguro aceda à VM, abra a porta 443 a partir da Internet com [az vm open-port](/cli/azure/vm#az_vm_open_port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testar a aplicação Web segura
Agora, pode abrir um browser e introduzir *https://<publicIpAddress>* na barra de endereço. Forneça o seu próprio endereço IP público a partir do processo de criação da VM. Aceite o aviso de segurança se utilizou um certificado autoassinado:

![Aceitar o aviso de segurança do browser](./media/tutorial-secure-web-server/browser-warning.png)

O site NGINX protegido é apresentado como no exemplo seguinte:

![Ver site NGINX seguro em execução](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, protegeu um servidor Web NGINX com um certificado SSL armazenado no Azure Key Vault. Aprendeu a:

> [!div class="checklist"]
> * Criar um Azure Key Vault
> * Gerar ou carregar um certificado para o Key Vault
> * Criar uma VM e instalar o servidor Web NGINX
> * Inserir o certificado na VM e configurar o NGINX com um enlace SSL

Siga esta ligação para ver os exemplos de scripts de máquina virtual pré-criados.

> [!div class="nextstepaction"]
> [Exemplos de scripts de máquina virtual com Linux](./cli-samples.md)

