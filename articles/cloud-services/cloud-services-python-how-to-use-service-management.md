---
title: "Utilize a API do serviço de gestão (Python) - guia de funcionalidades"
description: "Saiba como programaticamente efetuar tarefas de gestão comuns do serviço do Python."
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: b89f1aad46621d35728934ea068a5893ba674094
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2018
---
# <a name="use-service-management-from-python"></a>Utilize a gestão de serviço do Python
Este guia mostra como programaticamente efetuar tarefas de gestão comuns do serviço do Python. O **ServiceManagementService** classe no [Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python) suporta acesso programático para muito a funcionalidade relacionadas com a gestão do serviço que está disponível no [Azure Portal][management-portal]. Pode utilizar esta funcionalidade para criar, atualizar e eliminar serviços em nuvem, implementações, os serviços de gestão de dados e as máquinas virtuais. Esta funcionalidade pode ser útil na criação de aplicações que necessitam de acesso programático para gestão de serviço.

## <a name="WhatIs"></a>o que é a gestão de serviço?
A API de gestão de serviço do Azure fornece acesso programático para muito a funcionalidade de gestão do serviço, disponível através de [portal do Azure][management-portal]. Pode utilizar o Azure SDK para Python para gerir os seus serviços em nuvem e as contas de armazenamento.

Para utilizar a API de gestão de serviço, terá de [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Conceitos
O Azure SDK para Python encapsula num wrapper a [API da gestão de serviço][svc-mgmt-rest-api], que é uma API REST. Todas as operações de API são efetuadas através de SSL e mutuamente autenticadas utilizando certificados do x. 509 v3. O serviço de gestão pode ser acedido a partir de dentro de um serviço em execução no Azure. Também pode ser acedido diretamente através da Internet a partir de qualquer aplicação que pode enviar um pedido HTTPS e receber uma resposta HTTPS.

## <a name="Installation"></a>Instalação
Todas as funcionalidades descritas neste artigo estão disponíveis no `azure-servicemanagement-legacy` pacote, o que pode instalar por através do pip. Para obter mais informações sobre a instalação (por exemplo, se estiver familiarizado com o Python), consulte [instalar o Python e o Azure SDK](../python-how-to-install.md).

## <a name="Connect"></a>Ligar à gestão de serviço
Para ligar ao ponto final de gestão do serviço, terá do ID de subscrição do Azure e um certificado de gestão válido. Pode obter o ID de subscrição através de [portal do Azure][management-portal].

> [!NOTE]
> Agora pode utilizar certificados criados com OpenSSL quando em execução no Windows. Python 2.7.4 ou posterior é necessária. Recomendamos que utilize OpenSSL em vez de. pfx, porque o suporte para certificados. pfx é provável que seja removida no futuro.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gestão no Mac/Windows/Linux (OpenSSL)
Pode utilizar [OpenSSL](http://www.openssl.org/) para criar o certificado de gestão. Tem de criar dois certificados, um para o servidor (um `.cer` ficheiro) e outra para o cliente (uma `.pem` ficheiro). Para criar o `.pem` de ficheiros, execute:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o `.cer` de certificado, execute:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obter mais informações sobre os certificados do Azure, consulte [descrição geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros de OpenSSL, consulte a documentação em [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Depois de criar estes ficheiros, carregue o `.cer` ficheiro para o Azure. No [portal do Azure][management-portal], no **definições** separador, selecione **carregar**. Tenha em atenção onde guardou o `.pem` ficheiro.

Após obter o ID de subscrição, criar um certificado e carregar o `.cer` ficheiro para o Azure, ligar para o ponto final de gestão do Azure. Ligar através da transmissão do ID de subscrição e o caminho para o `.pem` do ficheiro para **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um **ServiceManagementService** objeto. O **ServiceManagementService** classe é a classe principal utilizada para gerir os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gestão no Windows (MakeCert)
Pode criar um certificado de gestão autoassinado no seu computador, utilizando `makecert.exe`. Abra um **linha de comandos do Visual Studio** como um **administrador** e utilize o seguinte comando, substituindo *AzureCertificate* com o nome do certificado que pretende utilizar:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o `.cer` de ficheiros e instala-na **pessoais** arquivo de certificados. Para obter mais informações, consulte [descrição geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md).

Depois de criar o certificado, carregue o `.cer` ficheiro para o Azure. No [portal do Azure][management-portal], no **definições** separador, selecione **carregar**.

Após obter o ID de subscrição, criar um certificado e carregar o `.cer` ficheiro para o Azure, ligar para o ponto final de gestão do Azure. Ligar através da transmissão do ID de subscrição e a localização do certificado no seu **pessoais** arquivo de certificados para **ServiceManagementService** (novamente, substitua *AzureCertificate* com o nome do seu certificado).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um **ServiceManagementService** objeto. O **ServiceManagementService** classe é a classe principal utilizada para gerir os serviços do Azure.

## <a name="ListAvailableLocations"></a>Lista as localizações disponíveis
Para listar as localizações que estão disponíveis para o alojamento de serviços, utilize o **lista\_localizações** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando cria um serviço em nuvem ou um serviço de armazenamento, tem de fornecer uma localização válida. O **lista\_localizações** método sempre devolve uma lista atualizada das localizações atualmente disponíveis. A partir desta redação, as localizações disponíveis são:

* Europa Ocidental
* Europa do Norte
* Sudeste Asiático
* Ásia Oriental
* EUA Central
* EUA Centro-Norte
* E.U.A. Centro-Sul
* EUA Oeste
* EUA Leste
* Leste do Japão
* Oeste do Japão
* Sul do Brasil
* Leste da Austrália
* Sudeste da Austrália

## <a name="CreateCloudService"></a>Criar um serviço em nuvem
Quando cria uma aplicação e executá-lo no Azure, o código e a configuração em conjunto são denominados um Azure [serviço em nuvem][cloud service]. (Era conhecido como um *serviço alojado* em sistemas operativos mais recentes do Azure.) Pode utilizar o **criar\_alojado\_serviço** método para criar um novo serviço alojado. Crie o serviço, fornecendo um nome de serviço alojado (que tem de ser exclusivo no Azure), uma etiqueta (automaticamente com codificação base64), uma descrição e uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Pode listar todos os serviços alojados para a sua subscrição com o **lista\_alojado\_serviços** método.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Para obter informações sobre um determinado serviço alojado, transmitir o nome do serviço alojado para o **obter\_alojado\_serviço\_propriedades** método.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de criar um serviço em nuvem, implementar o código para o serviço com o **criar\_implementação** método.

## <a name="DeleteCloudService"></a>Eliminar um serviço em nuvem
Pode eliminar um serviço em nuvem transferindo o nome do serviço para o **eliminar\_alojado\_serviço** método.

    sms.delete_hosted_service('myhostedservice')

Antes de poder eliminar um serviço, tem de ser apagadas todas as implementações para o serviço. Para obter mais informações, consulte [eliminar uma implementação](#DeleteDeployment).

## <a name="DeleteDeployment"></a>Eliminar uma implementação
Para eliminar uma implementação, utilize o **eliminar\_implementação** método. O exemplo seguinte mostra como eliminar uma implementação designada `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Criar um serviço de armazenamento
A [serviço de armazenamento](../storage/common/storage-create-storage-account.md) dá-lhe acesso ao Azure [blobs](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabelas](../cosmos-db/table-storage-how-to-use-python.md), e [filas](../storage/queues/storage-python-how-to-use-queue-storage.md). Para criar um serviço de armazenamento, é necessário um nome de serviço (entre 3 e 24 carateres em minúsculas e exclusivo no Azure). Terá também uma descrição, uma etiqueta (máximo de 100 carateres, codificados automaticamente para base64) e uma localização. O exemplo seguinte mostra como criar um serviço de armazenamento, especificando uma localização:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

No exemplo anterior, o estado do **criar\_armazenamento\_conta** operação pode ser obtida ao transferir o resultado devolvido pelo **criar\_armazenamento\_ conta** para o **obter\_operação\_estado** método. 

Pode listar as contas de armazenamento e as respetivas propriedades com o **lista\_armazenamento\_contas** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Eliminar um serviço de armazenamento
Para eliminar um serviço de armazenamento, transmitir o nome do serviço de armazenamento para o **eliminar\_armazenamento\_conta** método. Eliminar um serviço de armazenamento elimina todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Lista os sistemas operativos disponíveis
Para listar os sistemas operativos que estão disponíveis para o alojamento de serviços, utilize o **lista\_operativo\_sistemas** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Em alternativa, pode utilizar o **lista\_operativo\_sistema\_famílias** método, o que os sistemas operativos por família de grupos.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Criar uma imagem de sistema operativo
Para adicionar uma imagem do sistema operativo para o repositório de imagens, utilize o **adicionar\_SO\_imagem** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Para listar as imagens de sistema operativo que estão disponíveis, utilize o **lista\_SO\_imagens** método. Inclui todas as imagens de plataforma e imagens do utilizador.

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"></a>Eliminar uma imagem do sistema operativo
Para eliminar uma imagem do utilizador, utilize o **eliminar\_SO\_imagem** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Criar uma máquina virtual
Para criar uma máquina virtual, terá primeiro de criar um [serviço em nuvem](#CreateCloudService). Em seguida, criar a implementação da máquina virtual utilizando o **criar\_virtual\_máquina\_implementação** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"></a>Eliminar uma máquina virtual
Para eliminar uma máquina virtual, primeiro eliminar a implementação utilizando o **eliminar\_implementação** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço em nuvem, em seguida, pode ser eliminado utilizando o **eliminar\_alojado\_serviço** método.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Criar uma máquina virtual a partir de uma imagem capturada máquina virtual
Para capturar uma imagem de VM, tem primeiro de chamar o **capturar\_vm\_imagem** método.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Para se certificar de que capturada com êxito a imagem, utilize o **lista\_vm\_imagens** API. Certifique-se que a imagem é apresentada nos resultados.

    images = sms.list_vm_images()

Por fim, criar a máquina virtual utilizando a imagem capturada, utilize o **criar\_virtual\_máquina\_implementação** método como anteriormente, mas, desta vez passar o vm_image_name em vez disso.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Para obter mais informações sobre como capturar uma máquina virtual do Linux no modelo de implementação clássica, consulte [capturar uma máquina virtual Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Para obter mais informações sobre como capturar uma máquina virtual do Windows no modelo de implementação clássica, consulte [capturar uma máquina virtual do Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"></a>Próximos passos
Agora que aprendeu as noções básicas de gestão de serviço, pode aceder a [documentação de referência de API completa para o SDK Python](http://azure-sdk-for-python.readthedocs.org/) e efetuar as tarefas complexas facilmente para gerir a sua aplicação de Python.

Para obter mais informações, consulte o [Centro para Programadores do Python](/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
