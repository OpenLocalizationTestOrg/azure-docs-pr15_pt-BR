<properties
    pageTitle="Introdução à sincronização de dados de bancos de dados SQL"
    description="Este tutorial ajuda você a começar a usar a sincronização de dados do SQL Azure (visualização)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Introdução à sincronização de dados do SQL Azure (visualização)
Neste tutorial, você vai aprender os conceitos básicos da sincronização de dados do SQL Azure usando o Portal de clássico do Azure.

Este tutorial assume mínimo experiência anterior com SQL Server e Azure SQL Database. Neste tutorial, você cria um grupo de sincronização de híbrido (SQL Server e instâncias de banco de dados SQL) totalmente configurado e sincronizando na agenda que você definir.

> [AZURE.NOTE] A documentação técnica completa definida para sincronização de dados do SQL Azure, localizadas anteriormente no MSDN, está disponível como um PDF. Baixá-lo [aqui](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Etapa 1: Conectar o banco de dados do SQL Azure

1. Entre [Portal clássico](http://manage.windowsazure.com).

2. No painel esquerdo, clique em **Bancos de dados SQL** .

3. Clique em **sincronizar** na parte inferior da página. Quando você clica em sincronização, aparecerá uma lista mostrando as coisas que você pode adicionar - **Novo grupo de sincronização** e **Novo agente de sincronização**.

4. Para iniciar o Assistente de novo agente de sincronização de dados SQL, clique em **Novo agente de sincronização**.

5. Se você ainda não adicionou um agente anteriormente, **clique em baixá-lo aqui**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Etapa 2: Adicionar um agente de cliente
Esta etapa é necessária somente se você vai tem um banco de dados local do SQL Server incluído no seu grupo de sincronização. Vá para a etapa 4 se seu grupo de sincronização tiver apenas instâncias de banco de dados SQL.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Etapa 2a: instalar o software necessário
Certifique-se de que você tem os seguintes programas instalados no computador onde você instala o agente do cliente.

- **.NET framework 4.0**

 Instale o .NET Framework 4.0 [aqui](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 sistema CLR tipos (x86)**

 Instalar os Microsoft SQL Server 2008 R2 SP1 tipos CLR do sistema (x86) [aqui](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Objetos de gerenciamento (x86) de compartilhado do Microsoft SQL Server 2008 R2 SP1**

 Instalar os Microsoft SQL Server 2008 R2 SP1 compartilhado gerenciamento objetos (x86) [aqui](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Etapa 2 bilhões: instalar um novo agente de cliente

Siga as instruções em [instalar um agente de cliente (sincronização de dados do SQL)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) para instalar o agente.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Etapa 2c: concluir o Assistente de novo agente de sincronização de dados SQL

1.  Retornar ao Assistente de nova agente de sincronização de dados SQL.
2.  Dê um nome significativo para o agente.
3.  No menu suspenso, selecione a **região** (data centers) para hospedar este agente.
4.  No menu suspenso, selecione a **assinatura** para hospedar este agente.
5.  Clique na seta à direita.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Etapa 3: Registrar um banco de dados do SQL Server com o agente do cliente

Depois de instalar o agente do cliente, registre cada banco de dados do SQL Server local que pretende incluir em um grupo de sincronização com o agente.
Para registrar um banco de dados com o agente, siga as instruções em [registrar um banco de dados SQL Server com um agente de cliente](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## <a name="step-4-create-a-sync-group"></a>Etapa 4: Criar um grupo de sincronização


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Etapa 4a: iniciar o Assistente de novo grupo de sincronização

1.  Volte para o [Portal clássico](http://manage.windowsazure.com).
2.  Clique em **bancos de dados SQL**.
3.  Clique em **Adicionar sincronizar** na parte inferior da página e selecione o novo grupo de sincronização na registradora.

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Etapa 4b: insira as configurações básicas


1.  Insira um nome significativo para o grupo de sincronização.
2.  No menu suspenso, selecione a **região** (Data Center) para hospedar este grupo de sincronização.
3. Clique na seta à direita.

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Etapa 4c: definir o hub de sincronização

1. No menu suspenso, selecione a instância de banco de dados SQL para servir como o hub de grupo de sincronização.
2. Digite as credenciais para esta instância de banco de dados SQL - **HUB de usuário** e **Senha de HUB**.
3. Aguarde a sincronização de dados SQL confirmar o nome de usuário e senha. Você verá uma marca de seleção verde exibida à direita da senha quando as credenciais são confirmadas.
4. No menu suspenso, selecione a política de **Resolução de conflitos** .

 **Hub Wins** - qualquer alteração gravada a gravação de banco de dados de hub para os bancos de dados de referência, referência de sobrescrever alterações no mesmo registro de banco de dados. Funcionalidade, isso significa que a primeira alteração gravada hub propagada para outros bancos de dados.


 **Cliente Wins** - alterações gravadas hub são substituídos por alterações em bancos de dados de referência. Funcionalidade, isso significa que a última alteração gravada ao hub é aquele mantidos e propagado para outros bancos de dados.

5.  Clique na seta à direita.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Etapa 4d: adicionar um banco de dados de referência


Repita esta etapa para cada banco de dados adicional que você deseja adicionar ao grupo de sincronização.

1. No menu suspenso, selecione o banco de dados para adicionar.

    Bancos de dados na lista suspensa incluem dois bancos de dados do SQL Server que foram registrados com o agente e instâncias de banco de dados SQL.
2.  Insira as credenciais para este banco de dados - **nome de usuário** e **senha**.
3.  No menu suspenso, selecione a **Direção de sincronização** para este banco de dados.

    **Bidirecional** - alterações do banco de dados de referência são gravadas no banco de dados do hub, e as alterações no banco de dados do hub são gravadas no banco de dados de referência.

    **Sincronização do Hub** - o banco de dados recebe atualizações do Hub. Ele não enviar as alterações ao Hub.

    **Sincronizar com o Hub** - o banco de dados envia atualizações para o Hub. Alterações no Hub não são gravadas para este banco de dados.

4.  Para concluir a criação do grupo de sincronização, clique na marca de seleção no canto inferior direito do assistente. Aguarde a sincronização de dados SQL confirmar as credenciais. Uma marca de seleção verde indica que as credenciais são confirmadas.

5.  Clique na marca de seleção em uma segunda vez. Isso retorna para a página de **sincronização** em bancos de dados SQL. Este grupo de sincronização agora está listado com outros grupos de sincronização e agentes.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Etapa 5: Definir os dados para sincronizar

Sincronização de dados SQL Azure permite que você selecione tabelas e colunas para sincronizar. Se você também quiser filtrar uma coluna de modo que somente as linhas com valores específicos (como idade > = 65) sincronizar, usar o portal de sincronização de dados do SQL no Azure e a documentação em Selecione as tabelas, colunas e linhas para sincronizar para definir os dados para sincronizar.

1.  Volte para o [Portal clássico](http://manage.windowsazure.com).
2.  Clique em **bancos de dados SQL**.
3.  Clique na guia **sincronização** .
4.  Clique no nome desse grupo de sincronização.
5.  Clique na guia **Regras de sincronização** .
6.  Selecione o banco de dados que você deseja fornecer o esquema de grupo de sincronização.
7.  Clique na seta à direita.
8.  Clique em **Atualizar esquema**.
9.  Para cada tabela no banco de dados, selecione as colunas para incluir as sincronizações.
    - Não não possível selecionar colunas com tipos de dados sem suporte.
    - Se nenhuma colunas em uma tabela são selecionadas, a tabela não será incluída no grupo de sincronização.
    - Para selecionar/desmarcar todas as tabelas, clique em Selecionar na parte inferior da tela.
10. Clique em **Salvar**, aguarde o grupo de sincronização concluir a configuração.
11. Para retornar à página de aterrissagem da sincronização de dados, clique na seta Voltar no canto superior esquerdo da tela (acima do nome do grupo sincronização).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Etapa 6: Configurar seu grupo de sincronização

Você sempre pode sincronizar um grupo de sincronização clicando em Sincronizar na parte inferior da página de aterrissagem de sincronização de dados.
Para sincronizar em um cronograma, você configurar o grupo de sincronização.

1.  Volte para o [Portal clássico](http://manage.windowsazure.com).
2.  Clique em **bancos de dados SQL**.
3.  Clique na guia **sincronização** .
4.  Clique no nome desse grupo de sincronização.
5.  Clique na guia **Configurar** .
6.  **SINCRONIZAÇÃO AUTOMÁTICA**
    - Para configurar o grupo de sincronização para sincronizar em uma frequência de conjunto, clique em **Diante**. Você ainda pode sincronizar sob demanda clicando em sincronização.
    - Clique em **desativado** para configurar o grupo de sincronização para sincronizar apenas quando você clica em sincronização.
7.  **FREQUÊNCIA DE SINCRONIZAÇÃO**
    - Se a sincronização automática estiver ativado, defina a frequência de sincronização. A frequência deve estar entre 1 mês e 5 minutos.
8.  Clique em **Salvar**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Parabéns. Você criou um grupo de sincronização que inclui uma instância de banco de dados SQL e um banco de dados do SQL Server.

## <a name="next-steps"></a>Próximas etapas
Para obter informações adicionais sobre o banco de dados SQL e sincronização de dados do SQL, consulte:

* [Baixe a documentação técnica de sincronização de dados do SQL concluída](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Visão geral de banco de dados do SQL](sql-database-technical-overview.md)
* [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
 

 
