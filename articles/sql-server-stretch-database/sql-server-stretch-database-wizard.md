<properties
    pageTitle="Começar a executando o banco de dados habilitar para alongar Assistente | Microsoft Azure"
    description="Saiba como configurar um banco de dados do banco de dados de alongar executando o banco de dados habilitar para alongar assistente."
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
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Começar a executando o banco de dados habilitar para alongar Assistente

Para configurar um banco de dados para o banco de dados de alongar, execute o banco de dados habilitar para alongar assistente.  Este tópico descreve as informações que você precisa digitar e as opções que você precisará fazer no assistente.

Para saber mais sobre o banco de dados de alongar, consulte [Alongar banco de dados](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Posteriormente, se você desabilitar o banco de dados de alongar, lembre-se de que desabilitando o banco de dados de Alongar para uma tabela ou um banco de dados não exclui o objeto remoto. Se você quiser excluir tabela remota ou banco de dados remoto, você precisa soltá-lo usando o portal de gerenciamento do Azure. Objetos remotos continuam provoca custos Azure até você excluí-los manualmente. 

## <a name="launch-the-wizard"></a>Iniciar o Assistente

1.  No SQL Server Management Studio, no Pesquisador de objetos, selecione o banco de dados no qual você deseja habilitar Alongar.

2.  Direita\-clique em **tarefas**e em seguida, selecione **Alongar**e selecione selecione **Habilitar** para iniciar o assistente.

## <a name="Intro"></a>Introdução
Examine a finalidade do assistente e os pré-requisitos.

Os pré-requisitos importantes incluem o seguinte:

-   Você precisa ser um administrador para alterar as configurações de banco de dados.
-   Você precisa ter uma assinatura do Microsoft Azure.
-   O SQL Server deve ser capaz de se comunicar com o servidor do Azure remoto.

![Página de Introdução do assistente Alongar banco de dados][StretchWizardImage1]

## <a name="Tables"></a>Selecione as tabelas
Selecione as tabelas que você deseja habilitar para alongar.

Tabelas com muitas linhas aparecem na parte superior da lista classificada. Antes do assistente exibe a lista de tabelas, ele os analisa para tipos de dados que não são aceitos atualmente por Alongar banco de dados.

![Selecione a página de tabelas do banco de dados de alongar Assistente][StretchWizardImage2]

|Coluna|Descrição|
|----------|---------------|
|(sem título)|Marque a caixa de seleção nessa coluna para habilitar a tabela selecionada para alongar.|
|**Nome**|Especifica o nome da coluna na tabela.|
|(sem título)|Um símbolo nesta coluna pode representar um aviso que não\'t impedem a habilitação da tabela selecionada para alongar. Ele também pode representar um problema de bloqueio que impede que você habilitar a tabela selecionada para alongar \- por exemplo, porque a tabela usa um tipo de dados sem suporte. Passe o mouse sobre o símbolo para exibir mais informações em uma dica de ferramenta. Para obter mais informações, consulte [limitações para alongar banco de dados](sql-server-stretch-database-limitations.md).|
|**Alongada**|Indica se a tabela já está habilitada para alongar.|
|**Migrar**|Você pode migrar uma tabela inteira (**Tabela inteira**) ou você pode especificar um filtro em uma coluna existente na tabela. Se você quiser usar uma função de filtro diferente para selecionar linhas a serem migradas, execute a instrução ALTER TABLE para especificar a função filter após você sair do assistente. Para obter mais informações sobre a função de filtro, consulte [Selecionar linhas a serem migradas usando uma função de filtro](sql-server-stretch-database-predicate-function.md). Para obter mais informações sobre como aplicar a função, consulte [Habilitar Alongar de banco de dados para uma tabela](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Linhas**|Especifica o número de linhas na tabela.|
|**Tamanho (KB)**|Especifica o tamanho da tabela em KB.|

## <a name="Filter"></a>Opcionalmente, fornecer um filtro de linha

Se você quiser fornecer uma função de filtro para selecionar as linhas a serem migradas, faça o seguinte na página **Selecionar tabelas** .

1.  Na lista **Selecionar as tabelas que você deseja Alongar** , clique em **Tabela inteira** na linha da tabela. Abre a caixa de diálogo **Selecionar linhas para alongar** .

    ![Definir uma função de filtro][StretchWizardImage2a]

2.  Na caixa de diálogo **Selecionar linhas para alongar** , selecione **Escolher linhas**.

3.  No **campo nome**, forneça um nome para a função de filtro.

4.  Para a cláusula **Where** , escolha uma coluna da tabela, selecione um operador e fornecer um valor.

5. Clique em **Verificar** para testar a função. Se a função retorna os resultados da tabela - isto é, se houver linhas a serem migradas que satisfaçam a condição - o teste relatórios **sucesso**.

    >   [AZURE.NOTE] Caixa de texto que exibe a consulta de filtro é somente leitura. Você não pode editar a consulta na caixa de texto.

6.  Clique em concluído para retornar à página **Selecione as tabelas** .

A função de filtro é criada no SQL Server apenas quando você concluir o assistente. Até lá, você pode retornar para a página **Selecionar tabelas** para alterar ou renomear a função de filtro.

![Selecione página tabelas após definindo uma função de filtro][StretchWizardImage2b]

Se você quiser usar um tipo diferente de função de filtro para selecionar linhas a serem migradas, siga um destes procedimentos.  

-   Sair do assistente e executar a instrução ALTER TABLE para habilitar Alongar para a tabela e especificar uma função de filtro. Para obter mais informações, consulte [Habilitar Alongar de banco de dados para uma tabela](sql-server-stretch-database-enable-table.md).  

-   Execute a instrução ALTER TABLE para especificar uma função de filtro após você sair do assistente. Para as etapas necessárias, consulte [Adicionar uma função de filtro após executar o assistente](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurar a implantação do Azure

1.  Entrar no Microsoft Azure com uma conta da Microsoft.

    ![Entrar no Azure - Assistente Alongar banco de dados][StretchWizardImage3]

2.  Selecione a assinatura existente do Azure para usar para alongar banco de dados.

3.  Selecione uma região Azure.
    -   Se você criar um novo servidor, o servidor é criado nesta região.  
    -   Se você tiver servidores existentes na região selecionada, o assistente lista-los quando você escolhe o **servidor existente**.

    Para minimizar a latência, escolha o Azure região em que o SQL Server está localizado. Para obter mais informações sobre regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

4.  Especifique se deseja usar um servidor existente ou criar um novo servidor Azure.

    Se o Active Directory no SQL Server for federado com o Active Directory do Azure, opcionalmente, você pode usar uma conta de serviço federado para o SQL Server para se comunicar com o servidor do Azure remoto. Para obter mais informações sobre os requisitos para essa opção, consulte [Alterar banco de dados definir opções (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Criar novo servidor**

        1.  Crie um logon e uma senha para o administrador do servidor.

        2.  Opcionalmente, use uma conta de serviço federado do SQL Server para se comunicar com o servidor do Azure remoto.

        ![Criar novo servidor Azure - Assistente Alongar banco de dados][StretchWizardImage4]

    -   **Servidor existente**

        1.  Selecione o servidor do Azure existente.

        2.  Selecione o método de autenticação.

            -   Se você selecionar a **Autenticação do SQL Server**, forneça o logon de administrador e a senha.

            -   Selecione **Autenticação integrada do Active Directory** para usar uma conta de serviço federado para o SQL Server para se comunicar com o servidor do Azure remoto. Se o servidor selecionado não é integrado ao Azure Active Directory, essa opção não aparece.

        ![Selecione Azure server existente - Assistente Alongar banco de dados][StretchWizardImage5]

## <a name="Credentials"></a>Proteger credenciais
Você precisa ter uma chave mestre de banco de dados para proteger as credenciais de banco de dados de alongar usa para se conectar ao banco de dados remoto.  

Se já existir uma chave mestre de banco de dados, insira a senha para ele.  

![Página de credenciais seguro do assistente Alongar banco de dados][StretchWizardImage6b]

Se o banco de dados não tiver uma chave mestra existente, digite uma senha forte para criar uma chave mestre de banco de dados.  

![Página de credenciais seguro do assistente Alongar banco de dados][StretchWizardImage6]

Para obter mais informações sobre a chave mestra do banco de dados, consulte [criar chave mestre (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [criar uma chave mestre de banco de dados](https://msdn.microsoft.com/library/aa337551.aspx). Para obter mais informações sobre as credenciais que o assistente cria, consulte [Criar banco de dados com escopo CREDENCIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selecione endereço IP
Use o intervalo de endereços IP de sub-rede (recomendado) ou o endereço IP público do SQL Server, para criar uma regra de firewall no Azure que permite que o SQL Server se comunicar com o servidor do Azure remoto.

O endereço IP ou os endereços que você fornecer nesta página informe o servidor do Azure para permitir que os dados de entrada, consultas e as operações de gerenciamento iniciadas pelo SQL Server para passar pelo firewall do Azure. O assistente não permite fazer alterações nas configurações do firewall no SQL Server.

![Selecione a página de endereço IP do assistente Alongar banco de dados][StretchWizardImage7]

## <a name="Summary"></a>Resumo
Examine os valores que você inseriu e as opções que você selecionou no assistente e os custos estimados no Azure. Selecione **Concluir** para habilitar Alongar.

![Página de resumo do assistente Alongar banco de dados][StretchWizardImage8]

## <a name="Results"></a>Resultados
Examine os resultados.

Para monitorar o status de migração de dados, consulte [Monitor e solucionar problemas de migração de dados (banco de dados de alongar)](sql-server-stretch-database-monitor.md).

![Página de resultados do assistente Alongar banco de dados][StretchWizardImage9]

## <a name="KnownIssues"></a>O Assistente de solução de problemas
**O Assistente de banco de dados de alongar falha.**
Se o banco de dados de alongar ainda não estiver habilitado no nível do servidor e executar o assistente sem o sistema de permissões de administrador para ativá-lo, o assistente falha. Peça ao administrador de sistema para habilitar o banco de dados de Alongar na instância do servidor local e, em seguida, execute o assistente novamente. Para obter mais informações, consulte [pré-requisito: permissão para habilitar o banco de dados de alongar no servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Próximas etapas
Habilite tabelas adicionais para alongar banco de dados. Monitorar a migração de dados e gerenciar Alongar\-habilitado para bancos de dados e tabelas.

-   [Habilitar Alongar de banco de dados para uma tabela](sql-server-stretch-database-enable-table.md) para habilitar tabelas adicionais.

-   [Monitor e solucionar problemas de migração de dados](sql-server-stretch-database-monitor.md) para ver o status de migração de dados.

-   [Pausar e continuar Alongar banco de dados](sql-server-stretch-database-pause.md)

-   [Gerenciar e solucionar problemas de banco de dados de alongar](sql-server-stretch-database-manage.md)

-   [Bancos de dados habilitados para alongar backup](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Consulte também

[Habilitar Alongar banco de dados para um banco de dados](sql-server-stretch-database-enable-database.md)

[Ativar o banco de dados de Alongar para uma tabela](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png
