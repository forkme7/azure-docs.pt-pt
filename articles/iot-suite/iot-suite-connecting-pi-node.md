---
title: Aprovisionar Raspberry Pi para monitorização remota no Node.js - Azure | Microsoft Docs
description: Descreve como ligar um dispositivo Raspberry Pi pré-configuradas do Azure IoT Suite solução de monitorização remota utilizando uma aplicação de escrita no Node.js.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 7f489a6b26edb9a58b21d318785d3804197b33cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Ligar o seu dispositivo Raspberry Pi à solução pré-configurada monitorização remota (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como ligar um dispositivo físico a solução pré-configurada de monitorização remota. Neste tutorial, utilize Node.js, que é uma boa opção para ambientes com restrições de recurso mínima.

### <a name="required-hardware"></a>Hardware necessário

Um computador de secretária que lhe permite ligar remotamente à linha de comandos no Raspberry Pi.

[Microsoft IoT Starter Kit para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial utiliza os seguintes itens do kit:

- Raspberry Pi 3
- Cartão MicroSD (com NOOBS)
- Um cabo USB Mini
- Um cabo de Ethernet

### <a name="required-desktop-software"></a>Software de ambiente de trabalho necessárias

Terá de cliente SSH no seu computador de secretária que lhe permite aceder remotamente a linha de comandos no Raspberry Pi.

- Windows não inclui um cliente SSH. Recomendamos que utilize [PuTTY](http://www.putty.org/).
- A maioria das distribuições de Linux e Mac OS incluem o utilitário da linha de comandos do SSH. Para obter mais informações, consulte [SSH utilizando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi necessárias

Se não tiver o feito, instale o Node.js versão 4.0.0 ou posterior na sua Raspberry Pi. Os passos seguintes mostram como instalar o Node.js v6 no seu Raspberry Pi:

1. Ligar à sua utilização Raspberry Pi `ssh`. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site Raspberry Pi](https://www.raspberrypi.org/).

1. Utilize o seguinte comando para atualizar o seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Utilize os seguintes comandos para remover o seu Raspberry Pi qualquer instalação existente do Node.js:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Utilize o seguinte comando para transferir e instalar o Node.js v6 no seu Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs -y
    ```

1. Utilize o seguinte comando para verificar que instalou o Node.js v6.11.4 com êxito:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Criar uma solução de Node.js

Conclua os seguintes passos, utilizando o `ssh` ligação ao seu Raspberry Pi:

1. Crie uma pasta denominada `remotemonitoring` na pasta raiz em Raspberry Pi. Navegue para esta pasta na sua linha de comandos:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Para transferir e instalar os pacotes que precisa para concluir a aplicação de exemplo, execute os seguintes comandos:

    ```sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. No `remotemonitoring` pasta, crie um ficheiro chamado **remote_monitoring.js**. Abra este ficheiro num editor de texto. Em Raspberry Pi, pode utilizar o `nano` ou `vi` editores de texto.

1. No **remote_monitoring.js** ficheiro, adicione o seguinte `require` instruções:

    ```nodejs
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Adicione as seguintes declarações de variáveis a seguir às instruções `require`. Substitua o valor do marcador de posição `{device connection string}` com o valor indicado para o dispositivo que aprovisionou na solução de monitorização remota:

    ```nodejs
    var connectionString = '{device connection string}';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Para definir alguns dados de telemetria base, adicione as seguintes variáveis:

    ```nodejs
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Para definir alguns valores de propriedade, adicione as seguintes variáveis:

    ```nodejs
    var temperatureSchema = 'chiller-temperature;v1';
    var humiditySchema = 'chiller-humidity;v1';
    var pressureSchema = 'chiller-pressure;v1';
    var interval = "00:00:05";
    var deviceType = "Chiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Adicione a seguinte variável para definir as propriedades comunicadas para enviar para a solução. Estas propriedades incluem os metadados para descrever os métodos e utiliza a telemetria do dispositivo:

    ```nodejs
    var reportedProperties = {
      "Protocol": "MQTT",
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        "TemperatureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
          "MessageSchema": {
            "Name": temperatureSchema,
            "Format": "JSON",
            "Fields": {
              "temperature": "Double",
              "temperature_unit": "Text"
            }
          }
        },
        "HumiditySchema": {
          "Interval": interval,
          "MessageTemplate": "{\"humidity\":${humidity},\"humidity_unit\":\"${humidity_unit}\"}",
          "MessageSchema": {
            "Name": humiditySchema,
            "Format": "JSON",
            "Fields": {
              "humidity": "Double",
              "humidity_unit": "Text"
            }
          }
        },
        "PressureSchema": {
          "Interval": interval,
          "MessageTemplate": "{\"pressure\":${pressure},\"pressure_unit\":\"${pressure_unit}\"}",
          "MessageSchema": {
            "Name": pressureSchema,
            "Format": "JSON",
            "Fields": {
              "pressure": "Double",
              "pressure_unit": "Text"
            }
          }
        }
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Para imprimir os resultados da operação, adicione a seguinte função do programa auxiliar:

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adicione a seguinte função de programa auxiliar para utilizar a utilize uma ordem aleatória os valores de telemetria:

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Adicione a seguinte função genérica para processar as chamadas de método direta da solução. A função apresenta informações sobre o método direto que foi invocado, mas este exemplo não modificar o dispositivo de qualquer forma. A solução utiliza métodos diretos para agir em dispositivos:

    ```nodejs
    function onDirectMethod(request, response) {
      // Implement logic asynchronously here.
      console.log('Simulated ' + request.methodName);

      // Complete the response
      response.send(200, request.methodName + ' was called on the device', function (err) {
        if (err) console.error('Error sending method response :\n' + err.toString());
        else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
      });
    }
    ```

1. Adicione a seguinte função para processar o **FirmwareUpdate** direcionar chamadas de método da solução. A função verifica os parâmetros transmitidos no payload do método direto e, em seguida, executa no modo assíncrono uma simulação de atualização de firmware:

    ```node.js
    function onFirmwareUpdate(request, response) {
      // Get the requested firmware version from the JSON request body
      var firmwareVersion = request.payload.Firmware;
      var firmwareUri = request.payload.FirmwareUri;
      
      // Ensure we got a firmware values
      if (!firmwareVersion || !firmwareUri) {
        response.send(400, 'Missing firmware value', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
        });
      } else {
        // Respond the cloud app for the device method
        response.send(200, 'Firmware update started.', function(err) {
          if (err) console.error('Error sending method response :\n' + err.toString());
          else {
            console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

            // Run the simulated firmware update flow
            runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
          }
        });
      }
    }
    ```

1. Adicione a seguinte função para simular um fluxo de atualização de firmware de longa execução que comunica o progresso voltar para a solução:

    ```node.js
    // Simulated firmwareUpdate flow
    function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
      console.log('Simulating firmware update flow...');
      console.log('> Firmware version passed: ' + firmwareVersion);
      console.log('> Firmware URI passed: ' + firmwareUri);
      async.waterfall([
        function (callback) {
          console.log("Image downloading from " + firmwareUri);
          var patch = {
            FirmwareUpdateStatus: 'Downloading image..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Downloaded, applying firmware " + firmwareVersion);
          deviceOnline = false;
          var patch = {
            FirmwareUpdateStatus: 'Applying firmware..',
            Online: false
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(8000, callback);
        },
        function (callback) {
          console.log("Rebooting");
          var patch = {
            FirmwareUpdateStatus: 'Rebooting..'
          };
          reportUpdateThroughTwin(patch, callback);
          sleep(10000, callback);
        },
        function (callback) {
          console.log("Firmware updated to " + firmwareVersion);
          deviceOnline = true;
          var patch = {
            FirmwareUpdateStatus: 'Firmware updated',
            Online: true,
            Firmware: firmwareVersion
          };
          reportUpdateThroughTwin(patch, callback);
          callback(null);
        }
      ], function(err) {
        if (err) {
          console.error('Error in simulated firmware update flow: ' + err.message);
        } else {
          console.log("Completed simulated firmware update flow");
        }
      });

      // Helper function to update the twin reported properties.
      function reportUpdateThroughTwin(patch, callback) {
        console.log("Sending...");
        console.log(JSON.stringify(patch, null, 2));
        client.getTwin(function(err, twin) {
          if (!err) {
            twin.properties.reported.update(patch, function(err) {
              if (err) callback(err);
            });      
          } else {
            if (err) callback(err);
          }
        });
      }

      function sleep(milliseconds, callback) {
        console.log("Simulate a delay (milleseconds): " + milliseconds);
        setTimeout(function () {
          callback(null);
        }, milliseconds);
      }
    }
    ```

1. Adicione o seguinte código para enviar dados de telemetria para a solução. A aplicação cliente Adiciona propriedades para a mensagem para identificar o esquema de mensagem:

    ```node.js
    function sendTelemetry(data, schema) {
      if (deviceOnline) {
        var d = new Date();
        var payload = JSON.stringify(data);
        var message = new Message(payload);
        message.properties.add('$$CreationTimeUtc', d.toISOString());
        message.properties.add('$$MessageSchema', schema);
        message.properties.add('$$ContentType', 'JSON');

        console.log('Sending device message data:\n' + payload);
        client.sendEvent(message, printErrorFor('send event'));
      } else {
        console.log('Offline, not sending telemetry');
      }
    }
    ```

1. Adicione o seguinte código para criar uma instância de cliente:

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Adicione o seguinte código para:

    * Abra a ligação.
    * Configure um processador para propriedades pretendidos.
    * Envie comunicadas propriedades.
    * Registe processadores para os métodos diretos. Este exemplo utiliza um processador separado para o método de direta de atualização de firmware.
    * Começar a enviar telemetria.

    ```nodejs
    client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendTemperatureInterval = setInterval(function () {
          temperature += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit
          };
          sendTelemetry(data, temperatureSchema)
        }, 5000);

        var sendHumidityInterval = setInterval(function () {
          humidity += generateRandomIncrement();
          var data = {
            'humidity': humidity,
            'humidity_unit': humidityUnit
          };
          sendTelemetry(data, humiditySchema)
        }, 5000);

        var sendPressureInterval = setInterval(function () {
          pressure += generateRandomIncrement();
          var data = {
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, pressureSchema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
    });
    ```

1. Guardar as alterações para o **remote_monitoring.js** ficheiro.

1. Para iniciar a aplicação de exemplo, execute o seguinte comando na sua linha de comandos no Raspberry Pi:

    ```sh
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
