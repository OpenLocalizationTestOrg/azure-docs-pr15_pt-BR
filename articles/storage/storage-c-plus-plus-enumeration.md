<properties
    pageTitle="Lista de recursos de armazenamento do Azure com a biblioteca de cliente de armazenamento do Microsoft Azure para C++ | Microsoft Azure"
    description="Saiba como usar a listagem APIs na biblioteca do cliente de armazenamento do Microsoft Azure para C++ enumerar contêineres, blobs, filas, tabelas e entidades."
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>Recursos de armazenamento do Azure de lista em C++

Operações de listagem são fundamentais para muitos cenários de desenvolvimento com o armazenamento do Azure. Este artigo descreve como enumerar os objetos em armazenamento Azure usando a listagem APIs fornecidas na biblioteca de cliente de armazenamento do Microsoft Azure para C++ com mais eficiência.

>[AZURE.NOTE] Este guia se destina a biblioteca de cliente de armazenamento do Azure para C++ versão 2. x, que está disponível por meio do [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

A biblioteca de cliente do armazenamento fornece uma variedade de métodos para objetos de lista ou consulta no armazenamento do Azure. Este artigo aborda os cenários a seguir:

-   Contêineres de lista em uma conta
-   Blobs de lista em um contêiner ou diretório virtual blob
-   Filas de lista em uma conta
-   Lista de tabelas em uma conta
-   Entidades de consulta em uma tabela

Cada um desses métodos é mostrada usando sobrecargas diferentes para cenários diferentes.

## <a name="asynchronous-versus-synchronous"></a>Assíncrona versus síncrono

Como a biblioteca de cliente de armazenamento para C++ se baseia na parte superior de [biblioteca C++ REST](https://github.com/Microsoft/cpprestsdk), naturalmente suportamos operações assíncronas usando [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Operações síncronas quebrar as operações assíncronas correspondentes:

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Se você estiver trabalhando com vários threads aplicativos ou serviços, recomendamos que você usar o assíncrono APIs diretamente, em vez de criar um segmento para chamar a sincronização APIs, que afeta significativamente o seu desempenho.

## <a name="segmented-listing"></a>Listagem segmentada

A escala de armazenamento em nuvem requer listagem segmentada. Por exemplo, você pode ter sobre blobs de um milhão em um contêiner de blob do Microsoft Azure ou sobre um bilhões entidades em uma tabela do Azure. Esses não são números teóricos, mas casos de uso de clientes reais.

Portanto, não é prático para listar todos os objetos em uma única resposta. Em vez disso, você pode listar objetos usando paginação. Cada listagem APIs tem um *segmentado* sobrecarga.

A resposta para uma operação de listagem segmentado inclui:

-   <i>_segment</i>, que contém o conjunto de resultados retornados para uma única chamada para a API de listagem.
-   *continuation_token*, que é passado para a próxima chamada para obter a próxima página de resultados. Quando não houver nenhuma mais resultados para retornar, o token de continuação é nulo.

Por exemplo, uma chamada típica para listar todos os blobs em um contêiner pode parecer com o trecho de código a seguir. O código está disponível em nossos [exemplos](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Observe que o número de resultados retornados em uma página pode ser controlado pelo parâmetro *max_results* na sobrecarga de cada API, por exemplo:

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Se você não especificar o parâmetro *max_results* , o valor máximo padrão de até 5000 resultados será retornado em uma única página.

Observe também que uma consulta contra o armazenamento de tabela do Azure pode retornar sem registros ou menos registros que o valor do parâmetro *max_results* especificado, mesmo se o token de continuação não está vazio. Um motivo pode ser que a consulta não foi possível concluir em cinco segundos. Como o token de continuação não estiver vazio, a consulta deve continuar e seu código não pense que o tamanho dos resultados de segmento.

O padrão de codificação recomendado para a maioria dos cenários é segmentado listando, que fornece o andamento explícito de listagem ou consultando e como o serviço responde a cada solicitação. Particularmente para aplicativos C++ ou serviços, controle de baixo nível do progresso da listagem pode ajudar memória de controle e desempenho.

## <a name="greedy-listing"></a>Listagem greedy

Versões anteriores da biblioteca de cliente de armazenamento para C++ (visualizar versões 0.5.0 e anteriores) incluído não segmentado listagem APIs tabelas e filas, como no exemplo seguinte:

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Esses métodos foram implementados como conteúdos adicionais de APIs segmentado. Para cada resposta de listagem segmentada, o código acrescentado os resultados a um vetor e retornado todos os resultados após os contêineres completos foram digitalizados.

Essa abordagem pode funcionar quando a conta de armazenamento ou tabela contém um pequeno número de objetos. No entanto, com um aumento no número de objetos, a memória necessária poderia aumentar sem limite, porque todos os resultados permanecem na memória. Uma operação de listagem pode levar muito tempo, durante o qual o chamador não tivesse nenhuma informação sobre seu progresso.

Estas listagem greedy APIs no SDK não existe no c#, Java ou o ambiente JavaScript Node. Para evitar possíveis problemas de como usar essas APIs greedy, removemos-las na versão 0.6.0 visualização.

Se seu código chama esses APIs greedy:

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

Em seguida, você deve modificar o código para usar a listagem segmentada APIs:

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

Especificando o parâmetro *max_results* do segmento, você pode equilíbrio entre os números de solicitações e o uso de memória para atender às considerações de desempenho para o seu aplicativo.

Além disso, se você estiver usando o listagem segmentada APIs, mas armazena os dados em um conjunto de local em um estilo "cavalo", também recomendamos que você refatorar seu código para manipular o armazenamento de dados em um conjunto de local cuidadosamente em escala.

## <a name="lazy-listing"></a>Listagem preguiça

Embora listagem greedy elevado possíveis problemas, é conveniente se não houver muitos objetos no contêiner.

Se você também estiver usando c# ou Oracle Java SDKs, você deve estar familiarizado com o modelo de programação Enumerable, que oferece um preguiça-estilo listando, onde os dados em um determinado deslocamento são procurados somente se for necessário. No C++, o modelo baseado em iterador também fornece uma abordagem semelhante.

Uma listagem preguiça típica API, usando **list_blobs** como exemplo, tem esta aparência:

    list_blob_item_iterator list_blobs() const;

Um trecho de código típico que utiliza o padrão de listagem preguiça pode ter esta aparência:

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Observe que a listagem preguiça só está disponível no modo síncrono.

Comparado com listagem greedy, listagem preguiça busca dados somente quando necessário. Nos bastidores, ele busca dados do armazenamento do Azure somente quando o iterador próxima move para o próximo segmento. Portanto, o uso da memória é controlado com um tamanho limitado e a operação é rápida.

Listagem preguiça APIs estão incluídas na biblioteca de cliente do armazenamento para C++ na versão 2.2.0.

## <a name="conclusion"></a>Conclusão

Neste artigo, discutimos diferentes overloads para listar APIs para vários objetos na biblioteca de cliente do armazenamento para C++. Para resumir:

-   Async APIs é altamente recomendável em vários threads cenários.
-   Listagem segmentada é recomendada para a maioria dos cenários.
-   Listagem preguiça é fornecida na biblioteca como uma conveniente embalagem em cenários síncronos.
-   Listagem greedy não é recomendada e foi removida da biblioteca.

##<a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o armazenamento do Azure e biblioteca cliente para C++, consulte os seguintes recursos.

-   [Como usar o armazenamento de Blob do C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Como usar o armazenamento de tabela do C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Como usar o armazenamento de fila do C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Biblioteca de cliente de armazenamento Azure para a documentação da API C++.](http://azure.github.io/azure-storage-cpp/)
-   [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
