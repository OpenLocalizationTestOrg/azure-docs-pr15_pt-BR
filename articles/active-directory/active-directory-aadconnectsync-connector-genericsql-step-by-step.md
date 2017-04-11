<properties
   pageTitle="Genérico SQL Connector etapa por etapa | Microsoft Azure"
   description="Este artigo é percorrendo um sistema de RH simples passo a passo usando o conector de SQL genérico."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-step-by-step"></a>Conector de SQL genérico passo a passo
Este tópico é um guia passo a passo. Cria um banco de dados de RH de exemplo simples e usá-lo para importar alguns usuários e suas associações de grupo.

## <a name="prepare-the-sample-database"></a>Preparar o banco de dados de exemplo
Em um servidor executando o SQL Server, execute o script SQL no [Apêndice A](#appendix-a). Este script cria um banco de dados de exemplo com o nome GSQLDEMO. O modelo de objeto do banco de dados criado é semelhante a esta imagem:  
![Modelo de objeto](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Também crie um usuário que você deseja usar para se conectar ao banco de dados. Este passo a passo, o usuário é chamado FABRIKAM\SQLUser e localizado no domínio.

## <a name="create-the-odbc-connection-file"></a>Criar o arquivo de conexão ODBC
O conector de SQL genérico está usando ODBC para se conectar ao servidor remoto. Primeiro, precisamos criar um arquivo com as informações de conexão ODBC.

1. Inicie o utilitário de gerenciamento de ODBC no seu servidor:  
![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Selecione a guia **DSN de arquivo**. Clique em **Adicionar...**.
![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Works prontos de driver finos, portanto selecioná-la e clique em **Próximo >**.  
![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Nomeie o arquivo, como **GenericSQL**.  
![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Clique em **Concluir**.  
![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Tempo para configurar a conexão. Dê uma boa descrição de fonte de dados e fornecer o nome do servidor que executa o SQL Server.  
![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Selecione como autenticar com SQL. Nesse caso, podemos usar a autenticação do Windows.  
![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Fornece o nome do banco de dados do exemplo, **GSQLDEMO**.  
![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Manter tudo padrão nessa tela. Clique em **Concluir**.  
![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Para verificar se que tudo está funcionando conforme o esperado, clique em **Testar fonte de dados**.  
![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Verifique se que o teste for bem-sucedido.  
![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. O arquivo de configuração de ODBC agora deve estar visível no DSN do arquivo.  
![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Agora, temos o arquivo podemos precisa e podem começar a criar o conector.

## <a name="create-the-generic-sql-connector"></a>Criar o conector SQL genérico

1. Na UI Gerenciador de serviços de sincronização, selecione **conectores** e **criar**. Selecione **SQL genérico (Microsoft)** e dê um nome descritivo.  
![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Localize o arquivo DSN que você criou na seção anterior e carregue-o no servidor. Forneça as credenciais para se conectar ao banco de dados.  
![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. Este passo a passo, estamos está facilitando para nós e dizer que há dois tipos de objeto, **usuário** e **grupo**.
![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Para localizar os atributos, queremos conector para detectar esses atributos examinando a tabela em si. Como **os usuários** é uma palavra reservada no SQL, precisamos fornecê-la em colchetes [].  
![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Tempo para definir o atributo de âncora e o atributo DN. Para **usuários**, podemos usar a combinação dos dois atributos de nome de usuário e EmployeeID. Para **Agrupar**, podemos usar o nome do grupo (não realista no realísticos, mas para este passo a passo funciona).
![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Nem todos os tipos de atributo podem ser detectados em um banco de dados do SQL. O tipo de atributo de referência não pode em particular. Para o tipo de objeto do grupo, precisamos alterar o OwnerID e MemberID para fazer referência.  
![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Os atributos que selecionamos como atributos de referência na etapa anterior exigem o tipo de objeto esses valores são uma referência a. Em nosso caso, o tipo de objeto do usuário.  
![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Na página de parâmetros globais, selecione a **marca d'água** como a estratégia de delta. Digite também o formato de data/hora **AAAA-MM-dd hh**.
![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Na página **Configurar partições e hierarquias** , selecione os dois tipos de objeto.
![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. Na **Selecionar atributos**e **Selecione tipos de objeto** , selecione tipos de objeto e todos os atributos. Na página **Configurar âncoras** , clique em **Concluir**.

## <a name="create-run-profiles"></a>Criar perfis de execução

1. Na UI Gerenciador de serviços de sincronização, selecione **conectores**e **Configurar perfis de executar**. Clique em **novo perfil**. Vamos começar com **Importação completa**.  
![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Selecione o tipo de **Importação completa (estágio somente)**.  
![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Selecione a partição **objeto = usuário**.  
![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Selecione a **tabela** e digite **[USERS]**. Role para baixo até a seção de tipo de objeto múltiplos e insira os dados como a imagem a seguir. Selecione **Concluir** para salvar a etapa.
![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png)  
![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Selecione **nova etapa**. Desta vez, selecione **objeto = grupo**. Na última página, use a configuração como a imagem a seguir. Clique em **Concluir**.  
![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)  
![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Opcional: Se desejar, você pode configurar perfis de execução adicionais. Para este passo a passo, é usada somente a importação completa.
7. Clique em **Okey** para concluir a alteração perfis de execução.

## <a name="add-some-test-data-and-test-the-import"></a>Adicionar alguns dados de teste e testar a importação
Preencha alguns dados de teste em seu banco de dados de exemplo. Quando você estiver pronto, selecione **Executar** e **importação completa**.

Aqui está um usuário com dois números de telefone e um grupo com alguns membros.  
![CS1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png)  
![CS2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)  

## <a name="appendix-a"></a>Apêndice A
**Script SQL para criar o banco de dados de exemplo**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
