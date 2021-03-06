---
title: 'Implementar os serviços de aplicação: A pilha do Azure | Microsoft Docs'
description: Orientação detalhada para implementar o serviço de aplicações na pilha do Azure
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 330b8015bdddbbcf27e4325b97e8b734c4d98d12
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2018
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Adicionar um fornecedor de recursos do serviço de aplicações a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

> [!IMPORTANT]
> Aplicar a atualização 1802 ao seu sistema de pilha do Azure integrado ou implementar o kit de desenvolvimento de pilha do Azure mais recente antes de implementar o serviço de aplicações do Azure.
>
>

Como um operador de nuvem de pilha do Azure, pode conceder aos seus utilizadores a capacidade de criar aplicações API e web. Para tal, terá de adicionar primeiro o [fornecedor de recursos do serviço de aplicações](azure-stack-app-service-overview.md) para a implementação da pilha de Azure conforme descrito neste artigo. Depois de ter instalado o fornecedor de recursos do serviço de aplicações, pode incluí-la nas suas ofertas e planos. Os utilizadores, em seguida, podem subscrever para obter o serviço e começar a criar aplicações.

> [!IMPORTANT]
> Antes de executar o programa de instalação, certifique-se de que seguiu as orientações no [antes de começar](azure-stack-app-service-before-you-get-started.md).
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador de fornecedor de recursos do serviço de aplicações

Instalar o fornecedor de recursos do serviço de aplicações para o seu ambiente de pilha do Azure pode demorar, pelo menos, uma hora dependentes quantas instâncias de função que optar por implementar. Durante este processo, o instalador irá:

* Crie um contentor de BLOBs na conta do storage do Azure pilha especificada.
* Crie uma zona DNS e entradas para o serviço de aplicações.
* Registe o fornecedor de recursos do serviço de aplicações.
* Registe os itens de galeria do serviço de aplicações.

Para implementar o fornecedor de recursos do serviço de aplicações, siga estes passos:

1. Execute appservice.exe como administrador a partir de um computador que pode aceder a pilha de administração do Azure recursos gestão ponto final do Azure.

2. Clique em **implementar o serviço de aplicações ou Atualize para a versão mais recente**.

    ![Instalador do serviço de aplicações][1]

3. Reveja e aceite os termos de licenciamento de Software Microsoft e, em seguida, clique em **seguinte**.

4. Reveja e aceite os termos de licença de terceiros e, em seguida, clique em **seguinte**.

5. Certifique-se de que as informações de configuração do serviço de aplicações em nuvem estão corretas. Se utilizou as predefinições durante a implementação do Kit de desenvolvimento de pilha do Azure, pode aceitar os valores predefinidos aqui. No entanto, se as opções personalizado quando implementado pilha do Azure ou estiver a implementar um sistema integrado, tem de editar os valores nesta janela para refletir que. Por exemplo, se utilizar o mycloud.com de sufixo de domínio, o ponto final do Azure pilha inquilino do Azure Resource Manager tem de alterar para gestão. &lt;região&gt;. mycloud.com. Depois de confirmar as suas informações, clique em **seguinte**.

    ![Instalador do serviço de aplicações][2]

6. Na página seguinte:
    1. Clique em de **Connect** junto ao **subscrições de pilha do Azure** caixa.
        * Se estiver a utilizar o Azure Active Directory (Azure AD), introduza a conta de administrador do Azure AD e a palavra-passe que forneceu quando implementou a pilha do Azure. Clique em **sessão**.
        * Se estiver a utilizar serviços de Federação do Active Directory (AD FS), forneça a sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Introduza a palavra-passe e clique em **sessão**.
    2. No **subscrições de pilha do Azure** caixa, selecione o **subscrição do fornecedor predefinido**.
    3. No **localizações de pilha do Azure** caixa, selecione a localização que corresponde à região estiver a implementar. Por exemplo, seleccione **local** se a implementar o Kit de desenvolvimento de pilha do Azure.

    ![Instalador do serviço de aplicações][3]

4. Tem agora a opção de implementar numa rede Virtual existente, como configurado através dos passos [aqui](azure-stack-app-service-before-you-get-started.md#virtual-network), ou permitir que o instalador do serviço de aplicações criar uma rede Virtual e sub-redes associadas.
    1. Selecione **criar VNet com configurações predefinidas**, aceite as predefinições e clique em **seguinte**, ou;
    2. Selecione **utilizar VNet existente e sub-redes**.
        1. Selecione o **grupo de recursos** que contém a rede Virtual;
        2. Escolha o correto **rede Virtual** nome que pretende implementar numa;
        3. Selecione o correto **sub-rede** valores para cada um das sub-redes de função necessários;
        4. Clique em **Seguinte**

    ![Instalador do serviço de aplicações][4]

7. Introduza as informações para a partilha de ficheiros e, em seguida, clique em **seguinte**. O endereço da partilha de ficheiros tem de utilizar o nome de domínio completamente qualificado ou o endereço IP do seu servidor de ficheiros. Por exemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.

   > [!NOTE]
   > O instalador tenta testar a conectividade à partilha de ficheiros antes de continuar.  No entanto se tiver escolhido implementar uma rede Virtual existente, o instalador poderá não conseguir ligar à partilha de ficheiros e é apresentado um aviso a perguntar se pretende continuar.  Verifique as informações da partilha de ficheiros e continuar se estes estão corretos.
   >
   >

   ![Instalador do serviço de aplicações][7]

8. Na página seguinte:
    1. No **ID da aplicação de identidade** box, introduza o GUID para a aplicação estiver a utilizar para a identidade (a partir do Azure AD).
    2. No **ficheiro de certificado de identidade aplicação** caixa, introduza (ou navegue até à) a localização do ficheiro de certificado.
    3. No **palavra-passe de certificado de identidade aplicação** caixa, introduza a palavra-passe do certificado. Esta palavra-passe é aquele que anotou quando utilizou o script para criar os certificados.
    4. No **ficheiro de certificado de raiz do Azure Resource Manager** caixa, introduza (ou navegue até à) a localização do ficheiro de certificado.
    5. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações][9]

9. Para cada uma a três caixas de ficheiro de certificado, clique em **procurar** e navegue para o ficheiro de certificado adequado. Tem de fornecer a palavra-passe para cada certificado. Estes certificados são aqueles que criou no [passo de certificados necessários criar](azure-stack-app-service-before-you-get-started.md#get-certificates). Clique em **seguinte** após introduzir todas as informações.

    | Box | Exemplo de nome de ficheiro de certificado |
    | --- | --- |
    | **Ficheiro de certificado SSL do serviço de aplicações predefinido** | \_.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado SSL de API do serviço de aplicações** | api.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado de SSL de publicador do serviço de aplicações** | ftp.appservice.local.AzureStack.external.pfx |

    Se utilizou um sufixo de domínio diferente ao criar os certificados, não utilizem os nomes de ficheiro de certificado *local. AzureStack.external*. Em alternativa, utilize as informações de domínio personalizado.

    ![Instalador do serviço de aplicações][10]

10. Introduza os detalhes do SQL Server para a instância de servidor utilizada para alojar as bases de dados do fornecedor de recursos do serviço de aplicações e, em seguida, clique em **seguinte**. O instalador valida as propriedades de ligação do SQL Server.

    > [!NOTE]
    > O instalador tenta testar a conectividade com o SQl Server antes de continuar.  No entanto se tiver escolhido implementar uma rede Virtual existente, o instalador poderá não conseguir ligar ao SQL Server e é apresentado um aviso a perguntar se pretende continuar.  Verifique as informações do SQL Server e continuar se estes estão corretos.
    >
    >

    ![Instalador do serviço de aplicações][11]

11. Reveja as opções de SKU e a instância de função. As predefinições preenchido com o número mínimo de instância e o SKU mínimo para cada função numa implementação ASDK. É fornecido um resumo dos requisitos vCPU e memória para o ajudar a planear a implementação. Depois de efetuar as seleções, clique em **seguinte**.

    > [!NOTE]
    > Para implementações de produção, seguir as orientações no [planeamento de capacidade de funções de servidor do App Service do Azure na pilha de Azure](azure-stack-app-service-capacity-planning.md).
    >
    >

    | Função | Instâncias mínimas | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controlador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Gere e mantém o estado de funcionamento da nuvem do serviço de aplicações. |
    | Gestão | 1 | Standard_A2 - (vCPUs 2, 3584 MB) | Gere os pontos finais App Service do Azure Resource Manager e API, extensões portais (administrador inquilino, portal das funções) e o serviço de dados. Para suportar a ativação pós-falha, aumentar as instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica conteúdo através da implementação web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha os pedidos a aplicações de serviço de aplicações. |
    | Trabalho partilhado | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Anfitriões ou aplicações API aplicações web e funções do Azure. Pode querer adicionar mais instâncias. Como um operador, pode definir a sua oferta e escolha qualquer camada SKU. As camadas tem de ter um mínimo de um vCPU. |

    ![Instalador do serviço de aplicações][13]

    > [!NOTE]
    > **Windows Server 2016 Core não é uma imagem de plataforma suportada para utilização com o Azure App Service na pilha do Azure.  Não utilize imagens de avaliação para implementações de produção.**

12. No **selecione a imagem de plataforma** caixa, selecione a imagem de máquina virtual de implementação do Windows Server 2016 a partir de imagens disponíveis no fornecedor de recursos de computação para a nuvem de serviço de aplicações. Clique em **Seguinte**.

13. Na página seguinte:
     1. Introduza o nome de utilizador de administrador de máquina virtual de função de trabalho e a palavra-passe.
     2. Introduza o nome de utilizador de administrador de máquina virtual de outras funções e a palavra-passe.
     3. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações][15]    

14. Na página de resumo:
    1. Certifique-se seleções que fez. Para efetuar alterações, utilize o **anterior** botões para visitar às páginas anteriores.
    2. Se as configurações estão corretas, selecione a caixa de verificação.
    3. Para iniciar a implementação, clique em **seguinte**.

    ![Instalador do serviço de aplicações][16]

15. Na página seguinte:
    1. Controle o progresso da instalação. Serviço de aplicações na pilha de Azure demora cerca de 60 minutos para implementar com base nas seleções predefinido.
    2. Depois do programa de instalação for concluída com êxito, clique em **saída**.

    ![Instalador do serviço de aplicações][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicações numa instalação de pilha do Azure

1. No portal de administração de pilha do Azure, aceda a **administração - App Service**.

2. Na descrição geral em estado, certifique-se que o **estado** mostra **todas as funções, estará pronto**.

    ![Gestão de serviço de aplicações](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Se optar por implementar uma rede virtual existente e um endereço IP interno para conenct para o servidor de ficheiros, tem de adicionar uma regra de segurança de saída, permitindo o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB.  Para fazê-lo, aceda a WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
> * Origem: nenhuma
> * Intervalo de portas de origem: *
> * Destino: Endereços IP
> * Intervalo de endereços IP de destino: intervalo de IPs para o servidor de ficheiros
> * Intervalo de portas de destino: 445
> * Protocolo: TCP
> * Ação: permitir
> * Prioridade: 700
> * Nome: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Testar o serviço de aplicações na pilha do Azure

Depois de implementar e registar o fornecedor de recursos do serviço de aplicações, testá-lo para se certificar de que os utilizadores podem implementar web e API apps.

> [!NOTE]
> Terá de criar uma oferta que tenha o espaço de nomes Microsoft. Web dentro do plano. Em seguida, terá de ter uma subscrição de inquilino subscreve esta oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
*Tem* ter uma subscrição de inquilino para criar aplicações que utilizam o serviço de aplicações na pilha do Azure. As capacidades de apenas um administrador de serviço pode ser no portal de administração estão relacionadas com a administração de fornecedor de recursos do App Service. Estas capacidades incluem adicionar capacidade, configurar origens de implementação e adicionar camadas de trabalho e SKUs.
>
Criar web, a API e o Azure funciona aplicações, tem de utilizar o portal de inquilinos e ter uma subscrição de inquilino.

1. No portal de inquilinos pilha do Azure, clique em **novo** > **Web + móvel** > **aplicação Web**.

2. No **aplicação Web** painel, escreva um nome no **aplicação Web** caixa.

3. Em **grupo de recursos**, clique em **novo**. Escreva um nome no **grupo de recursos** caixa.

4. Clique em **plano do Serviço de Aplicações/Localização** > **Criar Novo**.

5. No **plano do App Service** painel, escreva um nome no **plano do App Service** caixa.

6. Clique em **escalão de preço** > **livres partilhados** ou **partilhados partilhados** > **selecione**  >   **OK** > **criar**.

7. Na sob um minuto, um mosaico para a nova aplicação web aparece no dashboard. Clique no mosaico.

8. No **aplicação Web** painel, clique em **procurar** para ver o Web site predefinido para esta aplicação.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementar um site WordPress, DNN ou Django (opcional)

1. No portal de inquilinos pilha do Azure, clique em **+**, vá para o Azure Marketplace, implementar um site do Django e aguarde pela conclusão com êxito. A plataforma de web Django utiliza um ficheiro com base no sistema base de dados do. Não requer quaisquer fornecedores de recursos adicionais, tais como SQL Server ou MySQL.

2. Se implementou também um fornecedor de recursos do MySQL, pode implementar um site WordPress no Marketplace. Quando lhe for pedida para parâmetros de base de dados, introduza o nome de utilizador como *User1@Server1*, com o nome de utilizador e o nome do servidor da sua preferência.

3. Se implementou também um fornecedor de recursos do SQL Server, pode implementar um site DNN do Marketplace. Quando lhe for pedida para parâmetros de base de dados, escolha uma base de dados no computador que executa o SQL Server que está ligada ao seu fornecedor de recursos.

## <a name="next-steps"></a>Passos Seguintes

Também pode experimentar o outro [plataforma como dos serviços de serviço (PaaS)](azure-stack-tools-paas-services.md).

- [Fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Fornecedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
