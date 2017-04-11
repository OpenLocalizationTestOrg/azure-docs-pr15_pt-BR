<properties
   pageTitle="Conexões de fonte de dados | Microsoft Azure"
   description="Descreve as conexões de fonte de dados para modelos de dados no Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Conexões de fonte de dados

Modelos de dados nos serviços de análise do Azure podem exigir provedores de dados diferentes ao se conectar a determinadas fontes de dados. Em alguns casos, modelos tabulares conectar fontes de dados usando provedores nativos como o SQL Server Native Client (SQLNCLI11) podem retornar um erro.

Por exemplo; Se você tiver na memória ou consulta direta de modelo de dados que se conecta a uma fonte de dados de nuvem como o banco de dados do SQL Azure, se você usar nativos provedores diferentes SQLOLEDB, você poderá ver a mensagem de erro: **"O provedor 'SQLNCLI11.1' não está registrado"**.

Ou, se você tiver um modelo de DirectQuery se conectar a fontes de dados locais, se você usar provedores nativos você pode ver a mensagem de erro: **"Erro ao criar o conjunto de linhas OLE DB. Sintaxe incorreta próxima 'Limite' "**.

## <a name="data-source-providers"></a>Provedores de fonte de dados

Os seguintes provedores de fonte de dados têm suporte na memória ou direcionar os modelos de dados de consulta ao conectar-se ao local ou fontes de dados em nuvem:

|               | **Fonte de dados**                     | **Na memória**                            |  **Consulta direta**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Nuvem**                     | Depósito de dados do SQL Azure      | Provedor de dados do .NET framework para SQL Server | Provedor de dados do .NET framework para SQL Server |
|                           | Banco de dados do SQL Azure            | Provedor de dados do .NET framework para SQL Server | Provedor de dados do .NET framework para SQL Server |
| **Local** (via Gateway) | SQL Server                    | SQL Server Native Client 11.0               | Provedor de dados do .NET framework para SQL Server |
|                           |  SQL Server                             | Provedor OLE DB da Microsoft para SQL Server    |   Provedor de dados do .NET framework para SQL Server                                          |
|                           |  SQL Server                             | Provedor de dados do .NET framework para SQL Server |  Provedor de dados do .NET framework para SQL Server                                           |
|                           | Oracle                        | Provedor OLE DB da Microsoft para Oracle        | Provedor de dados Oracle para .NET               |
|                           |  Oracle                             | Provedor de dados Oracle para .NET               | Provedor de dados Oracle para .NET                                            |
|                           | Teradata                      | Provedor OLE DB para Teradata                | Provedor de dados Teradata para .NET             |
|                           |  Teradata                             | Provedor de dados Teradata para .NET             |  Provedor de dados Teradata para .NET                                            |
|                           | Sistema de plataforma de análise | Provedor de dados do .NET framework para SQL Server | Provedor de dados do .NET framework para SQL Server |


> [AZURE.NOTE] Certifique-se de provedores de 64 bits são instalados quando usando o Gateway local.

Ao migrar um modelo de tabela do SQL Server Analysis Services no local para serviços de análise do Azure, talvez seja necessário alterar o provedor.

**Para especificar um provedor de fonte de dados**

1. No SSDT > **Gerenciador de modelos tabulares** > **Fontes de dados**, clique com botão direito uma conexão de fonte de dados e clique em **Editar fonte de dados**.

2. Em **Editar Conexão**, clique em **Avançado** para abrir a janela de propriedades avançadas.

3. Em **Definir propriedades avançadas** > **provedores**, depois selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. Conta de representação pode ser especificada em SSDT ou SSMS.

Para fontes de dados locais:

- Se usando autenticação do SQL, representação deve ser a conta de serviço.
- Se usando autenticação do Windows, defina o usuário/senha do Windows. Para SQL Server, a autenticação do Windows com uma conta de representação específicas é suportada apenas para modelos de dados na memória.

Para fontes de dados:

- Se usando autenticação do SQL, representação deve ser a conta de serviço.


## <a name="next-steps"></a>Próximas etapas

Se você tiver fontes de dados locais, certifique-se de instalar o [gateway local](analysis-services-gateway.md). Para saber mais sobre como gerenciar seu servidor em SSDT ou SSMS, consulte [Gerenciar seu servidor](analysis-services-manage.md).
