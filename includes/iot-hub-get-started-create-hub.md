## <a name="create-an-iot-hub"></a>Criar um hub IoT

Crie um hub IoT do seu dispositivo simulado para conectar-se a. As etapas a seguir mostram como concluir essa tarefa usando o portal do Azure.

1. Entre [portal do Azure][lnk-portal].

2. No Jumpbar, clique em **novo** > **Internet das coisas** > **Azure IoT Hub**.

    ![Portal do Azure Jumpbar][1]

3. Na lâmina **IoT hub** , escolha a configuração para o seu hub IoT.

    ![Blade de hub IoT][2]

    * Na caixa **nome** , digite um nome para seu hub IoT. Se o **nome** for válidas e disponíveis, uma marca de seleção verde aparece na caixa **nome** .
    * Selecione um [nível de preços e escala][lnk-pricing]. Este tutorial não exige um nível específico. Para este tutorial, use a camada F1 gratuita.
    * No **grupo de recursos**, crie um novo grupo de recursos ou selecione uma existente. Para obter mais informações, consulte [usando grupos de recursos para gerenciar os recursos Azure][lnk-resource-groups].
    * Em **local**, selecione o local para hospedar seu hub IoT. Para este tutorial, escolha o local mais próximo.

4. Quando você tiver escolhido seu hub IoT opções de configuração, clique em **criar**.  Pode levar alguns minutos para Azure criar o seu hub IoT. Para verificar o status, você pode monitorar o andamento do Startboard ou no painel notificações.

    ![Novo status de hub IoT][3]

5. Quando o hub de IoT tiver sido criado com êxito, clique em novo bloco de seu hub IoT no portal do Azure para abrir a lâmina para o hub de IoT novo. Anote o **nome do host**e clique em **políticas de acesso compartilhado**.

    ![Novo blade de hub IoT][4]

6. Na lâmina **as políticas de acesso compartilhado** , clique na política de **iothubowner** e em seguida, copie e anote a cadeia de conexão na lâmina **iothubowner** . Para obter mais informações, consulte [controle de acesso] [ lnk-access-control] no "guia de desenvolvedor do Azure IoT Hub".

    ![Blade de políticas de acesso compartilhado][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
