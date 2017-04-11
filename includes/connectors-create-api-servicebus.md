### <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma conta de [Barramento de serviço](https://azure.microsoft.com/services/service-bus/) .  

Antes de poder usar sua conta de barramento de serviço do Azure em um aplicativo de lógica, você deve autorizar o aplicativo de lógica para se conectar à sua conta de serviço de barramento. Felizmente, você pode fazer isso facilmente a partir de dentro de seu aplicativo de lógica no portal do Azure.  

Aqui estão as etapas para autorizar seu aplicativo de lógica para se conectar à sua conta de barramento de serviço:  

1. Para criar uma conexão para barramento de serviço, no designer de aplicativo de lógica, selecione **Mostrar Microsoft APIs gerenciadas** na lista suspensa. Insira **barramento de serviço** na caixa de pesquisa. Selecione o disparador ou a ação que você deseja usar.  
    ![Imagem de conexão de serviço barramento 1](./media/connectors-create-api-servicebus/servicebus-1.png)  

2. Se você ainda não criou qualquer conexões para barramento de serviço antes, você será solicitado a fornecer suas credenciais de barramento de serviço. Essas credenciais são usadas para autorizar seu aplicativo de lógica conectem e acessar os dados da sua conta de barramento de serviço. O conector de barramento de serviço necessidades a cadeia de conexão do namespace barramento de serviço. Também é necessário **Gerenciar** permissões. Uma boa maneira de saber se a cadeia de caracteres de conexão é para o namespace ou uma entidade específica é se ela contém o `EntityPath` parâmetro. Em caso afirmativo, não é a cadeia de conexão correto para um aplicativo de lógica.  
    ![Cadeia de conexão do barramento de serviço](./media/connectors-create-api-servicebus/connectionstring.png)

1. Depois que você recebeu a cadeia de conexão do namespace, você pode usá-lo para a conexão de API nos aplicativos de lógica.  
    ![Imagem de conexão de serviço barramento 2](./media/connectors-create-api-servicebus/servicebus-2.png)  

3. Observe que a conexão tiver sido criado, e agora estiver livre para continuar com as outras etapas em seu aplicativo de lógica.  
    ![Imagem de conexão de serviço barramento 3](./media/connectors-create-api-servicebus/servicebus-3.png)   
