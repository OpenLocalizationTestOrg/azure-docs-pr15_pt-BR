<properties 
   pageTitle="Protegendo dados armazenados no repositório do Azure dados Lucerne | Microsoft Azure" 
   description="Saiba como proteger os dados no repositório de Lucerne de dados do Azure usando grupos e listas de controle de acesso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protegendo dados armazenados no repositório de Lucerne de dados do Azure

A proteção de dados no armazenamento do Azure dados Lucerne é uma abordagem de três etapas.

1. Comece criando grupos de segurança no Azure Active Directory (AAD). Esses grupos de segurança são usados para implementar o controle de acesso baseado em função (RBAC) no Portal do Azure. Para obter mais informações, consulte [Controle de acesso baseado em função no Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Atribua os grupos de segurança AAD para a conta de armazenamento de Lucerne de dados do Azure. Isso controla o acesso à conta de armazenamento de Lucerne dados entre as operações de portal e gerenciamento do portal ou APIs.

3. Atribua os grupos de segurança AAD como acesso (ACLs) de listas de controle no sistema de arquivos de dados Lucerne Store.

4. Além disso, você também pode definir um intervalo de endereços IP para clientes que podem acessar os dados no repositório de Lucerne de dados.

Este artigo fornece instruções sobre como usar o portal do Azure para realizar as tarefas acima. Para obter informações detalhadas sobre como o armazenamento de dados de Lucerne implementa segurança no nível de conta e dados, consulte [segurança no repositório de Lucerne de dados do Azure](data-lake-store-security-overview.md). Para aprofundar obter informações sobre como ACLs são implementadas no repositório de Lucerne de dados do Azure, consulte [Visão geral do controle de acesso no repositório de Lucerne de dados](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).
- **Conta de um armazenamento de Lucerne de dados do Azure**. Para obter instruções sobre como criar uma, consulte [Introdução ao armazenamento de Lucerne de dados do Azure](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Active Directory do Azure

Para obter instruções sobre como criar grupos de segurança AAD e como adicionar usuários ao grupo, consulte [Gerenciando grupos de segurança no Active Directory do Azure](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Atribuir usuários ou grupos de segurança para contas de armazenamento de Lucerne de dados do Azure

Quando você atribui usuários ou grupos de segurança para contas do Azure dados Lucerne Store, você controla o acesso para as operações de gerenciamento da conta usando o portal do Azure e APIs do Gerenciador de recursos do Azure. 

1. Abra uma conta de armazenamento de Lucerne de dados do Azure. No painel esquerdo, clique em **Procurar**, clique em **Dados Lucerne Store**e da lâmina do armazenamento de Lucerne de dados, clique no nome de conta à qual você deseja atribuir um usuário ou grupo de segurança.

2. Em seu lâmina de conta de armazenamento de Lucerne de dados, clique em **configurações**. Da lâmina **configurações** , clique em **usuários**.

    ![Atribuir o grupo de segurança a conta de armazenamento de Lucerne de dados do Azure] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir o grupo de segurança a conta de armazenamento de Lucerne de dados do Azure")

3. A lâmina de **usuário** por padrão lista grupo de **administradores de assinatura** como um proprietário. 

    ![Adicionar usuários e funções] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Adicionar usuários e funções")
 
    Há duas maneiras de adicionar um grupo e atribuir funções relevantes.

    * Adicionar um usuário/grupo à conta e atribuir uma função, ou
    * Adicionar uma função e atribuir usuários/grupos à função.

    Nesta seção, examinaremos a primeira abordagem, a adição de um grupo e, em seguida, atribuir funções. Você pode executar etapas semelhantes para primeiro selecione uma função e atribuir grupos para essa função.
    
4. Na lâmina **usuários** , clique em **Adicionar** para abrir a lâmina de **acesso de adicionar** . Na lâmina **Adicionar acesso** , clique em **Selecionar uma função**e, em seguida, selecione uma função para usuário/grupo.

     ![Adicionar uma função do usuário] (./media/data-lake-store-secure-data/adl.add.user.1.png "Adicionar uma função do usuário")

    O **proprietário** e a função **Colaborador** fornecem acesso a uma variedade de funções de administração da conta de Lucerne de dados. Para usuários que irá interagir com os dados em Lucerne os dados, você pode adicioná-los à função **leitor **. O escopo dessas funções está limitado às operações de gerenciamento relacionadas à conta do Azure dados Lucerne Store.

    Dados de permissões do sistema de arquivos individuais de operações definem o que os usuários podem fazer. Portanto, um usuário com uma função de leitor pode apenas exibir configurações administrativas associadas à conta, mas pode potencialmente ler e gravar dados com base em permissões do sistema de arquivos atribuídas a eles. Permissões do sistema de arquivos de dados Lucerne Store são descritas no [Atribuir grupo de segurança como ACLs para o sistema de arquivos de armazenamento de Lucerne de dados do Azure](#filepermissions).



5. Na lâmina **Adicionar acesso** , clique em **Adicionar usuários** para abrir a lâmina de **Adicionar usuários** . Neste lâmina, procure o grupo de segurança que você criou anteriormente no Active Directory do Azure. Se você tiver muitos grupos da pesquisa, use a caixa de texto na parte superior para filtrar no nome do grupo. Clique em **Selecionar**.

    ![Adicionar um grupo de segurança] (./media/data-lake-store-secure-data/adl.add.user.2.png "Adicionar um grupo de segurança")

    Se você quiser adicionar um usuário/grupo que não esteja listado, você pode convidá-los usando o ícone **Convidar** e especificando o endereço de email para o usuário/grupo.

6. Clique em **Okey**. Você deve ver o grupo de segurança adicionado conforme mostrado abaixo.

    ![Grupo de segurança adicionado] (./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança adicionado")

7. O grupo de segurança do usuário agora tem acesso à conta de armazenamento de Lucerne de dados do Azure. Se você quiser fornecer acesso a usuários específicos, você pode adicioná-los ao grupo de segurança. Da mesma forma, se você quiser revogar o acesso para um usuário, você poderá removê-los do grupo de segurança. Você também pode atribuir vários grupos de segurança para uma conta. 

## <a name="filepermissions"></a>Atribuir usuários ou grupos de segurança como ACLs para o sistema de arquivos de armazenamento de Lucerne de dados do Azure

Atribuindo grupos de segurança do usuário para o sistema de arquivos Lucerne de dados do Azure, você definir o controle de acesso a dados armazenados no repositório de Lucerne de dados do Azure.

1. No seu blade de conta de armazenamento de Lucerne de dados, clique em **Explorador de dados**.

    ![Criar pastas na conta de armazenamento de Lucerne de dados] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar pastas na conta Lucerne de dados")

2. Na lâmina **Explorador de dados** , clique no arquivo ou pasta para a qual você deseja configurar a ACL e, em seguida, clique em **Access**. Para atribuir ACL para um arquivo, você deve clicar em **acesso** da lâmina **Visualização do arquivo** .

    ![Definir ACLs no sistema de arquivos de dados Lucerne] (./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de arquivos de dados Lucerne")

3. A lâmina de **acesso** lista o acesso padrão e personalizados já atribuídas à raiz. Clique no ícone **Adicionar** para adicionar ACLs nível personalizado.

    ![Acesso à lista padrão e personalizado] (./media/data-lake-store-secure-data/adl.acl.2.png "Acesso à lista padrão e personalizado")

    * **Acessar o padrão** é o acesso de estilo UNIX, onde você especifique ler, gravar, executar (rwx) para três classes de usuário distintas: proprietário, de grupo e outras pessoas.
    * **Acesso personalizado** corresponde ao ACLs POSIX que permite que você defina permissões para usuários específicos de nomeado ou grupos e não apenas o arquivo proprietário ou grupo. 
    
    Para obter mais informações, consulte [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obter mais informações sobre como ACLs são implementadas no repositório de Lucerne de dados, consulte [Controle de acesso no repositório de Lucerne de dados](data-lake-store-access-control.md).

4. Clique no ícone **Adicionar** para abrir a lâmina **Adicionar acesso de personalizado** . Neste lâmina, clique em **Selecionar usuário ou grupo**e em lâmina **Selecione usuário ou grupo** , procure o grupo de segurança que você criou anteriormente no Active Directory do Azure. Se você tiver muitos grupos da pesquisa, use a caixa de texto na parte superior para filtrar no nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.

    ![Adicionar um grupo] (./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")

5. Clique em **Selecionar permissões**, selecione as permissões e se você deseja atribuir as permissões como uma ACL padrão, acessar ACL, ou ambos. Clique em **Okey**.

    ![Atribuir permissões ao grupo] (./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões ao grupo")

    Para obter mais informações sobre permissões no repositório de Lucerne de dados e ACLs padrão de acesso, consulte [Controle de acesso no repositório de Lucerne de dados](data-lake-store-access-control.md).


6. Na lâmina **Adicionar acesso de personalizada** , clique em **Okey**. Grupo recém-adicionado, com as permissões associadas, agora será listado na lâmina **acesso** .

    ![Atribuir permissões ao grupo] (./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões ao grupo")

    > [AZURE.IMPORTANT] Na versão atual, você só pode ter 9 entradas em **Acesso personalizado**. Se você quiser adicionar mais de 9 usuários, você deve criar grupos de segurança, adicionar usuários a grupos de segurança, adicionar fornecer acesso a esses grupos de segurança da conta de armazenamento de Lucerne de dados.

7. Se necessário, você também pode modificar as permissões de acesso depois de ter adicionado o grupo. Marque ou desmarque a caixa de seleção para cada tipo de permissão (leitura, gravação, executar) com base em se você deseja remover ou atribuir essa permissão ao grupo de segurança. Clique em **Salvar** para salvar as alterações ou **Descartar** para desfazer as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir o intervalo de endereços IP para acesso a dados

Armazenamento de Lucerne de dados do Azure permite bloquear ainda mais o acesso ao seu armazenamento de dados no nível de rede. Você pode habilitar o firewall, especificar um endereço IP ou definir um intervalo de endereços IP para seus clientes confiáveis. Quando ativada, somente os clientes que tenham os endereços IP dentro de intervalo definido podem se conectar ao armazenamento de.

![Configurações de firewall e acesso IP] (./media/data-lake-store-secure-data/firewall-ip-access.png "Endereços IP e as configurações de firewall")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Remover grupos de segurança para uma conta de armazenamento de Lucerne de dados do Azure

Quando você remover grupos de segurança de contas do Azure dados Lucerne Store, você está alterando apenas acesso para as operações de gerenciamento da conta usando o Portal do Azure e APIs do Gerenciador de recursos do Azure.

1. Em seu lâmina de conta de armazenamento de Lucerne de dados, clique em **configurações**. Da lâmina **configurações** , clique em **usuários**.

    ![Atribuir o grupo de segurança conta Lucerne de dados do Azure] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir o grupo de segurança conta Lucerne de dados do Azure")

2. Na lâmina **usuários** clique no grupo de segurança que você deseja remover.

    ![Grupo de segurança para remover] (./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança para remover")

3. Na lâmina do grupo de segurança, clique em **Remover**.

    ![Grupo de segurança removido] (./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de segurança removido")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Remover o grupo de segurança ACLs do sistema de arquivos de armazenamento de Lucerne de dados do Azure

Quando você remover grupos de segurança ACLs do sistema de arquivos de armazenamento de Lucerne de dados do Azure, alterar o acesso aos dados de armazenamento de Lucerne de dados.

1. No seu blade de conta de armazenamento de Lucerne de dados, clique em **Explorador de dados**.

    ![Criar pastas na conta Lucerne de dados] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar pastas na conta Lucerne de dados")

2. Na lâmina **Explorador de dados** , clique no arquivo ou pasta para a qual você deseja remover a ACL e em lâmina sua conta, clique no ícone de **acesso** . Para remover ACL para um arquivo, você deve clicar em **acesso** da lâmina **Visualização do arquivo** .

    ![Definir ACLs no sistema de arquivos de dados Lucerne] (./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de arquivos de dados Lucerne")

3. Na lâmina **Access** , na seção **Acesso personalizado** , clique no grupo de segurança que você deseja remover. Na lâmina **Acesso personalizado** , clique em **Remover** e clique em **Okey**.

    ![Atribuir permissões ao grupo] (./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões ao grupo")


## <a name="see-also"></a>Consulte também

- [Visão geral do Azure Lucerne de repositório de dados](data-lake-store-overview.md)
- [Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados](data-lake-store-copy-data-azure-storage-blob.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introdução ao armazenamento de Lucerne de dados usando o PowerShell](data-lake-store-get-started-powershell.md)
- [Introdução ao armazenamento de Lucerne de dados usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
- [Logs de diagnóstico de acesso para armazenamento de Lucerne de dados](data-lake-store-diagnostic-logs.md)
