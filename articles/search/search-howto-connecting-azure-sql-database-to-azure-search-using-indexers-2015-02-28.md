<properties 
    pageTitle="Conexão de banco de dados do SQL Azure com pesquisa Azure usando indexadores | Microsoft Azure | Indexadores" 
    description="Saiba como extrair dados da Azure SQL Database em um índice de pesquisa do Azure usando indexadores." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="05/26/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Conexão Azure SQL Database para pesquisa de Azure usando indexadores

Serviço de pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que torna mais fácil fornecer uma experiência de pesquisa excelentes. Antes de pesquisar, você precisa preencher um índice de pesquisa do Azure com seus dados. Se os dados residem em um banco de dados do SQL Azure, o novo **indexador de pesquisa do Azure para Azure SQL Database** (ou **SQL Azure indexador** abreviada) na pesquisa do Azure poderá automatizar o processo de indexação. Isso significa que você tem menos código para escrever e menos infraestrutura para me preocupar com.

Atualmente, indexadores só funcionam com o Azure SQL Database, SQL Server em VMs do Azure e [DocumentDB do Azure](../documentdb/documentdb-search-indexer.md). Neste artigo, nos concentraremos na indexadores que funcionam com o Azure SQL Database. Se você quiser ver o suporte para fontes de dados adicionais, forneça seus comentários sobre o [Fórum de comentários de pesquisa do Azure](https://feedback.azure.com/forums/263029-azure-search/).

Este artigo abordará mecânica do uso de indexadores, mas podemos será também fazer busca detalhada em recursos e comportamentos que só estão disponíveis com bancos de dados do SQL (por exemplo, integrados controle de alterações).

## <a name="indexers-and-data-sources"></a>Indexadores e fontes de dados

Para instalar e configurar um indexador do SQL Azure, você pode chamar a [API de REST de pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkID=528173) para criar e gerenciar **indexadores** e **fontes de dados**. 

Você também pode usar a [classe de indexador](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) no [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)ou Assistente Importar dados no [portal do Azure](https://portal.azure.com) para criar e agendar um indexador.

Uma **fonte de dados** Especifica os dados que serão indexar, credenciais necessárias para acessar os dados e as políticas que habilitar a pesquisa do Azure com eficiência identificar alterações nos dados (novas, modificadas ou excluídas linhas). Ele é definido como um recurso independente para que ele pode ser usado por vários indexadores.

Um **indexador** é um recurso que se conecta a fontes de dados com os índices de pesquisa de destino. Um indexador é usado das seguintes maneiras:
 
- Execute uma única cópia dos dados para preencher um índice.
- Atualize um índice com alterações na fonte de dados em uma agenda.
- Execute sob demanda para atualizar um índice, conforme necessário. 

## <a name="when-to-use-azure-sql-indexer"></a>Quando usar indexador do SQL Azure

Dependendo de vários fatores relacionados aos seus dados, o uso de indexador do SQL Azure talvez ou pode não ser adequado. Se seus dados atender aos seguintes requisitos, você pode usar indexador do SQL Azure: 

- Todos os dados provenientes de uma única tabela ou modo de exibição
    - Se os dados é distribuídos em várias tabelas, você pode criar um modo de exibição e use esse modo de exibição com o indexador. No entanto, lembre-se de que se você usar um modo de exibição, você não conseguirá usar a detecção de alteração integrada do SQL Server. Consulte esta seção para obter mais detalhes. 
- Tipos de dados usados na fonte de dados são suportados pelo indexador. Quase todos os de SQL tipos são suportados. Para obter detalhes, consulte [mapeamento de tipos de dados na pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Você não precisa perto atualizações em tempo real ao índice quando uma linha é alterada. 
    - O indexador pode reindexar sua tabela no máximo a cada 5 minutos. Se os dados forem alterados com frequência e as alterações necessárias sejam refletidas no índice em segundos ou minutos único, recomendamos usando [API de índice de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) diretamente. 
- Se você tiver um conjunto de dados grande e planeja executar o indexador em uma agenda, seu esquema nos permite identificar com eficiência alterado (e excluído, se aplicável) linhas. Para obter mais detalhes, consulte "Capturando alterado e excluídas linhas" abaixo. 
- O tamanho dos campos indexados em uma linha não excede o tamanho máximo de uma pesquisa do Azure indexação solicitação, que é 16 MB. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Criar e usar um indexador do SQL Azure

Primeiro, crie a fonte de dados: 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Você pode obter a cadeia de conexão a partir do [Portal clássico do Azure](https://portal.azure.com); Use o `ADO.NET connection string` opção.

Em seguida, crie o índice de pesquisa do Azure de destino se você não tenha um. Você pode fazer isso a partir do [portal da interface do usuário](https://portal.azure.com) , ou usando a [API do índice de criar](https://msdn.microsoft.com/library/azure/dn798941.aspx).  Certifique-se de que o esquema de seu índice de destino é compatível com o esquema da tabela de origem - consulte [mapeamento entre SQL e Azure tipos de dados de pesquisa](#TypeMapping) para obter detalhes.

Finalmente, crie o indexador dar a ele um nome e referenciando o índice de origem e destino de dados:

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Um indexador criado dessa maneira não tem um cronograma. Ele é executado automaticamente uma vez assim que ele é criado. Você pode executá-lo novamente a qualquer momento usando uma solicitação de **Executar indexador** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Você pode personalizar vários aspectos de comportamento de indexador, como o tamanho do lote e podem ser ignorados quantos documentos antes de uma execução de indexador resultado será a falha. Para obter mais detalhes, consulte [Criar API do indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Talvez você precise permitir que os serviços do Azure para se conectar ao seu banco de dados. Consulte [Conectando do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para obter instruções sobre como fazer isso.

Para monitorar o status do indexador e a execução histórico (número de itens indexados, falhas, etc.), use uma solicitação de **status do indexador** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

A resposta deve ser semelhante ao seguinte: 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

Histórico de execução contém até 50 das execuções concluídas mais recentemente, que são classificadas em ordem cronológica inversa (de forma que a execução mais recente vier primeira na resposta). Informações adicionais sobre a resposta podem ser encontradas na [Get Status de indexador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores em um cronograma

Você também pode organizar o indexador para executar periodicamente em um cronograma. Para fazer isso, basta adicione a propriedade de **agendamento** ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação de colocar para atualizar o indexador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O parâmetro de **intervalo** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções indexador consecutivas. O menor intervalo permitido é 5 minutos; maior é um dia. Ele deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto restrito de um valor de [duração de ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

A **hora de início** de opcional indica quando as execuções agendadas devem iniciar; Se ele for omitido, a hora UTC atual será usada. Desta vez pode ser no passado – nesse caso a primeira execução será agendado como se o indexador está sendo executado continuamente desde a hora de início.  

Apenas uma execução de um determinado indexador pode ser executados por vez. Se um indexador já está em execução quando o outro deve para começar, a execução é ignorada e currículos no próximo intervalo, supondo que nenhum outro trabalho indexador está em execução.

Vamos considerar um exemplo para tornar isso mais concreto. Suponha que nós o seguinte agendamento por hora configurado: 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Veja aqui o que acontece: 

1. A primeira execução do indexador começa em ou ao redor de 1 de março de 2015, 12:00 a.m. UTC.
1. Suponha que essa execução leva 20 minutos (ou a qualquer momento menor que 1 hora).
1. A segunda execução começa em ou ao redor de 1 de março de 2015, 1:00 a.m. 
1. Agora, vamos supor que essa execução leva mais de uma hora (Isso requer um grande número de documentos para isso realmente ocorrer, mas é uma ilustração úteis) – por exemplo, 70 minutos – para que ele seja concluído em torno de 2:10 a.m.
1. Agora é hora de 2:00 a.m., para a terceira execução iniciar. No entanto, pois a segunda execução de 1h ainda está sendo executado, a execução de terceira será ignorada. A terceira execução inicia às 3.

Você pode adicionar, alterar ou excluir uma agenda para um indexador existente usando uma solicitação de **colocar indexador** . 

## <a name="capturing-new-changed-and-deleted-rows"></a>Captura novos, alterados e linhas excluídas

Se você estiver usando um cronograma e sua tabela contém um número incomum de linhas, você deve usar uma política de detecção de alteração de dados, para que o indexador com eficiência possa recuperar somente as linhas novas ou alteradas sem precisar reindexar em toda a tabela.

### <a name="sql-integrated-change-tracking-policy"></a>SQL integrada política de controle de alterações

Se seu banco de dados SQL tem suporte para [o controle de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos o uso de **SQL integrado alterar controle de política**. Essa política permite que o controle de alterações mais eficiente e também permite a pesquisa do Azure para identificar linhas excluídas sem precisar adicionar uma coluna de explícita "excluir suave" à sua tabela.

Controle de alterações integrada é suportada começando com as seguintes versões de banco de dados do SQL Server:
 
- SQL Server 2008 R2 e mais tarde, se você estiver usando o SQL Server em VMs do Azure. 
- Azure SQL banco de dados V12, se você estiver usando o Azure SQL Database.

Quando usando política, o controle de alterações SQL integrado não especificar uma política de detecção de exclusão de dados separado - esta política possui suporte interno para identificar linhas excluídas.

Essa política só pode ser usada com tabelas; ele não pode ser usado com modos de exibição. Você precisa habilitar o controle de alterações para a tabela que você está usando antes de poder usar essa política. Para obter instruções, consulte [Habilitar e desabilitar o controle de alterações](https://msdn.microsoft.com/library/bb964713.aspx) . 

Para usar esta política, criar ou atualizar sua fonte de dados como esta:
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

### <a name="high-water-mark-change-detection-policy"></a>Política de detecção de alteração de marca d'água alta

Enquanto a política de controle de alterações integrado do SQL é recomendada, você não conseguirá usá-lo se seus dados estiverem em um modo de exibição, ou se você estiver usando uma versão mais antiga do banco de dados do SQL Azure. Nesse caso, considere o uso da política de marca d'água alta. Essa política pode ser usada se sua tabela contém uma coluna que atenda aos seguintes critérios:

- Todas as inserções especificar um valor para a coluna. 
- Todas as atualizações para um item também alteram o valor da coluna. 
- O valor desta coluna aumenta com cada alteração.
- As consultas que usam uma `WHERE` cláusula similar ao `WHERE [High Water Mark Column] > [Current High Water Mark Value]` podem ser executadas com eficiência.

Por exemplo, uma coluna indexada **rowversion** é um candidato ideal para a coluna de marca d'água alta. Para usar esta política, criar ou atualizar sua fonte de dados como esta: 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de detecção de exclusão de coluna excluir suave

Quando linhas são excluídas da tabela de origem, provavelmente você deseja excluir essas linhas do índice de pesquisa. Se você usar a política de controle de alterações SQL integrado, isso é resolvido para você. No entanto, a política de controle de alterações de marca d'água alta não ajudarão-lo com linhas excluídas. O que fazer? 

Se as linhas física são removidas da tabela, você estiver fora sorte – há nenhuma maneira inferir a presença de registros que não existem mais.  No entanto, você pode usar a técnica de "suaves-delete" para marcar linhas como logicamente excluídos sem removê-los da tabela adicionando uma coluna e linhas de marcação como excluídas usando um valor de marcador dessa coluna.

Ao usar a técnica suaves-delete, você pode especificar o suaves Excluir política da seguinte maneira ao criar ou atualizar a fonte de dados: 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

Observe que o **softDeleteMarkerValue** deve ser uma cadeia de caracteres – use a representação de cadeia de caracteres de seu valor real. Por exemplo, se você tiver uma coluna inteira onde linhas excluídas são marcadas com o valor 1, use `"1"`; Se você tiver uma coluna de BIT onde linhas excluídas são marcadas com o valor de true booliano, use `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapeamento entre os tipos de dados de pesquisa do Azure e tipos de dados SQL

|Tipo de dados SQL | Os tipos de campo permitidos índice de destino |Anotações 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| flutuação real, |Edm.Double, Edm.String | |
| smallmoney, numérico decimal dinheiro | Edm.String| Pesquisa Azure não oferece suporte para converter tipos de casas decimais em Edm.Double porque isso faria perder precisão |
| CARACT, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Transformar uma coluna de cadeia de caracteres em Collection(Edm.String) requer o uso de uma visualização API versão Preview-28-02-2015. Consulte [Este artigo](search-api-indexers-2015-02-28-preview.md#CreateIndexer) para obter detalhes| 
|smalldatetime, datetime, datetime2, data, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|Geografia | Edm.GeographyPoint | Há suporte para apenas geografia instâncias do tipo POINT com 4326 SRID (que é o padrão) | | 
|rowversion| N/D |Colunas de versão de linha não podem ser armazenadas no índice de pesquisa, mas eles podem ser usados para controle de alterações | |
| hora, período de tempo, binário, varbinary, imagem, xml, geometria, tipos CLR | N/D |Sem suporte |


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Q:** Pode usar indexador do SQL Azure com bancos de dados do SQL em execução no IaaS VMs no Azure?

R: Sim. No entanto, é preciso permitir que o serviço de pesquisa para se conectar ao banco de dados seguindo as seguintes duas coisas. Consulte o artigo [Configurar uma conexão de um indexador de pesquisa do Azure para SQL Server em uma máquina virtual do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) para obter mais informações.

1. Talvez você precise configurar o seu banco de dados com um certificado confiável para que o serviço de pesquisa pode abrir conexões SSL para o banco de dados.
2. Configure o firewall para permitir o acesso para o endereço IP do seu serviço de pesquisa.

**Q:** Pode usar indexador do SQL Azure com bancos de dados do SQL em execução no local? 

R: nós não recomendamos ou dar suporte a isso, como fazer isso exigem que você abra seus bancos de dados para o tráfego de Internet. 

**Q:** Pode usar indexador do SQL Azure com bancos de dados diferente de SQL Server em execução no IaaS no Azure? 

R: não suportamos este cenário, pois ainda não testamos o indexador com bancos de dados diferente do SQL Server.  

**Q:** Posso criar vários indexadores em execução em um cronograma? 

R: Sim. No entanto, apenas um indexador pode estar executando ao mesmo tempo em um nó. Se você precisar vários indexadores executando simultaneamente, considere a possibilidade de dimensionamento de seu serviço de pesquisa mais de uma unidade de pesquisa. 

**Q:** Executar um indexador afeta a carga de trabalho minha consulta? 

R: Sim. Indexador funciona em um de nós no seu serviço de pesquisa, e os recursos do nó são compartilhados entre indexação e servindo tráfego de consulta e outras solicitações de API. Se você executar intenso cargas de trabalho de indexação e de consulta e encontrar uma alta taxa de 503 erros ou crescente tempos de resposta, considere a possibilidade de dimensionamento o serviço de pesquisa.
