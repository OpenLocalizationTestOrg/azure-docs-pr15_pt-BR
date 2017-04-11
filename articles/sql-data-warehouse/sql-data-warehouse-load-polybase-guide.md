<properties
   pageTitle="Guia para usar PolyBase no SQL Data Warehouse | Microsoft Azure"
   description="Diretrizes e recomendações para usar PolyBase em cenários de depósito de dados do SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guia para usar PolyBase no depósito de dados do SQL

Este guia fornece informações práticas para usar PolyBase no SQL Data Warehouse.

Para começar, consulte o tutorial de [carregar dados com PolyBase][] .


## <a name="rotating-storage-keys"></a>Rotação de chaves de armazenamento

De vez em quando deseja alterar a chave de acesso ao seu armazenamento de blob por razões de segurança.

A maneira mais elegante para executar esta tarefa é siga um processo conhecido como "girando as teclas". Você pode ter notado que você tem duas chaves de armazenamento para sua conta de armazenamento de blob. Isso é para que você pode fazer a transição

Girar suas chaves de conta de armazenamento do Azure é um processo simples de três etapas

1. Criar a segunda credencial de banco de dados com escopo com base na tecla de acesso de armazenamento secundário
2. Criar a segunda fonte de dados externa com base em desativar essa nova credencial
3. Cancele e crie as tabelas externas apontando para a nova fonte de dados externos

Quando você tiver migrado todos seus externos tabelas para a nova fonte de dados externa e em seguida, você pode executar as tarefas de limpeza:

1. Solte a primeira fonte de dados externos
2. Soltar primeiro banco de dados com escopo credencial com base na tecla de acesso do armazenamento principal
3. Faça logon em Azure e gerar novamente a chave de acesso primário pronta para a próxima vez

## <a name="query-azure-blob-storage-data"></a>Consultar dados de armazenamento de blob do Microsoft Azure
Consultas em tabelas externas simplesmente usam o nome da tabela, como se fosse uma tabela relacional.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Uma consulta em uma tabela externa pode falhar com o erro *"Consulta anulada – limite de rejeitar o máximo foi atingida durante a leitura de uma fonte externa"*. Isso indica que seus dados externos contém registros *sujo* . Um registro de dados é considerado 'Grosso' se os dados reais tipos/número de colunas não coincidem com as definições da coluna da tabela de dados externa ou se os dados não estão em conformidade com o formato de arquivo externo especificado. Para corrigir isso, certifique-se de que sua tabela externa e definições de formato de arquivo externo estão corretas e seus dados externos em conformidade com essas definições. No caso de um subconjunto dos registros de dados externos sujo, você pode optar por rejeitar esses registros para suas consultas usando as opções de rejeitar em criar DDL de tabela externa.


## <a name="load-data-from-azure-blob-storage"></a>Carregar dados do armazenamento de blob do Microsoft Azure
Este exemplo carrega dados de armazenamento de blob do Microsoft Azure ao banco de dados do SQL Data Warehouse.

Armazenar dados diretamente remove o tempo de transferência de dados para consultas. Armazenar dados com um índice de columnstore melhora o desempenho de consulta para consultas de análise por até 10 vezes.

Este exemplo usa a instrução criar tabela como Selecione para carregar dados. A nova tabela herda coluna nomeada na consulta. Ele herda os tipos de dados das colunas da definição de tabela externa.

Criar tabela como selecione é um alto desempenho instrução Transact-SQL que carrega os dados em paralelo para todos os nós de computação do SQL Data Warehouse.  Ele foi desenvolvido originalmente para o mecanismo de processamento altamente paralelo (MPP) no sistema de plataforma de análise e agora está no depósito de dados do SQL.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consulte [criar tabela como selecionar (Transact-SQL)][].

## <a name="create-statistics-on-newly-loaded-data"></a>Criar estatísticas na dados carregados recentemente

Depósito de dados SQL Azure faz ainda não suporte automático criar ou automático estatísticas de atualização.  Para obter o melhor desempenho de suas consultas, é importante que estatísticas de ser criada sobre todas as colunas de todas as tabelas após o carregamento primeiro ou quaisquer alterações substanciais ocorrerem nos dados.  Para obter uma explicação detalhada das estatísticas, consulte o tópico de [estatísticas][] no grupo desenvolver de tópicos.  Abaixo está um exemplo de como criar estatísticas sobre o em tabelas carregado neste exemplo rápido.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exportar dados para o armazenamento de blob do Microsoft Azure
Esta seção mostra como exportar dados do SQL Data Warehouse ao armazenamento de blob do Microsoft Azure. Este exemplo usa a criar EXTERNOS tabela como selecionar que é um alto desempenho instrução Transact-SQL para exportar os dados em paralelo de todos os nós de computação.

O exemplo a seguir cria uma tabela externa Weblogs2014 usando definições de coluna e dados de dbo. Tabela de weblogs. A definição de tabela externa é armazenada no depósito de dados do SQL e os resultados da instrução SELECT são exportados para o diretório "/ arquivar/log2014 /" sob o contêiner de blob especificado pela fonte de dados. Os dados são exportados no formato de arquivo de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Trabalhando em torno a exigência de PolyBase UTF-8
Em apresentar PolyBase suporta carregar arquivos de dados que foram codificados UTF-8. Como UTF-8 usa a mesma codificação de caracteres que ASCII PolyBase também serão dados de carregamento de suporte que é ASCII codificado. No entanto, PolyBase não suporta outros codificação de caracteres como UTF-16 / Unicode ou estendido de caracteres ASCII. ASCII estendido inclui caracteres com acentos como trema que é comum em alemão.

Para contornar esse requisito a melhor resposta é regravar a codificação UTF-8.

Existem várias maneiras de fazer isso. Abaixo estão duas abordagens usando o Powershell:

### <a name="simple-example-for-small-files"></a>Exemplo simples para arquivos pequeno

Abaixo está um script do Powershell que cria o arquivo simple, uma linha.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

No entanto, enquanto isso é uma maneira simples para codificar novamente os dados não é o mais eficiente. O exemplo de fluxo de io abaixo é muito, muito mais rápido e atinge o mesmo resultado.

### <a name="io-streaming-example-for-larger-files"></a>Exemplo de Streaming de IO para arquivos maiores

Exemplo de código abaixo é mais complexo, mas durante a transmissão as linhas de dados de origem para direcioná-lo é muito mais eficiente. Use essa abordagem para arquivos maiores.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como mover dados depósito de dados do SQL, consulte [Visão geral de migração de dados][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Carregar os dados com PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Visão geral de migração de dados]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Formato de arquivo externo criar (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026). aspx [criar tabela externa (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[Criar tabela como SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
