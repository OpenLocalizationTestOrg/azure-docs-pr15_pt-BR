<properties
 pageTitle="Executar o aplicativo de exemplo para enviar mensagens de dispositivo à nuvem | Microsoft Azure"
 description="Implantar e executar um aplicativo de amostra para seu framboesa Pi 3 que envia mensagens para IoT hub e pisca o LED."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="32-run-sample-application-to-send-device-to-cloud-messages"></a>3,2 execute o aplicativo de exemplo para enviar mensagens de dispositivo à nuvem

## <a name="321-what-you-will-do"></a>3.2.1 o que você fará

Implante e execute um aplicativo de exemplo em seu framboesa Pi 3 que envia mensagens para o seu hub IoT. Se você atender a todos os problemas, busca soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="322-what-you-will-learn"></a>3.2.2 o que você aprenderá

- Como usar a ferramenta de vez para implantar e executar o aplicativo de Node de amostra no seu Pi.

## <a name="323-what-you-need"></a>3.2.3 o que é necessário

- Você deve ter concluído com êxito a seção anterior nesta lição: [criar um aplicativo de função Azure e uma conta de armazenamento do Azure para processar e armazenar mensagens de hub IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

## <a name="324-get-your-iot-hub-and-device-connection-strings"></a>3.2.4 obter suas sequências de conexão IoT hub e dispositivo

A cadeia de conexão do dispositivo é usada para conectar o Pi ao seu hub IoT. O hub de IoT se conectar a cadeia de caracteres é usado para conectar o seu hub IoT para a identidade do dispositivo que representa sua Pi no hub do IoT.

- Obter a cadeia de conexão do hub IoT executando o seguinte comando CLI Azure:

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}`é o nome que você especificou na lição 2. Use `iot-sample` como o valor de `{resource group name}` se você não alterar o valor na lição 2.

- Obter a cadeia de conexão do dispositivo executando o seguinte comando:

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}`leva o mesmo valor usada com o comando anterior. Use `iot-sample` como o valor de `{resource group name}` e usar `myraspberrypi` como o valor de `{device id}` se você não alterar o valor na lição 2.

## <a name="325-configure-the-device-connection"></a>3.2.5 configurar a conexão do dispositivo

1. Inicialize o arquivo de configuração executando os seguintes comandos:

    ```bash
    npm install
    gulp init
    ```

2. Abra o arquivo de configuração de dispositivo `config-raspberrypi.json` no Visual Studio Code executando o seguinte comando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

    ![config.JSON](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)

3. Verifique as seguintes substituições no `config-raspberrypi.json` arquivo:

  - Substitua **[nome do host do dispositivo ou endereço IP]** com o endereço IP do dispositivo ou hostname obtido de `device-discovery-cli` ou com o valor herdada do que você configurou na lição 1.
  - Substitua **[cadeia de conexão do dispositivo IoT]** com o `device connection string` você obtido.
  - Substitua **[cadeia de conexão do hub IoT]** com o `iot hub connection string` você obtido.

Atualizar o `config-raspberrypi.json` arquivo para que você pode implantar o aplicativo de amostra do seu computador.

## <a name="326-deploy-and-run-the-sample-application"></a>3.2.6 implantar e executar o aplicativo de amostra

Implante e execute o aplicativo de amostra em seu Pi executando o seguinte comando:

```bash
gulp
```

> [AZURE.NOTE] A tarefa de vez padrão é executada `install-tools`, `deploy`, e `run` tarefas sequencialmente. Na [Lição 1](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md), você executou estas tarefas após o outro separadamente.

## <a name="327-verify-the-sample-application-works"></a>3.2.7 Verifique se o aplicativo de exemplo funcione

Você deve ver o LED que está conectado à sua Pi piscando cada dois segundos. Sempre que o LED pisca, o aplicativo de amostra envia uma mensagem para seu hub IoT e verifica que a mensagem foi enviada com êxito para o seu hub IoT. Além disso, para cada mensagem recebida pelo hub IoT, a mensagem é impresso na janela do console. O aplicativo de amostra termina automaticamente após o envio de mensagens de 20.

![](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="328-summary"></a>3.2.8 resumo de

Você já implantado e execute o aplicativo de amostra piscando novo em seu Pi para enviar mensagens de dispositivo à nuvem para seu hub IoT. Você pode mover para a próxima seção para monitorar suas mensagens como eles são gravados na conta de armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

[3.3 mensagens lidas persistentes no armazenamento do Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)