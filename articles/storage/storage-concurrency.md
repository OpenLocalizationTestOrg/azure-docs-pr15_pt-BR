<properties
    pageTitle="Gerenciando concorrência no armazenamento do Microsoft Azure"
    description="Como gerenciar concorrência para os serviços do Blob, fila, tabela e arquivo"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerenciando concorrência no armazenamento do Microsoft Azure

## <a name="overview"></a>Visão geral

Aplicativos de Internet com base modernos geralmente têm vários usuários exibir e atualizar dados simultaneamente. Isso exige que os desenvolvedores de aplicativo pensar cuidadosamente como fornecer uma experiência previsível aos seus usuários finais, particularmente para cenários em que vários usuários podem atualizar os mesmos dados. Há três estratégias de concorrência de dados principal desenvolvedores consideraremos normalmente:  


1.  Concorrência otimista – última um aplicativo executando uma atualização como parte de sua atualização verificará se os dados foram alterados desde o aplicativo ler esses dados. Por exemplo, se dois usuários exibindo uma página wiki fizer uma atualização para a mesma página a plataforma wiki deve garantir que a segunda atualização não substitui a primeira atualização – e que ambos os usuários entendam se a atualização foi bem-sucedida ou não. Essa estratégia é usada com mais frequência em aplicativos da web.
2.  Concorrência pessimista – um aplicativo olhando para executar uma atualização levará um bloqueio em um objeto impedindo que outros usuários atualizando os dados até que o bloqueio seja lançado. Por exemplo, em um cenário de replicação de dados mestre/escravo onde apenas o mestre executará as atualizações no mestre normalmente conterá um bloqueio exclusivo por um longo período de tempo nos dados para garantir que ninguém pode atualizá-la.
3.  Último autor vence – uma abordagem que permite que as operações de atualização prosseguir sem verificar se qualquer outro aplicativo atualizou os dados desde o aplicativo primeiro ler os dados. Essa estratégia (ou falta de uma estratégia formal) é geralmente usada onde os dados são particionados de forma que não há nenhum probabilidade que vários usuários acessarão os mesmos dados. Ele também pode ser útil em que fluxos de dados de curta duração estão sendo processados.  

Este artigo fornece uma visão geral de como a plataforma de armazenamento do Azure simplifica o desenvolvimento, fornecendo suporte de primeira classe para todos os três dessas estratégias de concorrência.  

## <a name="azure-storage--simplifies-cloud-development"></a>Armazenamento do Azure – simplifica o desenvolvimento de nuvem
O serviço de armazenamento do Azure suporta todos os três estratégias, embora seja distinto em sua capacidade de oferecem suporte completo para concorrência otimista e pessimista porque ela foi projetada para adotar um modelo de consistência fortes que garante que, quando o serviço de armazenamento confirma uma inserção de dados ou operação de atualização todos os acessos ainda mais a que dados verá a atualização mais recente. Plataformas de armazenamento que usam um modelo de consistência eventual tem um tempo de latência entre quando uma gravação é executada por um usuário e quando os dados atualizados podem ser vistos por outros usuários, portanto, dificultar desenvolvimento de aplicativos cliente para impedir inconsistências que afetam usuários finais.  

Além de selecionar uma estratégia de concorrência apropriado desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola alterações – particularmente alterações ao mesmo objeto em transações. O serviço de armazenamento do Azure usa isolamento de instantâneo para permitir operações de leitura a acontecer simultaneamente com operações de gravação em uma única partição. Ao contrário de outros níveis de isolamento, isolamento de instantâneo garante que todas as leituras vejam um instantâneo consistente dos dados, mesmo se atualizações estão ocorrendo – essencialmente retornando os últimos valores confirmados durante uma atualização transação estiver sendo processada.  

## <a name="managing-concurrency-in-blob-storage"></a>Gerenciando concorrência no armazenamento de Blob
Você pode optar por usar qualquer um dos modelos de concorrência otimista ou Pessimista para gerenciar o acesso aos blobs e contêineres no serviço blob. Se você não especificar explicitamente um wins gravações da última estratégia é o padrão.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Concorrência otimista para blobs e contêineres  

O serviço de armazenamento atribui um identificador para cada objeto armazenado. Esse identificador é atualizado sempre que uma operação de atualização é executada em um objeto. O identificador é retornado para o cliente como parte de uma resposta HTTP GET usando o cabeçalho ETag (marca de entidade) que é definido dentro do protocolo HTTP. Um usuário executar uma atualização em tal objeto pode enviar na ETag original juntamente com um cabeçalho condicional para garantir que uma atualização somente ocorrerá se uma determinada condição foi atendida – nesse caso, a condição é um cabeçalho de "If-correspondência" que requer o serviço de armazenamento garantir que o valor da ETag especificado na solicitação de atualização é a mesma que armazenadas no serviço de armazenamento.  

A estrutura de tópicos desse processo é a seguinte:  

1.  Recuperar um blob do serviço de armazenamento, a resposta inclui um valor de cabeçalho de ETag HTTP que identifica a versão atual do objeto no serviço de armazenamento.
2.  Quando você atualiza o blob, inclua o valor de ETag recebido na etapa 1 no cabeçalho condicional **If-correspondência** da solicitação de que enviar para o serviço.
3.  O serviço compara o valor de ETag na solicitação com o valor de ETag atual do blob.
4.  Se o valor de ETag atual do blob é uma versão diferente a ETag no cabeçalho na solicitação de condicional **If-correspondência** , o serviço retorna um erro 412 ao cliente. Isso indica ao cliente que outro processo atualizou o blob desde que o cliente de recuperá-la.
5.  Se o valor de ETag atual do blob é a mesma versão que a ETag no cabeçalho na solicitação de condicional **If-correspondência** , o serviço executa a operação solicitada e atualiza o valor de ETag atual do blob para mostrar que ele tenha criado uma nova versão.  

O seguinte c# trecho (usando a biblioteca de armazenamento do cliente 4.2.0) mostra um simples exemplo de como construir uma **Correspondência se AccessCondition** baseado no valor ETag acessado das propriedades de um blob que foi anteriormente recuperados ou inserido. Ele usa o objeto **AccessCondition** quando ele atualizando o blob: o objeto **AccessCondition** adiciona o cabeçalho **If-correspondência** à solicitação. Se outro processo tiver atualizado o blob, o serviço de blob retorna uma mensagem de status de HTTP 412 (pré falha). Você pode baixar o exemplo completo aqui: [Gerenciando concorrência usando o armazenamento do Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

O serviço de armazenamento também inclui suporte para cabeçalhos condicionais adicionais, como **Se-modificado-desde**, **If-inalterado-desde** e **Se-nenhum-correspondência** , bem como combinações dele. Para obter mais informações, consulte [Especificar cabeçalhos condicional Blob para operações de serviço](http://msdn.microsoft.com/library/azure/dd179371.aspx) no MSDN.  

A tabela a seguir resume as operações de contêiner que aceitar cabeçalhos condicionais como **Se-correspondência** na solicitação e que retornam um valor de ETag na resposta.  

| Operação                | Retorna o valor de ETag contêiner | Aceita cabeçalhos condicionais |
|:-------------------------|:-----------------------------|:----------------------------|
| Criar contêiner         | Sim                          | Não                          |
| Obter propriedades de contêiner | Sim                          | Não                          |
| Obter metadados de contêiner   | Sim                          | Não                          |
| Definir metadados de contêiner   | Sim                          | Sim                         |
| Obtenha o contêiner ACL        | Sim                          | Não                          |
| Definir o contêiner ACL        | Sim                          | Sim (*)                     |
| Excluir contêiner         | Não                           | Sim                         |
| Contêiner de concessão          | Sim                          | Sim                         |
| Blobs de lista               | Não                           | Não                          |

(*) As permissões definidas pelo SetContainerACL são armazenadas em cache e atualizações essas permissões levam 30 segundos para se propagarem durante qual período atualizações não garante sejam consistentes.  

A tabela a seguir resume as operações de blob que aceitar cabeçalhos condicionais como **Se-correspondência** na solicitação e que retornam um valor de ETag na resposta.

| Operação           | Retorna o valor de ETag | Aceita cabeçalhos condicionais           |
|:--------------------|:-------------------|:--------------------------------------|
| Colocar Blob            | Sim                | Sim                                   |
| Obter Blob            | Sim                | Sim                                   |
| Obter propriedades de Blob | Sim                | Sim                                   |
| Definir propriedades de Blob | Sim                | Sim                                   |
| Obter metadados de Blob   | Sim                | Sim                                   |
| Definir metadados de Blob   | Sim                | Sim                                   |
| Concessão Blob (*)      | Sim                | Sim                                   |
| Blob de instantâneo       | Sim                | Sim                                   |
| Copiar Blob           | Sim                | Sim (para blob de origem e destino) |
| Anular Blob de cópia     | Não                 | Não                                    |
| Excluir Blob         | Não                 | Sim                                   |
| Colocar o bloco           | Não                 | Não                                    |
| Colocar a lista de bloqueios      | Sim                | Sim                                   |
| Obter lista de bloco      | Sim                | Não                                    |
| Colocar a página            | Sim                | Sim                                   |
| Obter intervalos de página     | Sim                | Sim                                   |


(*) Concessão Blob não altera a ETag em um blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Concorrência pessimista para blobs
Para bloquear um blob para uso exclusivo, você pode adquirir uma [concessão](http://msdn.microsoft.com/library/azure/ee691972.aspx) nele. Quando você adquire uma concessão, você especificar por quanto tempo você precisa a concessão: isso pode ser para entre 15 a 60 segundos ou infinito que equivale a um bloqueio exclusivo. Você pode renovar uma concessão finita para estendê-lo e, em seguida, você pode solte qualquer concessão quando tiver terminado com ele. O serviço de blob automaticamente libera concessões finitos quando eles expiram.  

Concessões habilitar estratégias de sincronização diferentes a serem suportados, incluindo escrita exclusivo / compartilhado Gravação de leitura, exclusiva / exclusivo ler e compartilhadas gravação / ler exclusivo. Onde existe uma concessão o serviço de armazenamento impõe exclusivo gravações (colocar, definir e excluir operações) Entretanto garantir exclusividade para operações de leitura requer o desenvolvedor garantir que todos os aplicativos clientes usem um ID de concessão e que apenas um cliente por vez tem uma ID de concessão válida. Operações de leitura que não inclua um resultado de ID de concessão na leituras compartilhadas.  

O trecho c# a seguir mostra um exemplo de adquirir uma concessão exclusiva por 30 segundos em um blob, atualizando o conteúdo do blob e soltando a concessão. Se já houver uma concessão válida no blob quando você tenta adquirir uma nova concessão, o serviço de blob retorna um resultado de status "Conflito HTTP (409)". O trecho a seguir usa um objeto **AccessCondition** encapsular as informações de concessão quando faz uma solicitação para atualizar o blob no serviço de armazenamento.  Você pode baixar o exemplo completo aqui: [Gerenciando concorrência usando o armazenamento do Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Se você tentar uma operação de gravação em um blob concedida sem passando a identificação de concessão, a solicitação falhará com um erro 412. Observe que se a concessão expirar antes de chamar o método **UploadText** , mas você ainda passa a ID de concessão, a solicitação também falhar com um erro de **412** . Para obter mais informações sobre como gerenciar tempos de expiração de concessão e ids de concessão, consulte a documentação do resto de [Concessão Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) .  

As seguintes operações de blob podem usar concessões para gerenciar concorrência pessimista:  


-   Colocar Blob
-   Obter Blob
-   Obter propriedades de Blob
-   Definir propriedades de Blob
-   Obter metadados de Blob
-   Definir metadados de Blob
-   Excluir Blob
-   Colocar o bloco
-   Colocar a lista de bloqueios
-   Obter lista de bloco
-   Colocar a página
-   Obter intervalos de página
-   Instantâneo Blob - ID de concessão opcional se existe uma concessão
-   Copiar Blob - concessão ID necessário se uma concessão existe em blob de destino
-   Anular copiar Blob - ID de concessão obrigatório se uma concessão infinita existe no blob de destino
-   Concessão Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Concorrência pessimista para contêineres
Concessões em contêineres habilitar as mesmas estratégias de sincronização tem suporte como em blobs (exclusivo escrever / compartilhado Gravação de leitura, exclusiva / exclusivo ler e compartilhadas gravação / ler exclusivo) Entretanto diferentemente blobs o serviço de armazenamento somente impõe exclusividade em operações de exclusão. Para excluir um contêiner com uma concessão ativa, um cliente deve incluir o ID de concessão ativa com a solicitação de exclusão. Todas as outras operações de contêiner bem-sucedida em um contêiner concedido sem incluir a ID de concessão caso em que eles são compartilhados operações. Se exclusividade de atualização (colocar ou conjunto) ou operações de leitura é necessária, em seguida, os desenvolvedores devem garantir que todos os clientes usem um ID de concessão e que somente um cliente por vez tem uma ID de concessão válida.  

As seguintes operações de contêiner podem usar concessões para gerenciar concorrência pessimista:  

-   Excluir contêiner
-   Obter propriedades de contêiner
-   Obter metadados de contêiner
-   Definir metadados de contêiner
-   Obtenha o contêiner ACL
-   Definir o contêiner ACL
-   Contêiner de concessão  

Para obter mais informações, consulte:  

- [Especificando cabeçalhos condicionais para operações de serviço de Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Contêiner de concessão](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Concessão Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gerenciando concorrência no serviço de tabela
O serviço de tabela usa otimista de concorrência verifica como o comportamento padrão quando você estiver trabalhando com entidades, ao contrário de serviço blob onde você deve explicitamente escolher executar verificações de concorrência otimista. A outra diferença entre os serviços de blob e tabela é que você só pode gerenciar o comportamento de concorrência de entidades enquanto com o serviço de blob, você pode gerenciar a conformidade da contêineres e blobs.  

Usar concorrência otimista e verifique se outro processo modificado uma entidade desde que você recuperou-lo do serviço de armazenamento de tabela, você pode usar o valor de ETag que recebe quando o serviço de tabela retorna uma entidade. A estrutura de tópicos desse processo é a seguinte:  

1.  Recuperar uma entidade do serviço de armazenamento de tabela, a resposta inclui um valor de ETag que identifica o identificador atual associado a essa entidade no serviço de armazenamento.
2.  Quando você atualiza a entidade, inclua o valor de ETag recebido na etapa 1 no cabeçalho **If-correspondência** obrigatório da solicitação de que enviar para o serviço.
3.  O serviço compara o valor de ETag na solicitação com o valor de ETag atual da entidade.
4.  Se o valor de ETag atual da entidade for diferente a ETag no cabeçalho **If-correspondência** obrigatório na solicitação, o serviço retorna um erro 412 ao cliente. Isso indica ao cliente que outro processo atualizou a entidade desde que o cliente de recuperá-la.
5.  Se o valor de ETag atual da entidade é a mesma que a ETag no cabeçalho **If-correspondência** obrigatório na solicitação ou o cabeçalho **If-correspondência** contém o caractere curinga (*), o serviço executa a operação solicitada e atualiza o valor de ETag atual da entidade para mostrar que ele foi atualizado.  

Observe que, ao contrário de serviço blob, o serviço de tabela requer o cliente incluir um cabeçalho **If-correspondência** em solicitações de atualização. Entretanto, é possível forçar uma incondicional atualizar (último gravador wins estratégia) e ignorar verificações de concorrência se o cliente define o cabeçalho **If-correspondência** para o caractere curinga (*) na solicitação.  

O trecho c# a seguir mostra uma entidade de cliente que anteriormente foi criada ou recuperados tendo o endereço de email atualizado. A inicial inserir ou recuperar operação armazena o valor de ETag no objeto do cliente e porque a amostra usa a mesma instância do objeto quando ele executa a operação de substituição, ele envia automaticamente o valor de ETag volta para o serviço de tabela, permitindo que o serviço verificar violações de concorrência. Se outro processo tiver atualizado a entidade em armazenamento de tabela, o serviço retorna uma mensagem de status de HTTP 412 (pré falha).  Você pode baixar o exemplo completo aqui: [Gerenciando concorrência usando o armazenamento do Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Para explicitamente desativar a verificação de concorrência, você deve definir a propriedade de **ETag** do objeto **funcionário** para "*" antes de executar a operação de substituição.  

    customer.ETag = "*";  

A tabela a seguir resume como as operações de tabela de entidade usam ETag valores:

| Operação                | Retorna o valor de ETag | Requer o cabeçalho de solicitação If-correspondência |
|:-------------------------|:-------------------|:---------------------------------|
| Entidades de consulta           | Sim                | Não                               |
| Inserir entidade            | Sim                | Não                               |
| Atualizar entidade            | Sim                | Sim                              |
| Mesclar entidade             | Sim                | Sim                              |
| Excluir entidade            | Não                 | Sim                              |
| Inserir ou substituir entidade | Sim                | Não                               |
| Inserir ou entidade de mesclagem   | Sim                | Não                               |

Observe que as operações de **Inserir ou substituir entidade** e **Inserir ou entidade mesclar** realizar *não* executar verificações de concorrência porque eles não enviar um valor de ETag no serviço de tabela.  

Em geral, os desenvolvedores usando tabelas devem confiar em concorrência otimista ao desenvolver aplicativos scalable. Se for necessário pessimista, desenvolvedores de uma abordagem podem levar ao acessar tabelas é atribuir um blob designado para cada tabela e tentar realizar uma concessão em blob antes do operando na tabela. Essa abordagem requerem o aplicativo para garantir que todos os caminhos de acesso de dados obtém a concessão antes operando na tabela. Observe também que o tempo de concessão mínimo é de 15 segundos que requer atenção para escalabilidade.  

Para obter mais informações, consulte:  

- [Operações em entidades](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gerenciando concorrência no serviço de fila
Um cenário que no qual concorrência é um problema no serviço fila é onde vários clientes estiver recuperando mensagens de uma fila. Quando uma mensagem é recuperada da fila, a resposta inclui a mensagem e um valor de recebimento pop, que é necessário para excluir a mensagem. A mensagem não é excluída automaticamente da fila, mas após terem sido recuperado, não é visível para os outros clientes para o intervalo de tempo especificado pelo parâmetro visibilitytimeout. O cliente que recupera a mensagem é esperado para excluir a mensagem depois que ela foi processada, e antes da hora especificada pela TimeNextVisible elemento de resposta, que é calculada com base no valor do parâmetro visibilitytimeout. O valor de visibilitytimeout é adicionado para a hora em que a mensagem é recuperada para determinar o valor de TimeNextVisible.  

O serviço de fila não tem suporte para concorrência otimista ou pessimista e para isso clientes motivo processando mensagens recuperadas de uma fila devem garantir as mensagens são processadas de maneira idempotente. Uma estratégia de wins última gravador é usada para operações de atualização como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

- [API do resto de serviço de fila](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Receber mensagens](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gerenciando concorrência no serviço de arquivo
O serviço de arquivos pode ser acessado usando dois protocolo diferente pontos de extremidade – SMB e restante. O serviço REST não tem suporte para bloqueio otimista ou pessimista e todas as atualizações seguirá uma estratégia de wins gravador última. Clientes SMB que montar compartilhamentos de arquivos podem aproveitar mecanismos de bloqueio de sistema de arquivo para gerenciar o acesso a arquivos compartilhados – incluindo a capacidade de executar pessimista. Quando um cliente SMB abre um arquivo, especifica o acesso ao arquivo e o compartilhamento modo. Definir uma opção de acesso a arquivos de "Escrever" ou "Leitura/gravação" juntamente com um modo de compartilhamento de arquivo de "Nenhuma" resultará no arquivo sendo bloqueado por um cliente SMB até que o arquivo é fechado. Se você tentar operação restante em um arquivo onde um cliente SMB tem o arquivo bloqueado o serviço REST retornará o código de status 409 (conflito) com código de erro SharingViolation.  

Quando um cliente SMB abre um arquivo para excluir, ele marca o arquivo como pendentes excluir até que todas as outra cliente SMB identificadores abertos nesse arquivo sejam fechados. Enquanto um arquivo está marcado como com exclusão pendente, qualquer operação de REST nesse arquivo retornará o código de status 409 (conflito) com código de erro SMBDeletePending. Código de status 404 (não encontrado) não é retornado como é possível para o cliente SMB remover o sinalizador de exclusão pendente antes de fechar o arquivo. Em outras palavras, o código de status 404 (não encontrado) somente é esperado, quando o arquivo foi removido. Observe que enquanto um arquivo está em um SMB pendentes excluir estado, ele não será incluído nos resultados da lista de arquivos. Observe também que as operações restante excluir arquivo e o restante Delete Directory são confirmadas atomicamente e não resultam em exclusão pendente.  

Para obter mais informações, consulte:  

- [Gerenciamento de arquivo bloqueia](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Resumo e próximas etapas
O serviço de armazenamento do Microsoft Azure foi projetado para atender às necessidades dos aplicativos on-line mais complexos sem forçar os desenvolvedores comprometem ou pensar novamente suposições de design chave como consistência concorrência e os dados que eles passaram a fazer para concedido.  

Para o aplicativo de exemplo completos referenciado neste blog:  

- [Gerenciando concorrência usando o armazenamento do Azure - aplicativo de amostra](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações sobre consulte de armazenamento do Azure:  

- [Página inicial de armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introdução ao armazenamento do Azure](storage-introduction.md)
- Guia de Introdução para [Blob](storage-dotnet-how-to-use-blobs.md), [tabela](storage-dotnet-how-to-use-tables.md), [filas](storage-dotnet-how-to-use-queues.md)e [arquivos](storage-dotnet-how-to-use-files.md) de armazenamento
- Arquitetura de armazenamento – [armazenamento do Azure: um serviço de armazenamento de nuvem altamente disponível com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
