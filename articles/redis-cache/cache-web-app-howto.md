<properties 
    pageTitle="Como criar um aplicativo Web com Cache relacionada | Microsoft Azure" 
    description="Saiba como criar um aplicativo Web com Cache relacionada" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>Como criar um aplicativo Web com Cache relacionada

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tutorial mostra como criar e implantar um aplicativo de web ASP.NET para um aplicativo web no serviço de aplicativo do Azure usando o Visual Studio 2015. O aplicativo de exemplo exibe uma lista de estatísticas de equipe de banco de dados e mostra as diferentes maneiras de usar o Cache relacionada do Azure para armazenar e recuperar dados do cache. Quando você concluir o tutorial, você terá um aplicativo web em execução que lê e grava um banco de dados otimizado com Cache relacionada do Azure e hospedado no Azure.

Você aprenderá:

-   Como criar um aplicativo web do ASP.NET MVC 5 no Visual Studio.
-   Como acessar dados de um banco de dados usando a estrutura de entidade.
-   Como melhorar a taxa de transferência de dados e reduzir a carga de banco de dados, armazenando e recuperando dados usando o Azure relacionada Cache.
-   Como usar um relacionada classificados conjunto para recuperar as equipes de 5 superiores.
-   Como provisionar os Azure recursos para o aplicativo usando um modelo do Gerenciador de recursos.
-   Como publicar o aplicativo no Azure usando o Visual Studio.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, você deve ter os seguintes pré-requisitos.

-   [Conta do Azure](#azure-account)
-   [Visual Studio de 2015 com o SDK do Azure para .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Conta do Azure

Você precisa de uma conta do Azure para concluir este tutorial. É possível:

* [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenha créditos que podem ser usados para experimentar serviços Azure pagos. Mesmo após os créditos são usados para cima, você pode manter a conta e usar recursos e serviços Azure gratuitos.
* [Ativar o Visual Studio benefícios do assinante](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Sua assinatura do MSDN fornece créditos todo mês que você pode usar para serviços do Azure pagos.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>Visual Studio de 2015 com o SDK do Azure para .NET

O tutorial é escrito de Visual Studio de 2015 com o [SDK do Azure para .NET](../dotnet-sdk.md) 2.8.2 ou posterior. [Baixe o SDK mais recente do Azure de 2015 Studio Visual aqui](http://go.microsoft.com/fwlink/?linkid=518003). O Visual Studio é instalado automaticamente com o SDK se você ainda não tivê-lo.

Se você tiver o Visual Studio 2013, você pode [baixar o SDK mais recente do Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Algumas telas podem parecer diferentes das ilustrações mostradas neste tutorial.

>[AZURE.NOTE] Dependendo de quantos das dependências SDK você já tiver em sua máquina, instalar o SDK pode levar muito tempo, de alguns minutos a meia hora ou mais.

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Abra o Visual Studio e clique em **arquivo**, **novo** **projeto**.

2. Expanda o nó **Visual c#** na lista de **modelos** , selecione **nuvem**e clique em **Aplicativo Web ASP.NET**. Certifique-se de que o **.NET Framework 4.5.2** está selecionado.  Digite **ContosoTeamStats** na caixa de texto **nome** e clique em **Okey**.
 
    ![Criar projeto][cache-create-project]

3. Selecione **MVC** como o tipo de projeto. Desmarque a caixa de seleção do **Host na nuvem** . Você usará o [provisionar os recursos do Azure](#provision-the-azure-resources) e [publicar o aplicativo no Azure](#publish-the-application-to-azure) em etapas subsequentes no tutorial. Para um exemplo de um serviço de aplicativo web app do Visual Studio de provisionamento, deixando **Host na nuvem** marcada, consulte [Introdução ao Web Apps no serviço de aplicativo do Azure, usando ASP.NET e Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Selecione o modelo de projeto][cache-select-template]

4. Clique em **Okey** para criar o projeto.

## <a name="create-the-aspnet-mvc-application"></a>Criar o aplicativo do ASP.NET MVC

Nesta seção do tutorial, você vai criar o aplicativo básico que lê e exibe estatísticas de equipe de banco de dados.

-   [Adicionar o modelo](#add-the-model)
-   [Adicione o controlador](#add-the-controller)
-   [Configurar os modos de exibição](#configure-the-views)

### <a name="add-the-model"></a>Adicionar o modelo

1. **Modelos** no **Explorador de solução**de atalho e, em seguida, escolha **Adicionar** **classe**. 

    ![Adicionar modelo][cache-model-add-class]

2. Insira `Team` da classe nome e clique em **Adicionar**.

    ![Adicionar classe de modelo][cache-model-add-class-dialog]

3. Substituir o `using` instruções na parte superior da `Team.cs` arquivo com o seguinte usando instruções.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Substituir a definição do `Team` classe com o seguinte trecho de código que contém uma atualização `Team` definição, bem como algumas outras classes de auxiliar de estrutura de entidade de classe. Para obter mais informações sobre a primeira abordagem de código de estrutura de entidade que é usada neste tutorial, consulte [código primeiro para um novo banco de dados](https://msdn.microsoft.com/data/jj193542).


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. No **Solution Explorer**, clique duas vezes em **Web. config** para abri-lo.

    ![Web. config][cache-web-config]

3.  Adicionar a seguinte cadeia de conexão para o `connectionStrings` seção. O nome da cadeia de caracteres de conexão deve corresponder ao nome da classe de contexto de banco de dados Framework de entidade que é `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Depois de adicionar isso, a `connectionStrings` seção deve parecer com o exemplo a seguir.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Adicione o controlador

1. Pressione **F6** para construir o projeto. 
2. No **Solution Explorer**, clique com botão direito na pasta de **controladores** e escolha **Adicionar**, **controlador**.

    ![Adicionar controlador][cache-add-controller]

3. Escolha **Controlador de 5 MVC com modos de exibição, usando a estrutura de entidade**e clique em **Adicionar**. Se você receber um erro após clicar em **Adicionar**, certifique-se de que você criou o projeto primeiro.

    ![Adicionar classe de controlador][cache-add-controller-class]

5. Selecione **equipe (ContosoTeamStats.Models)** na lista suspensa de **classe do modelo** . Selecione **TeamContext (ContosoTeamStats.Models)** na lista suspensa de **classe de contexto de dados** . Tipo de `TeamsController` na caixa nome de **controlador de** texto (se ele não é preenchido automaticamente). Clique em **Adicionar** para criar a classe de controlador e adicionar os modos de exibição padrão.

    ![Configurar controlador][cache-configure-controller]

4. No **Solution Explorer**, expanda **asax** e clique duas vezes em **Global.asax.cs** para abri-lo.

    ![Global.asax.cs][cache-global-asax]

5. Adicione os dois procedimentos usando instruções na parte superior do arquivo em outro usando instruções.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Adicione a seguinte linha de código no final do `Application_Start` método.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. No **Solution Explorer**, expanda `App_Start` e clique duas vezes em `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Substituir `controller = "Home"` no código a seguir no `RegisterRoutes` método com `controller = "Teams"` conforme mostrado no exemplo a seguir.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Configurar os modos de exibição

1. No **Solution Explorer**, expanda a pasta de **modos de exibição** e, em seguida, a pasta **compartilhada** e clique duas vezes em **cshtml**. 

    ![Cshtml][cache-layout-cshtml]

2. Alterar o conteúdo do `title` elemento e substituir `My ASP.NET Application` com `Contoso Team Stats` conforme mostrado no exemplo a seguir.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. No `body` seção, atualize o primeiro `Html.ActionLink` instrução e substituir `Application name` com `Contoso Team Stats` e substituir `Home` com `Teams`.
    -   Antes:`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Depois:`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Alterações de código][cache-layout-cshtml-code]

4. Pressione **Ctrl + F5** para criar e executar o aplicativo. Esta versão do aplicativo lê os resultados diretamente do banco de dados. Observação as ações de **Criar novo**, **Editar**, **detalhes**e **Excluir** que foram adicionadas automaticamente ao aplicativo pelo scaffold **Controlador de 5 MVC com modos de exibição, usando a estrutura de entidade** . Na próxima seção do tutorial, você adicionará relacionada Cache para otimizar o acesso a dados e fornecem recursos adicionais para o aplicativo.

![Aplicativo de Starter][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurar o aplicativo para usar Cache relacionada

Nesta seção do tutorial, você irá configurar o aplicativo de exemplo para armazenar e recuperar estatísticas de equipe da Contoso de uma instância de Cache relacionada do Azure usando o cliente de cache de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

-   [Configurar o aplicativo para usar StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [Atualizar a classe de TeamsController para retornar resultados de cache ou o banco de dados](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [Atualizar os métodos de criar, editar e excluir para funcionar com o cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [Atualizar a exibição de índice de equipes para funcionar com o cache](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurar o aplicativo para usar StackExchange.Redis

1. Para configurar um aplicativo cliente no Visual Studio usando o pacote StackExchange.Redis NuGet, clique com botão direito do projeto no **Solution Explorer** e escolha **Gerenciar pacotes do NuGet**. 

    ![Gerenciar pacotes do NuGet][redis-cache-manage-nuget-menu]

2. Digite **StackExchange.Redis** na caixa de texto Pesquisar, selecione a versão desejada nos resultados e clique em **instalar**.

    ![Pacote StackExchange.Redis NuGet][redis-cache-stack-exchange-nuget]

    O pacote NuGet downloads e adiciona as referências de assembly necessário para o seu aplicativo cliente acessar o Cache do Azure relacionada com o cliente de cache de StackExchange.Redis. Se você preferir usar uma versão fortes da biblioteca cliente **StackExchange.Redis** , escolha **StackExchange.Redis.StrongName**; Caso contrário, escolha **StackExchange.Redis**.

3. No **Solution Explorer**, expanda a pasta de **controladores** e clique duas vezes em **TeamsController.cs** para abri-lo.

    ![Controlador de equipes][cache-teamscontroller]

4. Adicione os dois seguintes instruções using ao **TeamsController.cs**.

        using System.Configuration;
        using StackExchange.Redis;

5. Adicione as seguintes propriedades de duas para a `TeamsController` classe.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Criar um arquivo em seu computador chamado `WebAppPlusCacheAppSecrets.config` e coloque-o em um local que não ser verificado com o código-fonte do seu aplicativo de amostra, você decidir fazer check-in em outro. Neste exemplo o `AppSettingsSecrets.config` arquivo está localizado em `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Editar o `WebAppPlusCacheAppSecrets.config` de arquivo e adicione o seguinte conteúdo. Se você executar o aplicativo localmente essas informações são usadas para se conectar à sua instância de Cache relacionada do Azure. Mais tarde no tutorial você provisionar uma instância de Cache relacionada do Azure e atualizar o cache de nome e senha. Se você não planeja executar o aplicativo de amostra localmente você poderá ignorar a criação desse arquivo e as etapas subsequentes que fazem referência o arquivo, porque quando você implanta Azure o aplicativo recupera as informações de conexão do cache da configuração do aplicativo para o aplicativo da Web e não deste arquivo. Como o `WebAppPlusCacheAppSecrets.config` não é implantado no Azure com seu aplicativo, você não precisa-la, a menos que você vai executar o aplicativo localmente.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. No **Solution Explorer**, clique duas vezes em **Web. config** para abri-lo.

    ![Web. config][cache-web-config]

3. Adicione o seguinte `file` atributo o `appSettings` elemento. Se você usou um nome de arquivo diferente ou local, substitua esses valores para as mostrado no exemplo.
    -   Antes:`<appSettings>`
    -   Depois:` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    O tempo de execução do ASP.NET mescla o conteúdo do arquivo externo com a marcação no `<appSettings>` elemento. O tempo de execução ignora o atributo de arquivo se o arquivo especificado não pode ser encontrado. Seus segredos (a cadeia de conexão para o cache) não são incluídos como parte do código-fonte para o aplicativo. Quando você implanta o aplicativo web do Azure, o `WebAppPlusCacheAppSecrests.config` arquivo não ser implantado (que é o que você quer). Há várias maneiras para especificar esses segredos no Azure e neste tutorial eles são configurados automaticamente para você quando você [provisionar os recursos Azure](#provision-the-azure-resources) em um tutorial subsequente etapa. Para obter mais informações sobre como trabalhar com segredos no Azure, consulte [práticas recomendadas para implantação senhas e outros dados confidenciais ASP.NET e serviço de aplicativo do Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Atualizar a classe de TeamsController para retornar resultados de cache ou o banco de dados

Neste exemplo, as estatísticas de equipe podem ser recuperadas do banco de dados ou do cache. Estatísticas de equipe são armazenadas em cache como um serializado `List<Team>`e também como um conjunto classificado usando tipos de dados relacionada. Ao recuperar itens de um conjunto classificado, você pode recuperar alguns, todos ou consultar para certos itens. Neste exemplo, você deverá consultar o conjunto classificado para que as equipes de 5 superiores classificados pelo número de wins.

>[AZURE.NOTE] Não é necessária para armazenar as estatísticas de equipe em vários formatos no cache para poder usar o Azure relacionada Cache. Este tutorial usa vários formatos para demonstrar algumas maneiras diferentes e tipos de dados diferentes, que você pode usar os dados em cache.



1. Adicione o seguinte usando instruções para o `TeamsController.cs` arquivo na parte superior com o outro usando instruções.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Substituir o atual `public ActionResult Index()` método com implementação a seguir.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Adicione os seguintes três métodos para a `TeamsController` classe para implementar o `playGames`, `clearCache`, e `rebuildDB` tipos de ação da instrução switch adicionado no trecho de código anterior.

    O `PlayGames` método atualiza as estatísticas de equipe por simular uma estação dos jogos, salva os resultados ao banco de dados e limpa os dados agora desatualizados do cache.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    O `RebuildDB` método reinicializa o banco de dados com o conjunto padrão de equipes, gera estatísticas para elas e limpa os dados agora desatualizados do cache.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    O `ClearCachedTeams` método remove as estatísticas de equipe em cache do cache.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Adicione os seguintes métodos de quatro para o `TeamsController` classe implementar as diversas maneiras de recuperar as estatísticas de equipe do cache e o banco de dados. Cada um desses métodos retorna um `List<Team>` que é exibido pelo modo de exibição.

    O `GetFromDB` método lê as estatísticas de equipe do banco de dados.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    O `GetFromList` método lê as estatísticas de equipe do cache como um serializado `List<Team>`. Se houver um erro de cache, as estatísticas de equipe são lidos do banco de dados e, em seguida, armazenadas no cache para a próxima vez. Neste exemplo estamos usando JSON.NET serialização para serializar os objetos .NET para e do cache. Para obter mais informações, consulte [como trabalhar com objetos .NET no Azure relacionada Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    O `GetFromSortedSet` método lê as estatísticas de equipe de um conjunto classificado em cache. Se houver um erro de cache, as estatísticas de equipe são lidos do banco de dados e armazenadas em cache como um conjunto classificado.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    O `GetFromSortedSetTop5` método lê as equipes de 5 principais do conjunto de classificado em cache. Ele começa verificando o cache a existência do `teamsSortedSet` chave. Se esta chave não estiver presente, o `GetFromSortedSet` método é chamado para ler as estatísticas de equipe e armazená-los no cache. Em seguida o conjunto classificado em cache é consultado para as equipes de 5 principais que são retornadas.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Atualizar os métodos de criar, editar e excluir para funcionar com o cache

O código de estrutura que foi gerado como parte deste exemplo inclui métodos para adicionar, editar e excluir equipes. Sempre que uma equipe é adicionada, editada ou removida, os dados no cache ficarão desatualizados. Nesta seção, você vai modificar esses três métodos para limpar as equipes em cache para que o cache não estará fora de sincronia com o banco de dados.

1. Navegue até o `Create(Team team)` método na `TeamsController` classe. Adicionar uma chamada para a `ClearCachedTeams` método, conforme mostrado no exemplo a seguir.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Navegue até o `Edit(Team team)` método na `TeamsController` classe. Adicionar uma chamada para a `ClearCachedTeams` método, conforme mostrado no exemplo a seguir.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Navegue até o `DeleteConfirmed(int id)` método na `TeamsController` classe. Adicionar uma chamada para a `ClearCachedTeams` método, conforme mostrado no exemplo a seguir.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Atualizar a exibição de índice de equipes para funcionar com o cache

1. No **Solution Explorer**, expanda a pasta de **modos de exibição** , em seguida, a pasta de **equipes** e clique duas vezes em **cshtml**.

    ![Cshtml][cache-views-teams-index-cshtml]

2. Próximo à parte superior do arquivo, procure o seguinte elemento de parágrafo.

    ![Tabela de ação][cache-teams-index-table]

    Este é o link para criar uma nova equipe. Substitua o elemento de parágrafo com a tabela a seguir. Esta tabela possui links de ação para criar uma nova equipe, reproduzindo uma nova estação de jogos, limpar o cache, recuperar as equipes do cache em vários formatos, recuperando as equipes do banco de dados e recriar o banco de dados com dados de exemplo clara.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Role até o final do arquivo **cshtml** e adicione o seguinte `tr` elemento para que ela seja a última linha da tabela a última no arquivo.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    Essa linha exibe o valor de `ViewBag.Msg` que contém um relatório de status sobre a operação atual que é definido quando você clica em um dos links a ação da etapa anterior.   

    ![Mensagem de status][cache-status-message]

4. Pressione **F6** para construir o projeto.

## <a name="provision-the-azure-resources"></a>Provisionar os recursos do Azure

Para hospedar seu aplicativo no Azure, você deve primeiro provisionar os serviços do Azure que seu aplicativo requer. O aplicativo de exemplo neste tutorial usa os seguintes serviços Azure.

-   Cache relacionada Azure
-   Serviço de aplicativo Web App
-   Banco de dados SQL

Para implantar esses serviços em um grupo de recursos novos ou existentes de sua escolha, clique no botão de **implantar no Azure** a seguir.

[! [Implantar para Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Esse botão **implantar no Azure** usa o modelo de [Início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) [criar um aplicativo Web além de Cache relacionada além de banco de dados SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) para provisionar esses serviços e configurar a cadeia de conexão para o banco de dados do SQL e a configuração do aplicativo para a cadeia de conexão do Azure relacionada Cache.

>[AZURE.NOTE] Se você não tiver uma conta do Azure, você pode [criar uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) em apenas alguns minutos.

Clicando no botão de **implantar no Azure** leva você ao portal do Azure e inicia o processo de criação dos recursos descritos pelo modelo.

![Implantar para o Azure][cache-deploy-to-azure-step-1]

1. Na lâmina **implantação personalizada** , selecione a assinatura do Azure para usar e selecione um grupo de recursos existente ou crie um novo e especifique o local do grupo de recursos.
2. Na lâmina **parâmetros** , especifique um nome de conta de administrador (**ADMINISTRATORLOGIN** - não use **Administração**), a senha de logon do administrador (**ADMINISTRATORLOGINPASSWORD**) e o nome de banco de dados (**DATABASENAME**). Os outros parâmetros são configurados para um serviço de aplicativo gratuito plano e opções de custo inferiores para o banco de dados SQL e Azure relacionada Cache, que não vêm com um nível gratuito de hospedagem.
3. Alterar qualquer uma das outras configurações se desejar, ou manter os padrões e clique em **Okey**.


![Implantar para o Azure][cache-deploy-to-azure-step-2]

1. Clique em **revisão legais termos**.
2. Leia os termos na lâmina **compra** e clique em **comprar**.
3. Para começar os recursos de provisionamento, clique em **criar** na lâmina **implantação personalizada** .

Para exibir o andamento da sua implantação, clique no ícone de notificação e clique em **implantação iniciado**.

![Implantação iniciada][cache-deployment-started]

Você pode exibir o status da sua implantação na lâmina **Microsoft.Template** .

![Implantar para o Azure][cache-deploy-to-azure-step-3]

Quando provisionamento for concluído, você pode publicar seu aplicativo no Azure do Visual Studio.

>[AZURE.NOTE] Todos os erros que podem ocorrer durante o processo de provisionamento são exibidos na lâmina **Microsoft.Template** . Erros comuns são muitos servidores SQL ou muitos planos por assinatura de hospedagem de serviço gratuito do aplicativo. Resolver erros e reiniciar o processo clicando em **reimplantar** na lâmina **Microsoft.Template** ou o botão de **implantar no Azure** neste tutorial.

## <a name="publish-the-application-to-azure"></a>Publicar o aplicativo no Azure

Nesta etapa do tutorial, você irá publicar o aplicativo no Azure e executá-lo na nuvem.

1. Clique com botão direito do projeto **ContosoTeamStats** no Visual Studio e escolha **Publicar**.

    ![Publicar][cache-publish-app]

2. Clique em **Serviço de aplicativo do Microsoft Azure**.

    ![Publicar][cache-publish-to-app-service]

3. Selecione a assinatura usada ao criar os recursos Azure, expanda o grupo de recursos que contém os recursos, selecione o aplicativo Web desejada e clique **Okey**. Se você usou o botão de **implantar no Azure** inicia o seu nome de Web App com o **site** seguido por alguns caracteres adicionais.

    ![Selecione Web App][cache-select-web-app]

4. Clique em **Validar Conexão** para verificar as configurações e então clique em **Publicar**.

    ![Publicar][cache-publish]

    Após alguns instantes concluirá o processo de publicação e um navegador será iniciado com a execução aplicativo de exemplo. Se você receber um erro de DNS quando Validando ou publicação e o processo de configuração para os recursos Azure para o aplicativo concluiu recentemente, aguarde um pouco e tente novamente.

    ![Cache adicionadas][cache-added-to-application]

A tabela a seguir descreve cada link de ação do aplicativo de exemplo.

| Ação                  | Descrição                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criar novo              | Crie uma nova equipe.                                                                                                                                               |
| Reproduzir estação             | Reproduzir uma estação dos jogos, atualizar as estatísticas de equipe e desmarque qualquer desatualizadas dados do cache de equipe.                                                                          |
| Limpar Cache             | Limpe as estatísticas de equipe do cache.                                                                                                                             |
| Lista do Cache         | Recupere as estatísticas de equipe do cache. Se houver um erro de cache, carregar as estatísticas do banco de dados e salve o cache para a próxima vez.                                        |
| Conjunto classificado do Cache   | Recupere as estatísticas de equipe do cache usando um conjunto classificado. Se houver um erro de cache, carregar as estatísticas do banco de dados e salve o cache usando um conjunto classificado.  |
| Equipes de 5 principais do Cache  | Recupere as equipes de 5 superiores do cache usando um conjunto classificado. Se houver um erro de cache, carregar as estatísticas do banco de dados e salve o cache usando um conjunto classificado. |
| Carregar do banco de dados            | Recupere as estatísticas de equipe do banco de dados.                                                                                                                       |
| Recriar o banco de dados              | Recriar o banco de dados e recarregá-la com dados de equipe de exemplo.                                                                                                        |
| Editar / detalhes / excluir | Editar uma equipe, exibir detalhes de uma equipe, excluir uma equipe.                                                                                                             |


Clique em algumas das ações e experimentar recuperar os dados de diferentes fontes. Não as diferenças entre o tempo necessário para concluir as diversas maneiras de recuperar dados do banco de dados e o cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Excluir os recursos quando terminar com o aplicativo

Quando terminar com o aplicativo de amostra tutorial, você pode excluir os recursos Azure usados para economizar custos e recursos. Se você usar o botão de **implantar no Azure** na seção [provisionar os recursos do Azure](#provision-the-azure-resources) e todos os seus recursos estão contidos no mesmo grupo de recursos, você pode excluí-los juntos em uma operação excluindo grupo de recursos.

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **grupos de recursos**.
2. Digite o nome do seu grupo de recursos na caixa de texto **Filtrar itens...** .
3. Clique em **…** à direita de seu grupo de recursos.
4. Clique em **Excluir**.

    ![Excluir][cache-delete-resource-group]

5. Digite o nome do seu grupo de recursos e clique em **Excluir**.

    ![Confirmar a exclusão][cache-delete-confirm]

Após alguns instantes, o grupo de recursos e todos os seus recursos contidos são excluídos.

>[AZURE.IMPORTANT] Observe que a exclusão de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos são excluídos permanentemente. Certifique-se de que você não acidentalmente excluir o grupo de recursos errado ou recursos. Se você criou os recursos para hospedar este exemplo dentro de um grupo existente do recurso, você pode excluir cada recurso individualmente de seus respectivas lâminas.

## <a name="run-the-sample-application-on-your-local-machine"></a>Executar o aplicativo de exemplo em sua máquina local

Para executar o aplicativo localmente em seu computador, você precisa de uma instância do Azure relacionada Cache no qual os dados em cache. 

-   Se você tiver publicado seu aplicativo no Azure conforme descrito na seção anterior, você pode usar a instância do Azure relacionada Cache que foi provisionada durante esta etapa.
-   Se você tiver outra instância do Azure relacionada Cache existente, você pode usar que para executar esse exemplo localmente.
-   Se você precisa criar uma instância do Azure relacionada Cache, você pode seguir as etapas em [criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Depois que você selecionou ou criou o cache usar, navegue até o cache no portal do Azure e recuperar o [nome de host](cache-configure.md#properties) e [as teclas de acesso](cache-configure.md#access-keys) para o cache. Para obter instruções, consulte [Configurar relacionada configurações de cache](cache-configure.md#configure-redis-cache-settings).

1. Abrir o `WebAppPlusCacheAppSecrets.config` arquivo que você criou durante a etapa de [Configurar o aplicativo para usar o Cache relacionada](#configure-the-application-to-use-redis-cache) deste tutorial usando o editor de sua escolha.

2. Editar o `value` atributo e substituir `MyCache.redis.cache.windows.net` com o [nome do host](cache-configure.md#properties) do seu cache e especificar um da [chave primária ou secundária](cache-configure.md#access-keys) do cache como a senha.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Pressione **Ctrl + F5** para executar o aplicativo.

>[AZURE.NOTE] Observe que, como o aplicativo, incluindo o banco de dados está em execução localmente e o Cache relacionada está hospedado no Azure, o cache pode aparecer em realizar o banco de dados. Para obter melhor desempenho, o aplicativo cliente e a instância do Azure relacionada Cache devem ser no mesmo local. 

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [Introdução ao ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) no site do [ASP.NET](http://asp.net/) .
-   Para obter mais exemplos de criação de um aplicativo Web do ASP.NET no aplicativo de serviço, consulte [criar e implantar o aplicativo web do ASP.NET no serviço de aplicativo do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 conectar [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    -   Para mais guias de início rápido da demonstração de HealthClinic.biz, consulte [Início rápido de ferramentas de desenvolvedor do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
-   Saiba mais sobre a abordagem de [código primeiro para um novo banco de dados](https://msdn.microsoft.com/data/jj193542) de estrutura de entidade que é usada neste tutorial.
-   Saiba mais sobre [web apps no serviço de aplicativo do Azure](../app-service-web/app-service-web-overview.md).
-   Saiba como [monitorar](cache-how-to-monitor.md) seu cache no portal do Azure.

-   Explore os recursos premium do Azure relacionada Cache
    -   [Como configurar persistência para um Premium Azure relacionada Cache](cache-how-to-premium-persistence.md)
    -   [Como configurar clusters para um Premium Azure relacionada Cache](cache-how-to-premium-clustering.md)
    -   [Como configurar o suporte de rede Virtual para um Premium Azure relacionada Cache](cache-how-to-premium-vnet.md)
    -   Consulte o [Azure relacionada Cache perguntas Frequentes](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, a produtividade e largura de banda com caches premium.



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

