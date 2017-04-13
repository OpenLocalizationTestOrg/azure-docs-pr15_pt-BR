<properties
    pageTitle="Configurar PostgreSQL em uma VM Linux | Microsoft Azure"
    description="Saiba como instalar e configurar PostgreSQL em um computador de virtual Linux no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Instalar e configurar PostgreSQL no Azure

PostgreSQL é um código-fonte aberto banco de dados avançado semelhante ao Oracle e DB2. Ele inclui recursos prontos para empresas como conformidade total ACID, processamento de transações confiável e controle de concorrência de várias versões. Ele também oferece suporte a padrões como ANSI SQL e SQL/MED (incluindo conteúdos adicionais de dados externa para Oracle, MySQL, MongoDB e muitos outros). É altamente extensível com suporte para mais de 12 linguagens de procedimento, índices GIM e essência, suporte a dados espacial e vários recursos NoSQL para JSON ou aplicativos baseados em chave-valor.

Neste artigo, você aprenderá como instalar e configurar PostgreSQL em uma máquina virtual Azure executando Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Instalar PostgreSQL

> [AZURE.NOTE] Você já deve ter uma máquina virtual Azure executando Linux para concluir este tutorial. Para criar e configurar uma VM Linux antes de continuar, consulte o [tutorial de máquina virtual do Azure Linux](virtual-machines-linux-quick-create-cli.md).

Nesse caso, use a porta 1999 como a porta PostgreSQL.  

Conecte a máquina virtual que você criou via acabamento Linux. Se esta for a primeira vez que você estiver usando uma máquina virtual Linux do Azure, veja [como usar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md) para aprender a usar acabamento para se conectar a uma VM Linux.

1. Execute o seguinte comando para alternar para a raiz (admin):

        # sudo su -

2. Algumas distribuições tem dependências que você deve instalar antes de instalar PostgreSQL. Verificar sua distribuição desta lista e execute o comando apropriado:

    - Red Hat Linux base:

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian Linux base:

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux:

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Baixar PostgreSQL no diretório raiz e, em seguida, descompactar o pacote:

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    Acima é um exemplo. Você pode encontrar o endereço de download mais detalhado no [índice de /pub/origem /](https://ftp.postgresql.org/pub/source/).

4. Para começar a criar, execute estes comandos:

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Se você quiser criar tudo o que pode ser criado, incluindo a documentação (páginas HTML e homem) e módulos adicionais (Contribuidor), execute o seguinte comando em vez disso:

        # gmake install-world

    Você deve receber a seguinte mensagem de confirmação:

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar PostgreSQL

1. (Opcional) Crie um link simbólico para reduzir a referência PostgreSQL para não incluir o número da versão:

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Crie um diretório para o banco de dados:

        # mkdir -p /opt/pgsql_data

3. Criar um usuário não raiz e modificar o perfil de usuário. Em seguida, alterne para esse novo usuário (chamado *postgres* em nosso exemplo):

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Por razões de segurança, PostgreSQL usa um usuário não raiz para inicializar, iniciar ou encerrar o banco de dados.


4. Edite o arquivo *bash_profile* por meio de comandos a seguir. Essas linhas serão adicionadas ao final do arquivo *bash_profile* :

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Execute o arquivo *bash_profile* :

        $ source .bash_profile

6. Valide sua instalação usando o seguinte comando:

        $ which psql

    Se a instalação for bem-sucedida, você verá a seguinte resposta:

        /opt/pgsql/bin/psql

7. Você também pode verificar a versão de PostgreSQL:

        $ psql -V

8. Inicialize o banco de dados:

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Você deve receber a seguinte saída:

![imagem](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modificar duas variáveis no arquivo /etc/init.d/postgresql. O prefixo está definido como o caminho de instalação do PostgreSQL: **/opt/pgsql**. PGDATA está definido como o caminho de armazenamento de dados do PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![imagem](./media/virtual-machines-linux-postgresql-install/no2.png)

Altere o arquivo para torná-lo executável:

    # chmod +x /etc/init.d/postgresql

Inicie PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto de extremidade de PostgreSQL está ativado:

    # netstat -tunlp|grep 1999

Você verá a seguinte saída:

![imagem](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Conectar ao banco de dados Postgres

Alterne para o usuário postgres novamente:

    # su - postgres

Crie um banco de dados Postgres:

    $ createdb events

Conectar-se ao banco de dados eventos que você acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e excluir uma tabela de Postgres

Agora que você tiver conectado ao banco de dados, você pode criar tabelas nele.

Por exemplo, crie uma nova tabela Postgres exemplo usando o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

Agora você configurou uma tabela de quatro colunas com os seguintes nomes de coluna e restrições:

1. A coluna "nome" tem sido limitada pelo comando VARCHAR em 20 caracteres.
2. A coluna "alimentos" indica o item de alimento que cada pessoa colocará. VARCHAR limita este texto para ser em 30 caracteres.
3. A coluna "confirmada" registros se a pessoa tem RSVP'd para a festa americana. Os valores aceitáveis são "Y" e "N".
4. A coluna de "Data" mostra quando eles se inscreveu para o evento. Postgres requer que datas sejam gravados como aaaa-mm-dd

Se sua tabela tiver sido criada com êxito, você deverá ver o seguinte:

![imagem](./media/virtual-machines-linux-postgresql-install/no4.png)

Você também pode verificar a estrutura da tabela usando o comando a seguir:

![imagem](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela

Primeiro, inserir informações em uma linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Você deve ver este resultado:

![imagem](./media/virtual-machines-linux-postgresql-install/no6.png)

Você pode adicionar mais pessoas algumas à tabela também. Aqui estão algumas opções, ou você pode criar sua própria:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar tabelas

Use o seguinte comando para mostrar uma tabela:

    select * from potluck;

A saída é:

![imagem](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Excluir dados em uma tabela

Use o seguinte comando para excluir dados em uma tabela:

    delete from potluck where name=’John’;

Isso exclui todas as informações da linha de "João". A saída é:

![imagem](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar dados em uma tabela

Use o seguinte comando para atualizar dados em uma tabela. Para esta, areia da confirmou que ela está participando, portanto, alteraremos dela RSVP de "N" para "Y":

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Obter mais informações sobre PostgreSQL
Agora que você concluiu a instalação do PostgreSQL em uma máquina virtual Linux do Azure, você pode aproveitar usá-lo no Azure. Para saber mais sobre PostgreSQL, visite o [site de PostgreSQL](http://www.postgresql.org/).
