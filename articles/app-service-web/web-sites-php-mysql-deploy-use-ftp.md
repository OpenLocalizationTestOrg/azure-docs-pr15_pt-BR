<properties 
    pageTitle="Criar um aplicativo da web de PHP MySQL no serviço de aplicativo do Azure e implantar usando FTP" 
    description="Um tutorial que demonstra como criar um aplicativo web PHP que armazena dados em MySQL e uso implantação de FTP no Azure." 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Criar um aplicativo da web de PHP MySQL no serviço de aplicativo do Azure e implantar usando FTP

Este tutorial mostra como criar um aplicativo da web de PHP MySQL e como implantá-lo usando FTP. Este tutorial supõe que você possui [PHP][install-php], [MySQL][install-mysql], um servidor web e um cliente FTP instalado no seu computador. As instruções neste tutorial podem ser seguidas em qualquer sistema operacional, incluindo Windows, Mac e Linux. Após concluir este guia, você terá um aplicativo web do PHP/MySQL em execução no Azure.
 
Você aprenderá:

* Como criar um aplicativo web e um banco de dados MySQL usando o Portal do Azure. Como PHP está habilitado nos aplicativos Web por padrão, nada de especial é necessário para executar seu código PHP.
* Como publicar seu aplicativo no Azure usando FTP.
 
Seguindo este tutorial, você irá criar um aplicativo da web de registro simples no PHP. O aplicativo será hospedado em um aplicativo Web. Uma captura de tela da aplicação concluída está abaixo:

![Site da Web PHP Azure][running-app]

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório, sem compromissos. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Criar um aplicativo web e configurar a publicação de FTP

Siga estas etapas para criar um aplicativo web e um banco de dados MySQL:

1. Faça logon no [Portal do Azure][management-portal].
2. Clique no ícone **+ novo** na parte superior esquerdo do Portal do Azure.

    ![Criar novo Site Azure][new-website]

3. Em Pesquisar, digite **Web app + MySQL** e clique em **aplicativo Web + MySQL**.

    ![Personalizado criar um novo Site][custom-create]

4. Clique em **criar**. Insira um nome de serviço de aplicativo exclusiva, um nome válido para o grupo de recursos e um novo plano de serviço.

    ![Nome do grupo de recursos de conjunto][resource-group]


6. Insira valores para seu novo banco de dados, incluindo concordar com os termos legais.

    ![Criar um novo banco de dados MySQL][new-mysql-db]
    
7. Quando o aplicativo web tiver sido criado, você verá a nova lâmina de serviço de aplicativo.


6. Clique em **configurações** > **credenciais de implantação**. 

    ![Definir credenciais de implantação][set-deployment-credentials]

7. Para habilitar a publicação de FTP, você deve fornecer um nome de usuário e senha. Salvar as credenciais e anote o nome de usuário e senha que você cria.

    ![Criar publicação de credenciais][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Criar e testar seu aplicativo localmente

O aplicativo de registro é um aplicativo PHP simples que permite que você registre para um evento, fornecendo seu nome e endereço de email. Informações sobre inscritos anterior são exibidas em uma tabela. Informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste em dois arquivos:

* **index**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.
* **CreateTable.PHP**: cria a tabela de MySQL para o aplicativo. Este arquivo somente será usado uma vez.

Criar e executar o aplicativo localmente, siga as etapas abaixo. Observe que estas etapas pressupõem que você tem PHP, MySQL, e um servidor web configurar o seu computador local e que você habilitou a [extensão PDO para MySQL][pdo-mysql].

1. Criar um banco de dados MySQL chamado `registration`. Você pode fazer isso no prompt de comando MySQL com este comando:

        mysql> create database registration;

2. No diretório de raiz do seu servidor web, crie uma pasta chamada `registration` e criar dois arquivos no-- um chamado `createtable.php` e outro denominado `index.php`.

3. Abrir o `createtable.php` arquivo em um editor de texto ou IDE e adicione o código abaixo. Este código será usado para criar o `registration_tbl` tabela no `registration` banco de dados.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > Você precisará atualizar os valores para <code>$user</code> e <code>$pwd</code> com seu nome de usuário de MySQL local e a senha.

4. Abra um navegador da web e navegue até [http://localhost/registration/createtable.php][localhost-createtable]. Isso criará o `registration_tbl` tabela no banco de dados.

5. Abra o arquivo **index** em um editor de texto ou IDE e adicione o código HTML e CSS básico para a página (o código PHP será adicionado em etapas posteriores).

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

        ?>
        </body>
        </html>

6. Nas marcas PHP, adicione código PHP para conectar-se ao banco de dados.

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > Novamente, você precisará atualizar os valores para <code>$user</code> e <code>$pwd</code> com seu nome de usuário de MySQL local e a senha.

7. Após o código de conexão de banco de dados, adicione código para inserir informações de registro no banco de dados.

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

8. Por fim, seguindo o código acima, adicione código para recuperar dados do banco de dados.

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

Agora você pode navegar para [http://localhost/registration/index.php] [ localhost-index] para testar o aplicativo.

##<a name="get-mysql-and-ftp-connection-information"></a>Obter informações de conexão MySQL e FTP

Para se conectar ao banco de dados MySQL que é executado em aplicativos Web, sua serão necessárias as informações de conexão. Para obter informações de conexão do MySQL, siga estas etapas:

1. Do serviço de aplicativo blade de aplicativo web clique no link de grupo de recursos:

    ![Selecionar o grupo de recursos][select-resourcegroup]

1. No seu grupo de recursos, clique em banco de dados:

    ![Selecione o banco de dados][select-database]

2. No banco de dados resumo, selecione **configurações** > **Propriedades**.

    ![Selecione propriedades][select-properties]
    
2. Tome nota dos valores de `Database`, `Host`, `User Id`, e `Password`.

    ![Propriedades de anotação][note-properties]

3. De seu aplicativo web, clique no link **Download publicar perfil** no canto inferior direito da página:

    ![Download publicar perfil][download-publish-profile]

4. Abrir o `.publishsettings` arquivo em um editor de XML. 

3. Encontre o `<publishProfile >` elemento com `publishMethod="FTP"` que fica assim:

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Anote o `publishUrl`, `userName`, e `userPWD` atributos.

##<a name="publish-your-app"></a>Publicar seu aplicativo

Depois que você testar seu aplicativo localmente, você pode publicar no seu aplicativo web usando FTP. No entanto, primeiro é necessário atualizar as informações de conexão de banco de dados no aplicativo. Usando as informações de conexão de banco de dados que você obteve anteriormente ( **obter MySQL e informações de conexão de FTP** seção), atualize as seguintes informações em **ambos** os `createdatabase.php` e `index.php` arquivos com os valores apropriados:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Agora você está pronto para publicar seu aplicativo usando FTP.

1. Abra seu cliente de FTP de escolha.

2. Insira a *parte do nome de host* do `publishUrl` atributo você anotou anteriormente em seu cliente de FTP.

3. Insira o `userName` e `userPWD` inalterado atributos você anotou anteriormente em seu cliente de FTP.

4. Estabelece uma conexão.

Depois que você tiver conectado será capaz de carregar e baixar arquivos conforme necessário. Certifique-se de que você está carregando arquivos para o diretório raiz, que é `/site/wwwroot`.

Após o upload de ambos `index.php` e `createtable.php`, navegue até **http://[site name].azurewebsites.net/createtable.php** criar a tabela de MySQL para o aplicativo e, em seguida, navegue até **http://[site name].azurewebsites.net/index.php** para começar a usar o aplicativo.
 
## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [PHP Developer Center](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
