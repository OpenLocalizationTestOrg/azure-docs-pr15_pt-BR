<properties
    pageTitle="Começar a usar o SDK do Gateway de Hub IoT | Microsoft Azure"
    description="Este passo a passo SDK do Azure IoT Gateway usa Linux para ilustrar conceitos-chave que você deve compreender quando você usa o SDK do Azure IoT Gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>SDK de Gateway Azure IoT (beta) - começar a usar o Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar o exemplo

Antes de começar, você deve [Configurar o seu ambiente de desenvolvimento] [ lnk-setupdevbox] para trabalhar com o SDK no Linux.

1. Abra um shell.
2. Navegue até a pasta raiz em sua cópia local do repositório do **azure-iot-gateway-sdk** .
3. Execute o script **tools/build.sh** . Este script usa o utilitário **cmake** para criar uma pasta chamada **Construir** na pasta raiz de sua cópia local do repositório do **azure-iot-gateway-sdk** e gerar um makefile. Em seguida, o script cria a solução e executa os testes.

> [AZURE.NOTE]  Sempre que você executar o script **build.sh** , exclui e em seguida, recria a pasta **build** na pasta raiz de sua cópia local do repositório do **azure-iot-gateway-sdk** .

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.sh** gera sua saída na pasta **build** na sua cópia local do repositório do **azure-iot-gateway-sdk** . Isso inclui os dois módulos usados neste exemplo.

    O script de construção coloca **liblogger_hl.so** no **módulos/compilar/agente/** pasta e **libhello_world_hl.so** no **módulos/compilar/hello_world/** pasta. Use estes caminhos para o valor de **caminho de módulo** , conforme mostrado no arquivo de configurações de JSON abaixo.

2. O arquivo **hello_world_lin.json** na pasta **amostras/hello_world/src** é um exemplo de arquivo de configurações de JSON para Linux que você pode usar para executar a amostra. As configurações de JSON de exemplo mostradas abaixo supõe que você executará o exemplo na raiz do sua cópia local do repositório do **azure-iot-gateway-sdk** .

3. Para o módulo **logger_hl** , na seção **argumentos** , defina o valor de **nome do arquivo** ao nome e o caminho do arquivo que contém os dados do log.

    Este é um exemplo de um arquivo de configurações de JSON para Linux gravará o **log. txt** para a pasta onde você executar o exemplo.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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

3. No shell, navegue até a pasta **sdk azure-iot-gateway** .
4. Execute o seguinte comando:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
