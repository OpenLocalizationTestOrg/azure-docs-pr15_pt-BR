<properties
   pageTitle="Migrar seu armazém de dados do SQL Azure existente para o armazenamento de premium | Microsoft Azure"
   description="Instruções para migrar um depósito de dados do SQL existente para o armazenamento de premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Migração para detalhes de armazenamento Premium
SQL Data Warehouse recentemente introduzido [Premium armazenamento para maior capacidade de previsão de desempenho][].  Agora estamos prontos para migrar depósitos de dados existente no momento do armazenamento padrão para o armazenamento de Premium.  Leia mais para obter mais detalhes sobre como e quando ocorrem migrações automáticas e como migrar auto se você preferir controlar quando ocorre o tempo de inatividade.

Se você tiver mais de um depósito de dados, use o [cronograma de migração automática][] abaixo para determinar quando ele também será migrado.

## <a name="determine-storage-type"></a>Determinar o tipo de armazenamento
Se você criou um DW antes das datas abaixo, você está usando atualmente armazenamento padrão.  Cada Data Warehouse no armazenamento padrão que está sujeito a migração automática tem um aviso na parte superior da lâmina Data Warehouse no [Portal do Azure][] que diz "atualização de*um futuras ao armazenamento de premium exigirá uma interrupção.  Saiba mais ->*. "

| **Região**          | **DW criado antes desta data**   |
| :------------------ | :-------------------------------- |
| Austrália Oriental      | Armazenamento de Premium ainda não disponível |
| Austrália Sudeste | 5 de agosto de 2016                    |
| Brasil Sul        | 5 de agosto de 2016                    |
| Canadá Central      | 25 de maio de 2016                      |
| Canadá Leste         | 26 de maio de 2016                      |
| Centro dos EUA          | 26 de maio de 2016                      |
| China Leste          | Armazenamento de Premium ainda não disponível |
| América do Norte China         | Armazenamento de Premium ainda não disponível |
| Da Ásia Oriental           | 25 de maio de 2016                      |
| Leste EUA             | 26 de maio de 2016                      |
| Conosco2 Leste            | 27 de maio de 2016                      |
| Índia Central       | 27 de maio de 2016                      |
| Índia Sul         | 26 de maio de 2016                      |
| Índia Oeste          | Armazenamento de Premium ainda não disponível |
| Japão Leste          | 5 de agosto de 2016                    |
| Japão Oeste          | Armazenamento de Premium ainda não disponível |
| Centro Norte dos EUA    | Armazenamento de Premium ainda não disponível |
| Norte da Europa        | 5 de agosto de 2016                    |
| Centro Sul dos EUA    | 27 de maio de 2016                      |
| Sudeste Asiático      | 24 de maio de 2016                      |
| Europa Ocidental         | 25 de maio de 2016                      |
| Centro-Oeste dos EUA     | 26 de agosto de 2016                   |
| Oeste EUA             | 26 de maio de 2016                      |
| Conosco2 Oeste            | 26 de agosto de 2016                   |

## <a name="automatic-migration-details"></a>Detalhes de migração automática
Por padrão, podemos irá migrar seu banco de dados para você durante 6 pm e 6 horas no horário do local da sua região durante o [agendamento de migração automática][] abaixo.  Seu Data Warehouse existente será inutilizável durante a migração.  Podemos estimar que a migração levará cerca de uma hora por TB de armazenamento por Data Warehouse.  Podemos garantirá que você não é cobrados durante qualquer parte da migração automática.

> [AZURE.NOTE] Você não poderá usar seu Data Warehouse existente durante a migração.  Assim que a migração é concluída, seu Data Warehouse será online novamente.

Os detalhes abaixo são as etapas que a Microsoft está levando em seu nome para concluir a migração e não exige qualquer envolvimento de sua parte.  Neste exemplo, imagine que seu DW existente no armazenamento padrão atualmente é chamado "MyDW".

1.  Microsoft renomeia "MyDW" para "MyDW_DO_NOT_USE_ [carimbo de hora]"
2.  Microsoft pausa "MyDW_DO_NOT_USE_ [carimbo de hora]".  Durante esse tempo, um backup é feito.  Você pode ver várias pausar/currículos se podemos encontrar problemas durante este processo.
3.  Microsoft cria uma nova DW denominada "MyDW" Premium armazenamento do backup tirado na etapa 2.  "MyDW" não aparecerá até após a restauração for concluída.
4.  Quando a restauração for concluída, "MyDW" retorna à mesmas DWUs e estado pausado ou ativo que se encontrava antes da migração.
5.  Assim que a migração é concluída, o Microsoft exclui "MyDW_DO_NOT_USE_ [carimbo de hora]"
    
> [AZURE.NOTE] Essas configurações não são transferidos como parte da migração:
> 
>   -  Auditoria no nível de banco de dados precisa ser habilitada novamente
>   -  Regras de firewall no nível do **banco de dados** precisam ser adicionado novamente.  Regras de firewall no nível do **servidor** não são ser afetados.

### <a name="automatic-migration-schedule"></a>Cronograma de migração automática
Migrações automáticas ocorrerem de 6 pm – 6 horas (hora local por região) durante a seguinte agenda de interrupção.

| **Região**          | **Data de início estimada**     | **Data de término estimada**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Austrália Oriental      | Ainda não foi determinado           | Ainda não foi determinado           |
| Austrália Sudeste | 10 de agosto de 2016              | 24 de agosto de 2016              |
| Brasil Sul        | 10 de agosto de 2016              | 24 de agosto de 2016              |
| Canadá Central      | 23 de junho de 2016                | 1 de julho de 2016                 |
| Canadá Leste         | 23 de junho de 2016                | 1 de julho de 2016                 |
| Centro dos EUA          | 23 de junho de 2016                | 4 de julho de 2016                 |
| China Leste          | Ainda não foi determinado           | Ainda não foi determinado           |
| América do Norte China         | Ainda não foi determinado           | Ainda não foi determinado           |
| Da Ásia Oriental           | 23 de junho de 2016                | 1 de julho de 2016                 |
| Leste EUA             | 23 de junho de 2016                | 11 de julho de 2016                |
| Conosco2 Leste            | 23 de junho de 2016                | 8 de julho de 2016                 |
| Índia Central       | 23 de junho de 2016                | 1 de julho de 2016                 |
| Índia Sul         | 23 de junho de 2016                | 1 de julho de 2016                 |
| Índia Oeste          | Ainda não foi determinado           | Ainda não foi determinado           |
| Japão Leste          | 10 de agosto de 2016              | 24 de agosto de 2016              |
| Japão Oeste          | Ainda não foi determinado           | Ainda não foi determinado           |
| Centro Norte dos EUA    | Ainda não foi determinado           | Ainda não foi determinado           |
| Norte da Europa        | 10 de agosto de 2016              | 31 de agosto de 2016              |
| Centro Sul dos EUA    | 23 de junho de 2016                | 2 de julho de 2016                 |
| Sudeste Asiático      | 23 de junho de 2016                | 1 de julho de 2016                 |
| Europa Ocidental         | 23 de junho de 2016                | 8 de julho de 2016                 |
| Centro-Oeste dos EUA     | 14 de agosto de 2016              | 31 de agosto de 2016              |
| Oeste EUA             | 23 de junho de 2016                | 7 de julho de 2016                 |
| Conosco2 Oeste            | 14 de agosto de 2016              | 31 de agosto de 2016              |

## <a name="self-migration-to-premium-storage"></a>Auto migração para o armazenamento de Premium
Se você quiser controlar quando seu tempo de inatividade ocorrerá, você pode usar as seguintes etapas para migrar um depósito de dados existente no armazenamento padrão para o armazenamento de Premium.  Se você optar por migrar automática, você deve concluir a migração auto antes de começar a migração automática naquela região para evitar o risco da migração automática causando um conflito (consulte o [cronograma de migração automática][]).

### <a name="self-migration-instructions"></a>Instruções de migração automática
Se você quiser controlar seu tempo de inatividade, você pode migrar auto seu Data Warehouse usando backup e restauração.  A parte de restauração da migração deve levar cerca de uma hora por TB de armazenamento por DW.  Se você quiser manter o mesmo nome depois que a migração é concluída, siga as etapas para [as etapas para renomear durante a migração][]. 

1.  [Pausar][] sua DW que usa um backup automático
2.  [Restaurar][] a partir de seu instantâneo mais recente
3.  Exclua sua DW existente no armazenamento padrão. **Se você não conseguir concluir esta etapa, você será cobrado para ambas as DWs.**

> [AZURE.NOTE] Essas configurações não são transferidos como parte da migração:
> 
>   -  Auditoria no nível de banco de dados precisa ser habilitada novamente
>   -  Regras de firewall no nível do **banco de dados** precisam ser adicionado novamente.  Regras de firewall no nível do **servidor** não são ser afetados.

#### <a name="optional-steps-to-rename-during-migration"></a>Opcional: etapas para renomear durante a migração 
Dois bancos de dados no mesmo servidor lógico não podem ter o mesmo nome. SQL Data Warehouse agora suporta a capacidade de renomear um DW.

Neste exemplo, imagine que seu DW existente no armazenamento padrão atualmente é chamado "MyDW".

1.  Renomear "MyDW" usando o comando ALTER DATABASE que segue para algo como "MyDW_BeforeMigration".  Este comando elimina todas as transações existentes e deve ser executado no banco de dados mestre tenha êxito.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Pausar][] "MyDW_BeforeMigration", que usa um backup automático
3.  [Restaurar][] a partir de seu mais recente de um novo banco de dados com o nome que você usou para ter instantâneo (ex: "MyDW")
4.  Exclua "MyDW_BeforeMigration".  **Se você não conseguir concluir esta etapa, você será cobrado para ambas as DWs.**

> [AZURE.NOTE] Essas configurações não são transferidos como parte da migração:
> 
>   -  Auditoria no nível de banco de dados precisa ser habilitada novamente
>   -  Regras de firewall no nível do **banco de dados** precisam ser adicionado novamente.  Regras de firewall no nível do **servidor** não são ser afetados.

## <a name="next-steps"></a>Próximas etapas
Com a alteração ao armazenamento de Premium, podemos também têm aumentou o número de arquivos de blob do banco de dados na arquitetura subjacente do Data Warehouse.  Para maximizar os benefícios de desempenho dessa alteração, é recomendável que você recrie agrupada Columnstore índices usando o seguinte script.  O script abaixo funciona forçando alguns dos seus dados existentes para os blobs adicionais.  Se você não tirar nenhuma ação, os dados serão redistribuir naturalmente ao longo do tempo, como você carregar mais dados para suas tabelas de Data Warehouse.

**Pré-requisitos:**

1.  Data Warehouse deve ser executado com 1.000 DWUs ou superior (consulte [escala computação power][])
2.  Executando o script de usuário deve ser na [função mediumrc][] ou superior
    1.  Para adicionar um usuário a esta função, execute o seguinte: 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Se você encontrar problemas com Data Warehouse, [Crie um tíquete de suporte][] e referência "Migração para armazenamento de Premium" como a causa possível.

<!--Image references-->

<!--Article references-->
[cronograma de migração automática]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[Crie um tíquete de suporte]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pausar]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Restaurar]: sql-data-warehouse-restore-database-portal.md
[etapas para renomear durante a migração]: #optional-steps-to-rename-during-migration
[alimentação de computação de escala]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[função mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Armazenamento de Premium para maior capacidade de previsão de desempenho]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Portal do Azure]: https://portal.azure.com
