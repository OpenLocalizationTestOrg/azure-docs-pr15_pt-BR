<properties
   pageTitle="Gerenciar bancos de dados no depósito de dados do SQL Azure | Microsoft Azure"
   description="Visão geral de gerenciar bancos de dados do SQL Data Warehouse. Inclui ferramentas de gerenciamento, DWUs e desempenho de escala-out, solução de problemas de desempenho de consulta, estabelecendo políticas de segurança e restaurar um banco de dados de corrupção de dados ou de uma interrupção regional."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gerenciar bancos de dados no depósito de dados do SQL Azure

SQL Data Warehouse automatiza muitos aspectos de gerenciamento de seus bancos de dados. Por exemplo, para dimensionar o desempenho basta ajustar e pagar para o nível certo de recursos de computação e, em seguida, deixe o SQL Data Warehouse fazer todo o trabalho de dimensionamento e a escala de volta. 

Certamente, você desejará monitorar sua carga de trabalho para identificar suas necessidades de desempenho, bem como solucionar problemas em consultas de execução demorada. Você também precisará executar algumas tarefas de segurança para gerenciar permissões para usuários e logon.

Esta visão geral aborda esses aspectos de gerenciamento de depósito de dados do SQL.

- Ferramentas de gerenciamento
- Computação de escala
- Pausar e continuar
- Práticas recomendadas de desempenho
- Monitoramento de consulta
- Segurança
- Backup e restauração

## <a name="management-tools"></a>Ferramentas de gerenciamento

Você pode usar uma variedade de ferramentas para gerenciar bancos de dados em SQL Data Warehouse. Como gerenciar bancos de dados, você irá desenvolver preferências da ferramenta para cada tipo de tarefa, que você precisará executar.

### <a name="azure-portal"></a>Portal do Azure
O [portal do Azure][] é um portal baseado na web, onde você pode criar, atualizar, excluir bancos de dados e monitorar os recursos de banco de dados. Esta ferramenta é ótima é se você estiver começando a usar o Azure, Gerenciando um pequeno número de bancos de dados de depósito de dados, ou precisa fazer algo rapidamente.

Para começar a usar o portal do Azure, consulte [criar um depósito de dados do SQL (portal Azure)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>Ferramentas de dados do SQL Server no Visual Studio
[Ferramentas de dados do SQL Server][] (SSDT) no Visual Studio permite que você se conectar, gerenciar e desenvolver seus bancos de dados. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), tente usar SSDT no Visual Studio.

SSDT inclui o SQL Server Object Explorer que permite que você visualize, conectar e executar scripts em bancos de dados do SQL Data Warehouse. Para conectar-se rapidamente depósito de dados do SQL, você pode simplesmente clicar no botão **Abrir no Visual Studio** na barra de comando ao exibir os detalhes de banco de dados no Portal de clássico do Azure.  

Para começar a usar SSDT no Visual Studio, consulte [Consulta Azure SQL Data Warehouse com o Visual Studio][].

### <a name="command-line-tools"></a>Ferramentas de linha de comando
Ferramentas de linha de comando são ideais para automatizar suas cargas de trabalho.  PowerShell e sqlcmd são duas formas incríveis para automatizar seus processos.  Recomendamos que essas ferramentas para gerenciar um grande número de servidores lógicos e implantando alterações de recursos em um ambiente de produção como as tarefas necessárias podem ser por script e, em seguida, automatizadas.

### <a name="dynamic-management-views"></a>Exibições de gerenciamento dinâmico 

DMVs são a base do gerenciamento de depósito de dados do SQL. Quase todas as informações que revela no portal depende DMVs. Para ver uma lista de DMVs de depósito de dados do SQL, consulte [SQL Data Warehouse exibições do sistema][].

Para começar, consulte [conectar e consulta com sqlcmd][]e [criar um banco de dados (PowerShell)][].

## <a name="scale-compute"></a>Computação de escala

Em SQL Data Warehouse, você pode rapidamente dimensionar desempenho check-out ou por crescentes ou decrescentes recursos de computação de CPU, memória e largura de banda de e/s. Para dimensionar o desempenho, tudo o que você precisa fazer é ajustar o número de unidades de depósito de dados (DWUs) que SQL Data Warehouse aloca para seu banco de dados. SQL Data Warehouse rapidamente faz a alteração e lida com todas as alterações subjacentes de hardware e software.

Para saber mais sobre dimensionamento DWUs, consulte [desempenho de escala][].

##  <a name="pause-and-resume"></a>Pausar e continuar

Para salvar os custos, você pode pausar e continuar recursos de computação sob demanda. Por exemplo, se não usar o banco de dados durante a noite e nos fins de semana, você pode pausá-lo durante os horários e reiniciá-lo durante o dia. Você não ser cobrado DWUs enquanto o banco de dados está pausado.

Para obter mais informações, consulte [Pausar calcular][]e [computação de currículo][].

## <a name="performance-best-practices"></a>Práticas recomendadas de desempenho

Quando está começando a usar uma nova tecnologia, descobrir as dicas e truques que funcionam melhor direita desde o início pode economizar muito tempo.  Você encontrará as práticas recomendadas ao longo de muitos dos nossos tópicos.

Para ver muitos um resumo das mais importantes considerações ao desenvolver sua carga de trabalho, consulte [Práticas recomendadas de depósito de dados do SQL][].

## <a name="query-monitoring"></a>Monitoramento de consulta

Às vezes, uma consulta está funcionando muito longa, mas você não tiver certeza de qual é o culpado. SQL Data Warehouse tem gerenciamento dinâmico DMVs (exibições) que você pode usar para descobrir qual consulta está demorando muito. 

Para localizar as consultas de execução demorada, consulte [monitorar sua carga de trabalho usando DMVs][].

## <a name="security"></a>Segurança

Para manter um sistema seguro, você deve estar no alerta e proteção contra qualquer tipo de acesso não autorizado. Precisa de um sistema de segurança garantir que as regras de firewall estão no lugar autorizado para que apenas endereços IP podem se conectar. Ele precisa autenticação adequada de credenciais do usuário. Depois que um usuário conectou-se ao banco de dados, o usuário só deve ter permissões para executar um número mínimo de ações. Para proteger dados, você pode usar a criptografia. Também é importante ter de auditoria e controle para que você pode refazer eventos se houver qualquer atividade suspeita.

Para saber mais sobre gerenciamento de segurança, cabeça sobre para a [Visão geral de segurança][].

## <a name="backup-and-restore"></a>Backup e restauração

Ter backps confiável dos seus dados é uma parte essencial de qualquer banco de dados de produção. SQL Data Warehouse mantém seus dados seguros fazendo backup automático seus bancos de dados ativos em intervalos regulares. Esses backups permitem recuperar de cenários onde você seus dados corrompidos ou acidentalmente descartados seus dados ou o banco de dados.  Para o agendamento de backup de dados, política de retenção e como restaurar um banco de dados, consulte [restaurar a partir de instantâneo][].

## <a name="next-steps"></a>Próximas etapas
Usando o design de banco de dados bom princípios tornará mais fácil de gerenciar bancos de dados SQL Data Warehouse. Para saber mais, cabeça sobre para a [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Criar um depósito de dados do SQL (Portal Azure)]: sql-data-warehouse-get-started-provision.md
[Criar um banco de dados (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Consulta SQL Azure Data Warehouse com o Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Conectar-se e da consulta com sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md
[Monitorar sua carga de trabalho usando DMVs]: sql-data-warehouse-manage-monitor.md
[Computação de pausa]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restaurar a partir de instantâneo]: sql-data-warehouse-restore-database-overview.md
[Computação de currículo]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Dimensionar o desempenho]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Visão geral de segurança]: sql-data-warehouse-overview-manage-security.md
[Práticas recomendadas de depósito de dados do SQL]: sql-data-warehouse-best-practices.md
[Modos de exibição de sistema de depósito de dados do SQL]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[Ferramentas de dados do SQL Server]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal do Azure]: http://portal.azure.com/
