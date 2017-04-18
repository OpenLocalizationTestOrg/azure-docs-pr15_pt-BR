## <a name="create-a-device-management-enabled-iot-hub"></a>Criar um dispositivo gerenciamento habilitado IoT Hub

Como gerenciamento de dispositivo do IoT Hub é na visualização, você precisa criar um hub de IoT de gerenciamento habilitado do dispositivo. Quando o gerenciamento de dispositivo do IoT Hub atinge disponibilidade geral, este tutorial será atualizado. As etapas a seguir mostram como concluir essa tarefa usando o portal do Azure.

1.  Entrar no [portal do Azure].
2.  No Jumpbar, clique em **novo**, e em seguida, clique em **Internet das coisas**e clique em **Azure IoT Hub**.

    ![][img-new-hub]

3.  Na lâmina **IoT Hub** , escolha a configuração para o seu IoT Hub.

    ![][img-configure-hub]

  -   Na caixa **nome** , digite um nome para seu IoT Hub. Se o **nome** for válidas e disponíveis, uma marca de seleção verde aparece na caixa **nome** .
  -   Selecione um **nível de preços e escala**. Este tutorial não exige um nível específico.
  -   No **grupo de recursos**, crie um novo grupo de recursos ou selecione uma existente. Para obter mais informações, consulte [usando grupos de recursos para gerenciar os recursos do Azure].
  -   Marque a caixa para **Habilitar o gerenciamento de dispositivo - visualização**.
  -   Em **local**, selecione o local para hospedar seu IoT Hub. Gerenciamento de dispositivo do IoT Hub está disponível somente nos EUA Leste, Norte da Europa e da Ásia Oriental durante a visualização pública.

    > [AZURE.NOTE]Se você não marcar a caixa para **Ativar o gerenciamento de dispositivo**, as amostras não funcionam.<br/>Verificando a **Ativar o gerenciamento de dispositivo**, você cria uma visualização IoT Hub com suporte apenas nos EUA Leste, Norte da Europa e da Ásia Oriental e não destinados a cenários de produção. Você não pode migrar dispositivos dentro e fora hubs de gerenciamento habilitado do dispositivo.

4.  Quando você tiver escolhido seu IoT Hub opções de configuração, clique em **criar**. Pode levar alguns minutos para Azure criar o seu IoT Hub. Para verificar o status, você pode monitorar o andamento do **Startboard** ou no painel **notificações** .

    ![][img-monitor]

5.  Quando o Hub de IoT tiver sido criado com êxito, a lâmina para seu hub abrirá automaticamente. Anote o **nome do host**e clique em **políticas de acesso compartilhado**.

    ![][img-keys]

6.  Clique na política de **iothubowner** , copie-o e anote a cadeia de conexão na lâmina **iothubowner** . Copie-o para um local em que você possa acessar mais tarde, porque você precisa concluir este tutorial.

    > [AZURE.NOTE] Em cenários de produção, certifique-se evitar usando as credenciais de **iothubowner** .

    ![][img-connection]

Agora você criou um dispositivo gerenciamento ativado IoT Hub. É necessário a cadeia de conexão para concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta seção, você usar uma ferramenta de nó chamada [IoT Hub Explorer] [ iot-hub-explorer] para criar uma identidade de dispositivo para este tutorial.

1. Execute o seguinte no seu ambiente de linha de comando:

    NPM instalar -giothub-explorer@latest

2. Em seguida, execute o seguinte comando para fazer login no seu hub, lembre-se de substituir `{service connection string}` com a cadeia de conexão IoT Hub você copiou anteriormente:

    login iothub-explorer "{cadeia de conexão de serviço}"

3. Finalmente, crie uma nova identidade de dispositivo chamada `myDeviceId` com o comando:

    iothub-explorer criar myDeviceId – cadeia de conexão

Anote a cadeia de conexão de dispositivo do resultado. Essa cadeia de caracteres de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu IoT Hub como um dispositivo.

![][img-identity]

Consulte o [guia de Introdução do IoT Hub] [ lnk-getstarted] uma maneira de criar identidades de dispositivo programaticamente.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Portal do Azure]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[Usando grupos de recursos para gerenciar os recursos do Azure]: ../articles/azure-portal/resource-group-portal.md
