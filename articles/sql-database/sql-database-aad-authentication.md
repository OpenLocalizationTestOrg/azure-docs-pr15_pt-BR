<properties
   pageTitle="Conectar ao banco de dados SQL ou depósito de dados do SQL usando a autenticação do Active Directory do Azure | Microsoft Azure"
   description="Saiba como se conectar ao banco de dados SQL usando autenticação do Azure Active Directory."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Se conectando ao banco de dados SQL ou SQL Data Warehouse usando a autenticação do Active Directory do Azure

Autenticação do Azure Active Directory é um mecanismo de conexão com o banco de dados SQL do Microsoft Azure e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades do Azure Active Directory (AD Azure). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades dos usuários de banco de dados e outros serviços da Microsoft em um local central. Gerenciamento de identificação central fornece um local único para gerenciar usuários de banco de dados e simplifica o gerenciamento de permissões. Benefícios incluem o seguinte:

- Ele fornece uma alternativa para autenticação do SQL Server.
- Ajuda a parar a proliferação de identidades dos usuários em servidores de banco de dados.
- Permite a rotação de senha em um único lugar
- Os clientes podem gerenciar permissões de banco de dados usando grupos (AAD) externos.
- Ele pode eliminar o armazenamento de senhas Ativando autenticação integrada do Windows e outras formas de autenticação compatíveis com o Active Directory do Azure.
- Usos de autenticação do Azure Active Directory contido usuários de banco de dados para autenticar identidades no nível do banco de dados.
- Active Directory do Azure oferece suporte a autenticação baseada em token para aplicativos que se conectam ao banco de dados do SQL.
- Autenticação do Azure Active Directory suporta ADFS (federação de domínios) ou a autenticação de senha nativo para um local do Azure Active Directory sem sincronização de domínio.  
- Active Directory do Azure suporta conexões do SQL Server Management Studio que usam Universal autenticação do Active Directory, que inclui a autenticação multifator (MFA).  MFA inclui autenticação forte com uma variedade de opções de verificação de fácil — telefonema, mensagem de texto, cartões inteligentes com o pin ou notificação de aplicativo móvel. Para obter mais informações, consulte [SSMS suporte para Azure AD MFA com o banco de dados SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

As etapas de configuração incluem os seguintes procedimentos para configurar e usar a autenticação do Active Directory do Azure.

1. Criar e preencher um Active Directory do Azure.
2. Certifique-se de que seu banco de dados está V12 de banco de dados do SQL Azure. (Não é necessário para o SQL Data Warehouse.)
3. Opcional: Associar ou alterar o active directory que está associado a sua assinatura do Azure.
4. Crie um administrador do Active Directory do Azure para Azure SQL Server ou [Depósito de dados do SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configure computadores cliente.
6. Crie usuários de banco de dados contidos em seu banco de dados mapeado para identidades do Azure AD.
7. Conecte ao banco de dados usando identidades do Azure AD.


## <a name="trust-architecture"></a>Arquitetura confiável

Diagrama de alto nível a seguir resume a arquitetura de solução da usando autenticação do Azure AD com o Azure SQL Database. Aplicam os mesmos conceitos depósito de dados do SQL. Para oferecer suporte a senha de usuário nativa do Active Directory do Azure, apenas a parte de nuvem e o banco de dados do SQL Azure AD/Azure é considerada. Para dar suporte a autenticação de federados (ou usuário/senha para credenciais do Windows), a comunicação com bloco do ADFS é necessária. As setas indicam caminhos de comunicação.

![diagrama de auth AAD][1]

O diagrama a seguir indica a federação, a confiabilidade e a hospedagem relações que permitem que um cliente para se conectar a um banco de dados enviando um token. O token é autenticado por um anúncio Azure e é confiável para o banco de dados. Cliente 1 pode representar um Azure Active Directory com usuários nativos ou um Azure Active Directory com usuários federados. Cliente 2 representa uma solução possível incluindo usuários importados; Neste exemplo provenientes de uma federados do Azure Active Directory com ADFS sendo sincronizado com o Active Directory do Azure. É importante entender que o acesso a um banco de dados usando a autenticação do Azure AD requer que a inscrição de hospedagem está associada ao Azure Active Directory. Mesma assinatura deve ser usada para criar o SQL Server que hospeda o banco de dados do SQL Azure ou SQL Data Warehouse.

![relação de assinatura][2]

## <a name="administrator-structure"></a>Estrutura de administrador

Quando usando a autenticação do Azure AD, existem duas contas de administrador do servidor de banco de dados SQL; o administrador original do SQL Server e o administrador do Azure AD. Aplicam os mesmos conceitos depósito de dados do SQL. Somente o administrador com base em uma conta do Azure AD pode criar o primeiro usuário de banco de dados do Azure AD contido em um banco de dados do usuário. O logon de administrador do Azure AD pode ser um usuário do Azure AD ou um grupo do Azure AD. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo que vários administradores Azure AD para a instância do SQL Server. Usando a conta de grupo como um administrador aprimora o gerenciamento, permitindo que você centralmente adicionar e remover membros de grupo no Azure AD sem alterar as permissões no banco de dados do SQL ou usuários. Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][3]

## <a name="permissions"></a>Permissões

Para criar novos usuários, você deve ter o `ALTER ANY USER` permissão no banco de dados. O `ALTER ANY USER` permissão pode ser concedida a qualquer usuário de banco de dados. O `ALTER ANY USER` permissão também é mantida pela contas de administrador do servidor e os usuários de banco de dados com a `CONTROL ON DATABASE` ou `ALTER ON DATABASE` permissão para esse banco de dados e por membros do `db_owner` função banco de dados.

Para criar um usuário de banco de dados contidos no banco de dados do SQL Azure ou SQL Data Warehouse, você deve se conectar ao banco de dados usando uma identidade do Azure AD. Para criar o primeiro usuário de banco de dados independente, você deve se conectar ao banco de dados usando um administrador do Azure Active Directory (que é o proprietário do banco de dados). Isso é demonstrou nas etapas 4 e 5 abaixo. Qualquer autenticação do Active Directory do Azure só é possível se o administrador do Active Directory do Azure foi criado para Azure SQL Database ou o depósito de dados do SQL server. Se o administrador do Active Directory do Azure foi removido do servidor, usuários existentes do Active Directory do Azure criados anteriormente dentro do SQL Server não possível conectar ao banco de dados usando suas credenciais do Active Directory do Azure.

## <a name="azure-ad-features-and-limitations"></a>Limitações e os recursos do azure AD

Os seguintes membros do Azure Active Directory podem ser provisionados no Azure SQL Server ou SQL Data Warehouse:

- Membros nativos: um membro criado no Azure AD no domínio gerenciado ou em um domínio do cliente. Para obter mais informações, consulte [Adicionar seu próprio nome de domínio para o Azure AD](../active-directory/active-directory-add-domain.md).

- Federado membros do domínio: um membro criado no Azure AD com um domínio federado. Para obter mais informações, consulte [Microsoft Azure agora dá suporte à Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Membros importados de outros diretórios de ativos do Azure que são membros do domínio nativo ou federados.

- Grupos do Active Directory criados como grupos de segurança.

Contas da Microsoft (por exemplo, outlook.com, hotmail.com, @ live.com) ou outras contas de convidado (por exemplo gmail.com, yahoo.com) não são suportadas. Se você pode efetuar logon no [https://login.live.com](https://login.live.com) usando a conta e a senha, você está usando uma conta da Microsoft, que não há suporte para autenticação do Azure AD para o banco de dados do Azure SQL ou depósito de dados do SQL Azure.

### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gerenciamento, é recomendável que você provisionar um grupo do Active Directory do Azure dedicado como administrador.
- Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado para um servidor do SQL Azure ou um depósito de dados do SQL Azure a qualquer momento.
- Somente um administrador do Active Directory do Azure para o SQL Server inicialmente pode se conectar ao servidor do SQL Azure ou depósito de dados do SQL Azure usando uma conta do Active Directory do Azure. O administrador do Active Directory pode configurar subsequentes usuários de banco de dados do Active Directory do Azure.
- Recomendamos que você definir o tempo de limite de conexão a 30 segundos.
- SQL Server 2016 Management Studio e ferramentas de dados do SQL Server para Visual Studio 2015 (versão 14.0.60311.1April 2016 ou posterior) suporte à autenticação do Active Directory do Azure. (A autenticação do Active Directory do azure é suportada pelo **provedor de dados do .NET Framework para SQL Server**; no mínimo versão 4.6 do .NET Framework). Portanto, as versões mais recentes dessas ferramentas e aplicativos de camada de dados (DAC e .bacpac) podem usar a autenticação do Active Directory do Azure.
- [Versão ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339) dá suporte a autenticação do Active Directory do Azure Entretanto `bcp.exe` não consegue se conectar usando a autenticação do Active Directory do Azure porque ele usa um provedor ODBC mais antigo.
- `sqlcmd`suporta o início de autenticação do Active Directory do Azure com versão 13.1 disponíveis no [Centro de Download](http://go.microsoft.com/fwlink/?LinkID=825643)do.  
- Ferramentas de dados do SQL Server para Visual Studio 2015 exige pelo menos a versão de abril de 2016 das ferramentas de dados (versão 14.0.60311.1). Atualmente, os usuários do Active Directory do Azure não são mostrados no Pesquisador de objetos SSDT. Como alternativa, exiba os usuários na [database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) dá suporte à autenticação do Active Directory do Azure. Consulte também [Definindo as propriedades de Conexão](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase não pode autenticar usando a autenticação do Active Directory do Azure.
- Algumas ferramentas como BI e o Excel não são suportadas.
- Autenticação do Azure Active Directory há suporte para do banco de dados do SQL Azure portal **Banco de dados de importação** e **Exportação** lâminas. Também há suporte para importação e exportação usando autenticação do Active Directory do Azure do comando do PowerShell.


## <a name="1-create-and-populate-an-azure-ad"></a>1. criar e preencher um Azure AD

Crie um Active Directory do Azure e preenchê-la com usuários e grupos. O Azure Active Directory pode ser o domínio inicial Azure AD gerenciado domínio. O Active Directory do Azure também pode ser um local Active Directory Domain Services federado com o Azure Active Directory.

Para obter mais informações, consulte [integração suas identidades do local com o Active Directory do Azure](../active-directory/active-directory-aadconnect.md), [Adicionar seu próprio nome de domínio para o Azure AD](../active-directory/active-directory-add-domain.md), [Microsoft Azure agora dá suporte à Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)e [Gerenciar Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. garantir que seu banco de dados do SQL é versão 12

Autenticação do Azure Active Directory é compatível com as últimas V12 de banco de dados do SQL. Para obter informações sobre V12 de banco de dados do SQL e para saber se ele está disponível na sua região, consulte [o que há de novo nas últimas V12 de atualização de banco de dados SQL](sql-database-v12-whats-new.md). Esta etapa não é necessária para depósito de dados do SQL Azure, porque o SQL Data Warehouse está disponível somente no V12.

Se você tiver um banco de dados existente, verifique se que está hospedado no V12 de banco de dados do SQL conectando-se ao banco de dados (por exemplo usando o SQL Server Management Studio) e executar `SELECT @@VERSION;`. O resultado esperado para um banco de dados no V12 de banco de dados do SQL é pelo menos **Microsoft SQL Azure (RTM) - 12.0**. Se seu banco de dados não estiver hospedado em V12 de banco de dados do SQL, consulte [planejar e preparar a atualização para V12 de banco de dados do SQL](sql-database-v12-plan-prepare-upgrade.md)e, em seguida, visite o Portal de clássico do Azure para migrar o banco de dados para V12 de banco de dados do SQL.

Como alternativa, você pode criar um novo banco de dados no V12 de banco de dados do SQL seguindo as etapas indicadas em [criar seu primeiro banco de dados do SQL Azure](sql-database-get-started.md). **Dica**: ler a próxima etapa antes de selecionar uma assinatura para seu novo banco de dados.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. opcional: Associar ou alterar o active directory que está associado a sua assinatura do Azure

Para associar seu banco de dados com o diretório do Azure AD para sua organização, verifique o diretório de um diretório confiável para a assinatura Azure que hospeda o banco de dados. Para obter mais informações, consulte [como Azure assinaturas estão associadas a Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Informações adicionais:** Cada assinatura Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ele confia diretório para autenticar os usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma assinatura confia apenas um diretório. Você pode ver qual diretório é confiável para sua assinatura na guia **configurações** , em [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Essa relação de confiança que tem uma assinatura com um diretório é diferente de relação que tem uma assinatura com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais como os recursos de filho de uma assinatura. Se uma assinatura expira, em seguida, acesso aos outros recursos associados à inscrição também interrompe. Mas o diretório permanece no Azure, e você pode associar outra assinatura do diretório e continuar a gerenciar os usuários de diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso de recurso no Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Os procedimentos a seguir fornecem instruções passo a passo sobre como alterar o diretório associado para uma determinada assinatura.

1. Conecte-se ao seu [Portal clássico do Azure](https://manage.windowsazure.com/) usando um administrador de assinatura Azure.
2. Na faixa de opções à esquerda, selecione **configurações**.
3. Suas assinaturas aparecem na tela configurações. Se a assinatura desejada não aparecer, clique em **assinaturas** na parte superior, a caixa de **Filtro por diretório** suspensa Selecione a pasta que contém suas assinaturas e, em seguida, clique em **Aplicar**.

    ![Selecione assinatura][4]
4. Na área **configurações** , clique em sua assinatura e clique em **Editar pasta** na parte inferior da página.

    ![portal do AD-configurações][5]
5. Na caixa **Editar diretório** , selecione Azure Active Directory do que está associado com o SQL Server ou SQL Data Warehouse e, em seguida, clique na seta para Avançar.

    ![Editar-diretório-selecionar][6]
6. Na caixa de diálogo **Confirmar** diretório mapeamento, confirme se "**todos os administradores de colegas serão removidos.**"

    ![Confirmar a editar-diretório][7]
7. Clique na seleção para recarregar o portal.

> [AZURE.NOTE] Quando você alterar o diretório, acesso a todos os administradores de colegas, Azure AD usuários e grupos e os usuários do recurso feito o diretório são removidos e já não tiverem acesso a essa assinatura ou seus recursos. Somente você, como um administrador de serviço, pode configurar o acesso para objetos com base na nova pasta. Essa alteração pode demorar bastante tempo para se propagarem para todos os recursos. Alterando o diretório, também altera o administrador do Azure AD para o banco de dados SQL e SQL Data Warehouse e não permitir acesso de banco de dados para os usuários existentes do Azure AD. O administrador do Azure AD deve ser redefinir (conforme descrito abaixo) e Azure novos usuários do AD devem ser criados.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. criar um administrador do Azure AD para o Azure SQL Server

Cada Azure SQL Server (que hospeda um banco de dados SQL ou SQL Data Warehouse) começa com uma conta de administrador do servidor único que é o administrador de todo o servidor do SQL Azure. Um administrador do SQL Server segundo deve ser criado, que é uma conta do Azure AD. Este principal é criado como um usuário de banco de dados contidos no banco de dados mestre. Como administradores, as contas de administrador do servidor são membros da função **db_owner** em cada banco de dados do usuário e insira cada banco de dados do usuário como o usuário **dbo** . Para obter mais informações sobre as contas de administrador do servidor, consulte [Gerenciar bancos de dados e logon no banco de dados do Azure SQL](sql-database-manage-logins.md) e a seção de **logon e usuários** de [diretrizes de segurança de banco de dados do Azure SQL e limitações](sql-database-security-guidelines.md).

Ao usar o Active Directory do Azure com replicação de localização geográfica, o administrador do Active Directory do Azure deve ser configurado para o principal e os servidores secundários. Se um servidor não tiver um administrador do Active Directory do Azure, em seguida, usuários e o logon do Active Directory do Azure receberão uma "não é possível conectar" Erro de servidor.

> [AZURE.NOTE] Os usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server), não é possível criar usuários Azure baseada em AD, porque eles não tem permissão para validar os usuários de proposta de banco de dados com o Azure AD.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Provisionar um administrador do Active Directory do Azure para o seu servidor do SQL Azure usando o portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, clique em sua conexão para uma lista de possíveis diretórios ativas lista suspensa. Escolha o Active Directory correto como o padrão Azure AD. Esta etapa vincula a associação de assinatura com o Active Directory com o Azure SQL Server, lembrando-se de que a mesma assinatura é usada para ambos Azure AD e SQL Server. (O Azure SQL Server pode ser hospedando Azure SQL Database ou depósito de dados do SQL Azure.)

    ![Escolha ad][8]
2. Na faixa de opções esquerda selecione **SQL servers**, selecione o **SQL server**e na lâmina **Do SQL Server** , na parte superior, clique em **configurações**.

    ![configurações do AD][9]
3. Na lâmina **configurações** , clique em * * administrador do Active Directory.
4. Na lâmina **administrador do Active Directory** , clique em **administrador do Active Directory**e clique em **definir administrador**na parte superior.
5. Na **Administração de adicionar** lâmina, procure um usuário, selecione o usuário ou grupo ser um administrador e, em seguida, clique em **Selecionar**. (A lâmina de administração do Active Directory mostra todos os membros e grupos do Active Directory. Não não possível selecionar usuários ou grupos que estão esmaecidos porque eles não são suportados como administradores do Azure AD. (Consulte a lista de administradores com suporte no **Azure AD recursos e limitações** acima). Controle de acesso baseado em função (RBAC) aplica-se somente ao portal e não é propagado para SQL Server.
6. Na parte superior da lâmina **administrador do Active Directory** , clique em **Salvar**.
    ![Escolha administrador][10]

    O processo de alterar o administrador pode levar alguns minutos. Em seguida, o novo administrador aparece na caixa de **Administração do Active Directory** .

> [AZURE.NOTE] Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já existir no banco de dados mestre virtual como um usuário de autenticação do SQL Server. Se estiver presente, a configuração de administração do Azure AD falhará; Revertendo sua criação e indicando que tal administrador (nome) já existem. Como tal um usuário de autenticação do SQL Server não é parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover mais tarde administrador, na parte superior da lâmina **administrador do Active Directory** , clique em **Remover administração**e clique em **Salvar**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Provisionar um administrador do Azure AD para o Azure SQL Server usando o PowerShell

Para executar cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Para provisionar o administrador Azure AD, execute os seguintes comandos do PowerShell do Azure:

- AzureRmAccount adicionar
- Selecione AzureRmSubscription


Cmdlets usados para provisionar e gerenciar Azure AD administrador:

| Nome do cmdlet                                       | Descrição                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Provisiona um administrador do Active Directory do Azure para Azure SQL Server ou depósito de dados do SQL Azure. (Deve ser a assinatura atual). |
| [Remover AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Remove um administrador do Active Directory do Azure para Azure SQL Server ou depósito de dados do SQL Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Retorna informações sobre um administrador do Active Directory do Azure atualmente configurado para o Azure SQL Server ou um depósito de dados do SQL Azure. |

Usar o comando do PowerShell get-help para ver mais detalhes para cada um desses comandos, por exemplo ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

As seguintes cláusulas de script um grupo de administrador do Azure AD denominada **DBA_Group** (id do objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o **demo_server** servidor em um grupo de recursos denominado **grupo-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o nome de usuário Principal. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Grupos do Azure AD somente o Azure AD é compatível com o nome de exibição.

> [AZURE.NOTE] O comando do PowerShell do Azure ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` não impede os administradores do Azure AD para usuários sem suporte de provisionamento. Um usuário sem suporte pode ser provisionado, mas não pode se conectar a um banco de dados. (Consulte a lista de administradores com suporte no **Azure AD recursos e limitações** acima).

O exemplo a seguir usa opcional **ID do objeto**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Azure AD **ID do objeto** é necessária quando o **DisplayName** não é exclusiva. Para recuperar os valores de **ID do objeto** e **DisplayName** , use a seção do Active Directory do Portal de clássico do Azure e exibir as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o atual Azure AD administrador do servidor do SQL Azure:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

O exemplo a seguir remove um administrador do Azure AD:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

Você também pode provisionar um administrador do Azure Active Directory usando as APIs REST. Para obter mais informações, consulte [referência de API do serviço de gerenciamento restante e operações para operações de bancos de dados do SQL Azure para bancos de dados do SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. configurar seus computadores cliente

Em todos os computadores cliente, do qual seus aplicativos ou usuários se conectar ao banco de dados do SQL Azure ou depósito de dados do SQL Azure usando identidades do Azure AD, você deve instalar o software a seguir:

- .NET framework 4.6 ou posterior do [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Biblioteca de autenticação do Active Directory do Azure para o SQL Server (**ADALSQL. DLL**) está disponível em vários idiomas (x86 e amd64) no Centro de download no [Microsoft autenticação biblioteca do Active Directory para o Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Ferramentas

- Instalar o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [Ferramentas de dados do SQL Server para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende a exigência de .NET Framework 4.6.
- Versão de instala o x86 SSMS do **ADALSQL. DLL**.
- SSDT instala a versão amd64 do **ADALSQL. DLL**.
- O Visual Studio mais recente do [Visual Studio baixa](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito de .NET Framework 4.6, mas não instalar a versão amd64 necessários do **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. criar usuários de banco de dados contidos no seu banco de dados mapeado para identidades do Azure AD

### <a name="about-contained-database-users"></a>Sobre usuários do banco de dados independente

Autenticação do Azure Active Directory exige que os usuários de banco de dados seja criado como usuários de banco de dados independente. Um usuário de banco de dados independente com base em uma identidade do Azure AD, é um usuário de banco de dados que não tenha um logon no banco de dados mestre, e que mapeia para uma identidade no diretório Azure AD que está associado com o banco de dados. A identidade do Azure AD pode ser uma conta de usuário individual ou um grupo. Para obter mais informações sobre usuários do banco de dados independente, consulte [Tomada de usuários de banco de dados contidos seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Usuários de banco de dados (com exceção de administradores) não podem ser criados usando o portal. Funções RBAC não são propagadas para SQL Server, banco de dados SQL ou SQL Data Warehouse. Funções RBAC Azure são usadas para gerenciar recursos do Azure e não se aplicam às permissões do banco de dados. Por exemplo, a função **Colaborador do SQL Server** não conceder acesso ao conectar ao banco de dados SQL ou SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Conectar ao depósito de banco de dados ou dados de usuário usando SQL Server Management Studio ou ferramentas de dados do SQL Server

Para confirmar o administrador do Azure AD está configurado corretamente, se conectar ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um Azure usuário de banco de dados contidos baseada em AD (diferente de administrador do servidor que possui o banco de dados), conectar ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [AZURE.IMPORTANT] Suporte para autenticação do Active Directory do Azure está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [Ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio de 2015. O lançamento de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite que os administradores exijam autenticação multifator usando uma chamada telefônica, a mensagem de texto, cartões inteligentes com o pin ou notificação de aplicativo móvel.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Conectar-se usando a autenticação integrada do Active Directory

Use esse método se você estiver conectado ao Windows usando suas credenciais do Active Directory do Azure de um domínio federado.

1. Iniciar Management Studio ou ferramentas de dados e na caixa de diálogo **conectar ao servidor** (ou **conectar-se ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **Autenticação integrada do Active Directory**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais serão apresentados para a conexão.
    ![Selecione autenticação integrada do AD][11]

2. Clique no botão **Opções** e, na página **Propriedades de Conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário que você deseja se conectar.
    ![Selecione o nome do banco de dados][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Conectar-se usando autenticação de senha do Active Directory

Use esse método quando se conectar com um UPN do Azure AD usando o Azure AD gerenciado domínio. Você também pode usá-la para a conta federada sem acesso ao domínio, por exemplo, quando trabalhando remotamente.

Use esse método se você estiver conectado ao Windows usando credenciais de um domínio não federado com o Azure, ou quando usando autenticação do Azure AD usando o Azure AD com base em inicial ou o domínio de cliente.

1. Iniciar Management Studio ou ferramentas de dados e na caixa de diálogo **conectar ao servidor** (ou **conectar-se ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **Autenticação de senha do Active Directory**.
2. Na caixa **User name** , digite seu nome de usuário do Active Directory do Azure no formato **username@domain.com**. Isso deve ser uma conta do Azure Active Directory ou uma conta de um domínio federar-se com o Active Directory do Azure.
3. Na caixa **senha** , digite sua senha de usuário da conta do Active Directory do Azure ou federado conta de domínio.
    ![Selecione autenticação de senha AD][12]

4. Clique no botão **Opções** e, na página **Propriedades de Conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário que você deseja se conectar. (Consulte o gráfico na opção anterior).


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Criar um usuário de banco de dados do Azure AD contido em um banco de dados do usuário

Para criar um usuário de banco de dados contidos baseada em AD, com Azure (diferente de administrador do servidor que possui o banco de dados), conectar ao banco de dados com uma identidade do Azure AD, como um usuário com pelo menos a permissão de **Alterar qualquer usuário** . Em seguida, use a seguinte sintaxe de Transact-SQL:

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* pode ser o nome de usuário principal de um usuário do Azure AD ou o nome de exibição para um grupo do Azure AD.

**Exemplos:** Para criar um banco de dados contido usuário que representa um anúncio Azure federado ou managed usuário de domínio:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Para criar um usuário de banco de dados independente que representa um anúncio Azure ou federado grupo de domínio, forneça o nome de exibição de um grupo de segurança:

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Para criar um usuário de banco de dados independente que representa um aplicativo que conecta usando um token de Azure AD:

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Para obter mais informações sobre como criar usuários de banco de dados independente com base em identidades do Azure Active Directory, consulte [Criar usuário (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Remover o administrador do Active Directory do Azure para o Azure SQL Server impede que qualquer usuário de autenticação do Azure AD conectando ao servidor. Se necessário, inutilizável usuários do Azure AD podem ser descartados manualmente por um administrador de banco de dados SQL.

Quando você cria um usuário de banco de dados, o usuário recebe a permissão de **Conectar** e pode se conectar ao banco de dados como um membro da função **pública** . Inicialmente as únicas permissões disponíveis para o usuário são quaisquer permissões concedidas à função **pública** ou quaisquer permissões concedidas a grupos Windows que eles são um membro de. Depois que você provisionar um usuário de banco de dados contidos baseada em AD Azure, você pode conceder ao usuário permissões adicionais, da mesma maneira como você conceder permissão para qualquer outro tipo de usuário. Normalmente conceder permissões para funções de banco de dados e adicionar usuários a funções. Para obter mais informações, consulte [Noções básicas de permissão de mecanismo de banco de dados](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções de banco de dados SQL especiais, consulte [Gerenciar bancos de dados e logon no Azure SQL Database](sql-database-manage-logins.md).
Um usuário de domínio federado que é importado para um domínio de gerenciar, deve usar a identidade de domínio gerenciado.

> [AZURE.NOTE] Usuários do Azure AD são marcados nos metadados do banco de dados com tipo E (EXTERNAL_USER) e para os grupos com tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. se conectar usando identidades do Azure AD

Autenticação do Azure Active Directory oferece suporte para os seguintes métodos de se conectar a um banco de dados usando identidades do Azure AD:

- Usando a autenticação do Windows integrada
- Usando um nome principal do Azure AD e uma senha
- Usando a autenticação de token de aplicativo

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Conectando-se usando a autenticação integrada (Windows)

Para usar autenticação integrada do Windows, Active Directory do seu domínio deve ser federado com o Active Directory do Azure. Seu aplicativo cliente (ou um serviço) conectando ao banco de dados deve estar executando em um computador de domínio em um domínio as credenciais de usuário.

Para conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de conexão de banco de dados deve ser definida como integrado ao Active Directory. O seguinte exemplo de código em c# usa ADO .NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Observe que a conexão string palavra-chave ``Integrated Security=True`` não há suporte para conexão com o banco de dados do Azure SQL.
Observe que, ao fazer uma conexão ODBC você precisará remover espaços e configurar a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Conectar-se com um nome principal do Azure AD e uma senha
Para conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação deve ser definida como senha do Active Directory. A cadeia de conexão deve conter ID de usuário/UID e palavras-chave de senha/PWD e os valores. O seguinte exemplo de código em c# usa ADO .NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis no [Azure AD autenticação GitHub demonstração](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 conectando-se com um token de Azure AD
Este método de autenticação permite que os serviços de camada intermediária conectar ao banco de dados do SQL Azure ou depósito de dados do SQL Azure obtendo um token do Azure Active Directory (AAD). Ele permite cenários sofisticados, incluindo a autenticação baseada em certificado. Você deve concluir as quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registrar seu aplicativo com o Active Directory do Azure e obter a id do cliente para seu código. 
2. Crie um usuário de banco de dados que representa o aplicativo. (Concluído anteriormente na etapa 6).
3. Crie um certificado no computador de cliente é executado o aplicativo.
4. Adicione o certificado como uma chave para o seu aplicativo.

Cadeia de conexão de exemplo:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, consulte [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Conectando com sqlcmd  
As instruções a seguir, se conectar usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Consulte também

[Gerenciar bancos de dados e logon no banco de dados do SQL Azure](sql-database-manage-logins.md)

[Usuários do banco de dados independente](https://msdn.microsoft.com/library/ff929071.aspx)

[Criar usuário (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

