### <a name="prerequisites"></a>Pré-requisitos
- Uma conta de [SendGrid](https://www.SendGrid.com/) 

Antes de poder usar sua conta de SendGrid em um aplicativo de lógica, você deve autorizar o aplicativo de lógica para se conectar à sua conta de SendGrid. Felizmente, você pode fazer isso facilmente a partir de dentro de seu aplicativo de lógica no Portal do Azure. 

Aqui estão as etapas para autorizar seu aplicativo de lógica para se conectar à sua conta de SendGrid:

1. Para criar uma conexão para SendGrid, no designer de aplicativo de lógica, selecione **Mostrar Microsoft APIs gerenciadas** na lista suspensa e digite *SendGrid* na caixa de pesquisa. Selecione o disparador ou a ação que você gostará usar:  
  ![SendGrid etapa 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Se você ainda não criou as conexões SendGrid antes, você vai obter solicitado a fornecer suas credenciais de SendGrid. Essas credenciais serão usadas para autorizar seu aplicativo de lógica para se conectar ao e acessam os dados da sua conta de SendGrid:  
  ![SendGrid etapa 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Observe que a conexão tiver sido criado e agora você está livre para continuar com as outras etapas em seu aplicativo de lógica:  
  ![SendGrid etapa 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   
