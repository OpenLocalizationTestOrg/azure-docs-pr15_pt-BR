<properties
    pageTitle="Definir e recuperar propriedades e metadados de objetos no armazenamento do Azure | Microsoft Azure"
    description="Armazenar metadados personalizados em objetos no armazenamento do Azure e definir e recuperar propriedades do sistema."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e recuperar propriedades e metadados #

## <a name="overview"></a>Visão geral

Objetos em Propriedades do sistema de suporte de armazenamento do Azure e metadados definidos pelo usuário, além dos dados que elas contêm:

*   **Propriedades do sistema.** Propriedades do sistema existem em cada recurso de armazenamento. Algumas delas podem ser lidos ou definidas, enquanto outras pessoas são somente leitura. Nos bastidores, algumas propriedades do sistema correspondem a determinadas cabeçalhos HTTP padrão. A biblioteca de cliente de armazenamento do Azure mantém para você.  

*   **Metadados definidos pelo usuário.** Metadados definidos pelo usuário são metadados que você especificar em um determinado recurso, na forma de um par nome-valor. Você pode usar metadados para armazenar valores adicionais com um recurso de armazenamento; Esses valores são seus próprios somente para fins e não afetam como o recurso se comporta.  

Recuperando valores de propriedade e metadados para um recurso de armazenamento é um processo de duas etapas. Antes que possa ler esses valores, você deve buscá-los explicitamente chamando o método **FetchAttributes** .

> [AZURE.IMPORTANT] Valores de propriedade e metadados para um recurso de armazenamento não são preenchidas, a menos que você chamar um dos métodos **FetchAttributes** . 

## <a name="setting-and-retrieving-properties"></a>Configurando e recuperando propriedades

Para recuperar valores de propriedade, chame o método **FetchAttributes** no blob ou contêiner preencher as propriedades, em seguida, ler os valores.

Para definir propriedades em um objeto, especifique o valor da propriedade, em seguida, chame o método **SetProperties** .

O exemplo de código a seguir cria um contêiner e grava alguns dos seus valores de propriedade para uma janela do console:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Configurando e recuperando metadados

Você pode especificar metadados como um ou mais pares de valor de nome em um recurso de blob ou contêiner. Para definir metadados, adicionar pares de valor de nome para a coleção de **metadados** do recurso e chame o método **SetMetadata** para salvar os valores para o serviço.

> [AZURE.NOTE] O nome de sua metadados deve estar de acordo com as convenções de nomenclatura para c# identificadores.
 
O exemplo de código a seguir define metadados em um contêiner. Um valor é definido usando o método da coleção **Adicionar** . O outro valor é definido usando sintaxe de chave/valor implícito. Ambos são válidos.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Para recuperar metadados, chame o método **FetchAttributes** no blob ou contêiner para preencher a coleção de **metadados** , em seguida, ler os valores, conforme mostrado no exemplo abaixo.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Consulte também  

- [Biblioteca de cliente de armazenamento do Azure para .NET referência](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Biblioteca de cliente de armazenamento Azure para pacote .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 
