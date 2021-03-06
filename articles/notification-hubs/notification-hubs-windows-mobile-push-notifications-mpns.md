---
title: Introdução aos Hubs de Notificação do Azure para Aplicações Windows Phone | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Windows Phone 8 ou Windows Phone 8.1 Silverlight.
services: notification-hubs
documentationcenter: windows
keywords: notificação push, notificação push, push window phone
author: jwhitedev
manager: kpiteira
editor: ''
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/06/2018
ms.author: jawh
ms.openlocfilehash: 38d60001293a3bae6eb9f90179abb8af7815fbe7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>Introdução aos Hubs de Notificação do Azure para Aplicações Windows Phone
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
> [!NOTE]
> Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

Este tutorial mostra-lhe como utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se estiver a segmentar o Windows Phone 8.1 (não Silverlight), consulte a versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Neste tutorial, vai criar uma aplicação Windows Phone 8 em branco que recebe notificações push utilizando o Serviço de Notificações Push da Microsoft (MPNS). Quando tiver terminado, poderá utilizar o Notification Hub para difundir notificações push para todos os dispositivos a executar a sua aplicação.

> [!NOTE]
> O SDK dos Notification Hubs do Windows Phone não suporta a utilização do Serviço de Notificações Push do Windows (WNS) com aplicações do Windows Phone 8.1 Silverlight. Para utilizar o WNS (em vez do MPNS) com aplicações do Windows Phone 8.1 Silverlight, siga o [Tutorial Notification Hubs – Windows Phone Silverlight], que utiliza a APIs REST.
> 
> 

Este tutorial demonstra o cenário de difusão simples na utilização de Notification Hubs.

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, necessita do seguinte:

* [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações Windows Phone 8.

## <a name="create-your-notification-hub"></a>Criar o Notification Hub
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Em <b>Serviços de Notificação</b>, selecione <b>Windows Phone (MPNS)</b> e, em seguida, clique na caixa de verificação <b>Ativar push não autenticado</b>.</p>
</li>
</ol>

&emsp;&emsp;![Portal do Azure – Ativar notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

O seu hub já está criado e configurado para enviar notificações não autenticadas para Windows Phone.

> [!NOTE]
> Este tutorial utiliza MPNS no modo não autenticado. O modo MPNS não autenticado vem com restrições quanto às notificações que pode enviar para cada canal. Os Notification Hubs suportam o [Modo MPNS autenticado ](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) ao permitirem-lhe carregar o seu certificado.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub
1. No Visual Studio, crie uma nova aplicação do Windows Phone 8.
   
    ![Visual Studio – Novo Projeto – Aplicação do Windows Phone][13]
   
    No Visual Studio 2013 Update 2 ou posterior crie, em vez disso, uma aplicação Silverlight do Windows Phone.
   
    ![Visual Studio – Novo Projeto – Aplicação em Branco – Windows Phone Silverlight][11]
2. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet**.
   
    É apresentada a caixa de diálogo **Gerir Pacotes NuGet**.
3. Procure `WindowsAzure.Messaging.Managed`, clique em **Instalar** e aceite os termos de utilização.
   
    ![Visual Studio – Gestor de Pacotes NuGet][20]
   
    Esse procedimento transfere, instala e adiciona uma referência à biblioteca de Mensagens do Azure para Windows utilizando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">Pacote NuGet WindowsAzure.Messaging.Managed </a>.
4. Abra o ficheiro App,xaml.cs e adicione as seguintes instruções `using`:
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Adicione o seguinte código no topo do método **Application_Launching** em App.xaml.cs:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > O valor **MyPushChannel** é um índice que é utilizado para pesquisar um canal existente na coleção [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx). Se não existir, crie uma nova entrada com esse nome.
   > 
   > 
   
    Não se esqueça de inserir o nome do seu hub e a cadeia de ligação denominada **DefaultListenSharedAccessSignature** que obteve na secção anterior.
    Este código obtém o URI do canal para a aplicação a partir do MPNS e, em seguida, regista esse URI de canal no Notification Hub. Esta ação garante também que o URI do canal é registado no Notification Hub sempre que a aplicação for iniciada.
   
   > [!NOTE]
   > Este tutorial envia um alerta de notificação para o dispositivo. Se, em vez disso, enviar uma notificação de mosaico, tem de chamar o método o **BindToShellTile** no canal. Para suportar notificações de alerta e também de mosaico, chame **BindToShellTile** e **BindToShellToast**.
   > 
   > 
6. No Explorador de Soluções, expanda **Propriedades**, abra o `WMAppManifest.xml` ficheiro, clique no separador **Capacidades** e verifique se a capacidade **ID_CAP_PUSH_NOTIFICATION** está selecionada.
   
    ![Visual Studio – Capacidades das Aplicações Windows Phone][14]
   
    Esta ação garante que a aplicação pode receber notificações push. Sem ela, qualquer tentativa para enviar uma notificação push para a aplicação iria falhar.
7. Prima a tecla `F5` para executar a aplicação.
   
    É apresentada na aplicação uma mensagem de registo.
8. Feche a aplicação.  
   
   > [!NOTE]
   > Para receber um alerta de notificação push, a aplicação não pode estar a ser executada em primeiro plano.
   >

## <a name="next-steps"></a>Passos seguintes
Neste exemplo simples, difundiu notificações push para todos os seus dispositivos Windows Phone 8. 

Para visar utilizadores específicos, consulte o tutorial [Utilizar Notification Hubs para notificações push a utilizadores]. 

Se pretender segmentar os utilizadores por grupos de interesses, pode ler [Utilizar Notification Hubs para enviar notícias de última hora]. 

Saiba mais sobre como utilizar os Notification Hubs em [Documentação de Orientação dos Notification Hubs].

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Documentação de Orientação dos Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utilizar Notification Hubs para notificações push a utilizadores]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Tutorial Notification Hubs – Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

