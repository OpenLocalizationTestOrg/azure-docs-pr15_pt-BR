<properties
 pageTitle="Gerenciar a expiração de conteúdo do armazenamento do Azure blob no Azure CDN | Microsoft Azure"
 description="Saiba mais sobre as opções para controlar o tempo de vida para blobs no cache de CDN do Azure."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Gerenciar a expiração de conteúdo do armazenamento do Azure blob no Azure CDN

> [AZURE.SELECTOR]
- [Serviços de nuvem/aplicativos do Azure Web, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure serviço de armazenamento de blob](cdn-manage-expiration-of-blob-content.md)

O [serviço blob](../storage/storage-introduction.md#blob-storage) no [Armazenamento do Azure](../storage/storage-introduction.md) é uma das várias origens com base no Azure integradas ao Azure CDN.  Qualquer conteúdo acessível publicamente blob pode ser armazenados em cache Azure CDN até ociosa seu time to live (TTL).  O TTL é determinado pelo [cabeçalho *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do armazenamento do Azure.

>[AZURE.TIP] Você pode optar por não definir nenhum TTL em um blob.  Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias.
>
>Para obter mais informações sobre o funcionamento do Azure CDN para acelerar acesso a blobs e outros arquivos, consulte [Visão geral do Azure CDN](./cdn-overview.md).
>
>Para obter mais detalhes sobre o serviço de armazenamento do Azure blob, consulte [Conceitos de serviços de Blob](https://msdn.microsoft.com/library/dd179376.aspx). 

Este tutorial demonstra várias maneiras que você pode definir o TTL em um blob no armazenamento do Azure.  

## <a name="azure-powershell"></a>PowerShell Azure

[PowerShell do Azure](../powershell-install-configure.md) é uma das formas mais rápidas e mais potentes para administrar seus serviços Azure.  Use o `Get-AzureStorageBlob` cmdlet para obter uma referência para o blob, defina o `.ICloudBlob.Properties.CacheControl` propriedade. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] Você também pode usar o PowerShell para [Gerenciar os perfis CDN e pontos de extremidade](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Biblioteca de cliente de armazenamento do Azure para .NET

Para definir TTL de um blob usando .NET, use a [Biblioteca de cliente de armazenamento do Azure para .NET](../storage/storage-dotnet-how-to-use-blobs.md) para definir a propriedade [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Há muitos mais exemplos de código .NET disponíveis nas [Amostras de armazenamento de Blob do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Outros métodos

- [Interface de linha de comando Azure](../xplat-cli-install.md)

    Ao carregar o blob, defina a propriedade *cacheControl* usando o `-p` alternar.  Este exemplo define o valor TTL para uma hora (3600 segundos).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Serviços de armazenamento do Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Defina explicitamente a propriedade *x-ms-blob-cache-controle* em uma solicitação de [Colocar Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Colocar a lista de bloqueios](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)ou [Definir propriedades de Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Ferramentas de gerenciamento de armazenamento de terceiros

    Algumas ferramentas de gerenciamento de armazenamento do Azure de terceiros permitem que você definir a propriedade *CacheControl* em blobs. 

## <a name="testing-the-cache-control-header"></a>Teste o cabeçalho *Cache-Control*

Você pode verificar facilmente o TTL dos seus blobs.  Usando as [Ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)do seu navegador, teste que seu blob é incluindo o cabeçalho de resposta do *Cache-Control* .  Você também pode usar uma ferramenta como **wget**, [carteiro](https://www.getpostman.com/)ou [Fiddler](http://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximas etapas

- [Leia sobre o cabeçalho *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Saiba como gerenciar expiração de conteúdo de serviço de nuvem no Azure CDN](./cdn-manage-expiration-of-cloud-service-content.md)

