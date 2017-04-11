<properties 
    pageTitle="Criar um aplicativo web no Azure que se conecta ao MongoDB em execução em uma máquina virtual" 
    description="Um tutorial que ensina como usar gito para implantar um aplicativo do ASP.NET ao serviço de aplicativo do Azure, conectada a MongoDB em uma máquina Virtual do Azure."
    tags="azure-portal" 
    services="app-service\web, virtual-machines" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/29/2016" 
    ms.author="cephalin"/>


# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>Criar um aplicativo web no Azure que se conecta ao MongoDB em execução em uma máquina virtual

Usando gito, você pode implantar um aplicativo ASP.NET para aplicativos do Azure aplicativo de serviço Web. Neste tutorial, você criará um simples front-end ASP.NET MVC aplicativo de lista de tarefas que se conecta a um banco de dados MongoDB em execução em uma máquina virtual no Azure.  [MongoDB] [ MongoDB] é um banco de dados do alto desempenho NoSQL popular Abrir origem. Depois de executar e testar o aplicativo ASP.NET no computador de desenvolvimento, você irá carregar o aplicativo para o aplicativo de serviço Web Apps usando gito.

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.


## <a name="background-knowledge"></a>Conhecimento de plano de fundo ##

Conhecimento das seguintes opções é útil para este tutorial, porém, não é necessário:

* O c# driver para MongoDB. Para obter mais informações sobre como desenvolver aplicativos c# contra MongoDB, consulte o MongoDB [CSharp idioma centro][MongoC#LangCenter]. 
* A estrutura de aplicativo da web de ASP .NET. Saiba tudo sobre isso no [site do ASP.net][ASP.NET].
* A estrutura do aplicativo web ASP .NET MVC. Saiba tudo sobre isso no [site do ASP.NET MVC][MVCWebSite].
* Azure. Você pode começar a leitura no [Azure][WindowsAzure].

## <a name="prerequisites"></a>Pré-requisitos ##

- [O Visual Studio Express 2013 para Web]  [ VSEWeb] ou [Visual Studio 2013] [VSUlt]
- [SDK do Azure para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Uma assinatura ativa do Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 
## <a name="create-a-virtual-machine-and-install-mongodb"></a>Criar uma máquina virtual e instale o MongoDB ##

Este tutorial pressupõe que você tenha criado uma máquina virtual no Azure. Depois de criar a máquina virtual, você precisa instalar MongoDB na máquina virtual:

* Para criar uma máquina virtual Windows e instale o MongoDB, consulte [Instalar MongoDB em uma máquina virtual executando o Windows Server no Azure][InstallMongoOnWindowsVM].


Depois que você tiver criado a máquina virtual no Azure e instalou MongoDB, certifique-se de lembrar o nome DNS da máquina virtual ("testlinuxvm.cloudapp.net", por exemplo) e a porta externa MongoDB que você especificou no ponto de extremidade.  Você precisará dessas informações posteriormente no tutorial.

<a id="createapp"></a>
## <a name="create-the-application"></a>Criar o aplicativo ##

Nesta seção, você irá criar um aplicativo do ASP.NET chamado "Minha lista de tarefas" usando o Visual Studio e executar uma implantação inicial ao Azure aplicativo de serviço Web Apps. Você irá executar o aplicativo localmente, mas ele conectar à sua máquina virtual no Azure e usar a instância de MongoDB que você criou lá.

1. No Visual Studio, clique em **Novo projeto**.

    ![Iniciar página novo projeto][StartPageNewProject]

1. Na janela **Novo projeto** , no painel esquerdo, selecione **Visual c#**e selecione **Web**. No painel intermediário, selecione o **Aplicativo Web ASP.NET**. Na parte inferior, nomeie o projeto "MyTaskListApp" e clique em **Okey**.

    ![Caixa de diálogo Novo projeto][NewProjectMyTaskListApp]

1. Na caixa de diálogo **Novo projeto ASP.NET** , selecione **MVC**e, em seguida, clique em **Okey**.

    ![Selecione o modelo MVC][VS2013SelectMVCTemplate]

1. Se você já não estiver conectado ao Microsoft Azure, você será solicitado a entrar. Siga os prompts para entrar no Azure.
2. Quando você estiver conectado, você pode começar a configurar o aplicativo web do serviço de aplicativo. Especifique o **nome do aplicativo da Web**, **plano de serviço de aplicativo**, **grupo de recursos**e **região**, clique em **criar**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Depois do projeto criação for concluído, aguarde para o aplicativo web seja criado no serviço de aplicativo do Azure conforme indicado na janela de **Atividade de serviço de aplicativo do Azure** . Em seguida, clique em **Publicar MyTaskListApp para este aplicativo Web agora**.

1. Clique em **Publicar**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

    Depois que seu aplicativo de ASP.NET padrão é publicado no Azure aplicativo de serviço Web Apps, ele será iniciado no navegador.

## <a name="install-the-mongodb-c-driver"></a>Instalar o driver MongoDB c#

MongoDB oferece suporte do lado do cliente para aplicativos c# por meio de um driver, que você precisa instalar em seu computador de desenvolvimento local. O driver c# está disponível por meio do NuGet.

Para instalar o driver MongoDB c#:

1. No **Solution Explorer**, clique com botão direito no projeto **MyTaskListApp** e selecione **Gerenciar NuGetPackages**.

    ![Gerenciar pacotes do NuGet][VS2013ManageNuGetPackages]

2. Na janela **Gerenciar pacotes NuGet** , no painel esquerdo, clique em **Online**. Na caixa **Pesquisar Online** à direita, digite "mongodb.driver".  Clique em **instalar** para instalar o driver.

    ![Procurar MongoDB c# Driver][SearchforMongoDBCSharpDriver]

3. Clique em **aceito** para aceitar os termos de licença Inc., 10gen.

4. Clique em **Fechar** após o driver instalado.
    ![MongoDB c# Driver instalado][MongoDBCsharpDriverInstalled]


O driver MongoDB c# agora está instalado.  Referências às bibliotecas **MongoDB.Bson**, **MongoDB.Driver**e **MongoDB.Driver.Core** foram adicionadas ao projeto.

![Referências de MongoDB c# Driver][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>Adicionar um modelo ##
No **Solution Explorer**, clique com botão direito na pasta de *modelos* e **Adicionar** uma nova **classe** e chame- *TaskModel.cs*.  No *TaskModel.cs*, substitua o código existente com o seguinte código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;
    
    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }
    
            [BsonElement("Name")]
            public string Name { get; set; }
    
            [BsonElement("Category")]
            public string Category { get; set; }
    
            [BsonElement("Date")]
            public DateTime Date { get; set; }
    
            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }
    
        }
    }

## <a name="add-the-data-access-layer"></a>Adicionar camada de acesso a dados ##
No **Solution Explorer**, clique com botão direito do projeto de *MyTaskListApp* e **Adicionar** uma **Nova pasta** chamada *DAL*.  Clique com botão direito na pasta *DAL* e **Adicionar** uma nova **classe**. Nomeie o arquivo de classe *Dal.cs*.  No *Dal.cs*, substitua o código existente com o seguinte código:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    
    
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;
    
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
    
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
    
            // Default constructor.        
            public Dal()
            {
            }
    
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
    
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
    
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            # region IDisposable
    
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
        }
    }

## <a name="add-a-controller"></a>Adicionar um controlador ##
Abra o arquivo *Controllers\HomeController.cs* no **Solution Explorer** e substitua o código existente com o seguinte:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;
    
    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/
    
            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }
    
            //
            // GET: /MyTask/Create
    
            public ActionResult Create()
            {
                return View();
            }
    
            //
            // POST: /MyTask/Create
    
            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            # region IDisposable
    
            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
    
        }
    }

## <a name="set-up-the-styles"></a>Configurar os estilos ##
Para alterar o título na parte superior da página, abra a *Views\Shared\\cshtml* arquivo no **Solution Explorer** e substitua "Nome do aplicativo" no cabeçalho da barra de navegação "Meu aplicativo de lista de tarefas" para que ele tem esta aparência:

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Para configurar o menu de lista de tarefas, abra o arquivo *\Views\Home\Index.cshtml* e substitua o código existente com o seguinte código:
    
    @model IEnumerable<MyTaskListApp.Models.MyTask>
    
    @{
        ViewBag.Title = "My Task List";
    }
    
    <h2>My Task List</h2>
    
    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>
    
    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }
    
    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Para adicionar a capacidade de criar uma nova tarefa, clique com botão direito do *Views\Home\\ * pasta e **Adicionar** uma **exibição**.  *Criar*o modo de exibição de nome. Substitua o código com o seguinte:

    @model MyTaskListApp.Models.MyTask
    
    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
    
    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>
    
            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**Solution Explorer** deve ter esta aparência:

![Gerenciador de soluções][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>Definir a cadeia de conexão MongoDB ##
No **Solution Explorer**, abra o arquivo *DAL/Dal.cs* . Localize a linha de código a seguir:

    private string connectionString = "mongodb://<vm-dns-name>";

Substituir `<vm-dns-name>` com o nome DNS da máquina virtual executando MongoDB que você criou na etapa [criar uma máquina virtual e instale o MongoDB][] deste tutorial.  Para localizar o nome de sua máquina virtual DNS, acesse o Portal do Azure, selecione **máquinas virtuais**e localizar **O nome de DNS**.

Se o nome DNS da máquina virtual é "testlinuxvm.cloudapp.net" e MongoDB é ouvir a porta padrão 27017, a linha de cadeia de conexão de código será parecida com:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se o ponto de extremidade de máquina virtual Especifica uma porta externa diferente para MongoDB, você pode especificar a porta na cadeia de conexão:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obter mais informações sobre cadeias de caracteres de conexão MongoDB, consulte [conexões][MongoConnectionStrings].

## <a name="test-the-local-deployment"></a>Testar a implantação local ##

Para executar o aplicativo em seu computador de desenvolvimento, selecione **Iniciar depuração** no menu **Depurar** ou pressione **F5**. Inicia o IIS Express e um navegador é aberto e inicia a home page do aplicativo.  Você pode adicionar uma nova tarefa, o qual será adicionada ao banco de dados MongoDB executando em sua máquina virtual no Azure.

![Meu aplicativo de lista de tarefas][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>Publicar aplicativos do Azure serviço de aplicativo Web

Nesta seção você publica suas alterações aos aplicativos do Azure aplicativo de serviço Web.

1. No Solution Explorer, clique com botão direito **MyTaskListApp** novamente e clique em **Publicar**.
2. Clique em **Publicar**.

    Agora você deve ver seu aplicativo web em execução no serviço de aplicativo do Azure e acessar o banco de dados do MongoDB em máquinas virtuais do Azure.

## <a name="summary"></a>Resumo ##

Agora você tem implantado com êxito seu aplicativo ASP.NET para aplicativos do Azure aplicativo de serviço Web. Para exibir o web app:

1. Faça login no Portal do Azure.
2. Clique em **aplicativos da Web**. 
3. Selecione o aplicativo web na lista de **Aplicativos Web** .

Para obter mais informações sobre como desenvolver aplicativos c# contra MongoDB, consulte [Centro de idioma CSharp][MongoC#LangCenter]. 

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-windows-classic-install-mongodb.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Criar uma máquina virtual e instale o MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 