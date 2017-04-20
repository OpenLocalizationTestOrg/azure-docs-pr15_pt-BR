## <a name="typical-output"></a>Saída típica

Abaixo é um exemplo da saída gravada no arquivo de log pelo exemplo de Hello World. Nova linha e guia caracteres foram adicionado para legibilidade:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Trechos de código

Esta seção discute algumas partes principais do código no exemplo Hello World.

### <a name="gateway-creation"></a>Criação de gateway

O desenvolvedor deve gravar o *processo de gateway*. Este programa cria a infraestrutura interna (o agente), carrega os módulos e configura tudo para funcionar corretamente. O SDK fornece a função **Gateway_Create_From_JSON** lhe permitem bootstrap um gateway de um arquivo JSON. Para usar a função **Gateway_Create_From_JSON** você deve passar o caminho para um arquivo JSON que especifica os módulos de carga. 

Você pode encontrar o código para o processo de gateway na amostra de Olá mundo no [main.c] [ lnk-main-c] arquivo. Para obter legibilidade, o trecho a seguir mostra uma versão abreviada do código de processo de gateway. Este programa cria um gateway e, em seguida, aguarda enquanto o usuário pressionar a tecla **ENTER** antes que ele separa para baixo o gateway. 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

O arquivo de configurações de JSON contém uma lista dos módulos de carga. Cada módulo deve especificar r:

- **module_name**: um nome exclusivo para o módulo.
- **module_path**: o caminho para a biblioteca que contém o módulo. Para Linux Este é um arquivo. SO, no Windows, este é um arquivo. dll.
- **args**: nenhuma informação de configuração precisa o módulo.

O arquivo JSON também contém os vínculos entre os módulos serão passados para o agente. Um link tem duas propriedades:
- **fonte**: o nome de um módulo do `modules` seção, ou "\*".
- **coletor**: o nome de um módulo do `modules` seção

Cada link define uma rota de mensagens e a direção. Mensagens de módulo `source` devem ser entregues ao módulo `sink`. O `source` pode estar definida como "\*", indicando que as mensagens de qualquer módulo serão recebidas por `sink`.

O exemplo a seguir mostra o arquivo de configurações de JSON usado para configurar a amostra Olá mundo em Linux. Cada mensagem produzidas pelo módulo `hello_world` serão consumidos pelo módulo `logger`. Se um módulo requer que um argumento depende do design do módulo. Neste exemplo, o módulo de registro usa um argumento que é o caminho para o arquivo de saída e o módulo Olá mundo não usa argumentos:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Publicação de mensagem Hello World módulo

Você pode encontrar o código usado pelo módulo "Olá mundo" para publicar mensagens em que o ['hello_world.c'] [ lnk-helloworld-c] arquivo. O trecho a seguir mostra uma versão aperfeiçoada com comentários adicionais e alguns removido para legibilidade de código de tratamento de erros:

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Processamento de mensagens do Hello World módulo

O módulo Olá mundo nunca deve processar todas as mensagens que outros módulos de publicar ao broker. Isso torna a implementação do retorno de chamada de mensagem no módulo Hello World uma função não operacional.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Mensagem de módulo de registro de publicação e de processamento

O módulo de registro recebe mensagens do agente e os grava em um arquivo. Publica todas as mensagens nunca. Portanto, o código do módulo registro nunca chama a função **Broker_Publish** .

A função de **Logger_Recieve** no [logger.c] [ lnk-logger-c] arquivo é o retorno de chamada que o broker invoca para entregar mensagens para o módulo de registro. O trecho a seguir mostra uma versão aperfeiçoada com comentários adicionais e alguns removido para legibilidade de código de tratamento de erros:

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o SDK do Gateway IoT, veja o seguinte:

- [Gateway de IoT SDK – enviar mensagens de dispositivo para nuvem com um dispositivo simulado usando Linux][lnk-gateway-simulated].
- [Gateway de IoT Azure SDK] [ lnk-gateway-sdk] no GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md