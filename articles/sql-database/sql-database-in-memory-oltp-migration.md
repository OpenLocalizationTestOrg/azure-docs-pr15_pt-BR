<properties
    pageTitle="Na memória OLTP melhora o desempenho do SQL txn | Microsoft Azure"
    description="Adotar OLTP de na memória para melhorar o desempenho de transações em um banco de dados existente do SQL."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Uso de memória OLTP (visualização) para melhorar o desempenho do aplicativo no banco de dados SQL

[OLTP na memória](sql-database-in-memory.md) podem ser usados para melhorar o desempenho da carga de trabalho OLTP em bancos de dados do SQL [Premium](sql-database-service-tiers.md) Azure sem aumentar o nível de desempenho.

Siga estas etapas para adotar OLTP na memória em seu banco de dados existente.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Etapa 1: Garantir que seu banco de dados Premium dá suporte a OLTP na memória

Bancos de dados de Premium criados em novembro de 2015 ou posterior suporta o recurso de memória. Você pode verificar se o seu banco de dados Premium oferece suporte ao recurso de memória executando a seguinte instrução Transact-SQL. Na memória é suportada se o resultado for 1 (não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa de *Processamento de transação extremo*

Se seu banco de dados existente deve ser movido para um novo banco de dados de V12 Premium, você pode usar as seguintes técnicas para exportar e importar seus dados.

#### <a name="export-steps"></a>Etapas de exportação

Exporte seu banco de dados de produção para um bacpac usando:

- A funcionalidade [Exportar](sql-database-export.md) no [portal](https://portal.azure.com/).

- A funcionalidade do **aplicativo de camada de exportação de dados** em um [SSMS.exe atualizado](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. No **Pesquisador de objetos**, expanda o nó de **bancos de dados** .
 2. Clique com botão direito o nó do seu banco de dados.
 3. Clique em **tarefas** > **Exportar camada de dados de aplicativo**.
 4. Opere a janela do assistente que é exibida.


#### <a name="import-steps"></a>Etapas de importação

Importe o bacpac para um novo banco de dados de Premium.

1. No do Azure [portal](https://portal.azure.com/),
 - Navegue para o servidor.
 - Selecione a opção de [Banco de dados de importação](sql-database-import.md) .
 - Selecione um nível de preço de Premium.

2. Use SSMS para importar o bacpac:
 - No **Pesquisador de objetos**, clique com botão direito nó **bancos de dados** .
 - Clique em **aplicativo de camada de dados de importação**.
 - Opere a janela do assistente que é exibida.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Etapa 2: Identificar objetos para migrar para OLTP na memória

SSMS inclui um relatório de **Visão geral de análise de desempenho de transação** que podem ser executados em um banco de dados com uma carga de trabalho ativa. O relatório identifica tabelas e procedimentos armazenados que são candidatos à migração para OLTP na memória.

No SSMS, para gerar o relatório:
- No **Pesquisador de objetos**, clique com botão direito o nó do seu banco de dados.
- Clique em **relatórios** > **relatórios padrão** > **Visão geral de análise de desempenho de transação**.

Para obter mais informações, consulte [Determinando se uma tabela ou armazenados procedimento deve ser transferidos para OLTP na memória](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Etapa 3: Criar um banco de dados de teste comparável

Suponha que o relatório indica que seu banco de dados tem uma tabela que beneficia sendo convertido em uma tabela de memória otimizada. Recomendamos que você primeiro teste para confirmar a indicação testando.

Você precisa de uma cópia de teste do seu banco de dados de produção. O banco de dados de teste deve ser do mesmo nível serviço camada como seu banco de dados de produção.

Para facilitar o teste, ajuste seu banco de dados de teste da seguinte maneira:

1. Conecte ao banco de dados de teste usando SSMS.

2. Para evitar que precisam a opção com (instantâneo) em consultas, defina a opção de banco de dados, conforme mostrado na instrução de T-SQL a seguir:
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Etapa 4: Migrar tabelas

Você deve criar e preencher uma cópia de memória otimizada da tabela que você deseja testar. Você pode criá-lo usando:

- O Assistente de otimização de memória à mão em SSMS.
- Manual T-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de otimização de memória no SSMS

Para usar essa opção de migração:

1. Conecte ao banco de dados de teste com SSMS.

2. No **Pesquisador de objetos**, clique com botão direito na tabela e clique em **Supervisor de otimização de memória**.
 - O Assistente de **Tabela memória otimizador Advisor** é exibido.

3. No assistente, clique em **validação de migração** (ou no botão **Avançar** ) para ver se a tabela tem todos os recursos sem suporte que não têm suporte nas tabelas de memória otimizada. Para obter mais informações, consulte:
 - Lista de *lista de verificação de otimização de memória* no [Supervisor de otimização de memória](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Construções de transact-SQL não suportado pelo OLTP na memória](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migrando para o OLTP na memória](http://msdn.microsoft.com/library/dn247639.aspx).

4. Se a tabela tiver sem recursos sem suporte, o Supervisor pode executar a migração de dados e esquema real para você.


#### <a name="manual-t-sql"></a>Manual de T-SQL

Para usar essa opção de migração:

1. Conecte seu banco de dados de teste usando o SSMS (ou um utilitário semelhante).

2. Obtenha o script T-SQL completo para sua tabela e seus índices.
 - No SSMS, clique com botão direito seu nó de tabela.
 - Clique em **tabela de Script como** > **criar para** > **nova janela de consulta**.

3. Na janela script, adicione WITH (MEMORY_OPTIMIZED = ON) para a instrução CREATE TABLE.

4. Se houver um índice de cluster, altere-a para NONCLUSTERED.

5. Renomeie a tabela existente usando SP_RENAME.

6. Crie a nova cópia de memória otimizada da tabela executando o script CREATE TABLE editado.

7. Copie os dados para sua tabela de memória otimizada usando inserir … SELECIONE * EM:
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Etapa 5 (opcional): migrar procedimentos armazenados

O recurso de memória também pode modificar um procedimento armazenado para melhorar o desempenho.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações com procedimentos armazenados nativamente compilados

Um procedimento armazenado nativamente compilado deve ter as seguintes opções na sua cláusula T-SQL com:

- NATIVE_COMPILATION

- SCHEMABINDING: tabelas de significado que o procedimento armazenado não pode ter suas definições de coluna alteradas de qualquer maneira que possa afetar o procedimento armazenado, a menos que você soltar o procedimento armazenado.


Um módulo nativo deve usar um grandes [blocos ATÔMICOS](http://msdn.microsoft.com/library/dn452281.aspx) para gerenciamento de transações. Não há nenhuma função para uma transação explícita começar, ou para REVERTER transação. Se seu código detectar uma violação de uma regra comercial, ele pode encerrar o bloco atômico com uma instrução [JOGA](http://msdn.microsoft.com/library/ee677615.aspx) .


### <a name="typical-create-procedure-for-natively-compiled"></a>PROCEDIMENTO criar típico para compilados nativamente

Normalmente o T-SQL para criar um procedimento armazenado nativamente compilado é semelhante à seguinte modelo:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- Para o TRANSACTION_ISOLATION_LEVEL, instantâneo é o valor mais comum para o procedimento armazenado nativamente compilado. No entanto, um subconjunto dos outros valores também são suportados:
 - LEITURA REPETIDA
 - SERIALIZÁVEL


- O valor do idioma deve estar presente no modo de exibição sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado

As etapas de migração são:


1. Obtenha o script CREATE PROCEDURE regular interpretado procedimento armazenado.

2. Regrava seu cabeçalho para corresponder ao modelo anterior.

3. Verificar se o procedimento armazenado código T-SQL usa todos os recursos que não têm suporte para procedimentos armazenados nativamente compilados. Implementar soluções alternativas se necessário.
 - Para obter detalhes, consulte [Problemas de migração para nativamente compilado procedimentos armazenados](http://msdn.microsoft.com/library/dn296678.aspx).

4. Renomeie o procedimento armazenado antigo usando SP_RENAME. Ou simplesmente SOLTÁ-lo.

5. Execute o script de criar procedimento T-SQL editado.


## <a name="step-6-run-your-workload-in-test"></a>Etapa 6: Executar sua carga de trabalho no teste

Execute uma carga de trabalho em seu banco de dados de teste que seja parecido com a carga de trabalho que é executado em seu banco de dados de produção. Isso deve revelar o ganho de desempenho obtido pelo uso do recurso de memória para tabelas e procedimentos armazenados.

Principais atributos da carga de trabalho são:

- Número de conexões simultâneas.

- Taxa de leitura/gravação.


Para adaptar e executar a carga de trabalho de teste, considere usar a ferramenta de ostress.exe à mão, que ilustra [aqui](sql-database-in-memory.md).


Para minimizar a latência de rede, execute o teste na mesma Azure geográfico região onde existe o banco de dados.


## <a name="step-7-post-implementation-monitoring"></a>Etapa 7: Monitoramento de pós-implementação

Considere a possibilidade de monitoramento os efeitos de desempenho de suas implementações na memória em produção:

- [Monitor de memória armazenamento](sql-database-in-memory-oltp-monitoring.md).

- [Monitoramento de banco de dados do SQL Azure usando modos de exibição de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Links relacionados

- [OLTP (otimização de memória) na memória](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introdução aos procedimentos armazenados nativamente compilados](http://msdn.microsoft.com/library/dn133184.aspx)

- [Supervisor de otimização de memória](http://msdn.microsoft.com/library/dn284308.aspx)

