<properties
 pageTitle="Obter ferramentas Azure (Windows 7 +) | Microsoft Azure"
 description="Instale o Python e Interface de linha do Azure (Azure comando) no Windows 7 e versões posteriores."
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

# <a name="21-get-azure-tools-windows-7-"></a>2.1 obter ferramentas Azure (Windows 7 +)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 o que você fará

Instalar Python e o Azure de linha de comando Interface (CLI Azure). Se você atender a todos os problemas, busca soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 o que você aprenderá

- Como instalar Python.
- Como instalar o Azure CLI.

## <a name="213-what-you-need"></a>2.1.3 o que é necessário

- Um computador Windows com conexão de Internet
- Uma assinatura ativa do Azure. Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

## <a name="214-install-python"></a>2.1.4 instalar Python

Instale Python em seu computador com Windows. Você pode instalar o Python 2.7, 3.4 ou 3.5. Este tutorial se baseia Python 2.7. Se você já tiver instalado Python, vá para a seção 2.1.5.

[Obter Python para Windows](https://www.python.org/downloads/)

Você também precisa adicionar o caminho das pastas onde python.exe e pip.exe estão instalados no sistema `PATH` variável de ambiente. Por padrão, python.exe é instalado em `C:\Python27` e pip.exe estiver instalado no `C:\Python27\Scripts`.

## <a name="215-install-the-azure-cli"></a>2.1.5 instalar o Azure CLI

A CLI do Azure fornece uma experiência de linha de comando várias plataformas do Azure, permitindo que você trabalhe diretamente da sua linha de comando para provisionar e gerenciar recursos.

Para instalar o Azure CLI, siga estas etapas:

1. Abra uma janela do prompt de comando como administrador.
2. Execute os seguintes comandos:

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```
3. Verificar a instalação, execute o seguinte comando:

    ```bash
    az iot -h
    ```

Você verá a seguinte saída se a instalação for bem-sucedida.

![Janeiro RJ iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="216-summary"></a>2.1.6 resumo de

Você instalou CLI do Azure. Continue para a próxima seção para criar sua identidade de Azure IoT Hub e dispositivo usando a CLI do Azure.

## <a name="next-steps"></a>Próximas etapas

[2.2 criar seu hub IoT e registrar seu framboesa Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
