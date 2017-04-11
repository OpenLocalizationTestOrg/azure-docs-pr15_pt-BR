<properties
   pageTitle="Use Studio de plataforma de dados do Redgate para carregar dados em SQL Data Warehouse | Microsoft Azure"
   description="Saiba como usar Studio de plataforma de dados do Redgate para cenários de warehouse de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Carregar os dados com Redgate Studio de plataforma de dados

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Este tutorial mostra como usar [Studio de plataforma de dados do Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) para mover dados de um SQL Server no local depósito de dados do SQL Azure. Dados plataforma Studio aplica as mais apropriadas correções de compatibilidade e otimizações, portanto é a maneira mais rápida para começar a usar o SQL Data Warehouse.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) é um antigo parceiro da Microsoft que fornece diversas ferramentas do SQL Server. Este recurso no Studio de plataforma de dados foi disponibilizado livremente para uso comercial e não comercial.

## <a name="before-you-begin"></a>Antes de começar
### <a name="create-or-identify-resources"></a>Criar ou identificar recursos

Antes de iniciar este tutorial, você precisa ter:

- **Banco de dados no local SQL Server**: os dados que você deseja importar para o SQL Data Warehouse precisam vir de um SQL Server no local (versão 2008R2 ou superior). Studio de plataforma de dados não pode importar dados diretamente a partir de um banco de dados do SQL Azure ou arquivos de texto.
- **Conta de armazenamento do Azure**: dados plataforma Studio estágios os dados no armazenamento de Blob do Azure antes de carregá-lo no SQL Data Warehouse. A conta de armazenamento deve estar usando o modelo de implantação de "gerente de recursos" (padrão) em vez do modelo de implantação "Clássico". Se você não tiver uma conta de armazenamento, Aprenda a criar uma conta de armazenamento. 
- **Depósito de dados do SQL**: Este tutorial move os dados do local SQL Server para SQL Data Warehouse, então você precisa ter um depósito de dados online. Se você não tiver um depósito de dados, Aprenda a criar um depósito de dados do SQL Azure.

> [AZURE.NOTE] Desempenho aprimorado se a conta de armazenamento e o data warehouse são criados na mesma região.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Etapa 1: Entrar no Studio de plataforma de dados com sua conta do Azure
Abra seu navegador da web e navegue até o site [Studio de plataforma de dados](https://www.dataplatformstudio.com/) . Entrar com a mesma conta Azure que você usou para criar o depósito de conta e dados de armazenamento. Se seu endereço de email está associado um trabalho ou conta de escola e uma conta da Microsoft, certifique-se de escolher a conta que tenha acesso aos seus recursos.

> [AZURE.NOTE] Se esta for a primeira vez usando Studio de plataforma de dados, você é solicitado para conceder a permissão de aplicativo para gerenciar os recursos do Azure.

## <a name="step-2-start-the-import-wizard"></a>Etapa 2: Iniciar o Assistente de importação
Na tela principal do DPS, selecione a importação link de depósito de dados do SQL Azure para iniciar o Assistente de importação.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Etapa 3: Instalar o Gateway de Studio de plataforma de dados
Para se conectar ao seu banco de dados do SQL Server no local, você precisa instalar o Gateway DPS. O gateway é um agente de cliente que fornece acesso ao seu ambiente no local, extrai os dados e carrega sua conta de armazenamento. Seus dados nunca passam por meio de servidores do Redgate. Para instalar o Gateway:

1.  Clique no link **Criar Gateway**
2. Baixar e instalar o Gateway usando o instalador fornecido

![][2]

> [AZURE.NOTE] O Gateway pode ser instalado em qualquer computador com acesso à rede no banco de dados do SQL Server de origem. Ele acessa o banco de dados do SQL Server usando a autenticação do Windows com as credenciais do usuário atual.

Depois de instalado, o status do Gateway muda para conectado e você pode selecionar Avançar.

## <a name="step-4-identify-the-source-database"></a>Etapa 4: Identificar o banco de dados de origem
Na caixa de texto *Inserir o nome do servidor* , digite o nome do servidor que hospeda seu banco de dados e selecione **Avançar**. Em seguida, no menu suspenso, selecione o banco de dados que você deseja importar dados.

![][3]

DPS analisa o banco de dados selecionado para tabelas importar. Por padrão, o DPS importa todas as tabelas no banco de dados. Você pode marcar ou desmarcar tabelas expandindo o link de todas as tabelas. Selecione o botão Next para Avançar.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Etapa 5: Escolher uma conta de armazenamento para os dados do estágio
DPS solicitará um local para os dados do estágio. Escolha uma conta existente do armazenamento de sua assinatura e selecione **Avançar**.

> [AZURE.NOTE] DPS criará um novo contêiner de blob na conta de armazenamento escolhida e usar uma pasta distinta para cada importação.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Etapa 6: Selecione um depósito de dados
Em seguida, selecione um banco de dados de [Depósito de dados do SQL Azure](http://aka.ms/sqldw) online para importar os dados. Após selecionar o banco de dados, você precisa inserir as credenciais para se conectar ao banco de dados e selecione **Avançar**.

![][5]

> [AZURE.NOTE] DPS mescla as tabelas de dados de origem para o data warehouse. DPS avisa se o nome da tabela requê-lo para substituir as tabelas existentes no data warehouse. Você pode opcionalmente excluir quaisquer objetos existentes no data warehouse acionamento excluir todos os objetos existentes antes da importação.

## <a name="step-7-import-the-data"></a>Etapa 7: Importar os dados
DPS confirma que você gostaria de importar os dados. Basta clicar no botão de importação de iniciar para iniciar a importação de dados.

![][6]

DPS exibe uma visualização que mostra o andamento do extrair e carregar os dados do SQL Server no local e o progresso da importação para SQL Data Warehouse.

![][7]

Quando a importação for concluída, o DPS exibe um resumo da importação de dados e um relatório de alterar as correções de compatibilidade que foram realizadas.

![][8]

## <a name="next-steps"></a>Próximas etapas
Para explorar dados no depósito de dados do SQL, comece a exibição:

- [Consulta SQL Azure Data Warehouse (Visual Studio)][]
- [Visualizar dados com o Power BI][]

Para saber mais sobre Studio de plataforma de dados do Redgate:

- [Visite a home page DPS](http://www.dataplatformstudio.com/)
- [Assista a uma demonstração do DPS no Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Para obter uma visão geral de outras maneiras de migrar e carregar seus dados no depósito de dados do SQL, consulte:

- [Migrar sua solução depósito de dados do SQL][]
- [Carregar dados em um depósito de dados do SQL Azure](./sql-data-warehouse-overview-load.md)

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Consulta SQL Azure Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualizar dados com o Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrar sua solução depósito de dados do SQL]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md