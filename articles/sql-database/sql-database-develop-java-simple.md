<properties
    pageTitle="Conectar ao banco de dados SQL usando Java com JDBC no Windows | Microsoft Azure"
    description="Apresenta um exemplo de código de Java que você pode usar para se conectar ao banco de dados do SQL Azure. O exemplo usa JDBC e é executado em um computador de cliente do Windows."
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Conectar ao banco de dados SQL usando Java com JDBC no Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tópico apresenta uma amostra de código Java que você pode usar para se conectar ao banco de dados do SQL Azure. A amostra de Java se baseia no Java Development Kit (JDK) versão 1.8. O exemplo se conecta a um banco de dados do SQL Azure usando o driver JDBC.

## <a name="step-1--configure-development-environment"></a>Etapa 1: Configurar o ambiente de desenvolvimento

[Configurar o ambiente de desenvolvimento para desenvolvimento de Java](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>Etapa 2: Criar um banco de dados do SQL

Consulte a [página de Introdução](sql-database-get-started.md) para aprender a criar um banco de dados.  

## <a name="step-3-get-connection-string"></a>Etapa 3: Obter a cadeia de Conexão

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Se você estiver usando o driver JTDS JDBC, você precisará adicionar "ssl = exigir" para a URL da conexão cadeia de caracteres e você precisa definir a seguinte opção da JVM "-Djsse.enableCBCProtection=false". Esta opção JVM desativa uma correção para uma vulnerabilidade de segurança, portanto, verifique se que você compreender quais riscos envolvidos antes de definir essa opção.

## <a name="step-4-run-sample-code"></a>Etapa 4: Executar o código de exemplo

* [Prova de conceito conectando ao SQL usando Java](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>Próximas etapas

* Visite a [Central de desenvolvedores de Java](/develop/java/).
* Examine a [Visão geral de desenvolvimento de banco de dados do SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [Driver de JDBC da Microsoft para o SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>Recursos adicionais 

* [Padrões de design para aplicativos de vários locatários SaaS com banco de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorar todos os [recursos do banco de dados SQL](https://azure.microsoft.com/services/sql-database/)
