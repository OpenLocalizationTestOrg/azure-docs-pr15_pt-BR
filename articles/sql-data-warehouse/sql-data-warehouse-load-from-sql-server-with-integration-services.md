<properties
   pageTitle="Carregar dados do SQL Server em Azure dados depósito SSIS (SQL) | Microsoft Azure"
   description="Mostra como criar um pacote do SQL Server Integration Services (SSIS) para mover dados de uma grande variedade de fontes de dados para o SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Carregar dados do SQL Server em Azure dados depósito SSIS (SQL)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Crie um pacote do SQL Server Integration Services (SSIS) para carregar dados do SQL Server no depósito de dados do SQL Azure. Opcionalmente, você pode reestruturar, transformação e limpar os dados conforme ele passa pelo fluxo de dados SSIS.

Neste tutorial, você irá:

- Crie um novo projeto do Integration Services no Visual Studio.
- Conecte a fontes de dados, incluindo SQL Server (como uma fonte) e o SQL Data Warehouse (como um destino).
- Crie um pacote do SSIS que carrega os dados da origem para o destino.
- Execute o pacote do SSIS para carregar os dados.

Neste tutorial usa o SQL Server como fonte de dados. Pode estar executando o SQL Server no local ou em uma máquina virtual Azure.

## <a name="basic-concepts"></a>Conceitos básicos

O pacote é a unidade de trabalho no SSIS. Pacotes relacionados são agrupados em projetos. Criar projetos e pacotes de design no Visual Studio com ferramentas de dados do SQL Server. O processo de design é um processo visual na qual você arrasta e solta componentes da caixa de ferramentas para a superfície de design, conectá-los e definir suas propriedades. Depois de terminar seu pacote, você pode implantá-lo para o SQL Server opcionalmente para gerenciamento abrangente, monitoramento e segurança.

## <a name="options-for-loading-data-with-ssis"></a>Opções para carregar dados com SSIS

SSIS SQL Server Integration Services () é um conjunto flexível de ferramentas que fornece uma variedade de opções para se conectar a e carregar dados em, SQL Data Warehouse.

1. Use um destino de líquido ADO para conectar depósito de dados do SQL. Este tutorial usa um destino de líquido ADO porque ela tem as opções de configuração menor.
2. Use um destino OLE DB para conectar depósito de dados do SQL. Esta opção poderá fornecer desempenho ligeiramente melhor do que o destino de líquido ADO.
3. Use a tarefa de carregamento do Azure Blob para testar os dados no armazenamento de Blob do Azure. Use a tarefa SSIS Execute SQL para iniciar um script de Polybase que carrega os dados para o SQL Data Warehouse. Esta opção oferece o melhor desempenho das três opções listados aqui. Para obter a tarefa Azure Blob carregar, baixe o [Microsoft SQL Server 2016 Integration Services Feature Pack para Azure][]. Para saber mais sobre Polybase, consulte o [Guia de PolyBase][].

## <a name="before-you-start"></a>Antes de começar

Para percorrer neste tutorial, você precisa:

1. **SQL Server Integration Services (SSIS)**. SSIS é um componente do SQL Server e requer uma versão de avaliação ou uma versão licenciada do SQL Server. Para obter uma versão de avaliação do SQL Server 2016 Preview, consulte [Avaliações do SQL Server][].
2. **O visual Studio**. Para obter o Visual Studio 2015 Community Edition gratuita, consulte a [Comunidade do Visual Studio][].
3. **Ferramentas de dados do SQL Server para Visual Studio (SSDT)**. Para obter as ferramentas de dados do SQL Server para Visual Studio 2015, consulte [Ferramentas de dados servidor (SSDT) de SQL baixar][].
4. **Dados de exemplo**. Este tutorial usa armazenados no SQL Server no banco de dados de amostra AdventureWorks como dados de origem de dados de exemplo para ser carregado em SQL Data Warehouse. Para acessar o banco de dados de exemplo AdventureWorks, consulte [Bancos de dados de exemplo de 2014 AdventureWorks][].
5. **Permissões e banco de dados de um SQL Data Warehouse**. Este tutorial se conecta a uma instância do SQL Data Warehouse e carrega dados nele. Você precisa ter permissões para criar uma tabela e para carregar dados.
6. **Uma regra de firewall**. Você precisa criar uma regra de firewall no SQL Data Warehouse com o endereço IP do seu computador local antes de você pode carregar dados para o SQL Data Warehouse.

## <a name="step-1-create-a-new-integration-services-project"></a>Etapa 1: Criar um novo projeto do Integration Services

1. Inicie o Visual Studio 2015.
2. No menu **arquivo** , selecione novo **| Projeto**.
3. Navegue até o **instalado | Modelos | Business Intelligence | Serviços de integração de** tipos de projeto.
4. Selecione o **projeto do Integration Services**. Forneça os valores para o **nome** e **local**e, em seguida, selecione **Okey**.

Visual Studio abre e cria um novo projeto do Integration Services (SSIS). Visual Studio abre o designer para o pacote do SSIS novo único (Package. dtsx) do projeto. Você verá as áreas de tela a seguir:

- À esquerda, a **caixa de ferramentas** de componentes SSIS.
- No meio, superfície de design, com várias guias. Você normalmente use pelo menos o **Fluxo de controle** e as guias de **Fluxo de dados** .
- À direita, o **Solution Explorer** e os painéis de **Propriedades** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Etapa 2: Criar o fluxo de dados básico

1. Arraste uma tarefa de fluxo de dados da caixa de ferramentas para o centro da superfície de design (na guia **Fluxo de controle** ).

    ![][02]

2. Clique duas vezes a tarefa de fluxo de dados para alternar para a guia fluxo de dados.
3. Na lista de outras fontes na caixa de ferramentas, arraste uma fonte ADO.NET para a superfície de design. Com o adaptador de origem ainda selecionado, altere o nome para a **fonte do SQL Server** no painel **Propriedades** .
4. Na lista de outros destinos na caixa de ferramentas, arraste um destino ADO.NET para a superfície de design em fonte de ADO.NET. Com o adaptador de destino ainda selecionado, altere seu nome para **SQL DW destino** no painel **Propriedades** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Etapa 3: Configurar o adaptador de origem

1. Clique duas vezes o adaptador de fonte para abrir o **Editor de origem de ADO.NET**.

    ![][03]

2. Na guia **Gerenciador de Conexão** do **Editor de origem de ADO.NET**, clique no botão **novo** ao lado da lista de **Gerenciador de conexão de ADO.NET** para abrir a caixa de diálogo do **Gerenciador de Conexão de ADO.NET configurar** e criar as configurações de conexão do banco de dados do SQL Server do qual este tutorial carrega os dados.

    ![][04]

3. Na caixa de diálogo **Gerenciador de Conexão de ADO.NET configurar** , clique no botão **novo** para abrir a caixa de diálogo **Gerenciador de Conexão** e criar uma nova conexão de dados.

    ![][05]

4. Na caixa de diálogo **Gerenciador de Conexão** , faça o seguinte.

    1. Do **provedor**, selecione o provedor de dados SqlClient.
    2. Para **o nome do servidor**, digite o nome do SQL Server.
    3. Na seção de **logon no servidor** , selecione ou insira informações de autenticação.
    4. Na seção **conectar a um banco de dados** , selecione o banco de dados de exemplo AdventureWorks.
    5. Clique em **Testar Conexão**.
    
        ![][06]
    
    6. Na caixa de diálogo que relata os resultados de teste a conexão, clique em **Okey** para retornar à caixa de diálogo **Gerenciador de Conexão** .
    7. Na caixa de diálogo **Gerenciador de Conexão** , clique em **Okey** para retornar à caixa de diálogo **Configurar Gerenciador de Conexão de ADO.NET** .
 
5. Na caixa de diálogo **Gerenciador de Conexão de ADO.NET configurar** , clique em **Okey** para retornar ao **Editor de origem de ADO.NET**.
6. No **Editor de origem de ADO.NET**, na lista **nome da tabela ou modo de exibição** , selecione a tabela **SalesOrderDetail** .

    ![][07]

7. Clique em **Visualizar** para ver as primeiras 200 linhas de dados na tabela de origem na caixa de diálogo **Visualizar resultados de consulta** .

    ![][08]

8. Na caixa de diálogo **Visualizar resultados de consulta** , clique em **Fechar** para retornar ao **Editor de origem de ADO.NET**.
9. No **Editor de origem de ADO.NET**, clique em **Okey** para concluir a configuração da fonte de dados.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Etapa 4: Conectar o adaptador de origem para o adaptador de destino

1. Selecione o adaptador de origem na superfície de design.
2. Selecione a seta azul que estende a partir do adaptador de origem e arraste-o para o editor de destino até que ele se encaixe.

    ![][10]

    Em um pacote SSIS típico, use um número de outros componentes da caixa de ferramentas SSIS entre a origem e o destino para reestruturar, transformar e limpe os dados conforme ele passa pelo fluxo de dados SSIS. Para manter este exemplo tão simples quanto possível, podemos estiver se conectando a origem diretamente para o destino.

## <a name="step-5-configure-the-destination-adapter"></a>Etapa 5: Configurar o adaptador de destino

1. Clique duas vezes o adaptador de destino para abrir o **Editor de destino ADO.NET**.

    ![][11]

2. Na guia **Gerenciador de Conexão** do **Editor de destino ADO.NET**, clique no botão **novo** ao lado da lista de **Gerenciador de Conexão** para abrir a caixa de diálogo do **Gerenciador de Conexão de ADO.NET configurar** e criar as configurações de conexão do banco de dados de depósito de dados do SQL Azure na qual este tutorial carrega os dados.
3. Na caixa de diálogo **Gerenciador de Conexão de ADO.NET configurar** , clique no botão **novo** para abrir a caixa de diálogo **Gerenciador de Conexão** e criar uma nova conexão de dados.
4. Na caixa de diálogo **Gerenciador de Conexão** , faça o seguinte.
    1. Do **provedor**, selecione o provedor de dados SqlClient.
    2. Para **o nome do servidor**, digite o nome de SQL Data Warehouse.
    3. Na seção de **logon no servidor** , selecione **usar SQL Server autenticação** e insira as informações de autenticação.
    4. Na seção **conectar a um banco de dados** , selecione um banco de dados do SQL Data Warehouse existente.
    5. Clique em **Testar Conexão**.
    6. Na caixa de diálogo que relata os resultados de teste a conexão, clique em **Okey** para retornar à caixa de diálogo **Gerenciador de Conexão** .
    7. Na caixa de diálogo **Gerenciador de Conexão** , clique em **Okey** para retornar à caixa de diálogo **Configurar Gerenciador de Conexão de ADO.NET** .
5. Na caixa de diálogo **Gerenciador de Conexão de ADO.NET configurar** , clique em **Okey** para retornar ao **Editor de destino ADO.NET**.
6. No **Editor de destino ADO.NET**, clique em **novo** ao lado da lista de **usar uma tabela ou modo de exibição** para abrir a caixa de diálogo **Criar tabela** para criar uma nova tabela de destino com uma lista de colunas que coincida com a tabela de origem.

    ![][12a]

7. Na caixa de diálogo **Criar tabela** , faça o seguinte.

    1. Altere o nome da tabela de destino para **SalesOrderDetail**.
    2. Remova a coluna **rowguid** . Não há suporte para o tipo de dados de **identificador exclusivo** em SQL Data Warehouse.
    3. Altere o tipo de dados da coluna **LineTotal** para **dinheiro**. Não há suporte para o tipo de dados **decimal** em SQL Data Warehouse. Para obter informações sobre tipos de dados com suporte, consulte [Criar tabela (depósito de dados do SQL Azure, Parallel Data Warehouse)][].
    
        ![][12b]
    
    4. Clique em **Okey** para criar a tabela e retornar ao **Editor de destino ADO.NET**.

8. No **Editor de destino ADO.NET**, selecione a guia **mapeamentos** para ver como colunas na fonte são mapeadas para colunas no destino.

    ![][13]

9. Clique em **Okey** para concluir a configuração da fonte de dados.

## <a name="step-6-run-the-package-to-load-the-data"></a>Etapa 6: Executar o pacote para carregar os dados

Execute o pacote clicando no botão **Iniciar** na barra de ferramentas ou selecionando uma das opções de **Executar** no menu **Depurar** .

Como o pacote começa a executar, você verá rodas girando amarelas para indicar a atividade, bem como o número de linhas processadas até o momento.

![][14]

Quando o pacote concluiu a execução, você vê as marcas de seleção verdes para indicar sucesso bem como o número total de linhas de dados carregados da origem para o destino.

![][15]

Parabéns! Você usou com êxito o SQL Server Integration Services para carregar dados em depósito de dados do SQL Azure.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o fluxo de dados SSIS. Comece aqui: [Fluxo de dados][].
- Saiba como depurar e solucionar problemas de seu direito de pacotes no ambiente de design. Comece aqui: [Ferramentas de solução de problemas para o desenvolvimento de pacote][].
- Saiba como implantar os projetos de SSIS e pacotes ao servidor de serviços de integração ou para outro local de armazenamento. Comece aqui: [projetos de implantação e pacotes][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[Guia de PolyBase]: https://msdn.microsoft.com/library/mt143171.aspx
[Baixar ferramentas de dados do SQL Server (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[Criar tabela (depósito de dados do SQL Azure, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Fluxo de dados]: https://msdn.microsoft.com/library/ms140080.aspx
[Ferramentas de solução de problemas para desenvolvimento de pacote]: https://msdn.microsoft.com/library/ms137625.aspx
[Implantação de projetos e pacotes]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Serviços de integração com o Microsoft SQL Server 2016 recurso pacote para o Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[Avaliações do SQL Server]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Comunidade do Visual Studio]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[Bancos de dados de exemplo de 2014 AdventureWorks]: https://msftdbprodsamples.codeplex.com/releases/view/125550
