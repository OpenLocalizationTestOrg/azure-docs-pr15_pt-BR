<properties
   pageTitle="SSMS suporte do Azure AD MFA com o banco de dados SQL e SQL Data Warehouse | Microsoft Azure"
   description="Use Multi-acrescentado autenticação com SSMS para banco de dados SQL e SQL Data Warehouse."
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
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Suporte a SSMS Azure AD MFA com o banco de dados SQL e SQL Data Warehouse

Azure SQL Database e depósito de dados do SQL Azure agora suportam a conexões do SQL Server Management Studio (SSMS) usando *Autenticação Universal do Active Directory*. Autenticação Universal do Active Directory é um fluxo de trabalho interativo que suporta *Autenticação multifator do Azure* (MFA). Azure MFA ajuda a proteger o acesso a dados e aplicativos atendendo demanda do usuário para um processo de entrada simples. Ele oferece autenticação forte com uma variedade de opções de verificação de fácil — telefonema, mensagem de texto, cartões inteligentes com o pin ou notificação de aplicativo móvel — permitindo que os usuários escolham o método preferir. Para obter uma descrição da autenticação multifator, consulte [Autenticação multifator](../multi-factor-authentication/multi-factor-authentication.md).

SSMS agora dá suporte a:

- MFA interativo com o Azure AD com o potencial de validação de caixa de diálogo pop-up.
- Métodos de autenticação integrada do Active Directory e de senha do Active Directory não interativo que podem ser usados em vários aplicativos diferentes (ADO.NET JDBC, ODBC, etc.). Esses dois métodos nunca resultarem em caixas de diálogo pop-up.

Quando a conta de usuário está configurada para MFA o fluxo de trabalho de autenticação interativa requer interação adicional do usuário por meio de caixas de diálogo pop-up, o uso do cartão inteligente, etc. Quando a conta de usuário é configurada para MFA, o usuário deve selecionar Azure Universal autenticação para se conectar. Se a conta de usuário não requisitar MFA, o usuário ainda pode usar as outras duas opções de autenticação do Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações de autenticação universais para banco de dados SQL e SQL Data Warehouse

- SSMS é a única ferramenta atualmente habilitada para MFA por meio de autenticação Universal do Active Directory.
- Apenas uma única conta do Azure Active Directory pode efetuar login para uma instância do SSMS usando autenticação Universal. Para efetuar login como outra conta do Azure AD, você deve usar outra instância do SSMS. (Essa restrição está limitada a autenticação Universal do Active Directory; você pode fazer logon servidores diferentes usando a autenticação de senha do Active Directory, autenticação integrada do Active Directory ou autenticação do SQL Server).
- SSMS dá suporte à autenticação Universal do Active Directory para visualização Object Explorer, Editor de consultas e armazenamento de consulta.
- DacFx nem o Designer de esquema suporte à autenticação Universal.
- Contas MSA não são suportadas para autenticação Universal do Active Directory.
- Autenticação Universal do Active Directory não é suportada no SSMS para usuários que são importados para o Active Directory atual de outros diretórios de ativos do Azure. Esses usuários não são suportados, porque ela requer um ID de locatário para validar as contas e há nenhum mecanismo para fornecer que.
- Não há nenhum requisitos de software adicionais para autenticação Universal do Active Directory, exceto que você deve usar uma versão compatível do SSMS.

## <a name="configuration-steps"></a>Etapas de configuração

Implementar autenticação multifator requer quatro etapas básicas.

1. **Configurar um Active Directory do Azure** – para obter mais informações, consulte [integração suas identidades do local com o Active Directory do Azure](../active-directory/active-directory-aadconnect.md), [Adicionar seu próprio nome de domínio para o Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure agora dá suporte à Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administrando seu diretório Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)e [Gerenciar Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurar MFA** – para obter instruções passo a passo, consulte [Configurando a autenticação multifator Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Configurar o banco de dados SQL ou depósito de dados do SQL Azure AD autenticação** – para obter instruções passo a passo, consulte [Conectando-se ao banco de dados do SQL ou SQL dados depósito por usando Azure autenticação do Active Directory](sql-database-aad-authentication.md).

4. **Baixar SSMS** – Baixe o SSMS mais recente no computador cliente, (pelo menos agosto de 2016), de [Baixar o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectando-se usando autenticação Universal com SSMS

As etapas a seguir mostram como se conectar ao banco de dados SQL ou SQL Data Warehouse usando o SSMS mais recente.

1. Para se conectar usando a autenticação Universal, na caixa de diálogo **conectar ao servidor** , selecione **Autenticação Universal do Active Directory**.
![conectar-se 1mfa-universal][1]

2. Como de costume para o banco de dados SQL e SQL Data Warehouse, você deve clique em **Opções** e especifique o banco de dados na caixa de diálogo **Opções** . Clique em **Conectar**.
3. Quando for exibida a caixa de diálogo **entrar em sua conta** , forneça a conta e a senha de sua identidade do Active Directory do Azure.
![2mfa-entrar][2]

    > [AZURE.NOTE] Para obter autenticação Universal com uma conta que não exige MFA, você se conectar neste momento. Para usuários que exigem MFA, continue com as etapas a seguir.
 
4. Duas caixas de diálogo de configuração MFA podem aparecer. Dessa vez operação depende do administrador MFA configuração e, portanto, poderá ser opcional. Para um domínio MFA habilitado essa etapa, às vezes, é predefinida (por exemplo, o domínio requer usuários usar um cartão inteligente e pin).  
![configuração de 3mfa][3]

5. Os possíveis segundo caixa de diálogo permite que você selecione os detalhes do seu método de autenticação de uma vez. As opções possíveis são configuradas pelo seu administrador.
![4mfa-verificar-1][4]
 
6. O Active Directory do Azure envia as informações de confirmação para você. Quando você receber o código de verificação, insira-o na caixa **Digite o código de verificação** e clique em **entrar**.
![5mfa-verificar-2][5]

Quando a verificação for concluída, o SSMS conecta normalmente presumindo credenciais válidas e acesso de firewall.

##<a name="next-steps"></a>Próximas etapas  

Conceder acesso ao seu banco de dados a outras pessoas: [autenticação de banco de dados do SQL e a autorização: concedendo acesso](sql-database-manage-logins.md)  
Certificar-se de que outras pessoas podem se conectar por meio do firewall: [Configurar uma regra de firewall de nível de servidor de banco de dados do SQL Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

