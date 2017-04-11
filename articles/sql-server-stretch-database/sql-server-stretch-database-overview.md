<properties
    pageTitle="Esticar visão geral do banco de dados | Microsoft Azure"
    description="Saiba como banco de dados de alongar migra seus dados frio transparente e segura na nuvem do Microsoft Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Esticar visão geral do banco de dados

Banco de dados Alongar migra seus dados frio transparente e com segurança na nuvem do Microsoft Azure.

Se você quiser começar a usar o banco de dados de alongar imediatamente, consulte [Introdução executando o banco de dados habilitar para alongar Assistente](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Quais são os benefícios de banco de dados de alongar?
Banco de dados Alongar fornece os seguintes benefícios:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Fornece custo\-disponibilidade eficaz para dados fria
Esticar quentes / frio dados transações dinamicamente do SQL Server para Microsoft Azure com o banco de dados do SQL Server Alongar. Ao contrário de armazenamento de dados fria típica, seus dados estejam sempre online e disponível para consulta. Você pode fornecer mais cronogramas de retenção de dados sem quebrar banco para tabelas grandes como o histórico de pedidos do cliente. A vantagem do baixo custo do Azure em vez de dimensionamento caro, em\-locais de armazenamento. Escolha o nível de preços e definir configurações no Portal do Azure para manter o controle sobre os custos. Redimensione para cima ou para baixo, conforme necessário. Visite a página de [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) para obter detalhes.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Não requer alterações consultas ou aplicativos
Acessar seus dados do SQL Server perfeitamente independentemente do fato de ela ser\-local ou alongada para a nuvem.  Você definir a política que determina onde os dados são armazenados e SQL Server lida com a movimentação de dados no plano de fundo. A tabela inteira é sempre online e consultável. E, Alongar de banco de dados não requer quaisquer alterações consultas existentes ou aplicativos – o local dos dados é totalmente transparente para o aplicativo.

### <a name="streamlines-on-premises-data-maintenance"></a>Simplifica em\-local de manutenção de dados
Reduzir na\-locais de armazenamento para seus dados e a manutenção. Backups para seu em\-local dados executado com mais rapidez e término dentro da janela de manutenção. Backups para a parte de nuvem de seus dados são executadas automaticamente. Seu em\-necessidades de armazenamento local são bastante reduzidas. Armazenamento do Azure pode ser 80% mais barato do que adicionar ativado\-local SSD.

### <a name="keeps-your-data-secure-even-during-migration"></a>Mantém seus dados seguros mesmo durante a migração
Reproduzir tranquilidade como você afastar os aplicativos mais importantes com segurança para a nuvem. Sempre criptografadas do SQL Server fornece criptografia para seus dados em movimento. Linha nível RLS (segurança) e outros recursos avançados de segurança do SQL Server também funcionam com o banco de dados de Alongar para proteger seus dados.

## <a name="what-does-stretch-database-do"></a>O que faz Alongar banco de dados?
Depois de habilitar Alongar banco de dados para uma instância do SQL Server, um banco de dados e pelo menos uma tabela, banco de dados de alongar silenciosamente começa a migrar seus dados frio para Azure.

-   Se você armazenar dados frio em uma tabela separada, você pode migrar a tabela inteira.

-   Se sua tabela contém dados quente / frio, você pode especificar uma função de filtro para selecionar as linhas a serem migradas.

**Você não precisa alterar consultas existentes e aplicativos cliente.** Você continuar a ter acesso perfeito aos dados locais e remotos, mesmo durante a migração de dados. Há uma pequena quantidade de latência para consultas remotas, mas você encontrar essa latência somente quando você consulta os dados frio.

**Que banco de dados de alongar garante que nenhum dado é perdido** se ocorrer uma falha durante a migração. Ele também tem lógica de repetição de lidar com problemas de conexão que podem ocorrer durante a migração. Um modo de exibição de gerenciamento dinâmico fornece o status de migração.

**Você pode pausar a migração de dados** para solucionar problemas no servidor local ou maximizar a largura de banda de rede disponível.

![Esticar visão geral do banco de dados][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>É o banco de dados de Alongar para você?
Se você pode fazer as instruções a seguir, Alongar banco de dados pode ajudar a atender às suas necessidades e resolver seus problemas.

|Se você for um fabricante de decisão|Se você for um DBA|
|------------------------------|-------------------|
|Eu tenho manter dados de transações por um longo tempo.|O tamanho das minhas tabelas é tirar proveito do controle.|
|Às vezes, eu tenho que consultar os dados frio.|Meus usuários dizem que querem acesso aos dados frio, mas eles apenas raramente usá-lo.|
|Eu tenho aplicativos, incluindo os aplicativos antigos, que eu não quero atualizar.|Eu tenho que manter comprar e adicionar mais armazenamento.|
|Eu quero encontrar uma maneira de economizar dinheiro em armazenamento.|Não consigo fazer backup ou restaurar tais tabelas grandes dentro de SLA.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Que tipo de bancos de dados e tabelas são candidatas Alongar banco de dados?
Esticar banco destinos transações bancos de dados com grandes quantidades de dados fria, normalmente armazenados em um pequeno número de tabelas. Estas tabelas podem conter mais de um bilhões de linhas.

Se você usar o recurso de temporal tabela do SQL Server 2016, usar o banco de dados de alongar migrar todo ou parte da tabela de histórico associados a custos\-eficaz de armazenamento no Azure. Para obter mais informações, consulte [Gerenciar retenção de dados históricos em tabelas Temporal sistema versão](https://msdn.microsoft.com/library/mt637341.aspx).

Use o Supervisor de banco de dados de alongar, um recurso do SQL Server 2016 Supervisor de atualização, para identificar os bancos de dados e tabelas de banco de dados de Alongar. Para obter mais informações, consulte [identificar bancos de dados e tabelas de banco de dados de alongar](sql-server-stretch-database-identify-databases.md). Para saber mais sobre possíveis problemas de bloqueio, consulte [limitações para alongar banco de dados](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Unidade de teste Alongar banco de dados
**Teste unidade Alongar banco de dados com o banco de dados de exemplo AdventureWorks.** Para acessar o banco de dados de exemplo AdventureWorks, baixe pelo menos o arquivo de banco de dados e o arquivo de exemplos e scripts da [aqui](https://www.microsoft.com/download/details.aspx?id=49502). Depois de restaurar o banco de dados de exemplo para uma instância do SQL Server 2016, descompacte o arquivo de exemplos e abra o arquivo de alongar DB exemplos da pasta Alongar DB. Executar os scripts neste arquivo para verificar o espaço usado por seus dados antes e depois de habilitar Alongar banco de dados para controlar o andamento da migração de dados e para confirmar que você pode continuar consultar dados existentes e inserir novos dados durante e após a migração de dados.

## <a name="next-step"></a>Próxima etapa
**Identifique os bancos de dados e tabelas que são candidatos para alongar banco de dados.** Baixe o Supervisor de atualização do SQL Server 2016 e executar o Supervisor de banco de dados de Alongar para identificar bancos de dados e tabelas que são candidatos para alongar banco de dados. Alongar Supervisor de banco de dados também identifica problemas de bloqueio. Para obter mais informações, consulte [identificar bancos de dados e tabelas de banco de dados de alongar](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
