<properties
    pageTitle="Códigos de erro SQL - erro de conexão de banco de dados | Microsoft Azure"
    description="Saiba mais sobre os códigos de erro SQL para aplicativos de cliente do banco de dados SQL, como os erros comuns de conexão de banco de dados, problemas de cópia do banco de dados e erros gerais. "
    keywords="código de erro de SQL, sql do access, erro de conexão de banco de dados, códigos de erro de sql"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>Códigos de erro SQL para aplicativos de cliente do banco de dados do SQL: erro de conexão e outros problemas de banco de dados


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Este artigo lista os códigos de erro SQL para aplicativos de cliente do banco de dados SQL, inclusive erros de conexão de banco de dados, temporárias erros (também chamados de falhas temporárias), erros de gestão de recurso, problemas de cópia do banco de dados, pool elástica e outros erros. A maioria das categorias estão determinadas banco de dados de SQL Azure e não se aplicam ao Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erros de conexão de banco de dados, erros temporárias e outros erros temporários

A tabela a seguir aborda os códigos de erro SQL para erros de perda de conexão e outros temporárias erros que podem ocorrer quando o aplicativo tenta acessar o banco de dados SQL. Para obter tutoriais de introdução sobre como se conectar ao banco de dados do SQL Azure, consulte [Conectando-se ao Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erros de temporárias falhas e erros mais comuns de conexão de banco de dados

Infraestrutura do Azure tem a capacidade de reconfigurar dinamicamente servidores quando cargas de trabalho pesadas surgem no serviço de banco de dados SQL.  Esse comportamento dinâmico pode causar seu programa cliente perca sua conexão ao banco de dados SQL. Esse tipo de condição de erro é denominado uma *falha temporária*.

Se o programa cliente tiver lógica de repetição, ele pode tentar restabelecer uma conexão depois de conferir o tempo de falhas temporárias para corrigido.  Recomendamos que você espera por 5 segundos antes de sua primeira tentativa. Repetindo após um atraso menor do que os riscos de 5 segundos sobrecarregar o serviço de nuvem. Para cada tentativa subsequente o atraso deve crescimento exponencial, até no máximo 60 segundos.

Erros de falha de temporárias normalmente manifestam como uma das seguintes mensagens de erro de seus programas do cliente:

- Banco de dados < db_name > no servidor < Azure_instance > não está disponível atualmente. Tente a conexão mais tarde. Se o problema persistir, entre em contato com o suporte ao cliente e forneça a ele a ID de rastreamento de sessão de < session_id >

- Banco de dados < db_name > no servidor < Azure_instance > não está disponível atualmente. Tente a conexão mais tarde. Se o problema persistir, entre em contato com o suporte ao cliente e forneça a ele a ID de rastreamento de sessão de < session_id >. (Microsoft SQL Server, erro: 40613)

- Uma conexão existente obrigatoriamente foi fechado pelo host remoto.

- System.Data.Entity.Core.EntityCommandExecutionException: Ocorreu um erro ao executar a definição de comando. Consulte a exceção interna para obter detalhes. ---> System.Data.SqlClient.SqlException: Ocorreu um erro de nível de transporte quando receber os resultados do servidor. (provedor de serviços: provedor de sessão, erro: 19 - conexão física não é útil)

Para obter exemplos de código de lógica de repetição, consulte:

- [Bibliotecas de Conexão de banco de dados SQL e SQL Server](sql-database-libraries.md) 
- [Ações para corrigir erros de conexão e temporárias no banco de dados SQL](sql-database-connectivity-issues.md)

Uma discussão do *bloqueio de período* para clientes que usam o ADO.NET está disponível no [Pool de Conexão do SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de erro de falhas temporárias

Os seguintes erros são temporárias e devem ser repetidos na lógica de aplicativo 

| Código de erro | Gravidade | Descrição |
| ---: | ---: | :--- |
| 4060 | 16 | Não é possível abrir o banco de dados "% & #x2a; ls" solicitado pelo logon. Falha de logon. |
|40197|17|O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro %d.<br/><br/>Você receberá esse erro, quando o serviço for pressionada devido a software ou atualizações de hardware, falhas de hardware ou qualquer outro problema de failover. O código de erro (%d) incorporado a mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou failover que ocorreu. Alguns exemplos do erro códigos incorporados a mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Reconectar-se ao seu servidor de banco de dados SQL, você será conectado automaticamente a uma cópia íntegra do banco de dados. Seu aplicativo deve capturar log de erro de 40197, o código de erro incorporado (%d) dentro da mensagem para solução de problemas e reconectar-se ao banco de dados do SQL até os recursos estão disponíveis, e sua conexão for estabelecida novamente.|
|40501|20|O serviço está ocupado. Repita a solicitação após 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>*Observação:* Para obter mais informações, consulte:<br/>• [Limites de recursos do azure SQL Database](sql-database-resource-limits.md).
|40613|17|Banco de dados '%. & #x2a; ls' no servidor '%. & #x2a; ls' não está disponível atualmente. Tente a conexão mais tarde. Se o problema persistir, entre em contato com o suporte ao cliente e forneça a ele a ID de rastreamento de sessão de '% & #x2a; ls'.|
|49918|16|Não é possível processar a solicitação. Não há recursos suficientes para processar a solicitação.<br/><br/>O serviço está ocupado. Tente a solicitação mais tarde. |
|49919|16|Processo não pode criar ou atualizar solicitação. Muito muitos criar ou atualizar operações em andamento para assinatura "% ld".<br/><br/>O serviço está ocupado processando vários criar ou atualizar solicitações de sua assinatura ou o servidor. Solicitações atualmente são bloqueadas otimização de recursos. Consultar [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde pendentes criar ou atualizar solicitações foram concluídas ou exclua uma das suas solicitações pendentes e repita sua solicitação mais tarde. |
|49920|16|Não é possível processar a solicitação. Muitas operações em andamento para assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para essa assinatura. Solicitações atualmente são bloqueadas otimização de recursos. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para o status da operação de consulta. Aguarde até solicitações pendentes foram concluída ou exclua uma das suas solicitações pendentes e repetir a solicitação posteriormente. |

## <a name="database-copy-errors"></a>Erros de cópia do banco de dados

Os seguintes erros podem ser encontrados ao copiar um banco de dados no Azure SQL Database. Para obter mais informações, consulte [Copiar um banco de dados do SQL Azure](sql-database-copy.md).


|Código de erro|Gravidade|Descrição|
|---:|---:|:---|
|40635|16|Cliente com o endereço IP '% & #x2a; ls' está temporariamente desabilitado.|
|40637|16|Criar cópia do banco de dados está desativada no momento.|
|40561|16|Falha na cópia do banco de dados. Banco de dados de origem ou de destino não existe.|
|40562|16|Falha na cópia do banco de dados. O banco de dados de origem foi descartado.|
|40563|16|Falha na cópia do banco de dados. O banco de dados de destino foi descartado.|
|40564|16|Falha na cópia do banco de dados devido a um erro interno. Verifique descartar banco de dados de destino e tente novamente.|
|40565|16|Falha na cópia do banco de dados. Não mais do que 1 cópia do banco de dados simultâneas da mesma fonte é permitida. Verifique descartar banco de dados de destino e tente novamente mais tarde.|
|40566|16|Falha na cópia do banco de dados devido a um erro interno. Verifique descartar banco de dados de destino e tente novamente.|
|40567|16|Falha na cópia do banco de dados devido a um erro interno. Verifique descartar banco de dados de destino e tente novamente.|
|40568|16|Falha na cópia do banco de dados. Banco de dados de origem tornou disponível. Verifique descartar banco de dados de destino e tente novamente.|
|40569|16|Falha na cópia do banco de dados. Banco de dados de destino se tornou disponível. Verifique descartar banco de dados de destino e tente novamente.|
|40570|16|Falha na cópia do banco de dados devido a um erro interno. Verifique descartar banco de dados de destino e tente novamente mais tarde.|
|40571|16|Falha na cópia do banco de dados devido a um erro interno. Verifique descartar banco de dados de destino e tente novamente mais tarde.|

## <a name="resource-governance-errors"></a>Erros de gestão de recurso

Os seguintes erros são causados por uso excessivo de recursos enquanto estiver trabalhando com o Azure SQL Database. Por exemplo:

- Uma transação foi aberta por muito tempo.
- Uma transação está mantendo bloqueios demais.
- Um aplicativo está consumindo muita memória.
- Um aplicativo está consumindo muito `TempDb` espaço.

Tópicos relacionados:

* Informações mais detalhadas estão disponíveis aqui: [limites de recursos do banco de dados do SQL Azure](sql-database-resource-limits.md)

|Código de erro|Gravidade|Descrição|
|---:|---:|:---|
|10928|20|Identificação do recurso: %d. O limite de %s para o banco de dados é %d e foi atingido. Para obter mais informações, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>A identificação do recurso indica o recurso que atingiu o limite. Para threads de trabalho, a identificação do recurso = 1. Para sessões, a identificação do recurso = 2.<br/><br/>*Observação:* Para obter mais informações sobre esse erro e como solucioná-lo, consulte:<br/>• [Limites de recursos do azure SQL Database](sql-database-resource-limits.md). |
|10929|20|Identificação do recurso: %d. A garantia mínima %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores do que %d para este banco de dados. Para obter mais informações, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Caso contrário, tente novamente mais tarde.<br/><br/>A identificação do recurso indica o recurso que atingiu o limite. Para threads de trabalho, a identificação do recurso = 1. Para sessões, a identificação do recurso = 2.<br/><br/>*Observação:* Para obter mais informações sobre esse erro e como solucioná-lo, consulte:<br/>• [Limites de recursos do azure SQL Database](sql-database-resource-limits.md).|
|40544|20|O banco de dados atingiu sua cota de tamanho. Partição ou excluir dados, descartar índices ou consulte a documentação para possíveis resoluções.|
|40549|16|Sessão é encerrada porque você tem uma transação de longa duração. Tente reduzir sua transação.|
|40550|16|A sessão foi encerrada porque ele adquiriu muitos bloqueios. Tente leitura ou modificar menos linhas em uma única transação.|
|40551|16|A sessão foi encerrada devido excessiva `TEMPDB` uso. Tente modificar sua consulta para reduzir o uso de espaço de tabela temporária.<br/><br/>*Dica:* Se você estiver usando objetos temporários, economizar espaço na `TEMPDB` banco de dados soltando objetos temporários depois que eles não forem mais necessárias pela sessão.|
|40552|16|A sessão foi encerrada devido ao uso de espaço de log de transação excessiva. Tente modificar menos linhas em uma única transação.<br/><br/>*Dica:* Se você executar em massa inserções usando o `bcp.exe` utilitário ou o `System.Data.SqlClient.SqlBulkCopy` classe, tente usar o `-b batchsize` ou `BatchSize` opções para limitar o número de linhas copiadas para o servidor de cada transação. Se você estiver recriando um índice com a `ALTER INDEX` instrução, tente usar o `REBUILD WITH ONLINE = ON` opção.|
|40553|16|A sessão foi encerrada devido ao uso de memória excessiva. Tente modificar sua consulta para processar menos linhas.<br/><br/>*Dica:* Reduzir o número de `ORDER BY` e `GROUP BY` operações em seu código Transact-SQL reduz os requisitos de memória da sua consulta.|

## <a name="elastic-pool-errors"></a>Erros de pool elástica

Os seguintes erros estão relacionados à criando e usando Pools de Elastics.

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | O pool Elástico atingiu o limite de armazenamento. O uso do armazenamento para o pool de Elástico não pode exceder MB (%d). | Limite de espaço de pool elástica em MB. | Tentando gravar dados em um banco de dados quando você atingiu o limite de armazenamento do pool Elástico. | Por favor, considere aumentar os DTUs do pool Elástico se possível para aumentar seu limite de armazenamento, reduzir o armazenamento usado pelo bancos de dados individuais dentro do pool Elástico ou remover bancos de dados do pool Elástico. |
| 10929 | EX_USER | A garantia mínima %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores do que %d para este banco de dados. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obter assistência. Caso contrário, tente novamente mais tarde. | Mínimo DTU por banco de dados; Máximo DTU por banco de dados | O número total de trabalhadores simultâneos (solicitações) em todos os bancos de dados no pool Elástico tentou excede o limite de pool. | Por favor, considere aumentar os DTUs do pool Elástico se possível para aumentar seu limite de trabalho ou remover bancos de dados do pool Elástico. |
| 40844 | EX_USER | Banco de dados '%ls' no servidor '%ls' é um banco de dados de edição de '%ls' em um pool de Elástico e não pode ter uma relação de cópia contínuo. | nome, edição de banco de dados, o nome do servidor de banco de dados | Um comando StartDatabaseCopy é emitido para um db não sejam premium em um pool de Elástico. | Em breve |
| 40857 | EX_USER | Pool elástica não encontrado para servidor: '%ls', o nome do pool elástica: '%ls'. | nome do servidor; nome do pool elástica | Pool de elástica especificado não existe no servidor especificado. | Forneça um nome de pool elástica válida. |
| 40858 | EX_USER | Pool elástica '%ls' já existe no servidor: '%ls' | nome do pool elástica, o nome do servidor | Pool de elástica especificado já existe no servidor lógico especificado. | Fornece o nome do novo pool elástica. |
| 40859 | EX_USER | Pool elástica não dá suporte a camada de serviço '%ls'. | nível de serviço do pool elástica | Não há suporte para o nível de serviço especificado para provisionamento de pool elástica. | Forneça a edição correta ou deixe o nível de serviço em branco para usar o nível de serviço padrão. |
| 40860 | EX_USER | Combinação de pool elástica '%ls' e serviço objetivo '%ls' é inválida. | nome do pool elástica; nome de objetivo de nível de serviço | Elástico objetivo pool e serviços pode ser especificado juntos somente se o objetivo do serviço é especificado como 'ElasticPool'. | Especifique combinação correta de pool elástica e objetivo de serviço. |
| 40861 | EX_USER | A edição de banco de dados ' %. *ls' não podem ser diferentes do que o nível de serviço de pool elástica que é ' %.* ls'. | edição de banco de dados, nível de serviço do pool elástica | A edição de banco de dados é diferente do que o nível de serviço do pool elástica. | Não especifique uma edição de banco de dados que é diferente do que o nível de serviço do pool elástica.  Observe que a edição de banco de dados não precisa ser especificado. |
| 40862 | EX_USER | Nome do pool elástica deve ser especificado se o objetivo de serviço do pool elástica estiver especificado. | Nenhum | Objetivo de serviço do pool elástica não identifica exclusivamente um pool Elástico. | Especifique o nome do pool elástica se usando o objetivo de serviço do pool elástica. |
| 40864 | EX_USER | Os DTUs para o pool de Elástico devem ter pelo menos (%d) DTUs para o nível de serviço ' %. * ls'. | DTUs para pool elástica; nível de serviço do pool elástica. | Tentativa de definir os DTUs para o pool de Elástico abaixo do limite mínimo. | Tente definir os DTUs para a elástica pool pelo menos o limite mínimo. |
| 40865 | EX_USER | Os DTUs para o pool de Elástico não podem exceder (%d) DTUs para o nível de serviço ' %. * ls'. | DTUs para pool elástica; nível de serviço do pool elástica. | Tentativa de definir os DTUs para o pool de Elástico acima do limite máximo. | Tente definindo as DTUs para o pool de Elástico para não maior que o limite máximo. |
| 40867 | EX_USER | O DTU máx por banco de dados deve estar no mínimo (%d) para o nível de serviço ' %. * ls'. | Máximo DTU por banco de dados; nível de serviço do pool elástica | Tentativa de definir o máximo DTU por banco de dados abaixo do limite compatível. | Considere usar o nível de serviço de pool elástica que ofereça suporte a configuração desejada. |
| 40868 | EX_USER | O DTU máx por banco de dados não pode exceder (%d) para o nível de serviço ' %. * ls'. | Máximo DTU por banco de dados; nível de serviço do pool elástica. | Tentativa de definir o máximo DTU por banco de dados fora do limite compatível. | Considere usar o nível de serviço de pool elástica que ofereça suporte a configuração desejada. |
| 40870 | EX_USER | O mínimo DTU por banco de dados não pode exceder (%d) para o nível de serviço ' %. * ls'. | Mínimo DTU por banco de dados; nível de serviço do pool elástica. | Tentativa de definir o mínimo DTU por banco de dados fora do limite compatível. | Considere usar o nível de serviço de pool elástica que ofereça suporte a configuração desejada. |
| 40873 | EX_USER | O número de bancos de dados (%d) e o mínimo DTU por banco de dados (%d) não pode exceder os DTUs do pool Elástico (%d). | Número de bancos de dados no pool elástica; Mínimo DTU por banco de dados; DTUs de pool elástica. | Tentando especificar min DTU para bancos de dados no pool Elástico que excede os DTUs do pool Elástico. | Considere aumentar os DTUs do pool Elástico, ou diminuir o mínimo DTU por banco de dados ou diminuir o número de bancos de dados no pool Elástico. |
| 40877 | EX_USER | Um pool de Elástico não pode ser excluído, a menos que ele não contém bancos de dados. | Nenhum | O pool Elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. | Remova bancos de dados do pool Elástico para excluí-la. |
| 40881 | EX_USER | O pool Elástico ' %. * ls' atingiu o limite de contagem de banco de dados.  O limite de contagem de banco de dados para o pool Elástico não pode exceder (%d) para um pool de Elástico com DTUs (%d). | Nome do pool elástica; limite de contagem de banco de dados do pool elástica; e DTUs para pool de recursos. | Tentando criar ou adicionar banco de dados ao pool elástica quando você atingiu o limite de contagem de banco de dados do pool Elástico. | Por favor, considere aumentar os DTUs do pool Elástico se possível para aumentar seu limite de banco de dados ou remover bancos de dados do pool Elástico. |
| 40889 | EX_USER | O DTUs ou o limite de armazenamento para o pool de Elástico ' %. * ls' não pode ser reduzidas desde que não seria oferece espaço de armazenamento suficiente para seus bancos de dados. | Nome do pool elástica. | Tentando diminuir o limite de armazenamento do pool Elástico abaixo seu uso de armazenamento. | Verifique Reduza o uso do armazenamento de bancos de dados individuais no pool Elástico ou remover bancos de dados do pool para reduzir seu DTUs ou o limite de armazenamento. |
| 40891 | EX_USER | O mínimo DTU por banco de dados (%d) não pode exceder o máximo DTU por banco de dados (%d). | Mínimo DTU por banco de dados; DTU máximo por banco de dados. | Tentativa de definir o mínimo DTU por banco de dados maior que o máximo DTU por banco de dados. | Certifique-se de que o mínimo DTU por bancos de dados não exceda o máximo DTU por banco de dados. |
| EM ABERTO | EX_USER | O tamanho de armazenamento para um banco de dados individual em um pool Elástico não pode exceder o tamanho máximo permitido pela ' %. * ls' pool elástica de nível de serviço. | nível de serviço do pool elástica | O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástica. | Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástica. |

Tópicos relacionados:

* [Criar um pool de banco de dados elástica (c#)](sql-database-elastic-pool-create-csharp.md) 
* [Gerenciar um pool de banco de dados elástica (c#)](sql-database-elastic-pool-manage-csharp.md). 
* [Criar um pool de banco de dados elástica (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Monitor e gerenciar um pool de banco de dados elástica (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Erros gerais

Os seguintes erros não se encaixam em qualquer categorias anterior.

|Código de erro|Gravidade|Descrição|
|---:|---:|:---|
|15006|16|<AdministratorLogin>não é um nome válido porque ele contém caracteres inválidos.|
|18452|14|Falha no login. O logon é de um domínio não confiável e não podem ser usado com o Windows authentication.%. & #x2a; ls (logon do Windows não é suportado nesta versão do SQL Server.)|
|18456|14|Falha de logon do usuário ' % #x2a;ls'.%. & #x2a ls %. & #x2a; ls (Falha de logon do usuário "% & #x2a; ls". Falha ao alterar a senha. Alteração de senha durante o logon não é suportada nesta versão do SQL Server.)|
|18470|14|Falha de logon do usuário '% & #x2a; ls'. Motivo: A conta é disabled.%. & #x2a; ls|
|40014|16|Vários bancos de dados não podem ser usados na mesma transação.|
|40054|16|Tabelas sem um índice agrupado não são suportadas nesta versão do SQL Server. Criar um índice agrupado e tente novamente.|
|40133|15|Não há suporte para esta operação nesta versão do SQL Server.|
|40506|16|SID especificado é inválido para esta versão do SQL Server.|
|40507|16|' % & #x2a; ls' não podem ser chamados com parâmetros nesta versão do SQL Server.|
|40508|16|Não há suporte para a política de uso para alternar entre os bancos de dados. Use uma nova conexão para se conectar a um banco de dados diferente.|
|40510|16|Instrução '% & #x2a; ls' não é compatível com esta versão do SQL Server|
|40511|16|Função interna '% & #x2a; ls' não é compatível com esta versão do SQL Server.|
|40512|16|Não há suporte para o recurso reprovado '%ls' nesta versão do SQL Server.|
|40513|16|Servidor variável '% & #x2a; ls' não é compatível com esta versão do SQL Server.|
|40514|16|Não há suporte para '%ls' nesta versão do SQL Server.|
|40515|16|Referência ao nome do banco de dados e/ou o servidor em '% & #x2a; ls' não é compatível com esta versão do SQL Server.|
|40516|16|Objetos de temp globais não são suportados nesta versão do SQL Server.|
|40517|16|Opção de palavra-chave ou comprovante '% & #x2a; ls' não é compatível com esta versão do SQL Server.|
|40518|16|Comando DBCC '% & #x2a; ls' não é compatível com esta versão do SQL Server.|
|40520|16|Classe protegíveis '% S_MSG' não têm suporte nesta versão do SQL Server.|
|40521|16|Classe protegíveis '% S_MSG' não têm suporte no escopo do servidor nesta versão do SQL Server.|
|40522|16|Não há suporte para o tipo de banco de dados principal '% & #x2a; ls' nesta versão do SQL Server.|
|40523|16|Não há suporte para a criação de '% & #x2a; ls' de usuário implícito nesta versão do SQL Server. Crie o usuário explicitamente antes de usá-lo.|
|40524|16|Tipo de dados '%. & #x2a; ls' não é compatível com esta versão do SQL Server.|
|40525|16|WITH '%.ls' não é suportado nesta versão do SQL Server.|
|40526|16|' % & #x2a; provedor de conjunto de linhas dos ls não têm suportado nesta versão do SQL Server.|
|40527|16|Servidores vinculados não são suportados nesta versão do SQL Server.|
|40528|16|Os usuários não podem ser mapeados para certificados, chaves assimétricas ou logon Windows nesta versão do SQL Server.|
|40529|16|Função interna '% & #x2a; ls' representação contexto não é compatíveis com esta versão do SQL Server.|
|40532|11|Não é possível abrir o servidor "% & #x2a; ls" solicitado pelo logon. Falha de logon.|
|40553|16|A sessão foi encerrada devido ao uso de memória excessiva. Tente modificar sua consulta para processar menos linhas.<br/><br/>*Observação:* Reduzir o número de `ORDER BY` e `GROUP BY` operações em seu código Transact-SQL ajuda a reduzir os requisitos de memória da sua consulta.|
|40604|16|Poderia não CREATE/ALTER DATABASE porque ela excede a cota do servidor.|
|40606|16|Não há suporte para anexar bancos de dados nesta versão do SQL Server.|
|40607|16|Logon do Windows não é suportado nesta versão do SQL Server.|
|40611|16|Os servidores podem ter no máximo 128 regras de firewall definidas.|
|40614|16|Endereço IP do início de regra de firewall não pode exceder o endereço IP final.|
|40615|16|Não é possível abrir o servidor '{0}' solicitado pelo logon. Cliente com o endereço IP '{1}' não tem permissão para acessar o servidor.  Para habilitar o acesso, use o Portal de banco de dados do SQL ou execute sp_set_firewall_rule no banco de dados mestre para criar uma regra de firewall para este endereço IP ou o intervalo de endereços.  Ele pode levar até cinco minutos para que essa alteração seja efetivada.|
|40617|16|O nome da regra de firewall que começa com <rule name> é muito longo. Comprimento máximo é 128.|
|40618|16|O nome da regra de firewall não pode ser vazio.|
|40620|16|Falha de logon do usuário "% & #x2a; ls". Falha ao alterar a senha. Não há suporte para alteração de senha durante o logon nesta versão do SQL Server.|
|40627|20|Operação no servidor '{0}' e o banco de dados '{1}' está em andamento. Aguarde alguns minutos antes de tentar novamente.|
|40630|16|Falha na validação de senha. A senha não atende aos requisitos de política porque ele é muito curto.|
|40631|16|A senha que você especificou é muito longa. A senha deve ter não mais do que 128 caracteres.|
|40632|16|Falha na validação de senha. A senha não atende aos requisitos de política porque ela não é suficiente complexa.|
|40636|16|Não é possível usar um nome de banco de dados reservado '% & #x2a; ls' nesta operação.|
|40638|16|Id da assinatura inválidos < id da assinatura >. Assinatura não existe.|
|40639|16|Solicitação não de acordo com esquema: <schema error>.|
|40640|20|O servidor encontrou uma exceção inesperada.|
|40641|16|O local especificado é inválido.|
|40642|17|O servidor está muito ocupado no momento. Tente novamente mais tarde.|
|40643|16|O valor de cabeçalho x-ms-version especificado é inválido.|
|40644|14|Falha ao autorizar o acesso à assinatura especificada.|
|40645|16|Nomedoservidor <servername> não pode ser vazia ou nula. Só podem ser feita para cima de letras minúsculas 'a'-'z', os números de 0 a 9 e o hífen. O hífen não pode levar ou trilha no nome.|
|40646|16|ID da assinatura não pode ser vazio.|
|40647|16|Assinatura < id da assinatura não tem NomeServidor de servidor.|
|40648|17|Muitas solicitações foram executadas. Tente novamente mais tarde.|
|40649|16|Tipo de conteúdo inválido é especificado. Aplicativo/xml só é suportada.|
|40650|16|Assinatura < id da assinatura > não existir ou não está pronta para a operação.|
|40651|16|Falha ao criar servidor porque a assinatura < id da assinatura > está desabilitada.|
|40652|16|Não é possível mover ou criar o servidor. Assinatura < id da assinatura > excederá cota de servidor.|
|40671|17|Falha de comunicação entre o gateway e o serviço de gerenciamento. Tente novamente mais tarde.|
|40852|16|Não é possível abrir o banco de dados ' %. *ls' no servidor ' %.* ls' solicitado pelo logon. Acesso ao banco de dados é permitido somente usando uma cadeia de conexão de segurança habilitada. Para acessar este banco de dados, modificar suas cadeias de caracteres de conexão para conter 'seguro' no servidor FQDN -.database.windows 'server name'.net deve ser modificada para .database 'server name'. `secure`. no windows.net.|
|45168|16|O sistema de SQL Azure está sob carga e está colocando um limite superior em operações de DB CRUD simultâneas para um único servidor (por exemplo, criar banco de dados). O servidor especificado na mensagem de erro excedeu o número máximo de conexões simultâneas. Tente novamente mais tarde.|
|45169|16|O sistema SQL azure está sob carga e está colocando um limite superior no número de operações de CRUD servidor simultâneo para uma assinatura única (por exemplo, criar servidor). A inscrição especificada na mensagem de erro excedeu o número máximo de conexões simultâneas e a solicitação foi negada. Tente novamente mais tarde.|

## <a name="related-links"></a>Links relacionados

- [Diretrizes e limitações de geral de banco de dados do SQL Azure](sql-database-general-limitations.md)
- [Limites de recursos de banco de dados do SQL Azure](sql-database-resource-limits.md)
