<properties
 pageTitle="Introdução ao Pi framboesa 3 | Microsoft Azure"
 description="Introdução ao framboesa Pi 3, crie seu Hub de IoT do Azure e conectar seu Pi hub IoT"
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

# <a name="get-started-with-raspberry-pi-3"></a>Introdução ao Pi framboesa 3

Neste tutorial, você começar aprendendo Noções básicas de como trabalhar com framboesa Pi 3 que Raspbian em execução. Você, em seguida, saiba como conectar perfeitamente seus dispositivos na nuvem com [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Para obter exemplos de Windows 10 IoT Core, visite [windowsondevices.com](http://www.windowsondevices.com/).

## <a name="lesson-1-configure-your-device"></a>Lição 1: Configurar seu dispositivo

![Diagrama de E2E lição 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

Nesta lição, você configurar seu dispositivo de framboesa Pi 3 com um sistema operacional, configure o seu ambiente de desenvolvimento e implanta um aplicativo para o Pi.

### <a name="configure-your-device"></a>Configurar seu dispositivo

Configurar seu framboesa Pi 3 para uso pela primeira vez e instale o sistema operacional Raspbian, um sistema operacional gratuito que é otimizado para o hardware framboesa Pi.

*Tempo previsto para concluir: 30 minutos* 

[Vá para 'Configurar seu dispositivo'](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)

### <a name="get-the-tools"></a>Obtenha as ferramentas
Baixe as ferramentas e software criem e implantem seu primeiro aplicativo para o 3 de Pi framboesa.

*Tempo previsto para concluir: 20 minutos* 

[Vá para 'Obter as ferramentas'](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

### <a name="create-and-deploy-the-blink-application"></a>Criar e implantar o aplicativo piscando

Clonar a amostra Node do aplicativo Github e gulp para implantar esse aplicativo para seu quadro de framboesa Pi 3. Este aplicativo de amostra pisca o LED conectado ao quadro de cada dois segundos.

*Tempo previsto para concluir: 5 minutos* 

[Vá para ' criar e implantar o aplicativo piscando '](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)

## <a name="lesson-2-create-your-iot-hub"></a>Lição 2: Criar seu hub IoT

![Diagrama de E2E lição 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

Nesta lição, você cria sua conta do Azure gratuita, provisionar seu hub IoT do Azure e cria seu primeiro dispositivo no hub do Azure IoT.

Conclua a lição 1 antes de iniciar esta lição.

### <a name="get-the-azure-tools"></a>Obtenha as ferramentas Azure

Instalar o Azure Interface de linha (comando Azure).

*Tempo previsto para concluir: 10 minutos* 

[Vá para 'Ferramentas do Azure obter'](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)

### <a name="create-your-iot-hub-and-register-your-raspberry-pi-3"></a>Criar seu hub IoT e registrar seu framboesa Pi 3

Criar seu grupo de recursos, provisionar seu hub IoT Azure primeiro e adicionar seu dispositivo primeiro ao Azure IoT Hub usando CLI do Azure. 

*Tempo previsto para concluir: 10 minutos* 

[Vá para 'Criar seu hub IoT e registrar seu framboesa Pi 3'](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


## <a name="lesson-3-send-device-to-cloud-messages"></a>Lição 3: Enviar mensagens de dispositivo à nuvem

![Diagrama de E2E lição 3](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

Nesta lição, você envia mensagens de seu Pi para seu hub IoT. Você também pode criar um aplicativo de função Azure que seleciona as mensagens de entrada do seu hub IoT e grava-los ao armazenamento de tabela do Microsoft Azure.

Conclua lições 1 e 2 antes de iniciar esta lição.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de função Azure e a conta de armazenamento do Azure

Use um modelo do Gerenciador de recursos do Azure para criar um aplicativo de função Azure e uma conta de armazenamento do Azure.

*Tempo previsto para concluir: 10 minutos* 

[Vá para 'Criar um aplicativo de função Azure e a conta de armazenamento do Azure'](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)

### <a name="run-sample-application-to-send-device-to-cloud-messages"></a>Executar o aplicativo de exemplo para enviar mensagens de dispositivo à nuvem

Implante e execute um aplicativo de exemplo para o seu dispositivo de framboesa Pi 3 que envia mensagens para IoT hub.

*Tempo previsto para concluir: 10 minutos* 

[Vá para 'Executar o aplicativo de exemplo para enviar mensagens de dispositivo à nuvem'](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

### <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens mantidas no armazenamento do Azure
Monitore as mensagens do dispositivo na nuvem, como eles são gravados ao seu armazenamento do Azure.

*Tempo previsto para concluir: 5 minutos* 

[Vá para 'ler mensagens mantidas no armazenamento do Azure'](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)


## <a name="lesson-4-send-cloud-to-device-messages"></a>Lição 4: Enviar mensagens de nuvem para dispositivo

![Diagrama de E2E lição 4](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Esta lição demonstrações como enviar mensagens de seu hub IoT do Azure para seu framboesa Pi 3. As mensagens de controlam ativar e desativar o comportamento do LED que está conectado à sua Pi. Um exemplo de aplicativo está preparado para alcançar esta tarefa.

Conclua lições 1, 2 e 3 antes de iniciar esta lição.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Execute o aplicativo de amostra para receber mensagens de nuvem para dispositivo

O aplicativo de exemplo na lição 4 é executado em seu Pi e monitora as mensagens de entrada do seu hub IoT. Uma nova tarefa de vez envia mensagens para sua Pi do seu hub IoT piscando o LED.

*Tempo previsto para concluir: 10 minutos* 

[Vá para 'Executar o aplicativo de exemplo para receber mensagens de nuvem para dispositivo'](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Seção opcional: alterar ativar e desativar o comportamento do LED

Personalize as mensagens para alterar o LED e desativar o comportamento.

*Tempo previsto para concluir: 10 minutos* 

[Vá para ' seção opcional: alterar ativar e desativar o comportamento do LED'](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


## <a name="troubleshooting"></a>Solução de problemas

Se você atender a quaisquer problemas durante as lições, você pode buscar soluções desta página.

[Vá para 'Solução de problemas'](iot-hub-raspberry-pi-kit-node-troubleshooting.md)
