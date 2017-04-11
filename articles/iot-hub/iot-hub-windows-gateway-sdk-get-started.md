<properties
    pageTitle="Começar a usar o SDK do Gateway de Hub IoT | Microsoft Azure"
    description="Azure IoT Gateway SDK explicação passo a passo usando o Windows para ilustrar conceitos-chave que você deve compreender quando você usa o SDK do Azure IoT Gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>SDK de Gateway Azure IoT (beta) - começar a usar o Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar o exemplo

Antes de começar, você deve [Configurar o seu ambiente de desenvolvimento] [ lnk-setupdevbox] para trabalhar com o SDK no Windows.

1. Abra um prompt de comando do **Prompt de comando do desenvolvedor para VS2015** .
2. Navegue até a pasta raiz em sua cópia local do repositório do **azure-iot-gateway-sdk** .
3. Executar o **ferramentas\\build.cmd** script. Este script cria um arquivo de solução do Visual Studio, cria a solução e executa os testes. Você pode encontrar a solução do Visual Studio na pasta **build** na sua cópia local do repositório do **azure-iot-gateway-sdk** .

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.cmd** cria uma pasta chamada **Construir** em sua cópia local do repositório. Esta pasta contém os dois módulos usados neste exemplo.

    O script de construção coloca **logger_hl.dll** no **Construir\\módulos\\agente\\depurar** pasta e **hello_world_hl.dll** no **Construir\\módulos\\hello_world\\depurar** pasta. Use estes caminhos para o valor de **caminho de módulo** , conforme mostrado no arquivo de configurações de JSON abaixo.

2. O arquivo **hello_world_win.json** no **amostras\\hello_world\\src** pasta é um exemplo de arquivo de configurações de JSON para Windows que você pode usar para executar a amostra. As configurações de JSON de exemplo mostradas abaixo supõe que você clonar repositório IoT SDK de Gateway para a raiz da sua unidade **c:** . Se você baixou-lo para outro local, você precisa ajustar os valores de **caminho de módulo** na **amostras\\hello_world\\src\\hello_world_win.json** arquivo adequadamente.

3. Para o módulo **logger_hl** , na seção **argumentos** , defina o valor de **nome do arquivo** ao nome e o caminho do arquivo que contém os dados do log.

    Este é um exemplo de um arquivo de configurações de JSON para Windows que gravará o arquivo de **log. txt** na raiz da sua unidade **c:** .

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. No prompt de comando, navegue até a pasta raiz da sua cópia local do repositório do **azure-iot-gateway-sdk** .
4. Execute o seguinte comando:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md