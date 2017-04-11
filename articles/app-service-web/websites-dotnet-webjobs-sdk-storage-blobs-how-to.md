<properties 
    pageTitle="Como usar o armazenamento de blob do Microsoft Azure com o SDK do WebJobs" 
    description="Aprenda a usar o armazenamento de blob do Microsoft Azure com o SDK do WebJobs. Acionar um processo quando um novo blob é exibida em um contêiner e tratar 'suspeitas blobs'." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Como usar o armazenamento de blob do Microsoft Azure com o SDK do WebJobs

## <a name="overview"></a>Visão geral

Este guia fornece c# exemplos de código que mostram como acionar um processo quando um Azure blob é criado ou atualizado. Os exemplos de código usam [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versão 1. x.

Para exemplos de código que mostram como criar blobs, veja [como usar o armazenamento de fila Azure com o SDK do WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
Guia supõe que você sabe [como criar um projeto de WebJob no Visual Studio com cadeias de caracteres de conexão que apontam para sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md) ou a [várias contas de armazenamento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Como disparar uma função quando um blob é criado ou atualizado

Esta seção mostra como usar o `BlobTrigger` atributo. 

> [AZURE.NOTE] O SDK do WebJobs verifica arquivos de log para assistir para blobs novas ou alteradas. Esse processo não é em tempo real; uma função não pode ser disparada até vários minutos ou mais após o blob é criado. Além disso, base de [armazenamento logs são criados em um "esforços"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; Não há nenhuma garantia de que todos os eventos serão capturados. Sob algumas condições, os logs podem ser perdidos. Se as limitações de velocidade e confiabilidade de disparadores blob não são aceitáveis para seu aplicativo, o método recomendado é criar uma mensagem de fila quando você cria o blob e use o atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) em vez da `BlobTrigger` atributo na função que processa o blob.

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

## <a id="types"></a>Tipos que você pode vincular a blobs

Você pode usar o `BlobTrigger` atributo nos seguintes tipos:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Outros tipos desserializados pelo [ICloudBlobStreamBinder](#icbsb) 

Se você quiser trabalhar diretamente com a conta de armazenamento do Azure, você também pode adicionar uma `CloudStorageAccount` parâmetro à assinatura do método.

Para obter exemplos, consulte o [código de vinculação de blob no repositório sdk do azure-webjobs em GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Obtendo conteúdo de blob de texto por associação a cadeia de caracteres

Se blobs de texto são esperados, `BlobTrigger` podem ser aplicadas a uma `string` parâmetro. O exemplo de código a seguir vincula um blob de texto para um `string` parâmetro chamado `logMessage`. A função usa esse parâmetro para gravar o conteúdo do blob ao painel WebJobs SDK. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Obtendo serializados conteúdo blob usando ICloudBlobStreamBinder

O exemplo de código a seguir usa uma classe que implementa `ICloudBlobStreamBinder` para habilitar o `BlobTrigger` atributo vincular um blob para o `WebImage` tipo.

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

O `WebImage` código de associação é fornecido em um `WebImageBinder` classe que deriva de `ICloudBlobStreamBinder`.

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

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Para o blob disparo obter o caminho de blob

Para obter o nome do contêiner e blob do blob que disparou a função, inclua um `blobTrigger` parâmetro na assinatura função de cadeia de caracteres.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>Como lidar com blobs suspeitas

Quando um `BlobTrigger` função falhar, o SDK chama novamente, caso a falha foi causada por um erro temporário. Se a falha for causada pelo conteúdo do blob, a função falhará sempre que ele tenta processar o blob. Por padrão, o SDK chama uma função até 5 vezes para um determinado blob. Se o quinto experimentar falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-inviabilização*.

O número máximo de tentativas é configurável. A mesma configuração de [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) é usada para manipulação de blob suspeita e manipulação de mensagens suspeitas fila. 

A mensagem de fila para blobs suspeitas é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{WebJob name}*. Funções. *{Nome de função}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

No exemplo de código a seguir, o `CopyBlob` função possui código que faz com que ele falha sempre que ele é chamado. Após o SDK chama o número máximo de tentativas, será criada uma mensagem na fila blob suspeita e essa mensagem é processada pela `LogPoisonBlob` função. 

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

O SDK automaticamente desserializa a mensagem JSON. Aqui está a `PoisonBlobMessage` classe: 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>Algoritmo de sondagem de blob

O SDK do WebJobs examina todos os contêineres especificados pelo `BlobTrigger` atributos no início do aplicativo. Em uma conta de armazenamento grande essa verificação pode levar algum tempo, para que ele pode ser um pouco antes de blobs novo forem encontrados e `BlobTrigger` funções são executadas.

Para detectar blobs novos ou alterados após o início do aplicativo, o SDK periodicamente lê a partir os logs de armazenamento de blob. Os logs de blob são armazenados em buffer e apenas obtém gravados cada 10 minutos ou então, assim pode haver atraso considerável após um blob é criado ou atualizado antes correspondente `BlobTrigger` executa de função. 

Há uma exceção para bolhas que você criar usando o `Blob` atributo. Quando o SDK WebJobs cria um novo blob, ela passa o novo blob imediatamente para qualquer correspondência `BlobTrigger` funções. Portanto, se você tiver uma cadeia de blob entradas e saídas, o SDK pode processá-los com eficiência. Mas se você quiser baixa latência executando sua blob funções de processamento de bolhas que são criadas ou atualizadas por outros meios, recomendamos o uso `QueueTrigger` em vez de `BlobTrigger`.

### <a id="receipts"></a>Confirmações de blob

O SDK do WebJobs garante que nenhum `BlobTrigger` função obtém chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *blob confirmações* para determinar se uma versão de blob determinado foi processada.

Confirmações de blob são armazenadas em um contêiner chamado *azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de conexão AzureWebJobsStorage. Uma confirmação de blob tem as seguintes informações:

* A função que foi chamada o blob ("*{WebJob name}*. Funções. *{Nome de função}*", por exemplo:"WebJob1.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome de blob
* A ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Se você quiser forçar reprocessamento de um blob, você pode excluir manualmente a confirmação de blob para blob do contêiner *azure-webjobs-hosts* .

## <a id="queues"></a>Tópicos relacionados cobertos pelo artigo filas

Para obter informações sobre como lidar com processamento de blob acionado por uma mensagem de fila ou para WebJobs SDK cenários não específicos blob processamento, consulte [como usar o armazenamento de fila Azure com o SDK do WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Tópicos relacionados abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento
* Usar atributos de WebJobs SDK no corpo de uma função
* Defina as cadeias de caracteres de conexão do SDK no código.
* Definir valores para WebJobs SDK parâmetros do construtor no código
* Configurar `MaxDequeueCount` para tratamento de blob suspeita.
* Disparar uma função manualmente
* Gravar logs

## <a id="nextsteps"></a>Próximas etapas

Este guia forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com o Azure blobs. Para obter mais informações sobre como usar WebJobs do Azure e o SDK do WebJobs, consulte [Azure WebJobs recomendados recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
