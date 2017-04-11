<properties
    pageTitle="Carregar arquivos de dispositivos usando IoT Hub | Microsoft Azure"
    description="Siga este tutorial para saber como carregar arquivos de dispositivos usando o Azure IoT Hub com c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Tutorial: Como carregar arquivos de dispositivos na nuvem com IoT Hub

## <a name="introduction"></a>Introdução

Hub de IoT Azure é um serviço totalmente gerenciado que permite confiável e segura comunicação bidirecional entre milhões de dispositivos IoT e um aplicativo back-end. Tutoriais anteriores ([Introdução ao IoT Hub] e [Enviar mensagens de nuvem para dispositivo com IoT Hub]) ilustram a dispositivo à nuvem básica e funcionalidade de mensagens de nuvem para dispositivo do IoT Hub e o tutorial de [mensagens do processo de dispositivo à nuvem] descreve uma maneira de armazenar mensagens de dispositivo à nuvem confiavelmente armazenamento de blob do Microsoft Azure. No entanto, em alguns cenários você não pode facilmente mapear os dados que seus dispositivos enviar para as mensagens de dispositivo à nuvem relativamente pequenas que IoT Hub aceita. Os exemplos incluem arquivos grandes que contêm dados de vibração alta frequência de amostra, vídeos, imagens, ou que contenham algum tipo de dados pré-processado. Esses arquivos normalmente são processadas na nuvem usando ferramentas como [Fábrica de dados do Azure] ou pilha [Hadoop] em lote. Quando carregamentos de arquivo em um dispositivo preferenciais para envio de eventos, você ainda pode usar a funcionalidade de segurança e confiabilidade IoT Hub.

Este tutorial cria no código no tutorial [Enviar mensagens de nuvem para dispositivo com IoT Hub] para mostrar a você como usar os recursos de carregamento de arquivo do IoT Hub. Mostra como fornecer com segurança um dispositivo com um Azure blob URI para carregar um arquivo e como usar as notificações de carregamento de arquivo IoT Hub para disparar processamento do arquivo em seu back-end do aplicativo.

No final deste tutorial, você executar dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Enviar mensagens de nuvem para dispositivo com IoT Hub] , que carrega um arquivo para armazenamento usando um SAS URI fornecido pelo seu hub IoT.
* **ReadFileUploadNotification**, que recebe as notificações de carregamento de arquivo do seu hub IoT.

> [AZURE.NOTE] Hub de IoT oferece suporte a muitas plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) por meio do Azure IoT dispositivo SDKs. Consulte a [Central de desenvolvedores do Azure IoT] para obter instruções passo a passo sobre como conectar seu dispositivo para o código mostrado neste tutorial e geralmente Azure IoT Hub.

Para concluir este tutorial, você precisa do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta do Azure active. (Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta de armazenamento do Azure para IoT Hub

Porque o dispositivo simulado carrega um arquivo para um blob, você deve ter uma conta de [Armazenamento do Azure] associada ao IoT Hub. Quando você associa uma conta de armazenamento do Azure com um hub de IoT, o hub pode gerar um URI SAS que pode usar um dispositivo com segurança carregar um arquivo para um contêiner de blob. O serviço de IoT Hub e SDK do dispositivo coordenar o processo que gera o URI SAS e disponibiliza para um dispositivo usar para carregar um arquivo.

Siga as instruções em [uploads de arquivo de configurar usando o portal do Azure] [ lnk-configure-upload] para associar uma conta de armazenamento do Azure para seu hub IoT.

## <a name="upload-a-file-from-a-simulated-device"></a>Carregar um arquivo em um dispositivo simulado

Nesta seção, você modificar o aplicativo de dispositivo simulado criado em [Enviar mensagens de nuvem para dispositivo com IoT Hub] para receber mensagens de nuvem para dispositivo do hub IoT.

1. No Visual Studio, o projeto de **SimulatedDevice** de atalho, clique em **Adicionar**e, em seguida, clique em **Novo Item**. Navegue até um arquivo de imagem e inclua-o no seu projeto. Este tutorial assume a imagem é chamada `image.jpg`.

2. Clique com botão direito na imagem e clique em **Propriedades**. Certifique-se de que **Copiar para diretório de saída** for definido como **Copiar sempre**.

    ![][1]

3. No arquivo **Program.cs** , adicione as instruções a seguir na parte superior do arquivo:

        using System.IO;

4. Adicione o seguinte método à classe **programa** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    O `UploadToBlobAsync` método leva na fonte de nome e fluxo de arquivo do arquivo a ser carregado e manipula o carregamento ao armazenamento. O aplicativo de console exibe o tempo necessário para carregar o arquivo.

5. Adicione o seguinte método no método **principal** , logo antes do `Console.ReadLine()` linha:

        SendToBlobAsync();

> [AZURE.NOTE] Para simplificar, este tutorial não implementar qualquer diretiva de repetição. No código de produção, você deve implementar diretivas de repetição (como retirada exponencial), conforme sugerido no artigo da MSDN [Temporárias manipulação de falhas].

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento de arquivo

Nesta seção, você escrever um aplicativo de console do Windows que recebe mensagens de notificação de carregamento de arquivo de IoT Hub.

1. Na solução atual Visual Studio, crie um novo projeto do Visual c# Windows usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **ReadFileUploadNotification**.

    ![Novo projeto no Visual Studio][2]

2. No Solution Explorer, clique com botão direito no projeto **ReadFileUploadNotification** e clique em **Gerenciar pacotes do NuGet**.

    Isso exibe a janela Gerenciar pacotes do NuGet.

2. Procurar por `Microsoft.Azure.Devices`, clique em **instalar**e aceite os termos de uso. 

    Isso downloads, instalações e adiciona uma referência para o [Azure IoT - pacote serviço SDK NuGet] no projeto **ReadFileUploadNotification** .

3. No arquivo **Program.cs** , adicione as instruções a seguir na parte superior do arquivo:

        using Microsoft.Azure.Devices;

4. Adicione os campos a seguir para a classe de **programa** . Substitua o valor de espaço reservado com a cadeia de conexão do hub de IoT de [Introdução ao IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Adicione o seguinte método à classe **programa** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Observe que o padrão de recebimento é o mesmo usado para receber mensagens de nuvem para dispositivo do aplicativo do dispositivo.

6. Finalmente, adicione as seguintes linhas para o método **principal** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Visual Studio, sua solução de atalho e selecione **projetos de inicialização definido**. Selecionar **vários projetos de inicialização**e, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**.

2. Pressione **F5**. Os dois aplicativos devem começar. Você deve ver o carregamento concluído em um aplicativo de console e a mensagem de notificação de carregamento sendo recebido pelo aplicativo do console. Você pode usar o [portal do Azure] ou Visual Studio Server Explorer para verificar a presença do arquivo carregado em sua conta de armazenamento do Azure.

  ![][50]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como aproveitar os recursos de carregamento de arquivo do IoT Hub para simplificar carregamentos de arquivo de dispositivos. Você pode continuar a explorar os recursos de hub IoT e cenários com os seguintes artigos:

- [Criar um hub IoT programaticamente][lnk-create-hub]
- [Introdução ao SDK C][lnk-c-sdk]
- [Hub de IoT SDKs][lnk-sdks]

Para explorar os recursos do IoT Hub, consulte:

- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portal do Azure]: https://portal.azure.com/

[Fábrica de dados do Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Enviar mensagens de nuvem para dispositivo com IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Processar mensagens de dispositivo à nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Central de desenvolvedores do Azure IoT]: http://www.azure.com/develop/iot

[Manipulação de falhas temporárias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT - pacote serviço SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


