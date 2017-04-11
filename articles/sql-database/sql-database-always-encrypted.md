<properties
    pageTitle="Sempre criptografadas: Proteger dados confidenciais no banco de dados do SQL Azure com a criptografia do banco de dados | Microsoft Azure"
    description="Proteger dados confidenciais no seu banco de dados do SQL em minutos."
    keywords="criptografar dados, criptografia sql, criptografia de banco de dados, dados confidenciais, sempre criptografadas"
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

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Sempre criptografadas: Proteger dados confidenciais no banco de dados SQL e armazenar suas chaves de criptografia no repositório de certificado do Windows

> [AZURE.SELECTOR]
- [Azure cofre chave](sql-database-always-encrypted-azure-key-vault.md)
- [Repositório de certificados do Windows](sql-database-always-encrypted.md)


Este artigo mostra como proteger os dados confidenciais em um banco de dados do SQL com a criptografia do banco de dados usando o [Assistente sempre criptografado](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Ele também mostra como armazenar suas chaves de criptografia no repositório de certificado do Windows.

Sempre criptografado é uma nova tecnologia de criptografia de dados no banco de dados do SQL Azure e SQL Server que ajuda a proteger dados confidenciais inativos no servidor, durante a movimentação entre cliente e servidor e enquanto os dados estiverem em uso, garantir que os dados confidenciais nunca será exibido como texto sem formatação dentro do sistema de banco de dados. Depois de criptografar dados, apenas os servidores de aplicativo que têm acesso às chaves ou aplicativos cliente podem acessar dados de texto sem formatação. Para obter informações detalhadas, consulte [Sempre criptografadas (mecanismo de banco de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar o banco de dados para usar sempre criptografadas, você irá criar um aplicativo cliente em c# com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo para saber como configurar o sempre criptografados para um banco de dados do SQL Azure. Neste artigo, você aprenderá a executar as seguintes tarefas:

- Use o assistente sempre criptografadas em SSMS para criar [Chaves sempre criptografadas](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Crie uma [Chave de mestre de coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crie uma [chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Criar uma tabela de banco de dados e criptografar colunas.
- Crie um aplicativo que insere, seleciona e exibe dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisará:

- Uma conta do Azure e assinatura. Se você não tiver um, inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- O [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [O visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## <a name="create-a-blank-sql-database"></a>Criar um banco de dados do SQL em branco
1. Entrar no [portal do Azure](https://portal.azure.com/).
2. Clique em **novo** > **dados + armazenamento** > **banco de dados SQL**.
3. Crie um banco de dados **em branco** chamado **clínica** em um servidor novo ou existente. Para obter instruções detalhadas sobre como criar um banco de dados no portal do Azure, consulte [criar um banco de dados SQL em minutos](sql-database-get-started.md).

    ![Criar um banco de dados em branco](./media/sql-database-always-encrypted/create-database.png)

Você precisará a cadeia de conexão posteriormente no tutorial. Depois que o banco de dados for criado, vá para o novo banco de dados de clínica e copie a cadeia de conexão. Você pode obter a cadeia de conexão a qualquer momento, mas é fácil copiá-lo quando você estiver no portal do Azure.

1. Clique em **bancos de dados SQL** > **clínica** > **mostram cadeias de caracteres de conexão de banco de dados**.
2. Copie a cadeia de conexão do **ADO.NET**.

    ![Copie a cadeia de conexão](./media/sql-database-always-encrypted/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Conectar ao banco de dados com SSMS

Abra o SSMS e se conectar ao servidor com o banco de dados clínica.


1. Abra o SSMS. (Clique em **Conectar** > **Mecanismo de banco de dados** para abrir a janela de **conectar ao servidor** se não estiver aberto).
2. Insira seu nome de servidor e credenciais. O nome do servidor pode ser encontrado na lâmina banco de dados SQL e na cadeia de conexão que você copiou anteriormente. Digite o nome completo do servidor incluindo *database.windows.net*.

    ![Copie a cadeia de conexão](./media/sql-database-always-encrypted/ssms-connect.png)

Se abre a janela de **Nova regra de Firewall** , entre no Azure e deixar o SSMS criar uma nova regra de firewall para você.


## <a name="create-a-table"></a>Criar uma tabela

Nesta seção, você criará uma tabela para armazenar dados de pacientes. Esta será uma tabela normal inicialmente – você configurará criptografia na próxima seção.

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

SSMS fornece um Assistente para configurar facilmente sempre criptografadas Configurando o CMK, CEK e colunas criptografadas para você.

1. Expanda **bancos de dados** > **clínica** > **tabelas**.
2. A tabela **pacientes** de atalho e selecione **Criptografar colunas** para abrir o assistente sempre criptografadas:

    ![Criptografar colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

O assistente sempre criptografadas inclui as seguintes seções: **Seleção de coluna**, **Configuração de chave mestre** (CMK), **validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna ###

Clique em **Avançar** na página de **Introdução** para abrir a página de **Seleção de coluna** . Nesta página, você selecionará as colunas que você deseja criptografar, [o tipo de criptografia e quais chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

Criptografe informações de **número de seguridade social** e **Data de nascimento** de cada paciente. A coluna de **número de seguridade social** usará criptografia determinante, que suporta pesquisas de igualdade, junções e agrupar por. Coluna **DataDeNascimento** usará criptografia aleatório, que não suporta operações.

Defina o **Tipo de criptografia** para a coluna **número de seguridade social** **Deterministic** e a coluna **DataDeNascimento** **Randomized**. Clique em **Avançar**.

![Criptografar colunas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuração de chave mestre###

A página de **Configuração de chave mestre** é onde você configurar seu CMK e selecione o provedor de armazenamento de chaves onde o CMK será armazenado. Atualmente, você pode armazenar um CMK no Windows certificate store, Azure chave cofre ou um módulo de segurança de hardware (HSM). Este tutorial mostra como armazenar suas chaves no repositório de certificado do Windows.

Verifique se o **repositório de certificados do Windows** está selecionada e clique em **Avançar**.

![Configuração de chave mestre](./media/sql-database-always-encrypted/master-key-configuration.png)


### <a name="validation"></a>Validação###

Você pode criptografar as colunas agora ou salvar um script PowerShell e executar mais tarde. Para este tutorial, selecione **Continuar para concluir agora** e clique em **Avançar**.

### <a name="summary"></a>Resumo###

Verifique se as configurações estão todas corrigir e clique em **Concluir** para concluir a configuração para sempre criptografados.

![Resumo](./media/sql-database-always-encrypted/summary.png)


### <a name="verify-the-wizards-actions"></a>Verifique se ações do Assistente

Quando o assistente for concluído, o banco de dados está configurado para sempre criptografados. O assistente executou as seguintes ações:

- Criado um CMK.
- Criado um CEK.
- Configurado as colunas selecionadas para criptografia. Sua tabela **pacientes** atualmente não tem dados, mas os dados existentes nas colunas selecionadas agora são criptografados.

Você pode verificar a criação das chaves no SSMS indo para **clínica** > **segurança** > **Sempre chaves criptografadas**. Agora você pode ver as novas chaves que o assistente gerado para você.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar um aplicativo cliente que funciona com os dados criptografados

Agora que sempre criptografadas estiver configurada, você pode criar um aplicativo que realiza *insere* e *seleciona* em colunas criptografadas. Para executar o aplicativo de exemplo com sucesso, você deve executá-lo no mesmo computador onde você executou o Assistente de sempre criptografados. Para executar o aplicativo em outro computador, você deve implantar os certificados sempre criptografados para o computador executando o aplicativo cliente.  

> [AZURE.IMPORTANT] Seu aplicativo deve usar [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos ao passar dados de texto sem formatação para o servidor com colunas sempre criptografados. Passar valores literais sem usar objetos SqlParameter resultará em uma exceção.


1. Abra o Visual Studio e crie um novo aplicativo de console em c#. Verifique se que seu projeto está definido para **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleApp** e clique em **Okey**.

![Novo aplicativo de console](./media/sql-database-always-encrypted/console-app.png)



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de caracteres de conexão para habilitar sempre criptografadas

Esta seção explica como habilitar sempre criptografadas em sua cadeia de conexão de banco de dados. Você irá modificar o aplicativo de console que você acabou de criar na próxima seção, "Sempre criptografadas aplicativo de console de amostra".


Para habilitar sempre criptografadas, você precisa adicionar a palavra-chave de **Configuração de criptografia de coluna** à sua cadeia de conexão e defini-lo como **ativado**.

Você pode definir isso diretamente na cadeia de conexão, ou você pode defini-la usando um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Esta é a única alteração necessária em um aplicativo cliente específico sempre criptografados. Se você tiver um aplicativo existente que armazena sua cadeia de conexão externamente (ou seja, em um arquivo de configuração), talvez seja possível habilitar sempre criptografadas sem alterar qualquer código.


### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar sempre criptografadas na cadeia de conexão

Adicione a palavra-chave seguinte à sua cadeia de conexão:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Habilitar sempre criptografada com um SqlConnectionStringBuilder

O código a seguir mostra como habilitar sempre criptografadas definindo a [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) como [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Sempre criptografado aplicativo de console de amostra

Este exemplo demonstra como:

- Modificar a cadeia de caracteres de conexão para habilitar sempre criptografados.
- Inserir dados em colunas criptografadas.
- Selecione um registro filtrando um valor específico em uma coluna criptografada.

Substitua o conteúdo de **Program.cs** com o seguinte código. Substitua a cadeia de conexão para a variável de connectionString global na linha diretamente acima o método principal com sua cadeia de conexão válida do portal do Azure. Esta é a única alteração que você precisa fazer no código.

Execute o aplicativo para ver sempre criptografadas em ação.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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

Rapidamente, você pode verificar que os dados reais no servidor estão criptografados consultando os dados de **pacientes** com SSMS. (Usar sua conexão atual onde a configuração de criptografia de coluna ainda não está habilitada.)

Execute a seguinte consulta no banco de dados clínica.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Você pode ver que colunas criptografadas não contêm os dados de texto sem formatação.

   ![Novo aplicativo de console](./media/sql-database-always-encrypted/ssms-encrypted.png)


Para usar SSMS para acessar os dados de texto sem formatação, você pode adicionar o **configuração de criptografia de coluna = habilitado** parâmetro para a conexão.

1. No SSMS, seu servidor no **Pesquisador de objetos**de atalho e, em seguida, clique em **Desconectar**.
2. Clique em **Conectar** > **Mecanismo de banco de dados** para abrir a janela **conectar ao servidor** e, em seguida, clique em **Opções**.
3. Clique em **Parâmetros de Conexão adicionais** e digite **configuração de criptografia de coluna = habilitado**.

    ![Novo aplicativo de console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Execute a seguinte consulta no banco de dados **clínica** .

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora você pode ver os dados de texto sem formatação em colunas criptografadas.


    ![Novo aplicativo de console](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Se você se conectar com SSMS (ou qualquer cliente) de um computador diferente, ele não terá acesso às chaves de criptografia e não poderão descriptografar os dados.



## <a name="next-steps"></a>Próximas etapas
Depois de criar um banco de dados que usa sempre criptografadas, talvez você queira fazer o seguinte:

- Execute esse exemplo de um computador diferente. Ele não terá acesso às chaves de criptografia, portanto, ele não terá acesso aos dados de texto sem formatação e não será executado com êxito.
- [Girar e limpar suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar dados que já estão criptografados com sempre criptografados](https://msdn.microsoft.com/library/mt621539.aspx).
- [Implantar sempre criptografadas certificados para outras máquinas de cliente](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte a seção "Como fazer certificados disponíveis para aplicativos e usuários").

## <a name="related-information"></a>Informações relacionadas

- [Sempre criptografadas (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Criptografia de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Criptografia do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistente de sempre criptografado](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog sempre criptografada](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
