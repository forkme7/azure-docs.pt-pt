---
title: VPN do Azure gateway RADIUS autenticação segura com o servidor NPS para o multi-factor Authentication | Microsoft Docs
description: Descreve integrar a autenticação de RADIUS do gateway do Azure com o servidor NPS para multi-factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: genli
ms.openlocfilehash: 665e1914f44d7c5e650a1b632d8b11c6d8a5931a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2018
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrar autenticação RADIUS do gateway de VPN do Azure com o servidor NPS para multi-factor Authentication 

O artigo descreve como integrar o servidor de políticas de rede (NPS) com a autenticação RADIUS do gateway de VPN do Azure para fornecer o multi-factor Authentication (MFA) para ligações de VPN ponto a site. 

## <a name="prerequisite"></a>Pré-requisito

Para ativar a MFA, os utilizadores tem de ser no Azure Active Directory (Azure AD), que tem de ser sincronizados a partir no local ou ambiente de nuvem. Além disso, o utilizador tem de ter já concluído o processo de inscrição automática para a MFA.  Para obter mais informações, consulte [configurar a minha conta para a verificação de dois passos](../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Passos detalhados

### <a name="step-1-create-a-virtual-network-gateway"></a>Passo 1: Criar um gateway de rede virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na rede virtual que irá alojar o gateway de rede virtual, selecione **sub-redes**e, em seguida, selecione **sub-rede do Gateway** para criar uma sub-rede. 

    ![A imagem sobre como adicionar sub-rede do gateway](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Crie um gateway de rede virtual ao especificar as seguintes definições:

    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo de VPN**: selecione **baseado na rota**.
    - **SKU**: selecione um tipo SKU com base nos seus requisitos.
    - **Rede virtual**: selecione a rede virtual que criou a sub-rede do gateway.

        ![A imagem sobre as definições do gateway de rede virtual](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Passo 2 configurar o NPS para o MFA do Azure

1. No servidor NPS, [instalar a extensão NPS do MFA do Azure](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Abra a consola do NSP, faça duplo clique **RADUIS clientes**e, em seguida, selecione **novo**. Crie o cliente RADUIS especificando as seguintes definições:

    - **Nome amigável**: escreva um nome.
    - **Endereços (IP ou DNS)**: escreva a sub-rede do gateway que criou no passo 1.
    - **Segredo partilhado**: escreva qualquer chave secreta e não se esqueça dela para utilização posterior.

    ![A imagem sobre definições de cliente RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  No **avançadas** separador, defina o nome do fornecedor para **RADIUS padrão** e certifique-se de que o **opções adicionais** não estiver selecionada a caixa de verificação.

    ![A imagem sobre as definições avançadas do RADUIS cliente](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Aceda a **políticas** > **políticas de rede**, faça duplo clique em **ligações ao servidor de Microsoft Routing e acesso remoto** política, selecione  **Conceder acesso**e, em seguida, clique em **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Passo 3 de configurar o gateway de rede virtual

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o gateway de rede virtual que criou. Certifique-se de que o tipo de gateway está definido como **VPN** e de que o tipo VPN é **baseado na rota**.
3. Clique em **apontar para a configuração do site** > **configurar agora**e, em seguida, especifique as seguintes definições:

    - **Conjunto de endereços**: escreva a sub-rede do gateway que criou no passo 1.
    - **Tipo de autenticação**: selecione **autenticação RADIUS**.
    - **Endereço IP do servidor**: escreva o endereço IP do servidor NPS.

    ![A imagem sobre ponto para as definições do site](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Passos Seguintes

- [Multi-factor Authentication do Azure](../active-directory/authentication/multi-factor-authentication.md)
- [Integrar a infraestrutura NPS existente com o Multi-Factor Authentication do Azure](../active-directory/authentication/howto-mfa-nps-extension.md)
