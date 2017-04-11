<properties
   pageTitle="Notas de versão do Azure catálogo de dados | Microsoft Azure"
   description="Notas de versão do catálogo de dados do Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Notas de versão do Azure catálogo de dados

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Anotações para 20 de novembro de 2015 lançamento do catálogo de dados do Azure

### <a name="opening-data-sources-in-power-bi-desktop"></a>Fontes de dados de abertura na área de trabalho do Power BI

Ao usar a opção "Abrir no Power BI Desktop" a partir do portal de **Catálogo de dados do Azure** , os usuários podem encontrar um dos dois problemas no aplicativo Power BI Desktop:

- Uma caixa de diálogo com o título "Não é possível ao documento aberto" é exibida
- O aplicativo de área de trabalho do Power BI é aberto, mas o arquivo parece estar vazio

Para cada situação, o problema pode ser resolvido por baixar e instalar a versão mais recente do Power BI Desktop da [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notas de 13 de novembro de 2015 da versão do catálogo de dados do Azure

### <a name="registering-and-connecting-to-teradata"></a>Registrando e conexão para Teradata

Ao se conectar a fontes de dados do Teradata usuários devem ter instalado o driver ODBC Teradata correto que coincidem com o número de bits (32 bits ou 64 bits) do software está sendo usado.

Nessa data de lançamento do ADC, o mais recente [driver ODBC do Teradata para windows (versão 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) é compatível com o Office 2013, mas não com o Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notas de 13 de julho de 2015 da versão do catálogo de dados do Azure

### <a name="registering-and-connecting-to-oracle-database"></a>Registrando e conectando ao banco de dados Oracle

Ao se conectar a fontes de dados do banco de dados Oracle usuários devem ter instalado os drivers Oracle corretos que coincidem com o número de bits (32 bits ou 64 bits) do software está sendo usado.

-   Ao registrar fontes de dados do Oracle em um computador executando o Windows de 32 bits, os drivers de Oracle de 32 bits serão usados
-   Ao registrar fontes de dados do Oracle em um computador executando o Windows de 64 bits, os drivers de Oracle de 64 bits serão usados
-   Ao se conectar a fontes de dados do Oracle usando o Excel em um computador executando a versão de 32 bits do Microsoft Office, inclusive no Windows de 64 bits, os drivers de Oracle de 32 bits serão usados
-   Ao se conectar a fontes de dados do Oracle usando o Excel em um computador executando a versão de 64 bits do Microsoft Office, os drivers de Oracle de 64 bits serão usados

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrando e conectando ao SQL Server Reporting Services

Suporte para fontes de dados do SQL Server Reporting Services (SSRS) está atualmente limitado a apenas os servidores de modo nativo. Suporte para SSRS no modo do SharePoint será adicionado em uma versão posterior.

### <a name="opening-data-assets-in-excel"></a>Ativos de dados de abertura no Excel

Ao abrir ativos de dados no Microsoft Excel a partir do portal de **Catálogo de dados do Azure** , os usuários podem ser solicitados com uma caixa de diálogo de **Aviso de segurança do Microsoft Excel** . Isso é padrão, comportamento esperado e usuários podem selecionar **Habilitar** para continuar.

Para obter mais informações, consulte [Habilitar ou desabilitar alertas de segurança sobre links e arquivos de sites suspeitos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Registro de fonte de dados e configuração de proxy e política

Os usuários podem encontrar uma situação onde eles podem fazer logon no portal do catálogo de dados do Azure, mas quando tentam com a ferramenta de registro de fonte de dados que eles encontrarem uma mensagem de erro que impede que logon fazer logon.

Existem duas causas possíveis para esse comportamento de problema:

**Causa 1: configuração de serviços de Federação do Active Directory** A ferramenta de registro de fonte de dados usa autenticação de formulários para validar o logon de usuário no Active Directory. Para logon bem-sucedido, formulários de autenticação deve estar habilitado na política de autenticação Global por um administrador do Active Directory.

Em algumas situações, esse comportamento de erro pode ocorrer somente quando o usuário está na rede da empresa, ou apenas quando o usuário está se conectando de fora da rede da empresa. A política de autenticação Global permite métodos de autenticação sejam habilitadas separadamente para intranet e extranet conexões. Podem ocorrer erros de logon se autenticação de formulários não estiver habilitada para a rede de onde o usuário está se conectando.

Para obter mais informações, consulte [Configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: configuração de proxy de rede** Se a rede corporativa usa um servidor proxy, a ferramenta de registro não poderá se conectar ao Azure Active Directory através do proxy. Os usuários podem garantir que a ferramenta de registro editando o arquivo de configuração da ferramenta, adicionando desta seção para o arquivo:


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Para localizar o arquivo RegistrationTool.exe.config, inicie a ferramenta de registro e, em seguida, abra o utilitário de Gerenciador de tarefas do Windows. Na guia detalhes no Gerenciador de tarefas, clique com botão direito em RegistrationTool.exe e escolha Abrir local do arquivo no menu pop-up.
