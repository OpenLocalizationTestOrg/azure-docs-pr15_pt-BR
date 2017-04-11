<properties
   pageTitle="Introdução ao Temporal tabelas no banco de dados do SQL Azure | Microsoft Azure"
   description="Saiba como começar a usar usando tabelas Temporal no Azure SQL Database."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introdução ao Temporal tabelas no banco de dados do SQL Azure

Tabelas Temporais são um novo recurso de programação do Azure SQL Database que permite controlar e analisar o histórico completo de alterações em seus dados, sem a necessidade de codificação personalizada. Tabelas temporais mantém dados estreitamente relacionados ao contexto de tempo para que fatos armazenados podem ser interpretados como válido somente dentro do período específico. Essa propriedade de tabelas Temporal permite eficiente análise baseada em tempo e obter percepções do evolução de dados.

##<a name="temporal-scenario"></a>Cenário temporal

Este artigo ilustra as etapas para utilizar Temporal tabelas em um cenário de aplicativo. Suponha que você queira controlar atividades do usuário em um novo site que está sendo desenvolvido a partir do zero ou em um site existente que você deseja estender com análises de atividades do usuário. Neste exemplo simplificado, vamos supor que o número de páginas da web visitados durante um período de tempo é um indicador que precisa ser capturado e monitorados no banco de dados site está hospedado no Azure SQL Database. O objetivo da análise histórica de atividades do usuário é obter entradas reformular site e fornecer a melhor experiência para os visitantes.

O modelo de banco de dados para esse cenário é bem simple – métricas de atividade de usuário é representada com um campo único inteiro, **PageVisited**e é capturada juntamente com informações básicas sobre o perfil de usuário. Além disso, para análise de tempo com base, você prefere manter uma série de linhas para cada usuário, onde cada linha representa o número de páginas que um determinado usuário visitou em um período específico de tempo.

![Esquema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Felizmente, você não precisa colocar qualquer esforço em seu aplicativo para manter essas informações de atividade. Com tabelas Temporal, esse processo é automatizado - dando a você flexibilidade completa durante o design do site e mais tempo para focalizar a análise de dados em si. A única coisa que você precisa fazer é garantir que **WebSiteInfo** tabela está configurada como [temporal sistema versão](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). As etapas exatas para utilizar tabelas Temporal neste cenário são descritas abaixo.

##<a name="step-1-configure-tables-as-temporal"></a>Etapa 1: Configurar tabelas como temporal

Dependendo se você estiver iniciando o desenvolvimento de novos ou atualizar o aplicativo existente, você irá criar tabelas temporais ou modificar as existentes adicionando atributos Temporais. Em geral caso, seu cenário pode ser uma mistura dessas duas opções. Execute estas ação usando o [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [Ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou qualquer outra ferramenta de desenvolvimento do Transact-SQL.


> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Criar nova tabela

Use o item de menu de contexto "Nova tabela sistema versão" no Pesquisador de objetos SSMS para abrir o editor de consulta com um script de modelo de tabela temporal e use "Especificar valores de parâmetros de modelo" (Ctrl + Shift + M) para preencher o modelo:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Em SSDT, escolheu modelo "Temporal tabela (sistema de número de versão)" ao adicionar novos itens para o projeto de banco de dados. Que serão abrir o designer de tabela e permitem especificar facilmente o layout da tabela:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Você também pode uma tabela temporal criar especificando as instruções Transact-SQL diretamente, conforme mostrado no exemplo abaixo. Observe que os elementos obrigatórios de cada tabela temporal são a definição do período e a cláusula SYSTEM_VERSIONING com uma referência a outra tabela de usuário que armazenará versões de histórico de linha:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Quando você criar tabela temporal sistema versão, a tabela de histórico que acompanha com a configuração padrão é criada automaticamente. A tabela de histórico padrão contém um índice de árvore B clusterizado nas colunas de período (final, Iniciar) com compactação de página ativada. Essa configuração é ideal para a maioria dos cenários em que tabelas temporais forem usadas, especialmente para [dados de auditoria](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Neste caso em particular, nosso objetivo executar análise de tendência baseada no tempo ao longo de um histórico de dados mais e com conjuntos de dados maiores, para que a opção de armazenamento para a tabela de histórico é um índice clusterizado columnstore. Um cluster columnstore oferece muito boa compactação e desempenho para consultas analíticas. Tabelas temporais oferecem a flexibilidade para configurar índices nas tabelas atuais e temporais completamente independentemente. 

**Observação**: Columnstore índices estão disponíveis apenas no nível de serviço premium.

O script a seguir mostra como o índice de padrão na tabela de histórico pode ser alterado para o columnstore agrupada:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tabelas Temporais são representadas no Pesquisador de objetos com o ícone específico para facilitar a identificação, enquanto sua tabela de histórico é exibida como um nó filho.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Alterar tabela existente para temporal

Vamos abordar o cenário alternativo no qual a tabela de WebsiteUserInfo já existe, mas não foi projetada para manter um histórico de alterações. Nesse caso, você pode simplesmente estender a tabela existente se tornar temporal, conforme mostrado no exemplo a seguir:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Etapa 2: Executar sua carga de trabalho regularmente

A principal vantagem de tabelas Temporal é que você não precisa alterar ou ajustar seu site de qualquer forma ao executar o controle de alterações. Uma vez criado, tabelas Temporal persistir transparente versões anteriores de linha, sempre que você executar modificações em seus dados. 

Para aproveitar automáticas controle de alterações para esse cenário específico, vamos apenas atualizar coluna **PagesVisited** toda vez quando o usuário termina Component sessão no site:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

É importante notar que a consulta atualização não precisa saber o tempo exato quando a operação real ocorreu nem como dados históricos serão preservados para análise futura. Ambos os aspectos são tratados automaticamente o banco de dados do SQL Azure. O diagrama a seguir ilustra como os dados de histórico está sendo gerados em cada atualização.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Etapa 3: Executar análise de dados históricos

Agora quando sistema temporal-controle de versão está habilitado, a análise de dados históricos é apenas uma consulta longe de você. Neste artigo, forneceremos alguns exemplos desse endereço análise cenários comuns - saiba todos os detalhes, explorar várias opções introduzidas com a cláusula [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Para ver os principais 10 usuários ordenados pelo número de páginas da web visitados a partir de uma hora atrás, execute esta consulta:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Você pode modificar facilmente essa consulta para analisar as visitas de site a partir de um dia atrás, um mês atrás ou em qualquer ponto no passado desejar.

Para executar análises estatísticas básicas para o dia anterior, use o exemplo a seguir:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Para procurar as atividades de um usuário específico, dentro de um período de tempo, use a cláusula IN contidos:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualização de gráfica é especialmente conveniente para consultas temporais conforme você pode mostrar tendências e padrões de uso em um intuitiva maneira muito facilmente:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Evolução esquema de tabela

Normalmente, você precisará alterar o esquema de tabela temporal enquanto você está fazendo o desenvolvimento de aplicativos. Para isso, basta executar regulares ALTER TABLE instruções e Azure SQL Database irá adequadamente propagar as alterações na tabela de histórico. O script a seguir mostra como você pode adicionar o atributo adicional para acompanhamento:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Da mesma forma, você pode alterar a definição de coluna enquanto sua carga de trabalho está ativa:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Por fim, você pode remover uma coluna que você não precisa mais.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
Como alternativa, use mais recente [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) para alterar o esquema de tabela temporal enquanto você estiver conectado ao banco de dados (modo online) ou como parte do projeto banco de dados (modo offline).

##<a name="controlling-retention-of-historical-data"></a>Controlando a retenção de dados históricos

Com o sistema versão tabelas temporais, a tabela de histórico pode aumentar o tamanho do banco de dados em mais de tabelas comuns. Uma tabela de histórico de grandes e crescentes pode se tornar um problema ambos devido a custos de armazenamento puro, bem como impondo um desempenho imposto sobre consultando temporal. Portanto, ao desenvolvimento de uma política de retenção de dados de gerenciamento de dados na tabela de histórico é um aspecto importante de planejar e gerenciar o ciclo de vida de cada tabela temporal. Com o banco de dados do SQL Azure, você tem as seguintes abordagens para o gerenciamento de dados históricos na tabela de dados temporal:

- [Partição de tabela](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Script de limpeza personalizada](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Próximas etapas

Para obter informações detalhadas sobre tabelas Temporal, confira a [documentação MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visite o canal 9 para ouvir uma [história de sucesso do cliente real implementação temporal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e assistir a um [live demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
