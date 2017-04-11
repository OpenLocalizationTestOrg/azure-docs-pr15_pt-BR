<properties
    pageTitle="Criar e se conectar a um banco de dados MySQL no Azure"
    description="Saiba como usar o portal do Azure para criar um banco de dados MySQL e, em seguida, conectá-lo de um aplicativo da web PHP no Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Criar e se conectar a um banco de dados MySQL no Azure

Este tutorial mostra como criar um banco de dados MySQL no [portal do Azure](https://portal.azure.com) (provedor é [ClearDB](http://www.cleardb.com/)) e como se conectar a ele partir de um aplicativo web do PHP em execução no [Serviço de aplicativo do Azure](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] Você também pode criar um banco de dados MySQL como parte de um [modelo de aplicativo do Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Criar um banco de dados MySQL no portal do Azure

Para criar um banco de dados MySQL no portal do Azure, faça o seguinte:

1. Faça logon no [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, clique em **novo** > **dados + armazenamento** > **Banco de dados MySQL**.

    ![Criar um banco de dados MySQL no Azure - início](./media/store-php-create-mysql-database/create-db-1-start.png)

2. O novo banco de dados MySQL [blade](azure-portal-overview.md), configurar seu novo banco de dados MySQL da seguinte maneira (*blade*: uma página do portal que abre horizontalmente):

    - **Nome do banco de dados**: digite um nome exclusivamente identificável
    - **Assinatura**: escolha a assinatura usar
    - **Tipo de banco de dados**: selecione **compartilhado** para níveis de baixo custo ou livres ou **dedicada** obter recursos dedicados. 
    - **Grupo de recursos**: adicionar o banco de dados MySQL a um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) de existente ou colocá-lo em um novo. Recursos no mesmo grupo podem ser gerenciados facilmente juntos.
    - **Local**: selecione um local próximo a você. Ao adicionar a um grupo existente do recurso, você está bloqueado para local do grupo de recursos.
    - **Nível de preços**: clique em **Nível de preços**, selecione uma opção de preços (camada de**Mercúrio** é gratuita) e, em seguida, clique em **Selecionar**. 
    - **Termos legais**: clique **Termos legais**, examine os detalhes de compra e clique em **compra**.
    - **Fixar em dashboard**: selecione se desejar acessá-lo diretamente do dashboard. Isso é especialmente útil se você não estiver familiarizado com a navegação de portal ainda.
    
    A captura de tela a seguir é apenas um exemplo de como você pode configurar o seu banco de dados MySQL.  
    ![Criar um banco de dados MySQL no Azure - configurar](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Quando você terminar de configurar, clique em **criar**.

    ![Criar um banco de dados MySQL no Azure - criar](./media/store-php-create-mysql-database/create-db-3-create.png)

    Você verá uma notificação pop-up opção que permite que você sabe que iniciou uma implantação.

    ![Criar um banco de dados MySQL no Azure - em andamento](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Você obterá outro pop-up depois de implantação foi bem-sucedido. O portal também abrirá automaticamente sua blade de banco de dados MySQL.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Conectar ao seu banco de dados MySQL

Para ver as informações de conexão para seu novo banco de dados MySQL, basta clicar em **Propriedades** blade do web app.
    
![Criar um banco de dados MySQL no Azure - blade de banco de dados MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Agora você pode usar essas informações de conexão em qualquer aplicativo web. Um exemplo que mostra como usar as informações de conexão de um aplicativo PHP simple está disponível [aqui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Conectar um aplicativo da web de Laravel (do PHP get iniciado tutorial de)

Suponha que você acabou de [criar, configurar e implantar um aplicativo da web PHP no Azure](./app-service-web/app-service-web-php-get-started.md) tutorial e ter um aplicativo da web de [Laravel](https://www.laravel.com/) em execução no Azure. Você pode adicionar facilmente os recursos de banco de dados para o seu aplicativo de Laravel. Siga as etapas abaixo:

>[AZURE.NOTE] As etapas a seguir presumem que você concluiu o tutorial [criar, configurar e implantar um aplicativo da web PHP no Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configure o aplicativo de Laravel em seu ambiente de desenvolvimento local para apontar para o banco de dados MySQL. Para fazer isso, abra `.env` de seu aplicativo de Laravel pasta raiz e configure as opções de banco de dados MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] Na lâmina **Propriedades** , o nome do seu banco de dados MySQL pode ou não ser mostrada no campo **Nome do banco de dados** . É melhor verificar o parâmetro de banco de dados do campo de **Cadeia de conexão** . 
    >
    >![Criar um banco de dados MySQL no Azure - em andamento](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. A maneira mais rápida de verificar se você tem acesso de MySQL agora é usar a [estrutura de autenticação padrão do Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart). No terminal de linha de comando, execute os seguintes comandos do diretório de raiz do seu aplicativo de Laravel:

         php artisan migrate
         php artisan make:auth

    O primeiro comando cria as tabelas no Azure com base em migrações predefinidas na `database/migrations` diretório e o segundo comando scaffolds as exibições básicas e rotas para autenticação e registro do usuário.

3. Execute o servidor de desenvolvimento agora:

        php artisan serve

4. No navegador, navegue até http://localhost:8000 e registrar um novo usuário, como mostrado:

    ![Conectar ao banco de dados MySQL no Azure - registrar usuário](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Siga os prompts de interface do usuário completo o registro. Após a conclusão do registro, você vai ser conectado.
    
    ![Conectar ao banco de dados MySQL no Azure - registrar usuário](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Agora você está desenvolvendo seu aplicativo no banco de dados MySQL no Azure.

5. Agora, você só precisa replicar suas `.env` configurações para seu aplicativo web Azure. Execute os seguintes comandos do Azure:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Descubra como isso funciona no [Configure o Azure web app](./app-service-web/app-service-web-php-get-started.md#configure).

6. Em seguida, confirmar e enviar para Azure as alterações locais feitas anteriores durante a execução `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Navegue até o Azure web app.

        azure site browse

8. Faça logon usando as credenciais do usuário que você criou anteriormente.

    ![Conectar ao banco de dados MySQL no Azure - procurar Azure web app](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Após efetuar login, você verá a tela de login POST amigável.
    
    ![Conectar ao banco de dados MySQL no Azure - conectado](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Parabéns, seu aplicativo web do PHP no Azure agora está acessando dados de seu banco de dados MySQL. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [PHP Developer Center](/develop/php/).
