
Por padrão, APIs em um back-end do aplicativo Mobile pode ser chamado de forma anônima. Em seguida, você precisa restringir o acesso somente aos clientes autenticados.  

+ **Node back-end (via portal)** :  
    
    Em do seu aplicativo Mobile **configurações**, clique em **Tabelas fácil** e selecione sua tabela. Clique em **alterar permissões**, selecione **acesso autenticado somente** para todas as permissões e clique em **Salvar**. 

+ **.NET back-end (c#)**:  

    No project server, navegue até **controladores** > **TodoItemController.cs**. Adicionar o `[Authorize]` atributo à classe **TodoItemController** , da seguinte maneira. Para restringir o acesso somente para métodos específicos, você também pode aplicar esse atributo apenas para esses métodos em vez da classe. Republicar o project server.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node back-end (via código node)** :  
    
    Para exigir autenticação para acesso à tabela, adicione a seguinte linha para o script de servidor node:


        table.access = 'authenticated';

    Para obter mais detalhes, consulte [como: exigir autenticação para obter acesso a tabelas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para saber como fazer o download do projeto de código de início rápido do seu site, consulte [como: baixar o projeto de código de início rápido do Node back-end usando gito](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

