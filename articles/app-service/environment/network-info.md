---
title: Considerações de redes com um ambiente do App Service do Azure
description: Explica o tráfego de rede ASE e como definir os NSGs e UDRs com seu ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: ccompy
ms.openlocfilehash: 54257ae3e02a00c5097aa7880fa356da3bc0ecce
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2018
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações sobre o funcionamento em rede para um ambiente de serviço de aplicações #

## <a name="overview"></a>Descrição geral ##

 Azure [ambiente de serviço de aplicações] [ Intro] é uma implementação do App Service do Azure a uma sub-rede na sua rede virtual do Azure (VNet). Existem dois tipos de implementação para um ambiente de serviço de aplicações (ASE):

- **ASE externo**: expõe as aplicações alojadas ASE um endereço IP acessível através da internet. Para obter mais informações, consulte [criar ASE externo][MakeExternalASE].
- **ILB ASE**: expõe as aplicações alojadas ASE um endereço IP na sua VNet. O ponto final interno é um balanceador de carga interno (ILB), que é a razão foi chamado ILB ASE. Para obter mais informações, consulte [criar e utilizar ILB ASE][MakeILBASE].

Agora existem duas versões do ambiente de serviço de aplicações: ASEv1 e ASEv2. Para obter informações sobre ASEv1, consulte [introdução ao ambiente de serviço de aplicações v1][ASEv1Intro]. ASEv1 podem ser implementados num clássico ou VNet do Resource Manager. ASEv2 só pode ser implementada para uma VNet do Resource Manager.

Todas as chamadas de ASE aceder à internet, deixe a VNet através de um VIP atribuído para o ASE. O IP público este VIP é, em seguida, o IP de origem para todas as chamadas do ASE aceder à internet. Se as aplicações na sua ASE efetuar chamadas a recursos na sua VNet ou através de uma VPN, o IP de origem é uma dos IPs na sub-rede utilizada pelo seu ASE. Dado que o ASE dentro da VNet, também pode aceder aos recursos dentro da VNet sem qualquer configuração adicional. Se a VNet está ligada à sua rede no local, as aplicações na sua ASE têm também acesso a recursos não existe. Não precisa de configurar o ASE ou a sua aplicação quaisquer outras.

![ASE externo][1] 

Se tiver ASE externo, o VIP público também é o ponto final que as suas aplicações ASE resolver para:

* HTTP/S. 
* FTP/S. 
* Implementação na Web.
* Depuração remota.

![ASE DO ILB][2]

Se tiver ILB ASE, o endereço IP do ILB é o ponto final de HTTP/S, FTP/S, implementação na web e depuração remota.

As portas de acesso de aplicações normal são:

| Utilizar | De | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Utilizador configurável |  80, 443 |
|  FTP/FTPS    | Utilizador configurável |  21, 990, 10001-10020 |
|  Visual Studio depuração remota  |  Utilizador configurável |  4016, 4018, 4020, 4022 |

Isto é verdadeiro se estiver num ASE externo ou num ILB ASE. Se estiver num ASE externo, atingiu essas portas no VIP público. Se estiver num ILB ASE, atingiu essas portas no ILB. Se bloquear a porta 443, pode haver um efeito em algumas funcionalidades expostas no portal. Para obter mais informações, consulte [dependências de Portal](#portaldep).

## <a name="ase-subnet-size"></a>Tamanho da sub-rede ASE ##

O tamanho da sub-rede utilizada para alojar ASE não pode ser alterado depois do ASE é implementado.  O ASE utiliza um endereço para cada função de infraestrutura, bem como para cada instância de plano de serviço de aplicação isolado.  Além disso, existem 5 endereços utilizados pelas redes do Azure para cada sub-rede que é criado.  ASE com não planos de serviço de aplicações em todas as utilizará 12 endereços antes de criar uma aplicação.  Se for ILB ASE, em seguida, irá utilizar o 13 endereços antes de criar uma aplicação nesse ASE. À medida que os seus planos de serviço de aplicação exigirá endereços adicionais para cada Front-End que é adicionado.  Por predefinição, os servidores front-End são adicionadas para todos os 15 instâncias do plano de serviço de aplicações totais. 

   > [!NOTE]
   > Mais nada pode estar na sub-rede, mas o ASE. Lembre-se de que escolha um espaço de endereços que permite o crescimento futuro. Não é possível alterar esta definição mais tarde. Recomendamos um tamanho de `/25` com endereços de 128.

## <a name="ase-dependencies"></a>Dependências de ASE ##

ASE dependência de acesso de entrada é:

| Utilizar | De | Para |
|-----|------|----|
| Gestão | Endereços de gestão do serviço de aplicações | A sub-rede de ASE: 454, 455 |
|  Comunicação interna ASE | A sub-rede de ASE: todas as portas | A sub-rede de ASE: todas as portas
|  Permitir que o Balanceador de carga do Azure entrada | Balanceador de carga do Azure | A sub-rede de ASE: todas as portas
|  Aplicação de endereços IP atribuída | Aplicação endereços atribuída | A sub-rede de ASE: todas as portas

O tráfego de entrada fornece o comando e controlo de ASE para além de monitorização do sistema. A origem de IPs para este tráfego estão listados no [ASE gestão endereços] [ ASEManagement] documento. A configuração de segurança de rede tem de permitir o acesso a todos os IPs nas portas 454 e 455.

Dentro da sub-rede ASE existem muitos portas utilizadas para o componente interno comunicação e pode alterar.  Isto requer que todas as portas na sub-rede ASE esteja acessível a partir da sub-rede ASE. 

Para a comunicação entre o Azure load balancer e a sub-rede de ASE o mínimo de portas que têm de estar aberta é 454, 455 e 16001. A porta 16001 é utilizado para manter o tráfego alive entre o Balanceador de carga e a ASE. Se estiver a utilizar ILB ASE, em seguida, pode bloquear o tráfego para baixo para apenas o 454, 455, 16001 portas.  Se estiver a utilizar ASE externo, em seguida, tem de ter em conta as portas de acesso de aplicações normal.  Se estiver a utilizar endereços de aplicação atribuído terá de abri-lo para todas as portas.  Quando é atribuído um endereço para uma aplicação específica, o Balanceador de carga utilizará as portas que não sejam conhecidas do seguinte com antecedência para enviar tráfego HTTP e HTTPS para o ASE.

Se estiver a utilizar a aplicação de endereços IP, que terá de permitir o tráfego de IPs atribuídos às suas aplicações à sub-rede ASE atribuída.

Para acesso de saída, ASE depende da existência de vários sistemas externos. As dependências de sistema estão definidas com nomes DNS e não mapeiam para um conjunto de endereços IP fixo. Assim, o ASE necessita de acesso de saída da sub-rede ASE para todos os IPs externo através de diversas portas. ASE tem as seguintes dependências de saída:

| Utilizar | De | Para |
|-----|------|----|
| Storage do Azure | Sub-rede de ASE | w, w, q, w: 80, 443, 445 (445 só é necessário para ASEv1.) |
| Base de Dados SQL do Azure | Sub-rede de ASE | Database.Windows.NET: 1433, 11000 11999, 14000 14999 (para obter mais informações, consulte [utilização de porta de SQL Database V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).)|
| Gestão do Azure | Sub-rede de ASE | management.core.windows.net, management.azure.com: 443 
| Verificação de certificado SSL |  Sub-rede de ASE            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | Sub-rede de ASE            |  Internet: 443
| Gestão de serviço de aplicações        | Sub-rede de ASE            |  Internet: 443
| DNS do Azure                     | Sub-rede de ASE            |  Internet: 53
| Comunicação interna ASE    | A sub-rede de ASE: todas as portas |  A sub-rede de ASE: todas as portas

Se o ASE perder o acesso a estas dependências, deixa de funcionar. Quando isso acontece suficientemente longa, o ASE é suspenso.

### <a name="customer-dns"></a>Cliente DNS ###

Se a VNet está configurada com um servidor DNS definida pelo cliente, as cargas de trabalho de inquilino utilizá-lo. O ASE ainda tem de comunicar com o DNS do Azure para fins de gestão. 

Se a VNet está configurada com um cliente DNS no outro lado de uma VPN, o servidor DNS deve ser acessível a partir da sub-rede que contém o ASE.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dependências de portais ##

Para além das dependências de funcionais ASE, existem alguns itens adicionais relacionados com a experiência do portal. Algumas das funcionalidades no portal do Azure dependem acesso direto ao _SCM site_. Para cada aplicação no App Service do Azure, existem dois URLs. É o primeiro URL aceder à sua aplicação. O URL do segundo é aceder ao site SCM, também denominada a _consola Kudu_. As funcionalidades que utilizam o site SCM incluem:

-   Tarefas da Web
-   Funções
-   Transmissão em fluxo de registo
-   Kudu
-   Extensões
-   Explorador de Processos
-   Consola

Quando utilizar ILB ASE, o site SCM não internet acessível fora da VNet. Quando a aplicação estiver alojada num ILB ASE, algumas funcionalidades não funcionarão no portal.  

Muitas dessas funcionalidades que dependem do site SCM também estão disponíveis diretamente na consola do Kudu. Pode ligar-lhe diretamente em vez de utilizar o portal. Se a aplicação estiver alojada num ILB ASE, utiliza as credenciais da sua publicação para iniciar sessão. O URL para aceder ao site SCM de uma aplicação alojada num ILB ASE tem o seguinte formato: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Se o seu ASE ILB é o nome de domínio *contoso.net* e o nome da aplicação é *testapp*, a aplicação é atingiu *testapp.contoso.net*. O site SCM que fique com o mesmo é atingiu *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Funções e trabalhos Web ##

Funções e Web tarefas dependem do site SCM mas são suportadas para utilização no portal, mesmo que as aplicações estão num ILB ASE, desde que o browser pode chegar ao site SCM.  Se estiver a utilizar um certificado autoassinado com o seu ASE do ILB, terá de ativar o seu browser confiar esse certificado.  Para o i/e e limite que significa que o certificado tem de ser no arquivo de confiança do computador.  Se estiver a utilizar o Chrome, em seguida, o que significa que aceite o certificado no browser anteriormente pelo possivelmente atingir o site scm diretamente.  É a melhor solução utilizar um certificado comercial que está a ser a cadeia de fidedignidade de browser.  

## <a name="ase-ip-addresses"></a>Endereços ASE IP ##

ASE tem alguns endereços IP a ter em consideração. São:

- **Endereço IP público entrado**: utilizado para tráfego de aplicação está num ASE externo e o tráfego de gestão num ASE externo e ILB ASE.
- **IP público saído**: utilizada como o "de" IP de ligações de saída do ASE que deixam a VNet, que não são encaminhadas para baixo de uma VPN.
- **Endereço IP do ILB**: Se utilizar ILB ASE.
- **Aplicação atribuídos endereços SSL baseado em IP**: apenas possível com ASE externo e do SSL baseado em IP configurado.

Estes endereços IP são facilmente visíveis num ASEv2 no portal do Azure a IU de ASE. Se tiver ILB ASE, o IP para o ILB é listado.

![Endereços IP][3]

### <a name="app-assigned-ip-addresses"></a>Endereços IP atribuídas por aplicação ###

Com ASE externa, pode atribuir endereços IP para aplicações individuais. Não é possível fazê-lo com ILB ASE. Para obter mais informações sobre como configurar a sua aplicação com o seu próprio endereço IP, consulte [vincular um certificado SSL personalizado existente para aplicações web do Azure](../app-service-web-tutorial-custom-ssl.md).

Quando uma aplicação tiver o seu próprio endereço SSL baseado em IP, o ASE reserva-se em duas portas para mapear a esse endereço IP. É uma porta para tráfego HTTP e a outra porta é para HTTPS. As portas estão listadas na IU do ASE na secção de endereços IP. O tráfego deve ser capaz de alcançar essas portas de VIP ou as aplicações não estão acessíveis. Este requisito é importante lembrar-se ao configurar grupos de segurança de rede (NSGs).

## <a name="network-security-groups"></a>Grupos de Segurança de Rede ##

[Grupos de segurança de rede] [ NSGs] fornecem a capacidade de controlar o acesso de rede numa VNet. Quando utilizar o portal, uma regra de Negação implícita, está a prioridade mais baixa para negar tudo. A cria estão a permitir regras.

Está num ASE, não tem acesso às VMs utilizados para alojar o ASE próprio. Se estão numa subscrição gerida pela Microsoft. Se pretender restringir o acesso às aplicações no ASE, defina os NSGs na sub-rede ASE. Ao fazê-lo, preste atenção cuidada para as dependências de ASE. Se bloquear as dependências, o ASE deixa de funcionar.

Os NSGs podem ser configurados através do portal do Azure ou através do PowerShell. As informações aqui mostram o portal do Azure. Criar e gerir os NSGs no portal como um recurso de nível superior em **redes**.

Quando os requisitos de entrada e saídos estão levados em consideração, os NSGs devem ter um aspeto semelhantes os NSGs mostrados neste exemplo. O intervalo de endereços VNet é _192.168.250.0/23_, e a sub-rede que o ASE consta é _192.168.251.128/25_.

Os primeiro dois requisitos de entrada para ASE para a função são apresentados na parte superior da lista neste exemplo. Se ativar a gestão de ASE e permitem ASE comuniquem com ele próprio. As entradas de outras são todos os inquilinos configuráveis e podem controlam o acesso de rede para as aplicações alojadas ASE. 

![Regras de segurança de entrada][4]

Uma regra predefinida permite que os IPs na VNet para que comunique com a sub-rede ASE. Outra regra predefinida permite que o Balanceador de carga, também conhecido como o VIP público, para comunicar com o ASE. Para ver as regras predefinidas, selecione **predefinido regras** junto a **adicionar** ícone. Se o put um Negar tudo o resto da regra depois do NSG regras apresentadas, impedir que o tráfego entre o VIP e o ASE. Para impedir o tráfego proveniente de dentro da VNet, adicione as suas próprias regras para permitir a entrada. Utilizar uma origem igual a AzureLoadBalancer com um destino **qualquer** e um intervalo de portas de **\***. Porque a regra NSG é aplicada à sub-rede ASE, não precisa de ser específico no destino.

Se atribuir um endereço IP para a sua aplicação, certifique-se de que mantém as portas abertas. Para ver as portas, selecione **ambiente de serviço de aplicações** > **endereços IP**.  

Todos os itens apresentados nas seguintes regras de saída são necessárias, exceto o último item. Se ativar o acesso de rede para as dependências de ASE registados anteriormente no artigo. Se bloquear qualquer um deles, sua ASE deixa de funcionar. O último item na lista permite que a sua ASE comunicar com outros recursos na sua VNet.

![Regras de segurança de saída][5]

Depois de definidos os NSGs, atribua-lhes para a sub-rede no seu ASE. Não se lembra da ASE VNet ou sub-rede, pode vê-lo a partir da página de portal ASE. Para atribuir o NSG para a sub-rede, aceda à sub-rede da IU e selecione o NSG.

## <a name="routes"></a>Rotas ##

Imposição do túnel é quando definir rotas na sua VNet para que o tráfego de saída não aceda diretamente à internet, mas em qualquer outro como um gateway ExpressRoute ou de uma aplicação virtual local.  Se precisar de configurar o seu ASE de tal forma, em seguida, continue a ler o documento [configurar o ambiente de serviço de aplicações com a imposição do túnel][forcedtunnel].  Este documento indicam as opções disponíveis para trabalhar com o ExpressRoute e de imposição do túnel.

Quando criar ASE no portal também criamos um conjunto de tabelas de rota na sub-rede que é criada com o ASE.  As rotas simplesmente dizem enviar tráfego de saída diretamente à internet.  
Para criar manualmente as rotas mesmas, siga estes passos:

1. Aceda ao portal do Azure. Selecione **redes** > **tabelas de rotas**.

2. Crie uma nova tabela de rota na mesma região que a VNet.

3. A partir da dentro da sua tabela de rota IU, selecione **rotas** > **adicionar**.

4. Definir o **próximo salto tipo** para **Internet** e **prefixo de endereço** para **0.0.0.0/0**. Selecione **Guardar**.

    Verá algo como o seguinte:

    ![Rotas funcionais][6]

5. Depois de criar a nova tabela de rotas, vá para a sub-rede que contém o ASE. Selecione a tabela de rota da lista no portal. Depois de guardar a alteração, em seguida, deverá ver os NSGs e rotas indicadas com a sub-rede.

    ![Os NSGs e as rotas][7]

## <a name="service-endpoints"></a>Pontos Finais de Serviço ##

Os Pontos Finais de Serviço permitem restringir o acesso aos serviços multi-inquilino a um conjunto de sub-redes e redes virtuais do Azure. Pode ler mais sobre Pontos Finais de Serviço na documentação [Pontos Finais de Serviço de Rede Virtual][serviceendpoints]. 

Quando ativar Pontos Finais de Serviço num recurso, são criadas rotas com uma prioridade mais alta do que todas as outras rotas. Se utilizar Pontos Finais de Serviço com um ASE com túnel forçado, o tráfego de gestão do SQL do Azure e do Armazenamento do Azure não fica com um túnel forçado. 

Quando estão ativados Pontos Finais de Serviço numa sub-rede com uma instância do SQL do Azure, todas as instâncias do SQL do Azure ligadas a partir dessa sub-rede precisam de ter Pontos Finais de Serviço ativados. Se quiser aceder a várias instâncias de SQL do Azure a partir da mesma sub-rede, não pode ativar Pontos Finais de Serviço numa instância do SQL do Azure e não na outra. O Armazenamento do Azure não tem um comportamento semelhante ao SQL do Azure. Quando ativar Pontos Finais de Serviço com o Armazenamento do Azure, pode bloquear o acesso a esse recurso a partir da sua sub-rede, mas pode continuar a aceder a outras contas de Armazenamento do Azure, mesmo que não tenham Pontos Finais de Serviço ativados.  

![Pontos Finais de Serviço][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
