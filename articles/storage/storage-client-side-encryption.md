<properties
    pageTitle="Criptografia do lado do cliente com .NET para armazenamento do Microsoft Azure | Microsoft Azure"
    description="A biblioteca de cliente de armazenamento do Azure para .NET oferece suporte a criptografia do lado do cliente e integração com o Azure chave cofre para segurança máxima para seus aplicativos de armazenamento do Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Compartimento chave de criptografia do lado do cliente e Azure para armazenamento do Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Visão geral

A [Biblioteca de cliente de armazenamento do Azure para pacote .NET Nuget](https://www.nuget.org/packages/WindowsAzure.Storage) dá suporte à criptografia de dados em aplicativos cliente antes de carregar ao armazenamento do Azure e descriptografar dados durante o download do cliente. A biblioteca também oferece suporte a integração com o [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) para gerenciamento de chaves de conta de armazenamento.

Para um tutorial passo a passo que orienta você pelo processo de criptografar blobs usando criptografia do lado do cliente e Azure chave cofre, consulte [criptografar e descriptografar blobs no armazenamento do Microsoft Azure usando o Azure chave cofre](storage-encrypt-decrypt-blobs-key-vault.md).

Para criptografia do lado do cliente com Java, consulte [Criptografia do lado do cliente com Java para o armazenamento do Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Criptografia e descriptografia via a técnica de envelope

Os processos de criptografia e descriptografia siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Criptografia via a técnica de envelope

Criptografia via a técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente de armazenamento do Azure gera uma chave de criptografia do conteúdo (CEK), que é uma chave simétrica do uso de uma vez.
2. Dados de usuário são criptografados usando este CEK.
3. O CEK depois é empacotado (criptografados) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétrico ou uma chave simétrica e podem ser gerenciada localmente ou armazenado em compartimentos de chave do Azure.

    A biblioteca de cliente de armazenamento em si nunca tem acesso aos KEK. A biblioteca chama o algoritmo de quebra automática de chave que é fornecido pela chave cofre. Os usuários podem optar por usar provedores personalizados para disposição/desempacotamento chave se desejado.

4. Os dados criptografados, em seguida, são carregados para o serviço de armazenamento do Azure. A tecla quebra junto com alguns metadados adicionais de criptografia é armazenada como metadados (em um blob) ou interpolada com os dados criptografados (fila mensagens e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Descriptografia via a técnica de envelope

Descriptografia via a técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente pressupõe que o usuário está gerenciando a chave de criptografia de chave (KEK) localmente ou no compartimentos de chave do Azure. O usuário não precisa saber a chave específica que foi usada para criptografia. Em vez disso, uma resolução de chave que resolve identificadores de chave diferentes para chaves pode ser configurar e usada.
2. A biblioteca de cliente baixa os dados criptografados junto com qualquer material de criptografia que está armazenada no serviço.
3. A chave de criptografia de conteúdo quebrado (CEK) é desfeita (descriptografado) usando a chave de criptografia de chave (KEK). Veja novamente, a biblioteca de cliente não tem acesso ao KEK. Ele simplesmente invoca personalizado ou algoritmo encapsulada do provedor de chave cofre.
4. A chave de criptografia do conteúdo (CEK), em seguida, é usada para descriptografar os dados de usuário criptografadas.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia

A biblioteca de cliente do armazenamento usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar dados de usuário. Especificamente, modo [Codificação bloco encadeamento CBC ()](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona um pouco diferente, para que abordaremos cada uma delas aqui.

### <a name="blobs"></a>BLOBs

A biblioteca de cliente atualmente oferece suporte a criptografia de apenas blobs inteiros. Especificamente, a criptografia é suportada quando os usuários utilizam o **UploadFrom** * métodos ou o * *OpenWrite** método. Para downloads, ambas concluída e downloads de intervalo são suportados.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, junto com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes e executar a criptografia de envelope dos dados blob usando essas informações. O CEK quebrado e alguns metadados de criptografia adicional, em seguida, são armazenados como blob metadados juntamente com o blob criptografado no serviço.

> [AZURE.WARNING] Se você estiver editando ou carregar seus próprios metadados para o blob, você precisa garantir que esses metadados é preservado. Se você carregar novos metadados sem esses metadados, o CEK quebrado, IV e outros metadados serão perdidos e o conteúdo blob nunca será recuperável novamente.

Baixar um blob criptografado envolve a recuperar o conteúdo do blob inteiro usando a **DownloadTo***/**BlobReadStream** métodos de conveniência. O CEK quebrado é aberto e usado em conjunto com o IV (armazenado como blob metadados nesse caso) para retornar os dados descriptografados aos usuários.

Baixando um intervalo aleatório (**DownloadRange*** métodos) no blob criptografado envolve ajustando o intervalo fornecido por usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar com êxito o intervalo solicitado.

Todos os tipos de blob (bloquear blobs, blobs de página e acrescentar blobs) pode ser criptografados/descriptografar usando este esquema.

### <a name="queues"></a>Filas

Como mensagens de fila podem ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia do conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera uma IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa criptografia de envelope do texto da mensagem fila usando essas informações. O CEK quebrado e alguns metadados de criptografia adicional, em seguida, são adicionados à mensagem criptografada fila. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante descriptografia, a chave quebra é extraída da mensagem fila e aberta. O IV também é extraído da mensagem fila e usado juntamente com a tecla desfeita para descriptografar os dados de mensagem de fila. Observe que os metadados de criptografia são pequeno (em 500 bytes), enquanto ele contar em relação ao limite de 64KB para uma mensagem de fila, o impacto deve ser gerenciável.

### <a name="tables"></a>Tabelas

A biblioteca de cliente oferece suporte à criptografia de propriedades de entidade para inserir e substituir operações.

>[AZURE.NOTE] Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, basta mesclando as novas propriedades e atualizando os metadados resultará em perda de dados. Mesclar seja requer a fazer chamadas de serviço extra para ler a entidade preexistente do serviço, ou usando uma nova chave por propriedade, que não são adequados para questões de desempenho.

Criptografia de dados de tabela funciona da seguinte maneira:  

1. Os usuários especificar as propriedades sejam criptografados.
2. A biblioteca de cliente gera um vetor de inicialização aleatório (IV) de 16 bytes junto com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes para cada entidade e executa a criptografia do envelope nas propriedades individuais sejam criptografados criando um novo IV por propriedade. A propriedade criptografada é armazenada como dados binários.
3. O CEK quebrado e alguns metadados de criptografia adicional, em seguida, são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém informações sobre IV, versão e chave quebrado. A propriedade segundo reservada (_ClientEncryptionMetadata2) é uma propriedade binária que contém informações sobre as propriedades que estão criptografados. As informações nesta propriedade segunda (_ClientEncryptionMetadata2) são criptografada.
4. Devido a essas propriedades reservadas adicionais necessárias para criptografia, os usuários agora podem ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser menor que 1 MB.

Observe que somente as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades devem ser criptografados, eles devem ser convertidos em cadeias de caracteres. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias, e eles são convertidos em volta cadeias depois da descriptografia.

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades sejam criptografados. Isso pode ser feito especificando qualquer um atributo [EncryptProperty] (para entidades POCO que deriva de TableEntity) ou uma resolução de criptografia nas opções de solicitação. Uma resolução de criptografia é um representante que utiliza uma chave de partição, chave de linha e nome da propriedade e retorna um booliano que indica se a propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada ao gravar para a conexão. O representante também fornece a possibilidade de lógica em torno de como propriedades são criptografadas. (Por exemplo, se X, então criptografar A propriedade; caso contrário, criptografar propriedades A e B.) Observe que não é necessário fornecer estas informações ao ler ou consultando entidades.

### <a name="batch-operations"></a>Operações em lotes

Em operações de lote, a mesma KEK será usado em todas as linhas de operação lote porque a biblioteca de cliente permite apenas um objeto de opções (e, portanto, uma política/KEK) por operação em lote. No entanto, a biblioteca de cliente internamente gerará um novo IV aleatório e CEK aleatório por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação no lote definindo esse comportamento no solucionador de criptografia.

### <a name="queries"></a>Consultas

Para executar operações de consulta, você deverá especificar uma resolução de chave que seja capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um provedor, a biblioteca de cliente irá acionar um erro. Para qualquer consulta que realiza projeções de servidor, a biblioteca de cliente adicionará as propriedades de metadados de criptografia especial (_ClientEncryptionMetadata1 e _ClientEncryptionMetadata2) por padrão para as colunas selecionadas.

## <a name="azure-key-vault"></a>Azure cofre chave

Azure Cofre de chave ajuda a proteger chaves de criptografia e senhas usadas por aplicativos de nuvem e serviços. Usando o Azure chave cofre, os usuários podem criptografar chaves e senhas (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados. Arquivos PFX e senhas) usando teclas protegidos por módulos de segurança de hardware (HSMs). Para obter mais informações, consulte [o que é Azure chave cofre?](../key-vault/key-vault-whatis.md).

A biblioteca de cliente de armazenamento usa a biblioteca de núcleo de chave cofre para fornecer uma estrutura comum entre Azure de gerenciamento de chaves. Os usuários também obtêm o benefício adicional de usar a biblioteca de extensões de chave cofre. A biblioteca de extensões fornece funcionalidade útil em torno simple e simétrico/RSA local e provedores de chave de nuvem, bem como com agregação e armazenamento em cache.

### <a name="interface-and-dependencies"></a>Interface e dependências

Há três pacotes de Cofre de chave:

- Microsoft.Azure.KeyVault.Core contém a IKey e IKeyResolver. É um pequeno pacote sem nenhuma dependência. A biblioteca de cliente de armazenamento para .NET define como uma dependência.
- Microsoft.Azure.KeyVault contém o cliente de chave cofre restante.
- Microsoft.Azure.KeyVault.Extensions contém o código de extensão que inclui implementações de algoritmos e um RSAKey e um SymmetricKey. Ele depende os namespaces Core e KeyVault e fornece funcionalidade para definir uma resolução de agregação (quando os usuários querer usar vários provedores de chave) e uma resolução de chave cache. Embora a biblioteca de cliente do armazenamento não diretamente dependem desse pacote, se os usuários desejam usar Cofre de chave do Azure para armazenar suas chaves ou usar as extensões de chave cofre para consumir o local e na nuvem provedores de criptografia, eles precisam este pacote.

Chave cofre destina-se a chaves mestre de alto valor e limites de otimização por chave cofre foram projetados com isso em mente. Ao executar a criptografia do lado do cliente com chave cofre, o modelo preferencial é usar chaves mestre simétricas armazenados como segredos em chave cofre e armazenados em cache localmente. Os usuários devem fazer o seguinte:

1. Crie um segredo offline e carregue-chave cofre.
2. Use o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para criptografia e essas informações localmente em cache. Use CachingKeyResolver para armazenamento em cache; os usuários não deverão implementar seus próprios lógica de cache.
3. Use o Solucionador de cache como entrada ao criar a política de criptografia.

Obter mais informações sobre o uso do Cofre de chave podem ser encontradas nos [exemplos de código de criptografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Práticas recomendadas

Suporte de criptografia está disponível apenas na biblioteca de cliente do armazenamento para .NET. Tempo de execução do Windows e do Windows Phone não suportamos criptografia.

>[AZURE.IMPORTANT] Lembre-se das seguintes pontos importantes ao usar a criptografia do lado do cliente:
>
>- Ao ler ou gravar um blob criptografado, use comandos de carregamento do blob inteiro e comandos de download de blob do intervalo/inteiro. Evitar gravar em um blob criptografado usando o protocolo operações, tais como colocar bloco, colocar a lista de bloqueios, escrever páginas, páginas de limpar ou acrescentar bloco; Caso contrário, você pode danificar blob criptografado e torná-lo ilegível.
>- Para tabelas, uma restrição semelhante existe. Tenha cuidado para não atualizar propriedades criptografadas sem atualizar os metadados de criptografia.
>- Se você definir metadados em blob criptografado, você pode substituir os metadados relacionados à criptografia necessários para descriptografia, desde que a configuração de metadados não é aditivo. Isso também é verdadeiro para instantâneos; Evite especificar metadados durante a criação de um instantâneo de um blob criptografado. Se metadados devem ser definido, certifique-se chamar o método **FetchAttributes** primeiro para obter os metadados de criptografia atuais e evitar gravações simultâneas enquanto metadados está sendo definido.
>- Ative a propriedade **RequireEncryption** nas opções de solicitação de padrão para usuários que devem só funcionam com dados criptografados. Veja abaixo para obter mais informações.


## <a name="client-api--interface"></a>API do cliente / Interface

Ao criar um objeto de EncryptionPolicy, os usuários podem fornecer somente uma chave (Implementando IKey), somente uma resolução (Implementando IKeyResolver) ou ambas. IKey é o tipo de chave básico que é identificado usando um identificador de chave e que fornece a lógica para disposição/desempacotamento. IKeyResolver é usado para resolver uma tecla durante o processo de descriptografia. Ele define um método de ResolveKey que retorna um IKey recebe um identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias teclas que são gerenciadas em vários locais.

- Para criptografia, a chave é usada sempre e ausência de uma chave resultará em um erro.
- Para descriptografia:
    - O Solucionador de chave é invocado se especificada para obter a chave. Se a resolução for especificada, mas não tem um mapeamento para o identificador de chave, um erro é lançado.
    - Se a resolução não for especificada, mas uma chave for especificada, a chave é usada se seu identificador coincidir com o identificador de chave necessário. Se o identificador não corresponder, será gerado um erro.

As [amostras de criptografia](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) demonstram um cenário de ponta a ponta mais detalhado para blobs, filas e tabelas, juntamente com integração de chave cofre.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption

Os usuários podem ativar opcionalmente um modo de operação onde todos os uploads e downloads devem ser criptografados. Nesse modo, tentativas de dados sem uma política de criptografia de carregar ou baixar dados que não estão criptografados no serviço falhará no cliente. A propriedade **RequireEncryption** do objeto de opções de solicitação controla esse comportamento. Se seu aplicativo irá criptografar todos os objetos armazenados em armazenamento Azure, você pode definir a propriedade **RequireEncryption** sobre as opções de solicitação padrão para o objeto de cliente do serviço. Por exemplo, defina **CloudBlobClient.DefaultRequestOptions.RequireEncryption** como **true** para exigir criptografia para todas as operações de blob executadas por meio de objeto cliente.

### <a name="blob-service-encryption"></a>Criptografia de serviço de blob

Criar um objeto de **BlobEncryptionPolicy** e defini-lo nas opções de solicitação (por API ou em um nível de cliente usando **DefaultRequestOptions**). Todo o resto será tratado pela biblioteca cliente internamente.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Upload the encrypted contents to the blob.
    blob.UploadFromStream(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    MemoryStream outputStream = new MemoryStream();
    blob.DownloadToStream(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Criptografia de serviço de fila

Criar um objeto de **QueueEncryptionPolicy** e defini-lo nas opções de solicitação (por API ou em um nível de cliente usando **DefaultRequestOptions**). Todo o resto será tratado pela biblioteca cliente internamente.


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
    queue.AddMessage(message, null, null, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### <a name="table-service-encryption"></a>Criptografia do serviço de tabela

Além de criar uma política de criptografia e defini-la em Opções de solicitação, você deverá especificar um **EncryptionResolver** em **TableRequestOptions**, ou definir o atributo [EncryptProperty] na entidade.

#### <a name="using-the-resolver"></a>Usando a resolução


    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    {
        EncryptionResolver = (pk, rk, propName) =>
        {
            if (propName == "foo")
            {
                return true;
            }
            return false;
        },
        EncryptionPolicy = policy
    };

    // Insert Entity
    currentTable.Execute(TableOperation.Insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    {
        EncryptionPolicy = policy
    };

    TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
    TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### <a name="using-attributes"></a>Usando atributos

Conforme mencionado acima, se a entidade implementa TableEntity, as propriedades podem ser decoradas com o atributo [EncryptProperty] em vez de especificando o **EncryptionResolver**.

    [EncryptProperty]
    public string EncryptedProperty1 { get; set; }

## <a name="encryption-and-performance"></a>Criptografia e desempenho

Observe que criptografar seu armazenamento de dados resulta em sobrecarga adicional de desempenho. A chave de conteúdo e IV devem ser geradas, o próprio conteúdo deve estar criptografado e metadados adicionais devem ser formatados e carregados. Essa sobrecarga variará dependendo da quantidade de dados sendo criptografados. Recomendamos que os clientes sempre testar seus aplicativos para o desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criptografar e descriptografar blobs no armazenamento do Microsoft Azure usando Cofre de chave do Azure](storage-encrypt-decrypt-blobs-key-vault.md)
- Baixar a [Biblioteca de cliente de armazenamento do Azure para .NET NuGet pacote](https://www.nuget.org/packages/WindowsAzure.Storage)
- Baixar os pacotes do Azure chave cofre NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [cliente](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/)e [extensões](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
- Visite a [chave do Azure cofre documentação](../key-vault/key-vault-whatis.md)

