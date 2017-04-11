<properties
    pageTitle="Introdução ao blob Visual Studio e armazenamento conectado serviços (WebJob projetos) | Microsoft Azure"
    description="Como começar a usar o armazenamento de Blob em um projeto de WebJob depois de conectar a um armazenamento do Azure usando o Visual Studio conectado serviços."
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao Azure Blob Visual Studio e armazenamento conectado serviços (WebJob projetos)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Este artigo fornece c# exemplos de código que mostram como acionar um processo quando um Azure blob é criado ou atualizado. Os exemplos de código usam a versão de [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) 1. x. Quando você adicionar uma conta de armazenamento a um projeto de WebJob usando a caixa de diálogo do Visual Studio **Adicionar serviços conectados** , o pacote de NuGet de armazenamento do Azure apropriado estiver instalado, as referências de .NET apropriado são adicionadas ao projeto e cadeias de caracteres de conexão da conta de armazenamento são atualizadas no arquivo App.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Como disparar uma função quando um blob é criado ou atualizado

Esta seção mostra como usar o atributo **BlobTrigger** .

 **Observação:** O SDK do WebJobs verifica arquivos de log para assistir para blobs novas ou alteradas. Esse processo é naturalmente lento; uma função não pode ser disparada até vários minutos ou mais após o blob é criado.  Se seu aplicativo precisar processar blobs imediatamente, o método recomendado é criar uma mensagem de fila quando você cria o blob e use o atributo [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) em vez do atributo **BlobTrigger** na função que processa o blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Único espaço reservado para o nome de blob com extensão  

O exemplo de código a seguir copia bolhas de texto que aparecem no contêiner de *entrada* ao contêiner de *saída* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

O construtor de atributo utiliza um parâmetro de cadeia de caracteres que especifica o nome de contêiner e um espaço reservado para o nome de blob. Neste exemplo, se um blob chamado *Blob1.txt* é criado no contêiner de *entrada* , a função cria um blob denominado *Blob1.txt* no contêiner de *saída* .

Você pode especificar um padrão de nome com o espaço reservado nome de blob, conforme mostrado no exemplo de código a seguir:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Este código copia somente bolhas que têm nomes que começam com "original-". Por exemplo, *Blob1.txt original* no contêiner de *entrada* é copiado para a *Cópia-Blob1.txt* no contêiner de *saída* .

Se você precisar especificar um padrão de nome para nomes de blob que tenham chaves no nome, duplo as chaves. Por exemplo, se você quiser localizar blobs no contêiner de *imagens* que têm nomes como esta:

        {20140101}-soundfile.mp3

Use isto para seu padrão:

        images/{{20140101}}-{name}

No exemplo, o valor de *nome* do espaço reservado seria *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Espaços reservados de nome e a extensão do blob separado

O exemplo de código a seguir altera a extensão de arquivo como ele copia bolhas que aparecem no contêiner de *entrada* ao contêiner de *saída* . O código registra a extensão do blob *entrada* e define a extensão do blob *saída* para *. txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Tipos que você pode vincular a blobs

Você pode usar o atributo **BlobTrigger** nos seguintes tipos:

* **cadeia de caracteres**
* **TextReader**
* **Fluxo**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Outros tipos desserializados pelo [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se você quiser trabalhar diretamente com a conta de armazenamento do Azure, você também pode adicionar um parâmetro de **CloudStorageAccount** à assinatura do método.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Obtendo conteúdo de blob de texto por associação a cadeia de caracteres

Se blobs de texto são esperados, **BlobTrigger** podem ser aplicadas a um parâmetro de **cadeia de caracteres** . O exemplo de código a seguir vincula um blob de texto a um parâmetro de **cadeia de caracteres** chamado **logMessage**. A função usa esse parâmetro para gravar o conteúdo do blob ao painel WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Obtendo serializados conteúdo blob usando ICloudBlobStreamBinder

O exemplo de código a seguir usa uma classe que implementa **ICloudBlobStreamBinder** para habilitar o atributo **BlobTrigger** associar um blob para o tipo de **WebImage** .

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

O código de vinculação de **WebImage** é fornecido em uma classe **WebImageBinder** que deriva de **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Como lidar com blobs suspeitas

Quando uma função de **BlobTrigger** falhar, o SDK chama-lo novamente, caso a falha foi causada por um erro temporário. Se a falha for causada pelo conteúdo do blob, a função falhará sempre que ele tenta processar o blob. Por padrão, o SDK chama uma função até 5 vezes para um determinado blob. Se o quinto experimentar falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-inviabilização*.

O número máximo de tentativas é configurável. A mesma configuração de [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) é usada para manipulação de blob suspeita e manipulação de mensagens suspeitas fila.

A mensagem de fila para blobs suspeitas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{WebJob name}*. Funções. *{Nome de função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

No exemplo de código a seguir, a função de **CopyBlob** tem código que faz com que ele falha sempre que ele é chamado. Depois que o SDK chama o número máximo de tentativas, será criada uma mensagem na fila blob suspeita e essa mensagem é processada pela função **LogPoisonBlob** .

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

O SDK automaticamente desserializa a mensagem JSON. Aqui está a classe **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo de sondagem de blob

O SDK do WebJobs examina todos os contêineres especificados por atributos de **BlobTrigger** no início do aplicativo. Em uma conta de armazenamento grande esta verificação pode levar alguns instantes, portanto pode ser um pouco antes de novo blobs são encontrados e funções **BlobTrigger** são executadas.

Para detectar blobs novos ou alterados após o início do aplicativo, o SDK periodicamente lê a partir os logs de armazenamento de blob. Os logs de blob são armazenados em buffer e apenas obtém gravados cada 10 minutos ou então, para que pode haver atraso considerável após um blob é criado ou atualizado antes correspondente **BlobTrigger** função executa.

Não há uma exceção para bolhas que você criar usando o atributo de **Blob** . Quando o SDK WebJobs cria um novo blob, ele passa a ser o novo blob imediatamente para quaisquer funções **BlobTrigger** correspondentes. Portanto, se você tiver uma cadeia de blob entradas e saídas, o SDK pode processá-los com eficiência. Mas se você quiser baixa latência executando sua blob funções para bolhas que são criadas ou atualizadas por outros meios de processamento, recomendamos usando **QueueTrigger** em vez de **BlobTrigger**.

### <a name="blob-receipts"></a>Confirmações de blob

O SDK do WebJobs garante que nenhuma função **BlobTrigger** obtém chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *blob confirmações* para determinar se uma versão de blob determinado foi processada.

Confirmações de blob são armazenadas em um contêiner chamado *azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de conexão AzureWebJobsStorage. Uma confirmação de blob tem as seguintes informações:

* A função que foi chamada o blob ("*{WebJob name}*. Funções. *{Nome de função}*", por exemplo:"WebJob1.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome de blob
* A ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Se você quiser forçar reprocessamento de um blob, você pode excluir manualmente a confirmação de blob para blob do contêiner *azure-webjobs-hosts* .

## <a name="related-topics-covered-by-the-queues-article"></a>Tópicos relacionados cobertos pelo artigo filas

Para obter informações sobre como lidar com processamento de blob acionado por uma mensagem de fila ou para WebJobs SDK cenários não específicos blob processamento, consulte [como usar o armazenamento de fila Azure com o SDK do WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Tópicos relacionados abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento
* Usar atributos de WebJobs SDK no corpo de uma função
* Defina as cadeias de caracteres de conexão do SDK no código.
* Definir valores para WebJobs SDK parâmetros do construtor no código
* Configure **MaxDequeueCount** para manipulação de blob suspeita.
* Disparar uma função manualmente
* Gravar logs

## <a name="next-steps"></a>Próximas etapas

Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com o Azure blobs. Para obter mais informações sobre como usar WebJobs do Azure e o SDK do WebJobs, consulte [recursos de documentação WebJobs do Azure](http://go.microsoft.com/fwlink/?linkid=390226).
