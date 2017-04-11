<properties 
    pageTitle="Usando a biblioteca de cliente do banco de dados elástica com Dapper | Microsoft Azure" 
    description="Usando a biblioteca de cliente do banco de dados elástica com Dapper." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>Usando a biblioteca de cliente do banco de dados elástica com Dapper 

Este documento é para os desenvolvedores que dependem Dapper a criação de aplicativos, mas também queira adotar [Ferramentas de banco de dados Elástico](sql-database-elastic-scale-introduction.md) para criar aplicativos que implementam fragmentação para fora de escala sua camada de dados.  Este documento ilustra alterações em aplicativos baseados em Dapper que são necessários para integrar com ferramentas de banco de dados elástica. Nosso foco é em redigir o gerenciamento de fragmentar elástica banco de dados e os dados dependentes roteamento com Dapper. 

**Código de exemplo**: [Ferramentas de banco de dados elástica para Azure SQL Database - integração Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
É fácil integrar **Dapper** e **DapperExtensions** com a biblioteca de cliente elástica banco de dados do Azure SQL Database. Seus aplicativos podem usar dados dependentes roteamento alterando a criação e abertura de novos objetos de [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) usar a chamada de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da [biblioteca de cliente](http://msdn.microsoft.com/library/azure/dn765902.aspx). Isso limita as alterações em seu aplicativo apenas quando novas conexões são criadas e abertos. 

## <a name="dapper-overview"></a>Visão geral de dapper
**Dapper** é um mapeador relacional do objeto. Ele mapeia objetos .NET de seu aplicativo em um banco de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como você pode integrar a biblioteca de cliente do banco de dados elástica com aplicativos baseados em Dapper. A segunda parte do código de exemplo ilustra como integrar ao usar Dapper e DapperExtensions.  

A funcionalidade de mapeador em Dapper fornece métodos de extensão em conexões de banco de dados que simplificam remetente instruções T-SQL para execução ou consultar o banco de dados. Por exemplo, Dapper torna mais fácil para mapear entre seus objetos .NET e os parâmetros de instruções SQL para **Executar** chamadas ou consumir os resultados de suas consultas SQL em objetos .NET usando chamadas de **consulta** de Dapper. 

Ao usar DapperExtensions, você não precisa fornecer as instruções SQL. Métodos de extensões como **GetList** ou **Inserir** sobre a conexão de banco de dados criam as instruções SQL para os bastidores.
 
Outro benefício de Dapper e também DapperExtensions é que o aplicativo controla a criação da conexão de banco de dados. Isso ajuda a interagir com a biblioteca de cliente de banco de dados elástica que agentes conexões com base no mapeamento de shardlets a bancos de dados de banco de dados.

Para obter os conjuntos de Dapper, consulte [Dapper ponto líquido](http://www.nuget.org/packages/Dapper/). Para as extensões de Dapper, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Uma rápida olhada na biblioteca de cliente elástica banco de dados

Com a biblioteca de cliente do banco de dados elástica, você define partições dos seus dados de aplicativo chamados *shardlets* , mapeá-los para bancos de dados e identificá-los por *chaves de fragmentação*. Você pode ter tantos bancos de dados como você precisa e distribuir seu shardlets entre esses bancos de dados. O mapeamento de valores-chave fragmentação aos bancos de dados é armazenado por um mapa de fragmentar fornecido por APIs da biblioteca. Esse recurso é chamado **Fragmentar mapear gerenciamento**. O mapa de fragmentar também serve como o corretor de conexões de banco de dados para solicitações que contêm uma chave de fragmentação. Esse recurso é conhecido como **Roteamento dependentes de dados**.

![Mapas de fragmentar e roteamento dependentes de dados][1]

O Gerenciador de mapa fragmentar protege os usuários dos modos de exibição inconsistentes nos dados de shardlet que podem ocorrer quando operações de gerenciamento de shardlet simultâneas estão acontecendo nos bancos de dados. Para fazer isso, os mapas de fragmentar atuar como intermediário as conexões de banco de dados para um aplicativo criado com a biblioteca. Quando as operações de gerenciamento de fragmentar poderão afetar o shardlet, isso permite a funcionalidade de mapa fragmentar para eliminar automaticamente uma conexão de banco de dados. 

Em vez de usar a maneira tradicional de criar conexões para Dapper, precisamos usar o [método OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Isso garante que todos os a validação ocorrerá e conexões são gerenciados corretamente quando os dados se desloca entre fragmentos.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração Dapper

Ao trabalhar com a biblioteca de cliente elástica banco de dados e as APIs Dapper, queremos reter as seguintes propriedades:

* **Scaleout**: queremos adicionar ou remover bancos de dados de camada de dados do aplicativo sharded conforme necessário para as exigências de capacidade do aplicativo. 

-    **Consistência**: como nosso aplicativo é dimensionado usando fragmentação, precisamos executar roteamento dependentes de dados. Queremos usar os recursos de roteamento dependentes de dados da biblioteca para fazê-lo. Em particular, queremos manter a validação e consistência garante fornecida por conexões que são orientados por meio do Gerenciador de mapa fragmentar para evitar corrupção ou resultados de consulta errado. Isso garante que conexões para um determinado shardlet são rejeitadas ou interrompidos se (por exemplo) a shardlet atualmente for movida para um fragmentar diferente usando APIs de divisão/direta.

-    **Mapeamento de objeto**: queremos manter a conveniência dos mapeamentos fornecidos pelo Dapper traduzir entre classes no aplicativo e as estruturas de banco de dados subjacentes. 

A seção a seguir fornece orientação para esses requisitos para aplicativos com base em **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Orientações técnicas
### <a name="data-dependent-routing-with-dapper"></a>Dados dependentes de roteamento com Dapper 

Com Dapper, o aplicativo é geralmente responsável pela criação e abrindo as conexões ao banco de dados subjacente. Dado um tipo T pelo aplicativo, Dapper retorna os resultados da consulta como coleções .NET do tipo T. Dapper executa o mapeamento das linhas de resultado de T-SQL aos objetos do tipo T. Da mesma forma, Dapper mapeia objetos .NET em valores SQL ou parâmetros para instruções de linguagem (DML) de manipulação de dados. Dapper oferece essa funcionalidade por meio de métodos de extensão no objeto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regular de bibliotecas do ADO .NET SQL Client. A conexão de SQL retornado pelas APIs de escala elástica para DDR também são objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regulares. Isso nos permite usar diretamente extensões Dapper sobre o tipo retornado pela API de DDR da biblioteca cliente, como também é uma conexão de SQL Client simple.

Essas observações torná-la simples usar conexões orientados pela biblioteca cliente elástica banco de dados para Dapper.

Este exemplo de código (de amostra que acompanha) ilustra a abordagem onde a chave de fragmentação é fornecida pelo aplicativo para a biblioteca para a conexão para a direita fragmentar atuar como intermediário.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

A chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API substitui a criação de padrão e a abertura de uma conexão de SQL Client. A chamada de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) leva os argumentos que são necessários para o roteamento dependentes de dados: 

-    O mapa de fragmentar para acessar as interfaces de roteamento dependentes de dados
-    A chave de fragmentação para identificar o shardlet
-    As credenciais (nome de usuário e senha) para se conectar à fragmentar

O objeto do mapa fragmentar cria uma conexão para o fragmentar que detém o shardlet para a chave de fragmentação determinado. O cliente de banco de dados elástica APIs também marca a conexão para implementar seus garantias de consistência. Desde que a chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) retorna um objeto de conexão de SQL Client regular, a chamada para o método de extensão **Execute** subsequente Dapper segue a prática Dapper padrão.

Consultas funcionam muito da mesma maneira – você primeiro abra a conexão usando [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da API do cliente. Depois que você use os métodos de extensão Dapper regulares para mapear os resultados da sua consulta SQL em objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Observe que o **usando** bloco com a conexão de DDR escopos todas as operações de banco de dados dentro do bloco para a um fragmentar onde tenantId1 é mantido. A consulta retorna somente blogs armazenado na fragmentar atual, mas não os arquivos armazenados em qualquer outros fragmentos. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Dados dependentes de roteamento com Dapper e DapperExtensions

Dapper vem com um ecossistema de extensões adicionais que podem fornecer posterior conveniência e abstração do banco de dados ao desenvolver aplicativos de banco de dados. DapperExtensions é um exemplo. 

Usar DapperExtensions em seu aplicativo não altera como conexões de banco de dados são criadas e gerenciadas. Ainda é responsabilidade do aplicativo para abrir conexões e objetos de conexão de SQL Client regulares são esperados pelos métodos de extensão. Podemos contar com a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como mostram os exemplos de código a seguir, a única alteração é que já não temos escrever as instruções T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está o código de exemplo para a consulta: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Manipulando falhas temporárias

A equipe do Microsoft Patterns & Practices publicado o [Bloco de aplicativo de tratamento de falhas temporárias](http://msdn.microsoft.com/library/hh680934.aspx) para ajudar os desenvolvedores de aplicativos a atenuar condições temporárias falhas comuns encontradas durante a execução na nuvem. Para obter mais informações, consulte [Perseverance, segredo de todos os Triumphs: usando o bloco de aplicativo de tratamento de falhas temporárias](http://msdn.microsoft.com/library/dn440719.aspx).

O código de exemplo se baseia na biblioteca de falhas temporárias para se proteger contra falhas temporárias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetição de 10 e 5 segundos tempo de espera entre as tentativas. Se você estiver usando transações, certifique-se de que seu escopo de repetir volta para o início da transação no caso de uma falha temporária.

## <a name="limitations"></a>Limitações

As abordagens descritas neste documento envolvem algumas limitações:

* O código de exemplo para este documento não Demonstre como gerenciar o esquema em fragmentos.
* Considerando uma solicitação, vamos supor que todos os seu processamento de banco de dados está contido em um único fragmentar como identificado pela chave de fragmentação fornecida pela solicitação. No entanto, essa suposição não sempre mantenha, por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, a biblioteca de cliente do banco de dados elástica inclui a [classe de MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de conexão para consultar sobre vários fragmentos. Usando MultiShardQuery em combinação com Dapper está além do escopo deste documento.

## <a name="conclusion"></a>Conclusão

Aplicativos usando Dapper e DapperExtensions podem facilmente se beneficiar ferramentas elástica banco de dados do Azure SQL Database. As etapas descritas neste documento, esses aplicativos podem usar recursos da ferramenta para dados dependentes roteamento alterando a criação e abertura de novos objetos de [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) usar a chamada de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da biblioteca cliente elástica banco de dados. Isso limita as alterações de aplicativo necessárias para esses locais onde novas conexões são criadas e abertos. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 