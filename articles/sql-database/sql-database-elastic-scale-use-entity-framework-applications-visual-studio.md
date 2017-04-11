<properties 
    pageTitle="Usando a biblioteca de cliente do banco de dados elástica com estrutura de entidades | Microsoft Azure" 
    description="Usar biblioteca de cliente do banco de dados elástica e estrutura de entidade para bancos de dados de codificação" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Biblioteca de cliente elástica do banco de dados com estrutura de entidades 
 
Este documento mostra as alterações em um aplicativo de estrutura de entidade que são necessários para integrar com as [Ferramentas de banco de dados elástica](sql-database-elastic-scale-introduction.md). O foco está em redigir [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md) e [roteamento de dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) com a estrutura de entidade **Código primeira** abordagem. O tutorial [Code First – novo banco de dados](http://msdn.microsoft.com/data/jj193542.aspx) para EF serve como exemplo de execução neste documento. O código de exemplo que acompanha este documento é parte das ferramentas de banco de dados elástica conjunto de amostras nos exemplos de Visual Studio código.
  
## <a name="downloading-and-running-the-sample-code"></a>Baixar e executar o código de amostra
Para baixar o código deste artigo:

* Visual Studio 2012 ou posterior é necessário. 
* Inicie o Visual Studio. 
* No Visual Studio, selecione Arquivo -> Novo projeto. 
* Na caixa de diálogo 'Novo projeto', navegue até o **Amostras Online** para **Visual c#** e tipo "db Elástico" na caixa Pesquisar no canto superior direito.
    
    ![Estrutura de entidade e aplicativo de amostra elástica banco de dados][1] 

    Selecione a amostra chamada **Elástica ferramentas DB para SQL Azure – integração de Framework de entidade**. Após aceitar a licença, a amostra é carregado. 

Para executar o exemplo, você precisa criar três bancos de dados vazios no banco de dados do Azure SQL:

* Banco de dados do fragmentar Gerenciador de mapa
* Banco de dados de fragmentar 1
* Banco de dados de fragmentar 2

Depois de ter criado esses bancos de dados, preencha os espaços reservados para no **Program.cs** com o nome do seu servidor de banco de dados de SQL Azure, os nomes de banco de dados e suas credenciais para se conectar aos bancos de dados. Crie a solução no Visual Studio. Visual Studio será baixado os NuGet pacotes necessários para a biblioteca de cliente do banco de dados elástica, entidade Framework e falhas temporárias manipulando como parte do processo de compilação. Certifique-se de que a restauração de pacotes do NuGet está ativado para sua solução. Você pode habilitar essa configuração clicando no arquivo de solução no Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho de estrutura de entidades 

Desenvolvedores de estrutura de entidade confiam em um dos seguintes quatro fluxos de trabalho para criar aplicativos e para garantir a persistência para objetos de aplicativo: 

* **Code First (novo banco de dados)**: desenvolvedor EF o cria o modelo no código do aplicativo e, em seguida, o EF gera o banco de dados dele. 
* **Code First (banco de dados existente)**: O desenvolvedor permite EF gerar o código do aplicativo para o modelo de banco de dados existente.
* **Modelo primeiro**: O desenvolvedor cria o modelo no designer EF e EF cria o banco de dados do modelo.
* **Primeiro banco de dados**: O desenvolvedor usa EF ferramentas para inferir o modelo de banco de dados existente. 

Todas essas abordagens dependem da classe DbContext transparente gerenciar conexões de banco de dados e esquema de banco de dados para um aplicativo. Como abordaremos com mais detalhes posteriormente no documento, construtores diferentes na classe base DbContext permitem para diferentes níveis de controle sobre a criação de conexão, o criação de inicialização e o esquema de banco de dados. Os desafios surgir principalmente do fato de que o gerenciamento de conexão de banco de dados fornecido pelo EF cruza com os recursos de gerenciamento de conexão as dados dependentes de interfaces de roteamento fornecidas pela biblioteca cliente elástica banco de dados. 

## <a name="elastic-database-tools-assumptions"></a>Pressuposições de ferramentas de banco de dados elástica 

Para definições de termos, consulte [Glossário de ferramentas de banco de dados elástica](sql-database-elastic-scale-glossary.md).

Biblioteca de cliente do banco de dados elástica, você define partições dos seus dados de aplicativo chamados shardlets. Shardlets são identificadas por uma chave de fragmentação e são mapeados para bancos de dados específicos. Um aplicativo pode ter tantos bancos de dados conforme necessário e distribuir o shardlets para fornecer suficiente capacidade ou desempenho fornecidas atuais requisitos de negócios. O mapeamento de valores-chave fragmentação aos bancos de dados é armazenado por um mapa de fragmentar fornecido pelo cliente do banco de dados elástica APIs. Chamamos esse recurso **Gerenciamento de mapa fragmentar**ou SMM abreviada. O mapa de fragmentar também serve como o corretor de conexões de banco de dados para solicitações que contêm uma chave de fragmentação. Nós Consulte esse recurso como **Roteamento dependentes de dados**. 
 
O Gerenciador de mapa fragmentar protege os usuários dos modos de exibição inconsistentes nos dados de shardlet que podem ocorrer quando operações de gerenciamento de shardlet simultâneas (como realocação de dados de um fragmentar para outro) estão ocorrendo. Para fazer isso, os mapas de fragmentar gerenciadas pelo agente biblioteca cliente as conexões de banco de dados para um aplicativo. Isso permite a funcionalidade de mapa fragmentar para eliminar automaticamente uma conexão de banco de dados quando operações de gerenciamento de fragmentar poderão afetar o shardlet que foi criado para a conexão. Essa abordagem precisa integrar com algumas das funcionalidades do EF, como criando novas conexões a partir de uma existente para verificar a existência de banco de dados. Em geral, o nossa Observação foi que só funcionam confiavelmente para conexões de banco de dados fechado que podem ser duplicados com segurança para EF DbContext construtores padrão funcionam. O princípio de design do banco de dados Elástico em vez disso, é só atuar como intermediário de conexões abertas. Alguém pode pensar que fechar uma conexão orientado pela biblioteca cliente antes concedendo para o EF DbContext pode resolver esse problema. No entanto, fechando a conexão e depender EF para abri-lo novamente, um foregoes as verificações de validação e consistência realizadas pela biblioteca. A funcionalidade de migrações no EF, no entanto, usa essas conexões para gerenciar o esquema de banco de dados subjacente de uma maneira que seja transparente para o aplicativo. Ideal seria podemos gostaria de reter e combinar todos esses recursos da biblioteca de cliente do banco de dados elástica tanto EF no mesmo aplicativo. A seção a seguir descreve essas propriedades e requisitos mais detalhadamente. 


## <a name="requirements"></a>Requisitos 

Ao trabalhar com a biblioteca de cliente do banco de dados elástica e a entidade Framework APIs, queremos reter as seguintes propriedades: 

* **Dimensionamento**: Adicionar ou remover bancos de dados da camada de dados do aplicativo sharded conforme necessário para as exigências de capacidade do aplicativo. Isso significa controle sobre as APIs do Gerenciador de gerenciar bancos de dados e mapeamentos de shardlets de mapear a criação e a exclusão de bancos de dados e usando o fragmentar elástica banco de dados. 

* **Consistência**: O aplicativo utiliza fragmentação e usa os recursos de roteamento dependentes de dados da biblioteca cliente. Para evitar corrupção ou resultados de consulta errada, conexões são orientados por meio do Gerenciador de mapa fragmentar. Isso também retém validação e consistência.
 
* **Code First**: para reter a conveniência do paradigma de primeiro de código do EF. No código primeiro, classes no aplicativo estão mapeadas transparente as estruturas de banco de dados subjacentes. O código do aplicativo interage com DbSets que máscara maioria dos aspectos envolvidas no processamento do banco de dados subjacente.
 
* **Esquema**: entidade Framework lida com a criação de esquema de banco de dados inicial e evolução de esquema subsequentes por meio de migrações. Guardando esses recursos, adaptar seu aplicativo é fácil à medida que os dados evoluem. 

As seguintes diretrizes instrui como atender a esses requisitos para aplicativos Code First usando ferramentas de banco de dados elástica. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Dados dependentes de roteamento usando EF DbContext 

Conexões de banco de dados com estrutura de entidade normalmente são gerenciadas por meio de subclasses de **DbContext**. Crie esses subclasses por **DbContext**. Isso é onde você define o **DbSets** que implementar as coleções de banco de dados copiado dos objetos CLR do aplicativo. No contexto de roteamento dependentes de dados, podemos identificar várias propriedades úteis que não necessariamente segurar para outros cenários de aplicativo EF código primeiro: 

* O banco de dados já existe e tiver sido registrado no mapa fragmentar elástica banco de dados. 
* O esquema do aplicativo já foi implantado no banco de dados (explicado abaixo). 
* Conexões de roteamento de dependentes de dados no banco de dados são orientado por mapa fragmentar. 

Para integrar **DbContexts** com dependentes de dados roteamento para escala-out:

1. Criar conexões de banco de dados físico através das interfaces de cliente do banco de dados elástica do gerente fragmentar mapa, 
2. Quebrar a conexão com a subclasse **DbContext**
3. Passe a conexão para baixo para as classes base **DbContext** para garantir que todo o processamento do lado EF acontece também. 

O exemplo de código a seguir ilustra essa abordagem. (Esse código também é no projeto do Visual Studio que acompanha)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Principais pontos
* Um construtor novos substitui o construtor de padrão na subclasse DbContext 
* O novo construtor utiliza os argumentos que são necessários para o roteamento dependentes de dados por meio de biblioteca de cliente do banco de dados elástica:
    * o mapa de fragmentar para acessar as interfaces de roteamento dependentes de dados,
    * a chave de fragmentação para identificar o shardlet,
    * uma cadeia de conexão com as credenciais para a conexão de roteamento dependentes de dados para o fragmentar. 
 
* A chamada para o construtor de classe base leva um desvio em um método estático que executa todas as etapas necessárias para o roteamento de dependentes de dados. 
   * Ele usa a chamada de OpenConnectionForKey das interfaces de cliente do banco de dados elástica no mapa fragmentar para estabelecer uma conexão aberta.
   * O mapa de fragmentar cria a conexão aberta para o fragmentar que detém o shardlet para a chave de fragmentação determinado.
   * Essa conexão aberta é passado para o construtor de classe base da DbContext para indicar que essa conexão é a ser usado pelo EF em vez de deixar EF criar uma nova conexão automaticamente. Dessa maneira a conexão foi marcada pelo cliente de banco de dados elástica API para que ele pode garantir a consistência em operações de gerenciamento de mapa fragmentar.
 
  
Use o construtor new para sua subclasse DbContext em vez do construtor de padrão em seu código. Aqui está um exemplo: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

O construtor new abre a conexão para a fragmentar que mantém os dados para o shardlet identificado pelo valor de **tenantid1**. O código no bloco **usando** permanece inalterado para acessar o **DbSet** para blogs usando EF na fragmentar para **tenantid1**. Isso altera semântica para o código no usando bloquear, de forma que todas as operações de banco de dados agora têm como escopo o um fragmentar onde **tenantid1** é mantido. Por exemplo, uma consulta LINQ sobre blogs **DbSet** somente retorna blogs armazenado na fragmentar atual, mas não os arquivos armazenados em outros fragmentos.  

#### <a name="transient-faults-handling"></a>Manipulação de falhas temporárias
A equipe do Microsoft Patterns & Practices publicado o [O temporárias falhas manipulação de bloco de aplicativo](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é usada com a biblioteca de cliente de dimensionamento Elástico em combinação com EF. No entanto, certifique-se de que qualquer exceção temporária retorna para um local onde podemos garantir que o novo construtor está sendo usado após uma falha temporária para que qualquer nova tentativa de conexão é feita usando os construtores que podemos ter ajustado. Caso contrário, não há garantia de uma conexão para o fragmentar correto e não há nenhuma garantias que a conexão é mantida quando ocorrem alterações para o mapa de fragmentar. 

O exemplo de código a seguir ilustra como uma política de repetição SQL pode ser usada em torno os construtores de subclasse **DbContext** novos: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetição de 10 e 5 segundos tempo de espera entre as tentativas. Essa abordagem é semelhante para a orientação de EF e transações iniciado pelo usuário (consulte [limitações com repetindo estratégias de execução (EF6 em diante)](http://msdn.microsoft.com/data/dn307226). Ambas as situações exigem que o programa aplicativo controla o escopo ao qual a exceção temporária retorna: a reabrir a transação ou (conforme mostrado) recriar o contexto do construtor adequado que usa a biblioteca de cliente elástica banco de dados.

A necessidade de controlar onde exceções temporárias demorar de volta ao escopo também impede o uso de interno **SqlAzureExecutionStrategy** que vem com EF. **SqlAzureExecutionStrategy** usaria reabrir uma conexão, mas não **OpenConnectionForKey** e, portanto, ignorar a validação que é executada como parte da chamada **OpenConnectionForKey** . Em vez disso, o código de exemplo usa o interno **DefaultExecutionStrategy** que também vem com EF. Em vez de **SqlAzureExecutionStrategy**, funciona corretamente em combinação com a política de repetição de tratamento de falhas temporárias. A política de execução é definida na classe **ElasticScaleDbConfiguration** . Observe que podemos decidiu não usar **DefaultSqlExecutionStrategy** desde que ele sugere para usar **SqlAzureExecutionStrategy** se exceções temporárias ocorrem - que levaria comportamento errado conforme discutido. Para obter mais informações sobre as políticas de repetição diferente e EF, consulte [Resiliência de Conexão em EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Revisões de construtor
Os exemplos de código acima ilustram o padrão construtor novamente grava necessário para seu aplicativo para usar dados dependentes de roteamento com a estrutura de entidade. A tabela a seguir generaliza essa abordagem para outros construtores. 


Construtor atual  | Construtor reconfigurado para dados | Construtor base | Anotações
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A conexão precisa ser uma função de mapa fragmentar e a chave de roteamento dependentes de dados. Você precisa criação de conexão automática de desviar por EF e usa o mapa de fragmentar para atuar como intermediário a conexão. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |A conexão é uma função de mapa fragmentar e a chave de roteamento dependentes de dados. Uma cadeia de caracteres de nome ou conexão de banco de dados fixa não funcionará como eles validação desviar pelo mapa do fragmentar. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A conexão será obter criado para a chave de mapa e fragmentação fragmentar fornecido com o modelo fornecido. O modelo compilado será passado para o c'tor base.
MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |A conexão precisa ser inferida do mapa fragmentar e a chave. Ele não pode ser fornecido como entrada (a menos que essa entrada já estava usando o mapa de fragmentar e a chave). O booliano será repassado. 
MyContext (cadeia, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |A conexão precisa ser inferida do mapa fragmentar e a chave. Ele não pode ser fornecido como entrada (a menos que essa entrada estava usando o mapa de fragmentar e a chave). O modelo compilado será repassado. 
MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |O construtor new precisa garantir que qualquer conexão no ObjectContext passado como entrada é redirecionado para uma conexão gerenciada pelo dimensionamento Elástico. Uma discussão detalhada de ObjectContexts está além do escopo deste documento.
MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool)| DbContext (DbConnection, DbCompiledModel, bool); |A conexão precisa ser inferida do mapa fragmentar e a chave. A conexão não pode ser fornecida como entrada (a menos que essa entrada já estava usando o mapa de fragmentar e a chave). Modelo e booliano são passados para o construtor de classe base. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implantação de esquema fragmentar por meio de migrações de EF 

Gerenciamento de esquema automática é uma conveniência fornecida pela estrutura de entidades. No contexto de aplicativos usando ferramentas de banco de dados elástica, queremos manter esse recurso para provisionar automaticamente o esquema para fragmentos recém-criado quando bancos de dados são adicionados ao aplicativo sharded. O caso de uso principal é aumentar a capacidade na camada de dados para aplicativos sharded usando EF. Confiar em recursos do EF para gerenciamento de esquema reduz o esforço de administração do banco de dados com um aplicativo sharded criado no EF. 

Implantação de esquema por meio de migrações de EF funciona melhor em **conexões não abertas**. Isso está em contraste com o cenário para dados dependentes roteamento que depende da conexão aberta fornecido pelo cliente do banco de dados elástica API. Outra diferença é o requisito de consistência: enquanto desejável para garantir a consistência para todas as conexões de roteamento de dependentes de dados para se proteger contra manipulação de mapa fragmentar simultâneas, não é um problema com implantação de esquema inicial para um novo banco de dados que tenha ainda não é registrado no mapa fragmentar e ainda não foi alocado para armazenar shardlets. Portanto, podemos pode depender conexões de banco de dados regular para este cenários, em vez de roteamento dependentes de dados.  

Isso leva a uma abordagem onde implantação de esquema por meio de migrações de EF está associada com o registro do novo banco de dados como uma fragmentar no mapa de fragmentar do aplicativo. Isso depende os seguintes pré-requisitos: 

* O banco de dados já foi criado. 
* O banco de dados está vazio – mantém nenhum esquema de usuário e nenhum dado de usuário.
* O banco de dados ainda não pode ser acessado por meio do cliente de banco de dados elástica APIs para o roteamento de dependentes de dados. 

Com estes pré-requisitos no lugar, podemos criar um cancelamento aberta regulares **SqlConnection** disparar migrações de EF para implantação do esquema. O exemplo de código a seguir ilustra essa abordagem. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

Este exemplo mostra o método **RegisterNewShard** que registra o fragmentar no mapa fragmentar, implanta o esquema por meio de migrações de EF e armazena um mapeamento de uma chave de fragmentação para o fragmentar. Ele se baseia em um construtor da subclasse **DbContext** (**ElasticScaleContext** na amostra) que leva uma cadeia de conexão SQL como entrada. O código desse construtor é direta, como mostra o exemplo a seguir: 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
Um pode ter usado a versão do construtor herdada da classe base. Mas o código precisa garantir que o inicializador de padrão de EF é usado durante a conexão. Portanto, o short de volta para o método estático antes de chamar o construtor de classe base com a cadeia de conexão. Observe que o registro de fragmentos deve ser executado em um domínio de outro aplicativo ou processo para garantir que as configurações do inicializador de EF não entram em conflito. 


## <a name="limitations"></a>Limitações 

As abordagens descritas neste documento envolvem algumas limitações: 

* Aplicativos de EF que usam **LocalDb** primeiro precisam migrar um banco de dados do SQL Server normal antes de usar biblioteca de cliente do banco de dados elástica. Dimensionamento de um aplicativo por meio de fragmentação com escala elástica não é possível com **LocalDb**. Observe que o desenvolvimento ainda pode usar **LocalDb**. 

* Alterações para o aplicativo que sugerem alterações de esquema de banco de dados precisam passar pelo migrações de EF em todos os fragmentos. O código de exemplo para este documento não demonstram como fazer isso. Considere o uso de banco de dados de atualização com um parâmetro ConnectionString para iteração em todos os fragmentos; ou extrair o script T-SQL para a migração pendente usando banco de dados de atualização com o – Script opção e aplicar o script T-SQL para seus fragmentos.  

* Considerando uma solicitação, será considerado que todo seu processamento de banco de dados está contida em um único fragmentar como identificado pela chave de fragmentação fornecida pela solicitação. No entanto, essa suposição não sempre verdadeiras. Por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente fornece a classe **MultiShardQuery** que implementa uma abstração de conexão para consultar sobre vários fragmentos. Aprender a usar o **MultiShardQuery** em combinação com EF está além do escopo deste documento

## <a name="conclusion"></a>Conclusão

As etapas descritas neste documento, aplicativos de EF podem usar o recurso da biblioteca do banco de dados elástica cliente roteamento de dados dependentes por refatoração construtores das subclasses **DbContext** usadas no aplicativo EF. Isso limita as alterações necessárias para esses locais onde **DbContext** classes já existem. Além disso, aplicativos de EF podem continuar a vantagem de implantação de esquema automático combinando as etapas que invocar as migrações EF necessárias com o registro de novos fragmentos e mapeamentos no mapa fragmentar. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 