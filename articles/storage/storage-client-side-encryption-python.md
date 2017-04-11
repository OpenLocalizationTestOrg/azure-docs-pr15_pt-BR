<properties
    pageTitle="Criptografia do lado do cliente com Python para armazenamento do Microsoft Azure | Microsoft Azure"
    description="A biblioteca de cliente de armazenamento do Azure para Python dá suporte à criptografia do lado do cliente para segurança máxima para seus aplicativos de armazenamento do Azure."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Criptografia do lado do cliente com Python para armazenamento do Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Visão geral

A [Biblioteca de cliente de armazenamento do Azure para Python](https://pypi.python.org/pypi/azure-storage) dá suporte à criptografia de dados em aplicativos cliente antes de carregar ao armazenamento do Azure e descriptografar dados durante o download do cliente.

>[AZURE.NOTE] A biblioteca de Python de armazenamento do Azure está no modo de visualização.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Criptografia e descriptografia via a técnica de envelope
Os processos de criptografia e descriptografia siga a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Criptografia via a técnica de envelope
Criptografia via a técnica de envelope funciona da seguinte maneira:

1.  A biblioteca de cliente de armazenamento do Azure gera uma chave de criptografia do conteúdo (CEK), que é uma chave simétrica do uso de uma vez.

2.  Dados de usuário são criptografados usando este CEK.

3.  O CEK é fornecida (criptografados) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétrico ou uma chave simétrica, que é gerenciada localmente.
A biblioteca de cliente de armazenamento em si nunca tem acesso aos KEK. A biblioteca chama o algoritmo de quebra automática de chave fornecido pelo KEK. Os usuários podem optar por usar provedores personalizados para disposição/desempacotamento chave se desejado.

4.  Os dados criptografados, em seguida, são carregados para o serviço de armazenamento do Azure. A tecla quebra junto com alguns metadados adicionais de criptografia é armazenada como metadados (em um blob) ou interpolada com os dados criptografados (fila mensagens e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Descriptografia via a técnica de envelope
Descriptografia via a técnica de envelope funciona da seguinte maneira:

1.  A biblioteca de cliente pressupõe que o usuário está gerenciando a chave de criptografia de chave (KEK) localmente. O usuário não precisa saber a chave específica que foi usada para criptografia. Em vez disso, uma resolução de chave, que resolve identificadores de chave diferentes para chaves, pode ser configurar e usada.

2.  A biblioteca de cliente baixa os dados criptografados junto com qualquer material de criptografia que está armazenada no serviço.

3.  A chave de criptografia de conteúdo quebrado (CEK) é desfeita (descriptografado) usando a chave de criptografia de chave (KEK). Veja novamente, a biblioteca de cliente não tem acesso ao KEK. Ele simplesmente invoca algoritmo encapsulada do provedor personalizado.

4.  A chave de criptografia do conteúdo (CEK), em seguida, é usada para descriptografar os dados de usuário criptografadas.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia  
A biblioteca de cliente do armazenamento usa [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar dados de usuário. Especificamente, modo [Codificação bloco encadeamento CBC ()](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona um pouco diferente, para que abordaremos cada uma delas aqui.

### <a name="blobs"></a>BLOBs
A biblioteca de cliente atualmente oferece suporte a criptografia de apenas blobs inteiros. Especificamente, a criptografia é suportada quando os usuários utilizam o **criar*** métodos. Para downloads, ambas concluída e downloads de intervalo são suportados e paralelização de upload e download está disponível.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, junto com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes e executar a criptografia de envelope dos dados blob usando essas informações. O CEK quebrado e alguns metadados de criptografia adicional, em seguida, são armazenados como blob metadados juntamente com o blob criptografado no serviço.

>[AZURE.WARNING] Se você estiver editando ou carregar seus próprios metadados para o blob, você precisa garantir que esses metadados é preservado. Se você carregar novos metadados sem esses metadados, o CEK quebrado, IV e outros metadados serão perdidas e o conteúdo blob nunca será recuperável novamente.

Baixar um blob criptografado envolve a recuperar o conteúdo do blob inteiro usando o **obter*** métodos de conveniência. O CEK quebrado é aberto e usado em conjunto com o IV (armazenado como blob metadados nesse caso) para retornar os dados descriptografados aos usuários.

Baixando um intervalo aleatório (**obter*** métodos com parâmetros de intervalo passaram) no blob criptografado envolve ajustando o intervalo fornecido por usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar com êxito o intervalo solicitado.

Bloco blobs e blobs de página só podem ser criptografados/descriptografar usando este esquema. Há atualmente não há suporte para criptografia acrescentar blobs.

### <a name="queues"></a>Filas
Como mensagens de fila podem ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia do conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera uma IV aleatório de 16 bytes juntamente com um CEK aleatório de 32 bytes e executa criptografia de envelope do texto da mensagem fila usando essas informações. O CEK quebrado e alguns metadados de criptografia adicional, em seguida, são adicionados à mensagem criptografada fila. Esta mensagem modificada (mostrada abaixo) é armazenada no serviço.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Durante descriptografia, a chave quebra é extraída da mensagem fila e aberta. O IV também é extraído da mensagem fila e usado juntamente com a tecla desfeita para descriptografar os dados de mensagem de fila. Observe que os metadados de criptografia são pequeno (em 500 bytes), enquanto ele contar em relação ao limite de 64KB para uma mensagem de fila, o impacto deve ser gerenciável.

### <a name="tables"></a>Tabelas
A biblioteca de cliente oferece suporte à criptografia de propriedades de entidade para inserir e substituir operações.

>[AZURE.NOTE] Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, basta mesclando as novas propriedades e atualizando os metadados resultará em perda de dados. Mesclar seja requer a fazer chamadas de serviço extra para ler a entidade preexistente do serviço, ou usando uma nova chave por propriedade, que não são adequados para questões de desempenho.

Criptografia de dados de tabela funciona da seguinte maneira:

1.  Os usuários especificar as propriedades sejam criptografados.

2.  A biblioteca de cliente gera um vetor de inicialização aleatório (IV) de 16 bytes junto com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes para cada entidade e executa a criptografia do envelope nas propriedades individuais sejam criptografados criando um novo IV por propriedade. A propriedade criptografada é armazenada como dados binários.

3.  O CEK quebrado e alguns metadados de criptografia adicional, em seguida, são armazenados como duas propriedades reservadas adicionais. O primeiro reservado propriedade (\_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém informações sobre IV, versão e chave quebrado. O segundo reservado propriedade (\_ClientEncryptionMetadata2) é uma propriedade binária que contém informações sobre as propriedades que estão criptografados. As informações nesta propriedade segunda (\_ClientEncryptionMetadata2) é criptografada.

4.  Devido a essas propriedades reservadas adicionais necessárias para criptografia, os usuários agora podem ter apenas 250 propriedades personalizadas em vez de 252. O tamanho total da entidade deve ser menor que 1MB.

    Observe que somente as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades devem ser criptografados, eles devem ser convertidos em cadeias de caracteres. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias, e elas são convertidas novamente em cadeias de caracteres (brutas cadeias de caracteres, não EntityProperties com tipo EdmType.STRING) depois da descriptografia.

    Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades sejam criptografados. Isso pode ser feito por armazenar essas propriedades em objetos de TableEntity com o conjunto de tipo para EdmType.STRING e criptografar definido como true ou definindo a encryption_resolver_function no objeto tableservice. Uma resolução de criptografia é uma função que utiliza uma chave de partição, chave de linha e nome da propriedade e retorna um booliano que indica se a propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada ao gravar para a conexão. O representante também fornece a possibilidade de lógica em torno de como propriedades são criptografadas. (Por exemplo, se X, então criptografar A propriedade; caso contrário, criptografar propriedades A e B.) Observe que não é necessário fornecer estas informações ao ler ou consultando entidades.

### <a name="batch-operations"></a>Operações em lotes
Uma política de criptografia aplica-se a todas as linhas no lote. A biblioteca de cliente internamente irá gerar um novo IV aleatório e CEK aleatório por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação no lote definindo esse comportamento no solucionador de criptografia.
Se um lote é criado como um gerente de contexto através do método de batch() tableservice, diretiva de criptografia do tableservice será aplicada automaticamente ao lote. Se um lote é criado explicitamente chamando o construtor, a política de criptografia deve ser passada como um parâmetro e inalterada à esquerda para a vida útil do lote.
Observe que entidades são criptografadas conforme eles são inseridos em lote usando diretiva de criptografia do lote (entidades não são criptografadas no momento do confirmar o lote usando diretiva de criptografia do tableservice).

### <a name="queries"></a>Consultas
Para executar operações de consulta, você deverá especificar uma resolução de chave que seja capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não pode ser resolvida para um provedor, a biblioteca de cliente irá acionar um erro. Para qualquer consulta que realiza projeções de lado do servidor, a biblioteca de cliente adicionará as propriedades de metadados de criptografia especial (\_ClientEncryptionMetadata1 e \_ClientEncryptionMetadata2) por padrão para as colunas selecionadas.

>[AZURE.IMPORTANT] Lembre-se das seguintes pontos importantes ao usar a criptografia do lado do cliente:
>
>- Ao ler ou gravar um blob criptografado, use comandos de carregamento do blob inteiro e comandos de download de blob do intervalo/inteiro. Evitar gravar em um blob criptografado usando o protocolo operações, tais como colocar bloco, colocar a lista de bloqueios, escrever páginas ou páginas de limpar; Caso contrário, você pode danificar blob criptografado e torná-lo ilegível.
>
>- Para tabelas, uma restrição semelhante existe. Tenha cuidado para não atualizar propriedades criptografadas sem atualizar os metadados de criptografia.
>
>- Se você definir metadados em blob criptografado, você pode substituir os metadados relacionados à criptografia necessários para descriptografia, desde que a configuração de metadados não é aditivo. Isso também é verdadeiro para instantâneos; Evite especificar metadados durante a criação de um instantâneo de um blob criptografado. Se metadados devem ser definido, certifique-se chamar o método **get_blob_metadata** primeiro para obter os metadados de criptografia atuais e evitar gravações simultâneas enquanto metadados está sendo definido.
>
>- Habilite o sinalizador **require_encryption** no objeto de serviço para usuários que deve só funcionam com dados criptografados. Veja abaixo para obter mais informações.

A biblioteca de cliente do armazenamento espera a KEK fornecido e resolução de chave para implementar a interface a seguir. Suporte de [Cofre de chave do Azure](https://azure.microsoft.com/services/key-vault/) para gerenciamento de Python KEK está pendente e será integrado nessa biblioteca quando concluído.


## <a name="client-api--interface"></a>API do cliente / Interface
Depois de um objeto de serviço de armazenamento (ou seja, blockblobservice) tiver sido criado, o usuário pode atribuir valores de campos que constituem uma política de criptografia: key_encryption_key, key_resolver_function e require_encryption. Os usuários podem fornecer somente uma KEK, somente uma resolução, ou ambos. key_encryption_key é o tipo de chave básico que é identificado usando um identificador de chave e que fornece a lógica para disposição/desempacotamento. key_resolver_function é usado para resolver uma tecla durante o processo de descriptografia. Retorna uma KEK válido recebe um identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias teclas que são gerenciadas em vários locais.

O KEK deve implementar os seguintes métodos para criptografar com êxito dados:
- wrap_key(CEK): quebra o CEK especificado (bytes) usando um algoritmo de preferência do usuário. Retorna a tecla quebra.
- get_key_wrap_algorithm(): retorna o algoritmo usado para quebrar teclas.
- get_kid(): Retorna a identificação de chave de cadeia de caracteres para essa KEK.
O KEK deve implementar os seguintes métodos para com êxito descriptografar dados:
- unwrap_key (cek, algoritmo): retorna o formulário desfeito do CEK especificado usando o algoritmo de cadeia de caracteres especificado.
- get_kid(): retorna um id de chave de cadeia de caracteres para essa KEK.

O Solucionador de chave pelo menos deve implementar um método que, recebe uma id de chave, retorna o KEK correspondente Implementando a interface acima. Somente este método é a ser atribuído à propriedade key_resolver_function no objeto de serviço.

- Para criptografia, a chave é usada sempre e ausência de uma chave resultará em um erro.
- Para descriptografia:
    - O Solucionador de chave é invocado se especificada para obter a chave. Se a resolução for especificada, mas não tem um mapeamento para o identificador de chave, um erro é lançado.
    - Se a resolução não for especificada, mas uma chave for especificada, a chave é usada se seu identificador coincidir com o identificador de chave necessário. Se o identificador não corresponder, será gerado um erro.

      Os exemplos de criptografia na azure.storage.samples <fix URL>demonstram um cenário de ponta a ponta mais detalhado para blobs, filas e tabelas.
        Exemplos de implementações da KEK e resolução de chave são fornecidos nos arquivos de amostra como KeyWrapper e KeyResolver respectivamente.

### <a name="requireencryption-mode"></a>Modo de RequireEncryption
Os usuários podem ativar opcionalmente um modo de operação onde todos os uploads e downloads devem ser criptografados. Nesse modo, tentativas de dados sem uma política de criptografia de carregar ou baixar dados que não estão criptografados no serviço falhará no cliente. O sinalizador **require_encryption** no objeto de serviço controla esse comportamento.

### <a name="blob-service-encryption"></a>Criptografia de serviço de blob
Defina a criptografia campos de política no objeto blockblobservice. Todo o resto será tratado pela biblioteca cliente internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Criptografia de serviço de fila
Defina a criptografia campos de política no objeto queueservice. Todo o resto será tratado pela biblioteca cliente internamente.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Criptografia do serviço de tabela
Além de criar uma política de criptografia e defini-la em Opções de solicitação, você deve especificar um **encryption_resolver_function** sobre o **tableservice**, ou definir o atributo criptografar na EntityProperty.

### <a name="using-the-resolver"></a>Usando a resolução

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>Usando atributos
Conforme mencionado acima, uma propriedade pode ser marcada para criptografia armazená-la em um objeto de EntityProperty e definindo o campo criptografar.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Criptografia e desempenho
Observe que criptografar seu armazenamento de dados resulta em sobrecarga adicional de desempenho. A chave de conteúdo e IV devem ser geradas, deve ser criptografado próprio conteúdo e metadados adicionais devem ser formatados e carregado. Essa sobrecarga variará dependendo da quantidade de dados sendo criptografados. Recomendamos que os clientes sempre testar seus aplicativos para o desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximas etapas

- Baixar a [Biblioteca de cliente de armazenamento do Azure para pacote Java PyPi](https://pypi.python.org/pypi/azure-storage)
- Baixar o [código do GitHub de fonte de biblioteca de cliente de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)
