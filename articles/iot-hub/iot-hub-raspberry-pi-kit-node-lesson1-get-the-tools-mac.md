<properties
 pageTitle="Obtenha as ferramentas (macOS 10.10) | Microsoft Azure"
 description="Baixar e instalar as ferramentas necessárias e software para o primeiro aplicativo de amostra para seu Pi em macOS."
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

# <a name="12-get-the-tools-macos-1010"></a>1.2 Obtenha as ferramentas (macOS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 o que você fará

Baixe as ferramentas de desenvolvimento e o software para o primeiro aplicativo de amostra para seu framboesa Pi 3. Se você atender a todos os problemas, busca soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 o que você aprenderá
Nesta seção, você aprenderá:

- Como instalar gito e Node
    - [Gito](https://git-scm.com) é um sistema de controle de versão de fonte aberta distribuído. O aplicativo de amostra para esta lição está armazenado em gito.
    - [Node](https://nodejs.org/en/) é um tempo de execução de JavaScript com um ecossistema de pacote avançada.
- Como usar NPM para instalar as ferramentas de desenvolvimento de Node adicionais.
  - A versão mínima necessária do Node. js é 4,5 LTS.
  - [NPM](https://www.npmjs.com) é um dos gerentes de pacote de Node.

## <a name="123-what-you-need"></a>1.2.3 o que é necessário

- Uma conexão de Internet para baixar o software e as ferramentas de desenvolvimento
- Um Mac que está executando o macOS Yosemite (10.10) ou posterior

## <a name="124-install-git-and-nodejs"></a>1.2.4 instalar gito e Node

Para instalar gito e Node, use o utilitário de gerenciamento de pacote de [Homebrew](http://brew.sh) seguindo estas etapas:

1. Instale o Homebrew. Se você já tiver instalado o Homebrew, vá para a etapa 2.
  1. Pressione `Cmd + Space` e insira `Terminal` para abrir uma janela de terminal.
  2. Execute o seguinte comando:

    ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. Instale gito e Node executando o seguinte comando:

    ```bash
    brew install node git
    ```

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 instalar ferramentas adicionais de desenvolvimento de Node

Use [gulp.js](http://gulpjs.com) para automatizar a implantação do aplicativo de amostra para seu Pi. Você também pode usar o [dispositivo de descoberta de cli](https://github.com/Azure/device-discovery-cli) para recuperar informações de rede sobre seus dispositivos IoT.

Instalar `gulp` e `device-discovery-cli` executando o seguinte comando na janela Terminal:

```bash
sudo npm install -g device-discovery-cli gulp
```

Se você tiver problemas instalando node e essas ferramentas de desenvolvimento adicionais em macOS, consulte o [guia de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para soluções para problemas comuns.

## <a name="126-install-visual-studio-code"></a>1.2.6 instalar Visual Studio código

[Baixe](https://code.visualstudio.com/docs/setup/osx) e instale o Visual Studio código. Código do Visual Studio é um editor de código fonte simples, mas poderosa para Windows, Linux e macOS. Você pode usar esse editor posteriormente no tutorial para editar o código de amostra.

## <a name="127-summary"></a>1.2.7 resumo de

Você instalou as ferramentas de desenvolvimento necessárias e software para o primeiro aplicativo de amostra. Na próxima seção, você cria, implantar e executar o aplicativo de amostra no seu Pi.

## <a name="next-steps"></a>Próximas etapas

[1.3 criar e implantar o aplicativo de exemplo piscando](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
