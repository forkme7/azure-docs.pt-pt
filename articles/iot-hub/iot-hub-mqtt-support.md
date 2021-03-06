---
title: Compreender o suporte do Azure IoT Hub MQTT | Microsoft Docs
description: Guia para programadores - suporte para dispositivos estabelecer ligação a um ponto final orientado para dispositivos do IoT Hub utilizando o protocolo MQTT. Inclui informações sobre incorporado MQTT suporta os SDKs do dispositivo IoT do Azure.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 23f98d4e9f711496480d5e02b4d5b23cd8abab0c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2018
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicar com o seu IoT hub, utilizando o protocolo MQTT

IoT Hub permite que os dispositivos a comunicar com os pontos finais de dispositivos do IoT Hub com:

* [MQTT v 3.1.1] [ lnk-mqtt-org] na porta 8883
* MQTT v 3.1.1 através de WebSocket na porta 443.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Todas as comunicações de dispositivos com o IoT Hub devem ser protegida com TLS/SSL. Por conseguinte, o IoT Hub não suporta ligações não segura através da porta 1883.

## <a name="connecting-to-iot-hub"></a>A ligação ao IoT Hub

Um dispositivo pode utilizar o protocolo MQTT para ligar a um através do IoT hub:

* Bibliotecas no [SDKs IoT do Azure][lnk-device-sdks].
* Ou o protocolo MQTT diretamente.

## <a name="using-the-device-sdks"></a>Utilizar os SDKs do dispositivo

[SDKs do dispositivo] [ lnk-device-sdks] que suportam o protocolo MQTT estão disponíveis para Java, Node.js, C, c# e Python. Os SDKs do dispositivo utilizar a cadeia de ligação do IoT Hub padrão para estabelecer uma ligação a um IoT hub. Para utilizar o protocolo MQTT, o parâmetro de protocolo do cliente tem de ser definido **MQTT**. Por predefinição, o dispositivo SDKs ligam a um IoT Hub com a **CleanSession** sinalizador definido como **0** e utilizar **QoS 1** para a troca de mensagem com o IoT hub.

Quando um dispositivo está ligado a um IoT hub, o dispositivo SDKs fornecem métodos que ative o dispositivo trocar mensagens com um IoT hub.

A tabela seguinte contém hiperligações para os exemplos de código para cada idioma suportado e especifica o parâmetro a utilizar para estabelecer uma ligação ao IoT Hub, utilizando o protocolo MQTT.

| Idioma | Parâmetro de protocolo |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrar uma aplicação de dispositivo do AMQP para MQTT

Se estiver a utilizar o [SDKs do dispositivo][lnk-device-sdks], mudar de utilizar AMQP para MQTT exige a alteração o parâmetro de protocolo na inicialização do cliente, conforme indicado anteriormente.

Ao fazê-lo, certifique-se verificar os seguintes itens:

* AMQP devolve erros para várias condições, enquanto MQTT termina a ligação. Como resultado, a excepção a processar a lógica exijam algumas alterações.
* MQTT não suporta o *rejeitar* operações quando receber [mensagens da nuvem para dispositivo][lnk-messaging]. Se a aplicação de back-end tem de receber uma resposta da aplicação de dispositivo, considere utilizar [direcionar métodos][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Utilizando o protocolo MQTT diretamente

Se um dispositivo não é possível utilizar os SDKs do dispositivo, ainda pode ligar para os pontos finais de dispositivo público utilizando o protocolo MQTT na porta 8883. No **CONNECT** pacote o dispositivo deve utilizar os seguintes valores:

* Para o **ClientId** campo, utilize o **deviceId**.

* Para o **Username** campo, utilize `{iothubhostname}/{device_id}/api-version=2016-11-14`, onde `{iothubhostname}` é o CName completo do IoT hub.

    Por exemplo, se o nome do seu IoT hub **contoso.azure devices.net** e se o nome do seu dispositivo for **MyDevice01**, o completo **Username** campo deve conter:

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Para o **palavra-passe** campo, utilize um token SAS. O formato do SAS token é igual aos protocolos de HTTPS e o AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se utilizar a autenticação de certificado x. 509, palavras-passe de token do SAS não são necessárias. Para obter mais informações, consulte [como configurar a segurança de x. 509 no seu IoT Hub do Azure][lnk-x509]

  Para obter mais informações sobre como gerar SAS tokens, consulte a secção de dispositivo do [tokens de segurança a utilizar o IoT Hub][lnk-sas-tokens].

  Quando o testar, também pode utilizar o [Explorador de dispositivo] [ lnk-device-explorer] ferramenta rapidamente gerar um token SAS, que pode copiar e colar no seu próprio código:

  1. Vá para o **gestão** separador **Explorador de dispositivo**.
  2. Clique em **SAS Token** (principais direita).
  3. No **SASTokenForm**, selecione o seu dispositivo no **DeviceID** pendente. Definir o **TTL**.
  4. Clique em **gerar** para criar o token.

     O token SAS que é gerado tem a estrutura seguinte:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     A parte do token para utilizar como o **palavra-passe** campo para ligar através de MQTT é:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para MQTT ligar e desligar pacotes, o IoT Hub emite um evento no **operações de monitorização** canal. Este evento tem informações adicionais que podem ajudar a resolver problemas de conectividade.

A aplicação de dispositivo pode especificar um **será** mensagem no **CONNECT** pacotes. Deve utilizar a aplicação de dispositivo `devices/{device_id}/messages/events/{property_bag}` ou `devices/{device_id}/messages/events/{property_bag}` como o **será** para definir o nome do tópico **será** mensagens a reencaminhar como uma mensagem de telemetria. Neste caso, se a ligação de rede é fechada, mas a **desligar** pacote não foi anteriormente recebido do dispositivo, em seguida, envia o IoT Hub a **será** mensagem fornecida a **CONNECT** pacote para o canal de telemetria. O canal de telemetria pode ser ambos os **eventos** endpoint ou um ponto de final personalizado definidos pelo IoT Hub encaminhamento. A mensagem tem o **iothub MessageType** propriedade com um valor de **será** atribuída.

### <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para utilizar o MQTT protocolo diretamente, o cliente *tem* ligar através de TLS/SSL. As tentativas para ignorar este passo falharam com erros de ligação.

Para estabelecer uma ligação de TLS, poderá ter de transferir e referenciam o certificado de raiz Baltimore DigiCert. Este certificado é o Azure utiliza para proteger a ligação. Pode encontrar este certificado no [Azure-iot-sdk-c] [ lnk-sdk-c-certs] repositório. Podem encontrar mais informações sobre estes certificados no [site da Digicert][lnk-digicert-root-certs].

Um exemplo de como implementar isto utilizando a versão do Python do [biblioteca Paho MQTT] [ lnk-paho] pelo Foundation Eclipse aspeto que poderá ter o seguinte.

Em primeiro lugar, instale a biblioteca de Paho do seu ambiente de linha de comandos:

```cmd/sh
pip install paho-mqtt
```

Em seguida, implemente o cliente num script de Python. Substitua os marcadores de posição da seguinte forma:

* `<local path to digicert.cer>` é o caminho para um ficheiro local que contém o certificado de raiz de Baltimore DigiCert. Pode criar este ficheiro ao copiar as informações do certificado de [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no SDK do IoT do Azure para C. incluir as linhas `-----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----`, remova o `"` marcas de escala no início e fim de cada linha, e Remova o `\r\n` carateres no final de cada linha.
* `<device id from device registry>` é o ID de um dispositivo adicionado ao seu IoT hub.
* `<generated SAS token>` é um token SAS para o dispositivo criado como descrito anteriormente neste artigo.
* `<iot hub name>` o nome do seu IoT hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Enviar mensagens do dispositivo-nuvem

Depois de efetuar uma ligação com êxito, um dispositivo pode enviar mensagens para o IoT Hub com `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **o nome do tópico**. O `{property_bag}` elemento permite que o dispositivo enviar mensagens com propriedades adicionais num formato com codificação url. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Isto `{property_bag}` elemento utiliza a mesma codificação como para cadeias de consulta no protocolo HTTPS.

Segue-se uma lista dos comportamentos de implementação específicos do IoT Hub:

* IoT Hub não suporta mensagens QoS 2. Se uma aplicação de dispositivo publica uma mensagem com **QoS 2**, o IoT Hub fecha a ligação de rede.
* IoT Hub não persiste manter mensagens. Se um dispositivo envia uma mensagem com o **manter** sinalizador definido como 1, o IoT Hub adiciona o **x-optar ativamente por participar-manter** propriedade de aplicação para a mensagem. Neste caso, em vez da mensagem de manter a persistência, o IoT Hub passa-a para a aplicação de back-end.
* IoT Hub apenas suporta uma ligação de MQTT Active Directory por dispositivo. Qualquer nova ligação de MQTT em nome o mesmo ID de dispositivo faz com que o IoT Hub remover a ligação existente.

Para obter mais informações, consulte [mensagens guia para programadores][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Receber mensagens da nuvem para dispositivo

Para receber mensagens a partir do IoT Hub, um dispositivo deve subscrever com `devices/{device_id}/messages/devicebound/#` como um **tópico filtro**. O caráter universal múltiplos nível `#` no filtro tópico são utilizadas apenas para permitir que o dispositivo receber propriedades adicionais no nome do tópico. IoT Hub não permite a utilização do `#` ou `?` carateres universais para filtragem de subtópicos. Uma vez que o IoT Hub não é um mediador de mensagens para fins gerais pub-sub, só suporta os nomes de tópico documentado e filtros de tópico.

O dispositivo não receber as mensagens do IoT Hub, até que subscreveu com êxito para o ponto final específico do dispositivo, representado pelo `devices/{device_id}/messages/devicebound/#` filtro de tópico. Depois de uma subscrição tiver sido estabelecida, o dispositivo receber mensagens da nuvem para o dispositivo que foram enviadas para o mesmo após a hora da subscrição. Se o dispositivo estabelece ligação com **CleanSession** sinalizador definido como **0**, a subscrição é persistente em sessões diferentes. Neste caso, da próxima vez que o dispositivo estabelece ligação com **CleanSession 0** recebe mensagens pendentes enviadas enquanto desligado. Se o dispositivo utiliza **CleanSession** sinalizador definido como **1** , não receber as mensagens do IoT Hub até que cinge-se ao respetivo ponto final do dispositivo.

IoT Hub disponibiliza as mensagens com a **o nome do tópico** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` quando existem propriedades da mensagem. `{property_bag}` contém com codificação url pares chave-valor das propriedades da mensagem. Apenas as propriedades da aplicação e as propriedades do sistema pode ser definida utilizador (tais como **messageId** ou **correlationId**) estão incluídas na matriz de propriedades. Os nomes de propriedade de sistema têm o prefixo **$**, propriedades da aplicação utilizam o nome da propriedade original com nenhuma prefixo.

Quando uma aplicação de dispositivo subscreve um tópico com **QoS 2**, o IoT Hub concede máximo QoS nível 1 a **SUBACK** pacotes. Depois disso, o IoT Hub entrega mensagens ao dispositivo utilizar o QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Obter as propriedades de um dispositivo duplo

Em primeiro lugar, um dispositivo subscreve `$iothub/twin/res/#`, receber respostas a operação. Em seguida, envia uma mensagem vazia para tópico `$iothub/twin/GET/?$rid={request id}`, com um valor preenchido para **ID do pedido**. O serviço, em seguida, envia uma mensagem de resposta que contém os dados do dispositivo duplo no tópico `$iothub/twin/res/{status}/?$rid={request id}`, com o mesmo **ID do pedido** como o pedido.

ID do pedido pode ser qualquer valor válido para um valor de propriedade da mensagem, como por [mensagens guia para programadores do IoT Hub][lnk-messaging], e o estado é validado como um número inteiro.

O corpo da resposta contém a secção de propriedades do dispositivo duplo. O fragmento seguinte mostra o corpo da entrada de registo de identidade limitada para o membro "Propriedades", por exemplo:

```json
{
    "properties": {
        "desired": {
            "telemetrySendFrequency": "5m",
            "$version": 12
        },
        "reported": {
            "telemetrySendFrequency": "5m",
            "batteryLevel": 55,
            "$version": 123
        }
    }
}
```

Os códigos de estado possíveis são:

|Estado | Descrição |
| ----- | ----------- |
| 200 | Êxito |
| 429 | Demasiados pedidos (limitados), como por [limitação do IoT Hub][lnk-quotas] |
| 5** | Erros de servidor |

Para obter mais informações, consulte [dispositivos duplos guia para programadores do][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Atualizar as propriedades do dispositivo duplo comunicado

A sequência seguinte descreve como um dispositivo atualiza as propriedades comunicadas no dispositivo duplo no IoT Hub:

1. Um dispositivo tem primeiro de subscrever o `$iothub/twin/res/#` tópico receber respostas a operação a partir do IoT Hub.

1. Um dispositivo envia uma mensagem que contém a dispositivo duplo atualização o `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tópico. Esta mensagem inclui uma **ID do pedido** valor.

1. O serviço, em seguida, envia uma mensagem de resposta que contém o valor ETag novo para a coleção de propriedades comunicado no tópico `$iothub/twin/res/{status}/?$rid={request id}`. Esta mensagem de resposta utiliza o mesmo **ID do pedido** como o pedido.

O corpo da mensagem de pedido contém um documento JSON, que contém novos valores de propriedades comunicados. Cada membro no documento JSON atualizações ou adicionar um membro correspondente no documento do dispositivo duplo. Um membro definido como `null`, elimina o membro do objeto contentor. Por exemplo:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Os códigos de estado possíveis são:

|Estado | Descrição |
| ----- | ----------- |
| 200 | Êxito |
| 400 | Pedido incorreto. JSON com formato incorreto |
| 429 | Demasiados pedidos (limitados), como por [limitação do IoT Hub][lnk-quotas] |
| 5** | Erros de servidor |

Para obter mais informações, consulte [dispositivos duplos guia para programadores do][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Receber notificações de atualização de propriedades pretendida

Quando um dispositivo estiver ligado, o IoT Hub envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}`, que contêm o conteúdo da atualização efetuada pelo solução de back-end. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Para atualizações da propriedade, `null` valores significa que o membro de objeto JSON está a ser eliminado.

> [!IMPORTANT]
> IoT Hub gera notificações de alteração apenas quando os dispositivos estão ligados. Certifique-se de que implementa o [fluxo de restabelecimento de ligação do dispositivo] [ lnk-devguide-twin-reconnection] para manter as propriedades pretendidas sincronizadas entre o IoT Hub e a aplicação de dispositivo.

Para obter mais informações, consulte [dispositivos duplos guia para programadores do][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Responder a um método direto

Em primeiro lugar, um dispositivo tem de subscrever `$iothub/methods/POST/#`. IoT Hub envia pedidos de método para o tópico `$iothub/methods/POST/{method name}/?$rid={request id}`, com um JSON válido ou uma mensagem vazia.

Para responder, o dispositivo envia uma mensagem com um JSON válido ou a mensagem vazia para o tópico `$iothub/methods/res/{status}/?$rid={request id}`. Nesta mensagem, o **ID do pedido** tem corresponde da mensagem de pedido e **estado** tem de ser um número inteiro.

Para obter mais informações, consulte [direcionar método guia para programadores do][lnk-methods].

### <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se precisar de personalizar o comportamento de protocolo MQTT no lado de nuvem, deve rever o [gateway de protocolo do Azure IoT][lnk-azure-protocol-gateway]. Este software permite-lhe implementar um gateway de protocolo personalizado de elevado desempenho que interfaces diretamente com o IoT Hub. O gateway de protocolo do Azure IoT permite-lhe personalizar o protocolo de dispositivo para acomodar as implementações de MQTT brownfield ou outros protocolos personalizados. Esta abordagem requer, no entanto, que executa e operar um gateway de protocolo personalizado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o protocolo MQTT, consulte o [documentação MQTT][lnk-mqtt-docs].

Para saber mais sobre o planeamento da implementação do IoT Hub, consulte:

* [Certificado do Azure para o catálogo de dispositivos de IoT][lnk-devices]
* [Suporte de protocolos adicionais][lnk-protocols]
* [Comparar com os Event Hubs][lnk-compare]
* [Dimensionamento, HA e DR][lnk-scaling]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
