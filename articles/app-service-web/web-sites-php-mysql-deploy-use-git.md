<properties
    pageTitle="Criar um aplicativo da web de PHP MySQL no serviço de aplicativo do Azure e implantar usando gito"
    description="Um tutorial que demonstra como criar um aplicativo web PHP que armazena dados em MySQL e usar implantação gito no Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Criar um aplicativo da web de PHP MySQL no serviço de aplicativo do Azure e implantar usando gito

Este tutorial mostra como criar um aplicativo da web de PHP MySQL e como implantá-lo ao [Serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) usando gito. Você usará [PHP][install-php], a ferramenta de linha de comando de MySQL (parte do [MySQL][install-mysql]) e [gito] [ install-git] instalado no seu computador. As instruções neste tutorial podem ser seguidas em qualquer sistema operacional, incluindo Windows, Mac e Linux. Após concluir este guia, você terá um aplicativo web do PHP/MySQL em execução no Azure.

Você aprenderá:

* Como criar um aplicativo web e um banco de dados MySQL usando o [Portal do Azure][management-portal]. Como PHP está ativado no [Aplicativo de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) por padrão, nada de especial é necessário para executar seu código PHP.
* Como publicar e republicar em seu aplicativo no Azure usando gito.
* Como habilitar a extensão de compositor de compositor de automatizar tarefas em cada `git push`.

Seguindo este tutorial, você irá criar um aplicativo da web de registro simples no PHP. O aplicativo será hospedado nos aplicativos Web. Uma captura de tela da aplicação concluída está abaixo:

![Site da web PHP Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Este tutorial supõe que você possui [PHP][install-php], a ferramenta de linha de comando de MySQL (parte do [MySQL][install-mysql]) e [gito] [ install-git] instalado no seu computador.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Criar um aplicativo web e configurar a publicação de gito

Siga estas etapas para criar um aplicativo web e um banco de dados MySQL:

1. Faça logon no [Portal do Azure][management-portal].
2. Clique no ícone de **novo** .

3. Clique em **Ver tudo** ao lado de **Marketplace**. 

4. Clique em **Web + Mobile**, então **Web app + MySQL**. Em seguida, clique em **criar**.

4. Insira um nome válido para seu grupo de recursos.

    ![Nome do grupo de recursos de conjunto][resource-group]

5. Insira valores para seu novo aplicativo web.

    ![Crie aplicativo web][new-web-app]

6. Insira valores para seu novo banco de dados, incluindo concordar com os termos legais.

    ![Criar um novo banco de dados MySQL][new-mysql-db]

7. Quando o aplicativo web tiver sido criado, você verá a nova lâmina de aplicativo web.

7. Nas **configurações** na **Implantação contínuo**, clique em _Configurar configurações necessárias_.

    ![Configurar a publicação de gito][setup-publishing]

8. Selecione **Repositório gito Local** para a fonte.

    ![Configurar o repositório de gito][setup-repository]


9. Para habilitar a publicação de gito, você deve fornecer um nome de usuário e senha. Anote o nome de usuário e senha que você cria. (Se você tiver configurado um repositório gito antes, esta etapa será ignorada.)

    ![Criar publicação de credenciais][credentials]


## <a name="get-remote-mysql-connection-information"></a>Obter informações de conexão MySQL remotas

Para se conectar ao banco de dados MySQL que é executado em aplicativos Web, sua serão necessárias as informações de conexão. Para obter informações de conexão do MySQL, siga estas etapas:

1. No seu grupo de recursos, clique em banco de dados:

    ![Selecione o banco de dados][select-database]

2. Do banco de **configurações**, selecione **Propriedades**.

    ![Selecione propriedades][select-properties]

2. Tome nota dos valores de `Database`, `Host`, `User Id`, e `Password`.

    ![Propriedades de anotação][note-properties]

## <a name="build-and-test-your-app-locally"></a>Criar e testar seu aplicativo localmente

Agora que você criou um aplicativo web, você pode desenvolver seu aplicativo localmente, e implantá-lo após o teste.

O aplicativo de registro é um aplicativo PHP simples que permite que você registre para um evento, fornecendo seu nome e endereço de email. Informações sobre inscritos anterior são exibidas em uma tabela. Informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste em um arquivo (copiar/colar código disponível abaixo):

* **index**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.

Criar e executar o aplicativo localmente, siga as etapas abaixo. Observe que estas etapas pressupõem que você tem o PHP e MySQL de linha de comando ferramenta (parte do MySQL) configurada no computador local, e que você ativou a [extensão PDO para MySQL][pdo-mysql].

1. Conectar ao servidor MySQL remoto, usando o valor de `Data Source`, `User Id`, `Password`, e `Database` que você recuperou anteriormente:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Prompt de comando MySQL aparecerão:

        mysql>

3. Colar no seguinte `CREATE TABLE` comando para criar a `registration_tbl` tabela no seu banco de dados:

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Na raiz da sua pasta de aplicativo local crie arquivo de **index** .

5. Abra o arquivo **index** em um editor de texto ou IDE e adicione o seguinte código e conclua as alterações necessárias marcadas com `//TODO:` comentários.


        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
            if(!empty($_POST)) {
            try {
                $name = $_POST['name'];
                $email = $_POST['email'];
                $date = date("Y-m-d");
                // Insert data
                $sql_insert = "INSERT INTO registration_tbl (name, email, date)
                           VALUES (?,?,?)";
                $stmt = $conn->prepare($sql_insert);
                $stmt->bindValue(1, $name);
                $stmt->bindValue(2, $email);
                $stmt->bindValue(3, $date);
                $stmt->execute();
            }
            catch(Exception $e) {
                die(var_dump($e));
            }
            echo "<h3>Your're registered!</h3>";
            }
            // Retrieve data
            $sql_select = "SELECT * FROM registration_tbl";
            $stmt = $conn->query($sql_select);
            $registrants = $stmt->fetchAll();
            if(count($registrants) > 0) {
                echo "<h2>People who are registered:</h2>";
                echo "<table>";
                echo "<tr><th>Name</th>";
                echo "<th>Email</th>";
                echo "<th>Date</th></tr>";
                foreach($registrants as $registrant) {
                    echo "<tr><td>".$registrant['name']."</td>";
                    echo "<td>".$registrant['email']."</td>";
                    echo "<td>".$registrant['date']."</td></tr>";
                }
                echo "</table>";
            } else {
                echo "<h3>No one is currently registered.</h3>";
            }
        ?>
        </body>
        </html>

4.  Em um terminal ir para a pasta de aplicativo e digite o seguinte comando:

        php -S localhost:8000

Agora você pode navegar para **http://localhost:8000 /** para testar o aplicativo.


## <a name="publish-your-app"></a>Publicar seu aplicativo

Depois que você testar seu aplicativo localmente, você pode publicá-lo usando gito do Web Apps. Você irá inicializar seu repositório gito local e publicar o aplicativo.

> [AZURE.NOTE]
> Estas são as mesmas etapas mostradas no Portal do Azure no final do conjunto e criar um aplicativo web gito publicação seção acima.

1. (Opcional)  Se você esqueceu ou no local incorreto sua URL de download remoto gito, navegue para as propriedades de aplicativo da web no Portal do Azure.

1. Abra GitBash (ou um terminal, se gito estiver em sua `PATH`), altere diretórios para o diretório raiz do seu aplicativo e execute os seguintes comandos:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Você será solicitado a senha que você criou anteriormente.

    ![Envio inicial no Azure via gito][git-initial-push]

2. Navegue até **http://[site name].azurewebsites.net/index.php** para começar a usar o aplicativo (essas informações serão armazenadas no seu painel de conta):

    ![Site da web PHP Azure][running-app]

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações a ele e usar gito publicá-los.

## <a name="publish-changes-to-your-app"></a>Publicar alterações em seu aplicativo

Para publicar as alterações em seu aplicativo, siga estas etapas:

1. Fazer alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, it gito está em seu `PATH`), altere os diretórios para o diretório raiz do seu aplicativo e execute os seguintes comandos:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Você será solicitado a senha que você criou anteriormente.

    ![Enviando alterações no site do Azure via gito][git-change-push]

3. Navegue até **http://[site name].azurewebsites.net/index.php** para ver seu aplicativo e quaisquer alterações feitas:

    ![Site da web PHP Azure][running-app]

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Habilitar automação compositor com a extensão de compositor

Por padrão, o processo de implantação de gito no serviço de aplicativo não faz nada com composer.json, se você tiver um em seu projeto PHP. Você pode habilitar composer.json processamento durante `git push` habilitando a extensão de compositor.

1. No seu PHP web blade do aplicativo no [portal do Azure][management-portal], clique em **Ferramentas** > **extensões**.

    ![Configurações de extensão compositor][composer-extension-settings]

2. Clique em **Adicionar**, clique em **compositor**.

    ![Adicionar extensão compositor][composer-extension-add]
    
3. Clique em **Okey** para aceitar os termos legais. Clique em **Okey** novamente para adicionar a extensão.

    A lâmina **extensões instalados** agora mostrará a extensão compositor.  
    ![Modo de exibição de extensão compositor][composer-extension-view]
    
4. Agora, realize `git add`, `git commit`, e `git push` , como na seção anterior. Agora, você verá que o compositor está instalando dependências definidas no composer.json.

    ![Compositor extensão sucesso][composer-extension-success]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [PHP Developer Center](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
