---
title: Compreender o formato de mensagem do IoT Hub do Azure | Microsoft Docs
description: Guia para programadores - descibes o formato e o conteúdo esperado de mensagens do IoT Hub.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2018
ms.author: dobett
ms.openlocfilehash: 5979c0d79a0abd9897fd3970ccea1d9ba07f9dfd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler as mensagens de IoT Hub

Para suportar a interoperabilidade totalmente integrada nos protocolos, o IoT Hub define um formato de mensagem comum para todos os protocolos de orientado para o dispositivo. Este formato de mensagem que é utilizado para [dispositivo para nuvem] [ lnk-d2c] e [nuvem para o dispositivo] [ lnk-c2d] mensagens. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Um [mensagem do IoT Hub] [ lnk-messaging] é composta por:

* Um conjunto de *propriedades do sistema*. Propriedades que o IoT Hub interpreta ou define. Este conjunto é predeterminado.
* Um conjunto de *propriedades da aplicação*. Um dicionário de propriedades de cadeia que pode definir a aplicação e o acesso, sem necessidade de anular a serialização do corpo da mensagem. IoT Hub nunca modifica estas propriedades.
* Um corpo de binário opaco.

Valores e nomes de propriedade só podem conter carateres alfanuméricos ASCII, plus ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` quando tiver:  

* Envie mensagens dispositivo-nuvem utilizando o protocolo HTTPS.
* Envie mensagens da nuvem para o dispositivo.

Para obter mais informações sobre como codificar e descodificar as mensagens enviadas através de protocolos diferentes, consulte [SDKs IoT do Azure][lnk-sdks].

A tabela seguinte lista o conjunto de propriedades do sistema nas mensagens do IoT Hub.

| Propriedade | Descrição |
| --- | --- |
| MessageId |Um identificador de utilizador pode ser definida para a mensagem utilizado para padrões de pedido-resposta. : Uma maiúsculas e minúsculas cadeia de formato (Até 128 carateres de comprimento) de carateres alfanuméricos ASCII 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência |Um número de (exclusivo por dispositivo fila) atribuído pelo IoT Hub a cada mensagem da nuvem para o dispositivo. |
| Para |Um destino especificado na [nuvem para o dispositivo] [ lnk-c2d] mensagens. |
| ExpiryTimeUtc |Data e hora de expiração de mensagem. |
| EnqueuedTime |Data e hora de [nuvem para o dispositivo] [ lnk-c2d] foi recebida uma mensagem pelo IoT Hub. |
| CorrelationId |Uma propriedade de cadeia de uma mensagem de resposta que normalmente contém MessageId do pedido, em padrões de pedido-resposta. |
| ID de utilizador |Um ID utilizado para especificar a origem de mensagens em fila. Quando as mensagens são geradas pelo IoT Hub, está definida como `{iot hub name}`. |
| Confirmação |Um gerador de mensagem de comentários. Esta propriedade é utilizada em mensagens da nuvem para o dispositivo ao IoT Hub para gerar mensagens de comentários como resultado o consumo da mensagem de pedido pelo dispositivo. Os valores possíveis: **nenhum** (predefinição): nenhuma mensagem de comentários é gerada, **positivo**: receber uma mensagem de comentários, se a mensagem foi concluída, **negativo**: receber um mensagem de comentários, se a mensagem expirou (ou foi atingido o número máximo de entrega) sem que está a ser concluída por dispositivo, ou **completa**: positivos e negativos. Para obter mais informações, consulte [comentários da mensagem][lnk-feedback]. |
| ConnectionDeviceId |Um ID definido pelo IoT Hub nas mensagens do dispositivo para a nuvem. Contém o **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId |Um ID definido pelo IoT Hub nas mensagens do dispositivo para a nuvem. Contém o **generationId** (como por [propriedades de identidade de dispositivo][lnk-device-properties]) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod |Um método de autenticação definido pelo IoT Hub nas mensagens do dispositivo para a nuvem. Esta propriedade contém informações sobre o método de autenticação utilizado para autenticar o dispositivo que envia a mensagem. Para obter mais informações, consulte [dispositivo para a nuvem de spoofing de anti][lnk-antispoofing]. |
| CreationTimeUtc | Data e hora em que a mensagem foi criada num dispositivo. Um dispositivo tem de definir este valor explicitamente. |

## <a name="message-size"></a>Tamanho da mensagem

IoT Hub mede o tamanho da mensagem de uma forma de protocolo agnóstico, considerar apenas o payload real. O tamanho em bytes é calculado como a soma dos seguintes procedimentos:

* O tamanho do corpo em bytes.
* O tamanho de todos os valores das propriedades do sistema mensagem em bytes.
* O tamanho em bytes, de todos os valores e nomes de propriedade do utilizador.

Valores e nomes de propriedade são limitados aos carateres ASCII, pelo que o comprimento das cadeias é igual ao tamanho em bytes.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre limites de tamanho da mensagem no IoT Hub, consulte [quotas do IoT Hub e a limitação][lnk-quotas].

Para saber como criar e ler as mensagens várias linguagens de programação de IoT Hub, veja o [começar] [ lnk-get-started] tutoriais.

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
