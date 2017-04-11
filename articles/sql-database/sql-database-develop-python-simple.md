<properties
    pageTitle="Conectar ao banco de dados SQL usando Python | Microsoft Azure"
    description="Apresenta um exemplo de código Python que você pode usar para se conectar ao banco de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>Conectar ao banco de dados SQL usando Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tópico mostra como conectar e consultar um banco de dados de SQL Azure usando Python. Você pode executar este exemplo plataformas Windows, Mac ou Ubuntu Linux.


## <a name="step-1-create-a-sql-database"></a>Etapa 1: Criar um banco de dados do SQL

Consulte a [página de Introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo.  É importante que você segue o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos abaixo só funcionam com o **esquema de AdventureWorks**. Depois de criar seu banco de dados tornar-se você habilitar o acesso ao seu endereço de IP, permitindo que as regras de firewall, conforme descrito na [página de Introdução](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Etapa 2: Configurar o ambiente de desenvolvimento

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Instalar os módulos necessários
Abra o terminal e instalar

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Abra o terminal e navegue até uma pasta onde você planeja criar seu python script. Digite os seguintes comandos para instalar **FreeTDS** e **pymssql**. pymssql usa FreeTDS para se conectar aos bancos de dados do SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

Instale o pymssql [**aqui**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Certifique-se de que você escolha o arquivo de whl correta. Por exemplo: escolha se você estiver usando Python 2.7 em uma máquina de 64 bits: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Após baixar o arquivo .whl colocá-lo na pasta c: / Python27.

Agora, instale o driver de pymssql usando pip linha de comando. CD em c: / Python27 e execute o seguinte
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Instruções para habilitar o uso pip podem ser encontradas [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Etapa 3: Executar o código de exemplo

Crie um arquivo denominado **sql_sample.py** e cole o seguinte código dentro dela. Você pode executar esta linha de comando usando:
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Conectar ao seu banco de dados do SQL

A função [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) é usada para se conectar ao banco de dados SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Executar uma instrução SQL SELECT

A função [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) pode ser usada para recuperar um conjunto de resultados de uma consulta de banco de dados do SQL. Esta função essencialmente aceita qualquer consulta e retorna que um conjunto de resultados que pode ser iteradas com o uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserir uma linha, passar parâmetros e recuperar a chave primária gerada

No banco de dados SQL a propriedade de [identidade](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto de [SEQUÊNCIA](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente valores de [chave primária](https://msdn.microsoft.com/library/ms179610.aspx) . 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transações


Este exemplo de código demonstra o uso de transações na qual você:

* Iniciar uma transação
* Inserir uma linha de dados
* Reversão sua transação para desfazer a inserção 

Cole o seguinte código dentro sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Próximas etapas

* Examine a [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [Driver de Python da Microsoft para o SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visite a [Central de desenvolvedores do Python](/develop/python/).

## <a name="additional-resources"></a>Recursos adicionais 

* [Padrões de design para aplicativos de vários locatários SaaS com banco de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorar todos os [recursos do banco de dados SQL](https://azure.microsoft.com/services/sql-database/)
