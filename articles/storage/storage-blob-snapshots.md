<properties
    pageTitle="Criar um instantâneo de somente leitura de um blob | Microsoft Azure"
    description="Aprenda a criar um instantâneo de um blob para fazer backup de dados blob em um determinado momento. Compreenda como os instantâneos são cobrados e como usá-los para minimizar encargos de capacidade."
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

# <a name="create-a-blob-snapshot"></a>Criar um instantâneo de blob

## <a name="overview"></a>Visão geral

Um instantâneo é uma versão somente leitura de um blob executada em um ponto no tempo. Instantâneos são úteis para fazer backup de blobs. Depois de criar um instantâneo, você pode ler, copiar ou excluí-la, mas você não pode modificá-lo.

Um instantâneo de um blob é idêntico ao seu blob base, exceto que o blob URI tem um valor de **DateTime** acrescentado ao blob URI para indicar a hora em que o instantâneo foi criado. Por exemplo, se uma página blob URI é `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o instantâneo URI é semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Todos os instantâneos compartilham URI do blob base. A única distinção entre o blob base e o instantâneo é o valor de **DateTime** acrescentado.

Um blob pode ter qualquer número de instantâneos. Instantâneos persistem até explicitamente são excluídos. Um instantâneo não pode tempo de vida maior seu blob base. Você pode enumerar os instantâneos associados com o blob base para controlar sua instantâneos atuais.

Quando você cria um instantâneo de um blob, propriedades do sistema do blob são copiadas para o instantâneo com os mesmos valores. Metadados do blob base também é copiado ao instantâneo, a menos que você especificar metadados separado para o instantâneo quando criá-lo.

Quaisquer concessões associados com o blob base não afetam o instantâneo. Você não pode adquirir uma concessão em um instantâneo.

## <a name="create-a-snapshot"></a>Criar um instantâneo

O exemplo de código a seguir mostra como criar um instantâneo no .NET. Este exemplo especifica metadados separado para o instantâneo quando ele é criado.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Instantâneos de cópia

Operações de cópia envolvendo blobs e instantâneos siga estas regras:

- Você pode copiar um instantâneo pela sua blob base. Por promover um instantâneo para a posição do blob base, você pode restaurar uma versão anterior de um blob. O permanece instantâneo, mas a base blob é substituído com uma cópia gravável do instantâneo.

- Você pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.

- Quando um blob de origem é copiado, qualquer instantâneos do blob fonte não são copiados para o destino. Quando um blob de destino for sobrescrito com uma cópia, qualquer instantâneos associados com o blob de destino original permanecerão intactos.

- Quando você cria um instantâneo de um blob de bloco, lista de bloqueios confirmada do blob também é copiada para o instantâneo. Os blocos não confirmados não são copiados.

## <a name="specify-an-access-condition"></a>Especificar uma condição de acesso

Você pode especificar uma condição de acesso para que o instantâneo é criado somente se uma condição é atendida. Para especificar uma condição de acesso, use a propriedade **AccessCondition** . Se a condição especificada não for atendida, o instantâneo não é criado e o serviço de Blob retorna código de status HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Excluir instantâneos

Você não pode excluir um blob com instantâneos, a menos que os instantâneos também são excluídos. Você pode excluir um instantâneo individualmente ou especificar que todos os instantâneos sejam excluídos quando o blob de origem é excluído. Se você tentar excluir um blob que ainda tem instantâneos, ocorrerá um erro.

O exemplo de código a seguir mostra como excluir um blob e respectivos instantâneos no .NET, onde `blockBlob` é uma variável do tipo **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Instantâneos com o armazenamento do Azure Premium

Usando instantâneos com acompanhamento de armazenamento Premium estas regras:

- O número máximo de instantâneos por blob de página em uma conta de armazenamento premium é 100. Se esse limite for excedido, a operação de instantâneo Blob retorna o código de erro 409 (**SnapshotCountExceeded**).

- Você pode fazer um instantâneo de um blob de página em uma conta de armazenamento premium uma vez a cada 10 minutos. Se essa taxa for excedida, a operação de instantâneo Blob retorna o código de erro 409 (**SnaphotOperationRateExceeded**).

- Você não pode chamar obter Blob para ler um instantâneo de um blob de página em uma conta de armazenamento premium. Chamar obter Blob em um instantâneo de uma conta de armazenamento premium retorna o código de erro 400 (**InvalidOperation**). No entanto, você pode chamar obter propriedades de Blob e obter metadados de Blob em relação a um instantâneo de uma conta de armazenamento premium.

- Para ler um instantâneo, você pode usar a operação de Blob copiar para copiar um instantâneo para outro blob de página na conta. O blob de destino para a operação de cópia não deve ter qualquer instantâneos existentes. Se o blob de destino tem instantâneos, a operação de cópia Blob retorna o código de erro 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Retornar o URI absoluto para um instantâneo

Este exemplo de código c# cria um instantâneo e gravará o URI absoluto para o local principal.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Entender como instantâneos são acumulados encargos

Criação de um instantâneo, o que é uma cópia somente leitura de um blob, pode resultar em encargos de armazenamento de dados adicionais à sua conta. Ao projetar seu aplicativo, é importante saber como esses encargos podem são acumulados para que você pode minimizar custos desnecessários.

### <a name="important-billing-considerations"></a>Considerações importantes de cobranças

A lista a seguir inclui alguns pontos principais a serem consideradas ao criar um instantâneo.

- Conta de armazenamento você provoca encargos para blocos exclusivos ou páginas, independentemente de estarem no blob ou no instantâneo. Sua conta não provoca cobranças adicionais para instantâneos associados a um blob até que você atualize o blob em que eles se baseiam. Depois de atualizar o blob base, ele diverge do seus instantâneos. Quando isso acontece, cobrado para os blocos exclusivos ou páginas em cada blob ou instantâneo.

- Quando você substitui um bloco dentro um blob de bloco, esse bloco subsequentemente é cobrado como um bloco exclusivo. Isso é verdadeiro mesmo se o bloco tem a mesma identificação de bloco e os mesmos dados que ele tenha no instantâneo. Após o bloco é confirmado novamente, ele diverge do seu correspondente em qualquer instantâneo e você será cobrado para seus dados. O mesmo acontece com uma página em um blob de página que é atualizado com dados idênticos.

- Substituir um blob de bloco chamando o método **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** substitui todos os blocos no blob. Se você tiver um instantâneo associado ao blob, todos os blocos na base blob e instantâneo agora evoluir, e você será cobrado por todos os blocos em ambas as bolhas. Isso é verdadeiro mesmo se os dados no blob de base e o instantâneo permanecem idênticos.

- O serviço do Azure Blob não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado será tratado como exclusivo, mesmo se ela tem os mesmos dados e a mesma ID de bloco. Porque encargos acumulam blocos exclusivo, é importante levar em consideração que atualizando um blob que tem um resultado instantâneo blocos exclusivos adicionais e cobranças adicionais.

> [AZURE.NOTE] As práticas recomendadas determinam que você gerenciar instantâneos cuidadosamente para evitar encargos adicionais. Recomendamos que você gerenciar instantâneos da seguinte maneira:

> - Exclua e recrie instantâneos associados um blob sempre que você atualizar o blob, mesmo se você estiver atualizando com dados idênticos, a menos que o design do aplicativo exija que você mantenha instantâneos. Excluindo e recriando instantâneos do blob, você pode garantir que o blob e instantâneos não divergem.

> - Se você está mantendo instantâneos para um blob, evite chamando **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** para atualizar o blob. Esses métodos substituem todos os blocos no blob, para que seu blob base e instantâneos divergem significativamente. Em vez disso, atualize o menor número possível de blocos usando os métodos **PutBlock** e **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Instantâneo de cenários de cobrança


Cenários a seguir demonstram como encargos são acumulados para um blob de bloco e respectivos instantâneos.

Cenário 1, o blob base não foram atualizado depois que o instantâneo foi criado, portanto encargos incorridos somente para exclusivos blocos 1, 2 e 3.

![Recursos de armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Cenário 2, o blob base foi atualizado, mas o instantâneo não foi. Bloco 3 foi atualizado e mesmo que ela contém os mesmos dados e a mesma identificação, não é o mesmo que bloquear 3 no instantâneo. Como resultado, a conta é cobrada quatro blocos.

![Recursos de armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Cenário 3, o blob base foi atualizado, mas o instantâneo não foi. Bloco 3 foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete bloco 3. Como resultado, a conta é cobrada quatro blocos.

![Recursos de armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Cenário 4, o blob base foi completamente atualizado e contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada oito blocos de exclusivos. Este cenário pode ocorrer se você estiver usando um método de atualização como **UploadFile**, **UploadText**, **UploadFromStream**ou **UploadByteArray**, porque esses métodos substituir todo o conteúdo de um blob.

![Recursos de armazenamento Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Próximas etapas

Para usar o armazenamento de Blob de exemplos adicionais, consulte [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Você pode fazer o download de um aplicativo de exemplo e executá-lo ou procure o código no GitHub. 
