<properties
    pageTitle="Introdução ao fornecimento de conteúdo sob demanda usando .NET | Azure"
    description="Este tutorial orienta você pelas etapas de implementar um aplicativo de distribuição de conteúdo on demand com serviços de mídia do Azure usando .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introdução ao fornecimento de conteúdo sob demanda usando .NET SDK

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Visão geral 

Este tutorial orienta você pelas etapas de implementação de um aplicativo de distribuição de conteúdo de vídeo sob demanda (VoD) usando os serviços de mídia do Azure (AMS) SDK para .NET.


O tutorial apresenta o fluxo de trabalho de serviços de mídia básico e os objetos de programação mais comuns e tarefas necessárias para desenvolvimento de serviços de mídia. Após a conclusão do tutorial, você poderá transmitir ou progressivamente baixar um arquivo de mídia de exemplo que você carregou codificado e baixados.

## <a name="what-youll-learn"></a>O que você aprenderá

O tutorial mostra como realizar as seguintes tarefas:

1.  Crie uma conta de serviços de mídia (usando o portal do Azure).
2.  Configure o ponto de extremidade de streaming (usando o portal do Azure).
3.  Criar e configurar um projeto do Visual Studio.
5.  Conectar-se para a conta de serviços de mídia.
6.  Criar um novo ativo e carregar um arquivo de vídeo.
7.  Codifica o arquivo de origem em um conjunto de arquivos de MP4 adaptativa taxa de bits.
8.  Publicar o ativo e obter URLs para download de streaming e Avançado.
9.  Teste tocando seu conteúdo.

## <a name="prerequisites"></a>Pré-requisitos

A seguir é necessárias para concluir o tutorial.

- Para concluir este tutorial, você precisa de uma conta do Azure. 
    
    Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenha créditos que podem ser usados para experimentar serviços Azure pagos. Mesmo após os créditos são usados para cima, você pode manter a conta e usar serviços gratuitos do Azure e recursos, como o recurso de aplicativos Web do serviço de aplicativo do Azure.
- Sistemas operacionais: Windows 8 ou posterior, Windows 2008 R2, Windows 7.
- .NET framework 4.0 ou posterior
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.


##<a name="download-sample"></a>Exemplo de download

Obtenha e executar uma amostra de [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Crie uma conta de serviços de mídia do Azure usando o portal do Azure

As etapas desta seção mostram como criar uma conta de AMS.

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **mídia + CDN** > **Serviços de mídia**.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Em **Criar conta de serviços de mídia** insira valores necessários.

    ![Criam serviços de mídia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, insira o nome da nova conta AMS. Um nome de conta de serviços de mídia é todos os números em minúsculas ou letras sem espaços e caracteres de 3 a 24 de comprimento.
    2. Na assinatura, selecione entre as diferentes assinaturas do Azure que você tem acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que compartilham o ciclo de vida, permissões e políticas. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Em **local**, selecione a região geográfica é usada para armazenar os registros de mídia e metadados de sua conta de serviços de mídia. Essa região é usado para processar e transmitir sua mídia. Apenas as regiões de serviços de mídia disponíveis aparecem na caixa de listagem suspensa. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer armazenamento de blob do conteúdo mídia de sua conta de serviços de mídia. Você pode selecionar uma conta existente do armazenamento na mesma região geográfica como sua conta de serviços de mídia, ou você pode criar uma conta de armazenamento. Uma nova conta de armazenamento é criada na mesma região. As regras para nomes de contas de armazenamento são as mesmas contas de serviços de mídia.

        Saiba mais sobre armazenamento [aqui](storage-introduction.md).

    4. Selecione **Fixar no painel de controle** para ver o progresso da implantação conta.
    
7. Clique em **criar** na parte inferior do formulário.

    Depois que a conta é criada com êxito, o status é alterado para **execução**. 

    ![Configurações de serviços de mídia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerenciar sua conta de AMS (por exemplo, carregar vídeos, codificar ativos, monitorar o progresso de tarefa) use a janela **configurações** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar pontos de extremidade streaming usando o portal do Azure

Ao trabalhar com serviços de mídia do Azure um dos cenários mais comuns estiver entregando vídeo por meio de taxa de bits adaptativa streaming a seus clientes. Serviços de mídia compatível com a taxa de bits adaptativa seguinte streaming tecnologias: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Serviços de mídia fornece embalagem dinâmica, que permite que você fornecer sua taxa de bits adaptativa MP4 codificado conteúdo streaming formatos compatíveis com os serviços de mídia (MPEG traço, HLS, Streaming suave, HDS) just-in-time, sem precisar armazenar predefinidas versões de cada um desses streaming formatos.

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codifica seu arquivo mezzanine (origem) em um conjunto de arquivos de taxa de bits adaptativa MP4 (as etapas de codificação são demonstrou posteriormente neste tutorial).  
- Crie pelo menos uma unidade streaming para o *fluxo de extremidade* do qual você planeja entrega seu conteúdo. As etapas a seguir mostram como alterar o número de unidades de streaming.

Com embalagem dinâmica, você só precisa armazenar e pagar os arquivos no formato de armazenamento único e serviços de mídia cria e serve a resposta apropriada com base em solicitações de um cliente.

Para criar e alterar o número de unidades reservadas de streaming, faça o seguinte:


1. Na janela **configurações** , clique em **pontos de extremidade de Streaming**. 

2. Clique no padrão streaming ponto de extremidade. 

    A janela de **Detalhes de ponto de EXTREMIDADE de STREAMING de padrão** é exibida.

3. Para especificar o número de unidades streaming, deslize o controle deslizante de **unidades de Streaming** .

    ![Unidades de streaming](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Salvar** para salvar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode levar até 20 minutos para ser concluída.

##<a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Crie um novo aplicativo de Console em c# no Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Insira o **nome**, o **local**e o **nome da solução**e clique em **Okey**.

2. Use o pacote de NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **As extensões de SDK .NET de serviços de mídia do Azure**.  Extensões de SDK do .NET de serviços de mídia é um conjunto de funções de auxiliar que irá simplificar seu código e facilitar a desenvolver com os serviços de mídia e métodos de extensão. Instalar este pacote, também instala o **SDK do .NET de serviços de mídia** e adiciona todos os outras dependências necessárias.

3. Adicione uma referência ao assembly System. Configuration. Esse assembly contém a classe **System.Configuration.ConfigurationManager** que é usada para acessar arquivos de configuração, por exemplo, App.

4. Abra o arquivo App (adicionar o arquivo ao seu projeto se ele não foi adicionado por padrão) e adicione uma seção *appSettings* ao arquivo. Defina os valores para seu serviços de mídia do Azure nome e uma conta chave da conta, conforme mostrado no exemplo a seguir. Para obter o nome de conta e informações importantes, acesse o [portal do Azure](https://portal.azure.com/) e selecione sua conta de AMS. Em seguida, selecione **configurações** > **chaves**. As janelas de chaves de gerenciar mostra o nome da conta e as chaves primárias e secundárias é exibida.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Substitua as existente **usando** instruções no início do arquivo Program.cs pelo código a seguir.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Criar uma nova pasta no diretório de projetos e copiar um arquivo. mp4 ou. wmv que você deseja codificar e transmitir ou download progressivo. Neste exemplo, o caminho de "C:\VideoFiles" é usado.

##<a name="connect-to-the-media-services-account"></a>Conectar-se para a conta de serviços de mídia

Ao usar os serviços de mídia com .NET, você deve usar a classe **CloudMediaContext** para a maioria dos serviços de mídia tarefas de programação: conectar-se a conta de serviços de mídia; criar, atualizar, acessar e excluindo os seguintes objetos: ativos, arquivos de ativos, trabalhos, as políticas de acesso, locators, etc.

Substitua a classe de programa padrão com o seguinte código. O código demonstra como ler os valores de conexão do arquivo App e como criar o objeto **CloudMediaContext** para se conectar aos serviços de mídia. Para obter mais informações sobre como se conectar aos serviços de mídia, consulte [Conectando-se aos serviços de mídia com o SDK de serviços de mídia para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

A função **principal** chama métodos que serão definidos mais nesta seção.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Criar um novo ativo e carregar um arquivo de vídeo

Em serviços de mídia, você carrega (ou inclusão) os arquivos digitais até um ativo. A entidade de **ativos** pode conter vídeo, áudio, imagens, conjuntos de miniaturas, texto faixas e legendas arquivos (e os metadados sobre esses arquivos.)  Depois que os arquivos são carregados, o conteúdo está armazenado com segurança na nuvem para processamento posterior e streaming. Os arquivos em ativo são chamados **Arquivos de ativos**.

O método **UploadFile** definido abaixo chamadas **CreateFromFile** (definido nas extensões de SDK do .NET). **CreateFromFile** cria um novo ativo no qual o arquivo de origem especificado é carregado.

O método **CreateFromFile** utiliza **AssetCreationOptions** que permite especificar uma das opções de criação de ativo a seguir:

- **Nenhum** - sem criptografia é usado. Este é o valor padrão. Observe que ao usar esta opção, seu conteúdo não está protegido em trânsito ou inativos em armazenamento.
Se você planeja fornecer um MP4 usando o download progressivo, use essa opção.
- **StorageEncrypted** - Use essa opção para criptografar o conteúdo limpar localmente usando criptografia de bit-256 avançadas criptografia AES (padrão), que carrega o armazenamento do Azure onde ele está armazenado criptografada no restante. Ativos protegidos com criptografia de armazenamento são automaticamente não criptografados e colocados em um sistema de arquivos criptografados antes de codificação e, opcionalmente, criptografados novamente antes de carregar volta como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte inativos no disco.
- **CommonEncryptionProtected** - Use essa opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comuns ou PlayReady DRM (por exemplo, suave Streaming protegido com PlayReady DRM).
- **EnvelopeEncryptionProtected** – Use esta opção se você estiver carregando HLS criptografados com AES. Observe que os arquivos devem foram codificados e criptografados por Gerenciador de transformar.

O método **CreateFromFile** também permite especificar um retorno de chamada para relatar o progresso de carregamento do arquivo.

No exemplo a seguir, podemos especificar **Nenhuma** das opções de ativo.

Adicione o seguinte método à classe programa.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar o arquivo de origem em um conjunto de arquivos de MP4 de taxa de bits adaptativa

Após a inclusão de ativos para serviços de mídia, mídia pode ser codificado, transmuxed, marca d'água e assim por diante, antes de ela é entregue aos clientes. Essas atividades são agendadas e executadas várias instâncias de função de plano de fundo para garantir alto desempenho e disponibilidade. Essas atividades são chamadas trabalhos, e cada trabalho é composto de tarefas atômicas que fazer o trabalho real no arquivo ativo.

Conforme mencionado anteriormente, ao trabalhar com serviços de mídia do Azure, um dos cenários mais comuns está oferecendo adaptativa taxa de bits streaming aos seus clientes. Serviços de mídia dinamicamente pode empacotar um conjunto de arquivos de taxa de bits adaptativa MP4 em um dos seguintes formatos: Streaming Live HTTP (HLS), Streaming suave, MPEG traço e HDS (para somente licenciados Adobe faltava/acesso).

Para tirar proveito da embalagem dinâmico, você precisa fazer o seguinte:

- Codificar ou decodificar seu mezzanine (origem) do arquivo em um conjunto de taxa de bits adaptativa MP4 arquivos ou taxa de bits adaptativa Streaming suave.  
- Obtenha pelo menos uma unidade de streaming para o ponto de extremidade streaming do qual você planeja entrega seu conteúdo.

O código a seguir mostra como enviar um trabalho de codificação. O trabalho contém uma tarefa que especifica a decodificar o arquivo mezzanine em um conjunto de adaptativa taxa de bits MP4s usando **Mídia codificador padrão**. O código envia o trabalho e aguarda até que ela seja concluída.

Depois que o trabalho é concluído, você poderá transmitir seu ativo ou fazer o download de arquivos MP4 que foram criados como resultado de transcodificação cada vez.
Observe que você não precisa ter mais de 0 unidades streaming para cada vez baixar arquivos MP4.

Adicione o seguinte método à classe programa.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar o ativo e obter URLs para download de streaming e avançado

Para transmitir ou baixar um ativo, primeiro é necessário para o "Publicar" Criando um localizador. Locators fornecem acesso a arquivos contidos em ativo. Serviços de mídia compatível com dois tipos de localizadores: locators OnDemandOrigin, usados para transmitir mídia (por exemplo, MPEG traço, HLS ou Streaming suave) e locators de assinatura de acesso (SAS), usado para baixar arquivos de mídia.

Depois de criar os locators, você pode criar as URLs que são usadas para transmitir ou baixar arquivos.


Uma URL de streaming do Streaming suave tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Uma URL streaming para HLS tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Uma URL de streaming de traço MPEG tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Uma URL de SAS usada para baixar arquivos tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Extensões de Media Services .NET SDK fornecem métodos de auxiliar conveniente que retornam URLs formatadas para o ativo publicado.

O código a seguir usa extensões de SDK do .NET para criar locators e obter streaming e URLs de download progressivo. O código também mostra como baixar arquivos em uma pasta local.

Adicione o seguinte método à classe programa.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Testar tocando seu conteúdo  

Depois que você executa o programa definido na seção anterior, as URLs semelhantes à seguinte serão exibidas na janela do console.

URLs de streaming adaptativa:

Streaming suave

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG TRAÇO

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de download progressivo (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para você transmitir vídeo, use [O reprodutor de serviços de mídia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar o download progressivo, cole uma URL em um navegador (por exemplo, Internet Explorer, Chrome ou Safari).


##<a name="next-steps-media-services-learning-paths"></a>Próximas etapas: Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>Procurando algo mais?

Se este tópico não contêm o que você estava esperando, está faltando algo ou em alguma outra maneira não atender às suas necessidades, forneça-nos seus comentários usando o segmento Disqus abaixo.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
