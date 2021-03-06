---
title: Criar um gateway de aplicação com uma firewall de aplicação web - portal do Azure | Microsoft Docs
description: Saiba como criar um gateway de aplicação com uma firewall de aplicação web utilizando o portal do Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: fdddcfd6ac44a0675e33e7f389a03d6dbdcfb223
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Criar um gateway de aplicação com uma firewall de aplicação web no portal do Azure

Pode utilizar o portal do Azure para criar um [gateway de aplicação](overview.md) com um [firewall de aplicações web](waf-overview.md) (WAF). As utilizações WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) regras para proteger a sua aplicação. Estas regras incluem a proteção contra ataques, tais como a injeção de SQL, ataques de scripts entre sites e hijacks de sessão.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um gateway de aplicação com WAF ativada
> * Criar as máquinas virtuais utilizadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar o diagnóstico

![Exemplo de firewall de aplicação Web](./media/create-waf-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

É necessária uma rede virtual para a comunicação entre os recursos que criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicação e outra para servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que criar o gateway de aplicação.

1. Clique em **novo** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **redes** e, em seguida, selecione **Gateway de aplicação** na lista em destaque.
3. Introduza estes valores para o gateway de aplicação:

    - *myAppGateway* - para que o nome do gateway de aplicação.
    - *myResourceGroupAG* – para o novo grupo de recursos.
    - Selecione *WAF* para a camada do gateway de aplicação.

    ![Criar o novo gateway de aplicação](./media/create-waf-portal/application-gateway-create.png)

4. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
5. Clique em **escolha uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:

    - *myVNet* - para que o nome da rede virtual.
    - *10.0.0.0/16* - para que o espaço de endereços de rede virtual.
    - *myAGSubnet* - para que o nome de sub-rede.
    - *10.0.0.0/24* - para que o espaço de endereços de sub-rede.

    ![Criar a rede virtual](./media/create-waf-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **escolher um endereço IP público**, clique em **criar nova**e, em seguida, introduza o nome do endereço IP público. Neste exemplo, o endereço IP público é denominado *myAGPublicIPAddress*. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
8. Aceite os valores predefinidos para a configuração do serviço de escuta, deixe a firewall de aplicações Web desativada e, em seguida, clique em **OK**.
9. Reveja as definições na página de resumo e, em seguida, clique em **OK** para criar recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até o gateway de aplicação ser criado, aguarde a conclusão da implementação com êxito antes de continuar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **todos os recursos** no menu da esquerda e, em seguida, clique em **myVNet** na lista de recursos.
2. Clique em **sub-redes**e, em seguida, clique em **sub-rede**.

    ![Criar sub-rede](./media/create-waf-portal/application-gateway-subnet.png)

3. Introduza *myBackendSubnet* para o nome da sub-rede e, em seguida, clique em **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, crie duas máquinas virtuais a ser utilizada como servidores de back-end para o gateway de aplicação. Também pode instala o IIS nas máquinas virtuais para verificar se o gateway de aplicação foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **Novo**.
2. Clique em **computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Introduza estes valores para a máquina virtual:

    - *myVM* - para que o nome da máquina virtual.
    - *azureuser* - no nome de utilizador do administrador.
    - *Azure123456!* a palavra-passe.
    - Selecione **utilizar existente**e, em seguida, selecione *myResourceGroupAG*.

4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e a sub-rede é **myBackendSubnet**. 
7. Clique em **Desativado** para desativar o diagnóstico de arranque.
8. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a shell interativa e certifique-se de que está definido como **PowerShell**.

    ![Instalar a extensão personalizado](./media/create-waf-portal/application-gateway-extension.png)

2. Execute o seguinte comando para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Criar uma segunda máquina virtual e instale o IIS utilizando os passos que acabou de concluir. Introduza *myVM2* para o respetivo nome e para VMName no conjunto AzureRmVMExtension.

### <a name="add-backend-servers"></a>Adicionar servidores de back-end

1. Clique em **todos os recursos**e, em seguida, clique em **myAppGateway**.
2. Clique em **conjuntos back-end**. Um conjunto predefinido foi criado automaticamente com o gateway de aplicação. Clique em **appGateayBackendPool**.
3. Clique em **adicionar destino** para adicionar cada máquina virtual que criou para o conjunto de back-end.

    ![Adicionar servidores de back-end](./media/create-waf-portal/application-gateway-backend.png)

4. Clique em **Guardar**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar o diagnóstico

## <a name="create-a-storage-account"></a>Create a storage account

Neste tutorial, o gateway de aplicação utiliza uma conta do storage para armazenar dados para efeitos de deteção e prevenção. Também pode utilizar a análise de registos ou Hub de eventos para registar dados.

1. Clique em **novo** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** e selecione **Conta de Armazenamento - blob, ficheiro, tabela, fila**.
3. Introduza o nome da conta de armazenamento, selecione **utilização existente** para o grupo de recursos e, em seguida, selecione **myResourceGroupAG**. Neste exemplo, o nome de conta de armazenamento é *myagstore1*. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **criar**.

## <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure diagnósticos para registar dados para os registos ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog.

1. No menu da esquerda, clique em **todos os recursos**e, em seguida, selecione *myAppGateway*.
2. Em monitorização, clique em **registos de diagnóstico**.
3. Clique em **Adicionar definição de diagnóstico**.
4. Introduza *myDiagnosticsSettings* como o nome para as definições de diagnóstico.
5. Selecione **arquivo para uma conta de armazenamento**e, em seguida, clique em **configurar** para selecionar o *myagstore1* conta de armazenamento que criou anteriormente.
6. Selecione os registos do gateway de aplicação para recolher e manter.
7. Clique em **Guardar**.

    ![Configurar o diagnóstico](./media/create-waf-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>O gateway de aplicação de teste

1. Localize o endereço IP público para o gateway de aplicação no ecrã descrição geral. Clique em **todos os recursos** e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registar o endereço IP público do application gateway](./media/create-waf-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser.

    ![Gateway de aplicação de teste](./media/create-waf-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar um gateway de aplicação com WAF ativada
> * Criar as máquinas virtuais utilizadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar o diagnóstico

Para obter mais informações sobre gateways de aplicação e os recursos associados, avance para os artigos de procedimentos.