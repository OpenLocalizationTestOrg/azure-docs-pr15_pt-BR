<properties 
    pageTitle="Web app com o armazenamento de tabela (node) | Microsoft Azure" 
    description="Um tutorial que se baseia o aplicativo Web com Express tutorial adicionando serviços de armazenamento do Azure e o módulo Azure." 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>Aplicativo da Web de Node usando armazenamento

## <a name="overview"></a>Visão geral

Neste tutorial, você irá estender o aplicativo criado no tutorial do [Aplicativo de Web Node usando Express] usando as bibliotecas de cliente do Microsoft Azure para Node para trabalhar com os serviços de gerenciamento de dados. Você irá estender seu aplicativo para criar um aplicativo de lista de tarefas baseado na web que você pode implantar no Azure. Lista de tarefas permite que um usuário recuperar tarefas, adicionar novas tarefas e marcar tarefas como concluídas.

Os itens de tarefa são armazenados no armazenamento do Azure. Armazenamento do Azure fornece armazenamento de dados não estruturados que é tolerância e altamente disponível. Armazenamento do Azure inclui diversas estruturas de dados onde você pode armazenar e acessar dados e você pode utilizar os serviços de armazenamento das APIs incluído no SDK do Azure para Node ou por meio de APIs REST. Para obter mais informações, consulte [armazenando e acessando dados no Azure].

Este tutorial supõe que você concluiu os [Aplicativo da Web de Node] e [Node com Express][usando o aplicativo da Web de Node Express] tutoriais.

Você aprenderá:

-   Como trabalhar com o mecanismo de modelo Jade
-   Como trabalhar com os serviços de gerenciamento de dados do Azure

Uma captura de tela da aplicação concluída está abaixo:

![Página da web concluída no internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Configurando o armazenamento de credenciais em Web. config

Para acessar o armazenamento do Azure, você precisa passar credenciais de armazenamento. Para fazer isso, você utilizar configurações de aplicativo Web. config.
Essas configurações serão passadas como variáveis de ambiente para nó, que são então lidos pelo SDK do Azure.

> [AZURE.NOTE] Credenciais de armazenamento são usadas apenas quando o aplicativo é implantado no Azure. Quando em execução no emulador, o aplicativo usará o emulador de armazenamento.

Execute as seguintes etapas para recuperar as credenciais de conta de armazenamento e adicioná-los para as configurações de Web. config:

1.  Se ele não ainda estiver abrir, inicie o PowerShell Azure no menu **Iniciar** expandindo **todos os programas, Azure**, clique com botão direito **Do PowerShell do Azure**e selecione **Executar como administrador**.

2.  Altere os diretórios para a pasta contendo seu aplicativo. Por exemplo, c\\nó\\lista de tarefas\\WebRole1.

3.  Na janela do Powershell do Azure, insira o seguinte cmdlet para recuperar as informações de conta de armazenamento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Recupera a lista de contas de armazenamento e conta teclas associada ao seu serviço hospedado.

    > [AZURE.NOTE] Como o SDK do Azure cria uma conta de armazenamento ao implantar um serviço, uma conta de armazenamento já deve existir de implantação do aplicativo nas guias de anterior.

4.  Abra o arquivo de **ServiceDefinition.csdef** que contém as configurações de ambiente que são usadas quando o aplicativo é implantado no Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Insira o seguinte bloco em elemento de **ambiente** , substituindo {conta de armazenamento} e {tecla de acesso de armazenamento} com o nome da conta e a chave primária para a conta de armazenamento que você deseja usar para implantação:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![O conteúdo do arquivo web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Salve o arquivo e feche o bloco de notas.

### <a name="install-additional-modules"></a>Instalar módulos adicionais

2. Use o seguinte comando para instalar o [azure], [nó-uuid], [nconf] e [assíncrono] módulos localmente, bem como para salvar uma entrada para eles no arquivo **package.json** :

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    A saída deste comando deve aparecer semelhante ao seguinte:

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>Usando o serviço de tabela em um aplicativo de nó

Nesta seção, você irá estender o aplicativo básico criado pelo comando **express** adicionando um arquivo de **task.js** que contém o modelo de suas tarefas. Você também modificar o existente **app.js** e criar um novo arquivo de **tasklist.js** que usa o modelo.

### <a name="create-the-model"></a>Criar o modelo

1. No diretório **WebRole1** , crie um novo diretório chamado **modelos**.

2. No diretório de **modelos** , crie um novo arquivo chamado **task.js**. Esse arquivo irá conter o modelo para as tarefas criadas pelo seu aplicativo.

3. No início do arquivo **task.js** , adicione o seguinte código para fazer referência a bibliotecas necessárias:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Em seguida, você irá adicionar código para definir e exporte o objeto de tarefa. Esse objeto é responsável por conectar-se à tabela.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto de tarefa, que permitem interações com dados armazenados na tabela:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };

            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. Salve e feche o arquivo **task.js** .

### <a name="create-the-controller"></a>Criar o controlador

1. No diretório **WebRole1/rotas** , crie um novo arquivo chamado **tasklist.js** e abra-o em um editor de texto.

2. Adicione o seguinte código para **tasklist.js**. Isso carrega os módulos do azure e assíncrono, que são usados pelo **tasklist.js**. Isso também define a função de **lista de tarefas** , que é passada uma instância do objeto de **tarefa** que definimos anteriormente:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. Continue adicionando ao arquivo **tasklist.js** adicionando os métodos usados para **showTasks**, **addTask**e **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

3. Salve o arquivo de **tasklist.js** .

### <a name="modify-appjs"></a>Modificar app.js

1. No diretório **WebRole1** , abra o arquivo **app.js** em um editor de texto. 

2. No início do arquivo, adicione o seguinte para carregar o módulo azure e definir a chave de nome e partição de tabela:

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. No arquivo app.js, role para baixo até o local onde você pode ver a seguinte linha:

        app.use('/', routes);
        app.use('/users', users);

    Substitua as linhas acima com o código mostrado abaixo. Isto irá inicializar uma instância de <strong>tarefa</strong> com uma conexão à sua conta de armazenamento. Isso é passado para a <strong>lista de tarefas</strong>, que usá-lo a se comunicar com o serviço de tabela:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. Salve o arquivo de **app.js** .

### <a name="modify-the-index-view"></a>Modificar a exibição de índice

1. Altere diretórios para o diretório de **modos de exibição** e abra o arquivo **Jade** em um editor de texto.

2. Substitua o conteúdo do arquivo **Jade** com o código a seguir. Define o modo de exibição para exibir tarefas existentes, bem como um formulário para adicionar novas tarefas e marcação existentes como concluída.

        extends layout

        block content
          h1= title
          br
        
          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if tasks != []
                tr
                  td 
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name: 
            input(name="item[name]", type="textbox")
            label Item Category: 
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. Salve e feche o arquivo **Jade** .

### <a name="modify-the-global-layout"></a>Modifique o layout global

O arquivo de **layout.jade** no diretório de **modos de exibição** é usado como um modelo global para outros arquivos de **.jade** . Nesta etapa, você irá modificá-lo para usar [Inicialização Twitter](https://github.com/twbs/bootstrap), que é um kit de ferramentas que torna mais fácil criar um site de aparência interessante.

1. Baixar e extrair os arquivos para [Inicialização Twitter](http://getbootstrap.com/). Copiar o arquivo **bootstrap.min.css** o **inicialização\\dist\\css** pasta para a **públicas\\folhas de estilo** diretório de seu aplicativo de lista de tarefas.

2. Na pasta **modos de exibição** , abra a **layout.jade** no editor de texto e substitua o conteúdo com o seguinte:

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3. Salve o arquivo de **layout.jade** .

### <a name="running-the-application-in-the-emulator"></a>Executando o aplicativo no emulador

Use o seguinte comando para iniciar o aplicativo no emulador.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

O navegador será aberto e exibirá a página a seguir:

![Uma web paginada intitulado minha lista de tarefas com uma tabela contendo tarefas e campos para adicionar uma nova tarefa.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Use o formulário para adicionar itens ou remover itens existentes, marcando-os como concluída.

## <a name="publishing-the-application-to-azure"></a>Publicar o aplicativo no Azure


Na janela do Windows PowerShell, ligue para o seguinte cmdlet para reimplantar seu serviço hospedado no Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Substitua **myuniquename** com um nome exclusivo para este aplicativo. Substitua **datacentername** com o nome de uma central de dados do Azure, como **Oeste EUA**.

Depois que a implantação for concluída, você verá uma resposta semelhante à seguinte:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Como antes, porque você especificou a **-início** opção, o navegador abre e exibe seu aplicativo em execução no Azure quando a publicação está concluída.

![Uma janela do navegador exibindo a página Minha lista de tarefas. A URL indica que a página agora está sendo hospedada no Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Interrompendo e excluindo seu aplicativo

Após implantar seu aplicativo, talvez você queira desabilitá-lo para que possa evitar custos ou construir e implantar outros aplicativos dentro do período de avaliação gratuita.

Faturas Azure web instâncias de função por hora de tempo de servidor consumida.
Hora do servidor é consumida quando seu aplicativo é implantado, mesmo se as instâncias não estiver executando e estiverem no estado interrompido.

As etapas a seguir mostram como interromper e excluir seu aplicativo.

1.  Na janela do Windows PowerShell, interrompa a implantação de serviço criada na seção anterior, com o seguinte cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Interromper o serviço pode demorar vários minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que ele parou.

3.  Para excluir o serviço, ligue o seguinte cmdlet:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Quando solicitado, digite **Y** para excluir o serviço.

    Excluindo o serviço pode demorar vários minutos. Após o serviço foi excluído, você recebe uma mensagem indicando que o serviço foi excluído.

  [Aplicativo da Web de Node usando Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Armazenar e acessar dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Aplicativo Web Node]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
