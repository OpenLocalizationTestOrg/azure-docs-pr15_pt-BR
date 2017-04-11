<properties 
    pageTitle="Introdução às ferramentas de banco de dados elástica" 
    description="Explicação básica do recurso de ferramentas de banco de dados elástica do Azure SQL Database, incluindo fácil para executar o aplicativo de amostra." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Introdução às ferramentas de banco de dados elástica

Este documento apresenta a experiência do desenvolvedor executando o aplicativo de amostra. O exemplo cria um aplicativo de sharded simples e explora principais recursos das ferramentas de banco de dados elástica. O exemplo demonstra funções da [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md)

Para instalar a biblioteca, vá para [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Observe que a biblioteca é instalada com o aplicativo de amostra descrito a seguir.

## <a name="prerequisites"></a>Pré-requisitos

1. Visual Studio 2012 ou superior com c# é necessário. Baixe uma versão gratuita em [Downloads do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 ou superior. Para obter a versão mais recente, consulte [Instalar o NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Baixar e executar o aplicativo de amostra

O **elástica banco de dados com SQL Azure — Introdução** aplicativo de amostra ilustra os aspectos mais importantes da experiência de desenvolvimento para aplicativos sharded usando ferramentas de banco de dados elástica do SQL Azure. Ele se concentra nos principais casos de uso para [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md), [dados dependentes roteamento](sql-database-elastic-scale-data-dependent-routing.md) e [vários fragmentar consultando](sql-database-elastic-scale-multishard-querying.md). Para baixar e executar o exemplo, siga estas etapas: 

1. Abra o Visual Studio e selecione **arquivo -> Novo -> projeto**.
2. Na caixa de diálogo, clique em **Online**.

    ![Novo projeto > Online][2]
3. Em seguida, clique em **Visual c#** em **exemplos**.

    ![Clique em Visual C#][3]
4. Na caixa de pesquisa, digite **db elástica** para pesquisar a amostra. O título **Elástica ferramentas DB para SQL Azure - Introdução** aparece.

    ![Caixa de pesquisa][1]
 
5. Selecione a amostra, escolha um nome e um local para o novo projeto e pressione **Okey** para criar o projeto.
6. Abra o arquivo **App** na solução para o projeto de amostra e siga as instruções no arquivo para adicionar seu nome de servidor de banco de dados do SQL Azure e suas informações de logon (nome de usuário e senha).
7. Criar e executar o aplicativo. Quando for solicitado, permita que o Visual Studio restaurar os pacotes NuGet da solução. Isso irá baixar a versão mais recente da biblioteca cliente elástica banco de dados do NuGet.
8. Reproduzir com diferentes opções para saber mais sobre os recursos de biblioteca de cliente. Observe as etapas que o aplicativo leva no console de saída e fique à vontade para explorar o código nos bastidores.

    ![andamento][4]

Parabéns – você tiver criado com êxito e executar seu primeiro aplicativo sharded usando ferramentas de banco de dados elástica no Azure SQL Database. Dê uma olhada rápida os fragmentos que criou a amostra conectando-se com o Visual Studio ou o SQL Server Management Studio ao seu servidor de banco de dados do Azure. Você notará novos bancos de dados do exemplo fragmentar e fragmentar mapa manager banco de dados que criou a amostra.

> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>Principais partes do código de exemplo

1. **Gerenciando fragmentos e mapas de fragmentar**: O código ilustra como trabalhar com fragmentos, intervalos, e mapeamentos no arquivo **ShardMapManagerSample.cs**. Você pode encontrar mais informações sobre esse tópico aqui: [Gerenciamento de mapa fragmentar](http://go.microsoft.com/?linkid=9862595).  
2. **Dados dependentes roteamento**: roteamento de transações para a direita fragmentar seja mostrado em **DataDependentRoutingSample.cs**. Para obter mais detalhes, consulte [Roteamento dependentes de dados](http://go.microsoft.com/?linkid=9862596). 
3. **Consultar sobre vários fragmentos**: consultando entre fragmentos é ilustrado no arquivo **MultiShardQuerySample.cs**. Para obter mais detalhes, consulte [Fragmentar vários consultando](http://go.microsoft.com/?linkid=9862597).
4. **Adicionando vazios fragmentos**: A adição iterativo de novos fragmentos vazios é realizada pelo código no arquivo **AddNewShardsSample.cs**. Detalhes deste tópico são abordados aqui: [Gerenciamento de mapa fragmentar](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Outras operações de dimensionamento Elástico

1. **Dividindo uma fragmentar existente**: A capacidade de dividir fragmentos é fornecida por meio da **ferramenta de mesclagem de divisão**. Você pode encontrar mais informações sobre essa ferramenta aqui: [Visão geral da ferramenta de divisão direta](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Mesclando fragmentos existentes**: mescla a fragmentar também é executadas usando a **ferramenta de mesclagem de divisão**. Para obter mais informações, consulte: [Visão geral da ferramenta de divisão direta](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Custo

As ferramentas de banco de dados elástica são gratuitas. Ferramentas de banco de dados elástica não impõe cobranças adicionais sobre o custo de uso do Azure. 

Por exemplo, o aplicativo de exemplo cria novos bancos de dados. O custo depende da edição de banco de dados de DB do SQL Azure que escolher e o uso do Azure de seu aplicativo.

Para informações sobre preços consulte [Detalhes de preços de banco de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre as ferramentas de banco de dados elástica, consulte:

* [Mapa de documentos de ferramentas de banco de dados elástica](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Exemplos de código: 
    -    [BD elástica com SQL Azure - guia de Introdução](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [BD elástica com SQL Azure - integração com estrutura de entidades](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Fragmentar elasticidade no Centro de Script](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [comunicado de dimensionamento Elástico](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Canal 9: [vídeo de visão geral de dimensionamento Elástico](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Fórum de discussão: [Fórum de banco de dados do SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Para medir o desempenho: [contadores de desempenho do Gerenciador de mapa fragmentar](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
