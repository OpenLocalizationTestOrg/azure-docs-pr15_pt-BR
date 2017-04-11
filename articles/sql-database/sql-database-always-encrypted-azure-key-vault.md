<properties
    pageTitle="Sempre criptografadas: Proteger dados confidenciais no banco de dados do SQL Azure com a criptografia do banco de dados | Microsoft Azure"
    description="Proteger dados confidenciais no seu banco de dados do SQL em minutos."
    keywords="criptografia de dados, a chave de criptografia, a criptografia de nuvem"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Sempre criptografadas: Proteger dados confidenciais no banco de dados SQL e armazenar suas chaves de criptografia no Azure chave cofre

> [AZURE.SELECTOR]
- [Azure cofre chave](sql-database-always-encrypted-azure-key-vault.md)
- [Repositório de certificados do Windows](sql-database-always-encrypted.md)


Este artigo mostra como proteger os dados confidenciais em um banco de dados do SQL com criptografia de dados usando o [Assistente sempre criptografado](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Ele também inclui instruções que mostrarão como armazenar cada chave de criptografia no Azure chave cofre.

Sempre criptografado é uma nova tecnologia de criptografia de dados no banco de dados do SQL Azure e SQL Server que ajuda a proteger dados confidenciais inativos no servidor, durante a movimentação entre cliente e servidor e enquanto os dados estão em uso. Sempre criptografado garante que os dados confidenciais nunca seja exibida como texto sem formatação dentro do sistema de banco de dados. Depois de configurar a criptografia de dados, apenas os servidores de aplicativo que têm acesso às chaves ou aplicativos cliente podem acessar dados de texto sem formatação. Para obter informações detalhadas, consulte [Sempre criptografadas (mecanismo de banco de dados)](https://msdn.microsoft.com/library/mt163865.aspx).


Depois de configurar o banco de dados para usar sempre criptografadas, você irá criar um aplicativo cliente em c# com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo e saiba como configurar o sempre criptografados para um banco de dados do SQL Azure. Neste artigo, você aprenderá a executar as seguintes tarefas:

- Use o assistente sempre criptografados no SSMS para criar [chaves sempre criptografados](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Crie uma [coluna de chave de mestre (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crie uma [chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Criar uma tabela de banco de dados e criptografar colunas.
- Crie um aplicativo que insere, seleciona e exibe dados das colunas criptografadas.


## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisará:

- Uma conta do Azure e assinatura. Se você não tiver um, inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- O [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [O visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](../powershell-install-configure.md), versão 1.0 ou posterior. Tipo de **(Get-módulo azure - ListAvailable). Versão** para ver qual versão do PowerShell que você está executando.



## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Habilitar o seu aplicativo cliente acessar o serviço de banco de dados SQL

Você deve habilitar o seu aplicativo cliente acessar o serviço de banco de dados SQL Configurando a autenticação necessária e adquirir o *ClientId* e o *segredo* que você precisará autenticar seu aplicativo no código a seguir.

1. Abra o [portal clássico Azure](http://manage.windowsazure.com).
2. Selecione **Active Directory** e clique na instância do Active Directory que seu aplicativo usará.
3. Clique em **aplicativos**e, em seguida, clique em **Adicionar**.
4. Digite um nome para seu aplicativo (por exemplo: *myClientApp*), selecione o **Aplicativo da WEB**e clique na seta para continuar.
5. Para a **URL de SIGN-ON** e **URI de ID do aplicativo** , você pode digitar uma URL válida (por exemplo, *http://myClientApp*) e continuar.
6. Clique em **Configurar**.
7. Copie sua **ID do cliente**. (Você precisará esse valor em seu código posteriormente.)
8. Na seção de **chaves** , selecione **1 ano** na lista suspensa **Selecione duração** . (Você copiará a chave depois de salvar na etapa 14.)
11. Role para baixo e clique em **Adicionar aplicativo**.
12. Deixe o conjunto de **Mostrar** aos **Aplicativos do Microsoft** e selecione **Gerenciamento de serviço do Microsoft Azure**. Clique na marca de seleção para continuar.
13. Selecione **Gerenciamento de serviços do Access Azure** na lista suspensa de **Delegados permissões** .
14. Clique em **Salvar**.
15. Depois que termina de salvar, copie o valor da chave na seção **teclas** . (Você precisará esse valor em seu código posteriormente.)



## <a name="create-a-key-vault-to-store-your-keys"></a>Criar um cofre chave para armazenar suas chaves

Agora que seu aplicativo cliente está configurado e com sua ID de cliente, é hora de criar um chave cofre e configurar sua política de acesso para que você e seu aplicativo possam acessar segredos do cofre (as teclas sempre criptografadas). As permissões de *criar*, *obter*, *lista*, *entrada*, *Verifique se*, *wrapKey*e *unwrapKey* são necessárias para criar uma nova chave de mestre de coluna e de configuração de criptografia com o SQL Server Management Studio.

Você pode criar rapidamente um cofre chave executando o script a seguir. Para obter uma explicação detalhada desses cmdlets e obter mais informações sobre como criar e configurar um cofre chave, consulte [Introdução ao Azure chave cofre](../Key-Vault/key-vault-get-started.md).



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Criar um banco de dados do SQL em branco
1. Entrar no [portal do Azure](https://portal.azure.com/).
2. Vá para o **novo** > **dados + armazenamento** > **banco de dados SQL**.
3. Crie um banco de dados **em branco** chamado **clínica** em um servidor novo ou existente. Para obter instruções detalhadas sobre como criar um banco de dados no portal do Azure, consulte [criar um banco de dados SQL em minutos](sql-database-get-started.md).

    ![Criar um banco de dados em branco](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Você precisará a conexão string posteriormente no tutorial, portanto depois de criar o banco de dados, navegue até o novo banco de dados de clínica e copie a cadeia de conexão. Você pode obter a cadeia de conexão a qualquer momento, mas é fácil copiá-lo no portal do Azure.

1. Vá para **bancos de dados SQL** > **clínica** > **mostram cadeias de caracteres de conexão de banco de dados**.
2. Copie a cadeia de conexão do **ADO.NET**.

    ![Copie a cadeia de conexão](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Conectar ao banco de dados com SSMS

Abra o SSMS e se conectar ao servidor com o banco de dados clínica.


1. Abra o SSMS. (Acesse **Conectar** > **Mecanismo de banco de dados** para abrir a janela de **conectar ao servidor** se ele não estiver aberto.)
2. Insira seu nome de servidor e credenciais. O nome do servidor pode ser encontrado na lâmina banco de dados SQL e na cadeia de conexão que você copiou anteriormente. Digite o nome de servidor completa, incluindo *database.windows.net*.

    ![Copie a cadeia de conexão](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Se abre a janela de **Nova regra de Firewall** , entre no Azure e deixar o SSMS criar uma nova regra de firewall para você.


## <a name="create-a-table"></a>Criar uma tabela

Nesta seção, você criará uma tabela para armazenar dados de pacientes. Ele não está inicialmente criptografados – você configurará criptografia na próxima seção.

1. Expanda **bancos de dados**.
1. O banco de dados **clínica** de atalho e clique em **Nova consulta**.
2. Cole o seguinte Transact-SQL (T-SQL) a nova janela de consulta e **Execute** -lo.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Criptografar colunas (Configurar sempre criptografados)

SSMS fornece um assistente que ajuda você a configurar facilmente sempre criptografadas Configurando a coluna de chave mestre, chave de criptografia de coluna e colunas criptografadas para você.

1. Expanda **bancos de dados** > **clínica** > **tabelas**.
2. A tabela **pacientes** de atalho e selecione **Criptografar colunas** para abrir o assistente sempre criptografadas:

    ![Criptografar colunas](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

O assistente sempre criptografadas inclui as seguintes seções: **Seleção de coluna**, **Configuração de chave mestre**, **validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna##

Clique em **Avançar** na página de **Introdução** para abrir a página de **Seleção de coluna** . Nesta página, você selecionará as colunas que você deseja criptografar, [o tipo de criptografia e quais chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

Criptografe informações de **número de seguridade social** e **Data de nascimento** de cada paciente. A coluna de número de seguridade social usará criptografia determinante, que suporta pesquisas de igualdade, junções e agrupar por. Coluna DataDeNascimento usará criptografia aleatório, que não suporta operações.

Defina o **Tipo de criptografia** para a coluna número de seguridade social **Deterministic** e a coluna DataDeNascimento **Randomized**. Clique em **Avançar**.

![Criptografar colunas](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuração de chave mestre###

A página de **Configuração de chave mestre** é onde você configurar seu CMK e selecione o provedor de armazenamento de chaves onde o CMK será armazenado. Atualmente, você pode armazenar um CMK no Windows certificate store, Azure chave cofre ou um módulo de segurança de hardware (HSM).

Este tutorial mostra como armazenar suas chaves no Azure chave cofre.

1.     Selecione **cofre de chave Azure**.
1.     Selecione o cofre chave desejado na lista suspensa.
1.     Clique em **Avançar**.

![Configuração de chave mestre](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


### <a name="validation"></a>Validação###

Você pode criptografar as colunas agora ou salvar um script PowerShell e executar mais tarde. Para este tutorial, selecione **Continuar para concluir agora** e clique em **Avançar**.

### <a name="summary"></a>Resumo ###

Verifique se as configurações estão todas corrigir e clique em **Concluir** para concluir a configuração para sempre criptografados.


![Resumo](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### <a name="verify-the-wizards-actions"></a>Verifique se ações do Assistente

Quando o assistente for concluído, o banco de dados está configurado para sempre criptografados. O assistente executou as seguintes ações:

- Criou uma chave mestre de coluna e é armazenado no Azure chave cofre.
- Criou uma chave de criptografia de coluna e é armazenado no Azure chave cofre.
- Configurado as colunas selecionadas para criptografia. A tabela pacientes atualmente não tem dados, mas os dados existentes nas colunas selecionadas agora são criptografados.

Você pode verificar a criação das chaves no SSMS expandindo **clínica** > **segurança** > **Sempre chaves criptografadas**.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar um aplicativo cliente que funciona com os dados criptografados

Agora que sempre criptografadas estiver configurada, você pode criar um aplicativo que realiza *insere* e *seleciona* em colunas criptografadas.  

> [AZURE.IMPORTANT] Seu aplicativo deve usar [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos ao passar dados de texto sem formatação para o servidor com colunas sempre criptografados. Passar valores literais sem usar objetos SqlParameter resultará em uma exceção.

1. Abra o Visual Studio e crie um novo aplicativo de console em c#. Verifique se que seu projeto está definido para **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleAKVApp** e clique em **Okey**.
![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)
3. Instalar os seguintes pacotes do NuGet indo em **Ferramentas** > **NuGet Package Manager** > **Package Manager Console**.

Execute essas duas linhas de código no Console do Gerenciador de pacote.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de caracteres de conexão para habilitar sempre criptografadas

Esta seção explica como habilitar sempre criptografadas em sua cadeia de conexão de banco de dados.


Para habilitar sempre criptografadas, você precisa adicionar a palavra-chave de **Configuração de criptografia de coluna** à sua cadeia de conexão e defini-lo como **ativado**.

Você pode definir isso diretamente na cadeia de conexão, ou você pode defini-la usando [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.



### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar sempre criptografadas na cadeia de conexão

Adicione a palavra-chave seguinte à sua cadeia de conexão.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Habilitar sempre criptografada com SqlConnectionStringBuilder

O código a seguir mostra como habilitar sempre criptografadas definindo [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) como [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Registrar o provedor de Cofre de chave do Azure

O código a seguir mostra como registrar o provedor do Azure chave cofre com o driver ADO.NET.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Sempre criptografado aplicativo de console de amostra

Este exemplo demonstra como:

- Modificar a cadeia de caracteres de conexão para habilitar sempre criptografados.
- Registre Azure chave cofre como provedor de repositório de chaves do aplicativo.  
- Inserir dados em colunas criptografadas.
- Selecione um registro filtrando um valor específico em uma coluna criptografada.

Substitua o conteúdo de **Program.cs** com o seguinte código. Substitua a cadeia de conexão para a variável de connectionString global na linha que precede diretamente o método principal com a cadeia de conexão válida do portal do Azure. Esta é a única alteração que você precisa fazer no código.

Execute o aplicativo para ver sempre criptografadas em ação.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Verifique se que os dados são criptografados

Rapidamente, você pode verificar que os dados reais no servidor estão criptografados consultando os dados de pacientes com SSMS (usando sua conexão atual onde **Configuração de criptografia de coluna** ainda não está habilitado).

Execute a seguinte consulta no banco de dados clínica.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Você pode ver que colunas criptografadas não contêm os dados de texto sem formatação.

   ![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Para usar SSMS para acessar os dados de texto sem formatação, você pode adicionar o *configuração de criptografia de coluna = habilitado* parâmetro para a conexão.

1. No SSMS, seu servidor no **Pesquisador de objetos** de atalho e escolha **Desconectar**.
2. Clique em **Conectar** > **Mecanismo de banco de dados** para abrir a janela **conectar ao servidor** e clique em **Opções**.
3. Clique em **Parâmetros de Conexão adicionais** e digite **configuração de criptografia de coluna = habilitado**.

    ![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Execute a seguinte consulta no banco de dados clínica.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora você pode ver os dados de texto sem formatação em colunas criptografadas.


    ![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Próximas etapas
Depois de criar um banco de dados que usa sempre criptografadas, talvez você queira fazer o seguinte:

- [Girar e limpar suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar dados que já estão criptografados com sempre criptografados](https://msdn.microsoft.com/library/mt621539.aspx).


## <a name="related-information"></a>Informações relacionadas

- [Sempre criptografadas (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Criptografia de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Criptografia do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Sempre os Assistente criptografado](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog sempre criptografada](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
