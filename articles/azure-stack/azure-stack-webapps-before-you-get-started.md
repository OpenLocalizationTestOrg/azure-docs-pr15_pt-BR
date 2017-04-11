<properties
    pageTitle="Visualização técnica do serviço de aplicativo do Azure pilha 1 antes de começar a | Microsoft Azure"
    description="Etapas para concluir antes de implantar Web Apps em pilha do Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="before-you-get-started-with-azure-stack-web-apps"></a>Antes de começar com o Azure pilha Web Apps

> [AZURE.NOTE] As informações a seguir se aplica somente implantações de TP1 de pilha do Azure.

Você precisará de alguns itens para instalar os aplicativos Web do Azure pilha:

- Uma implantação concluída do [Azure pilha Technical Preview 1](azure-stack-run-powershell-script.md)
- Há espaço suficiente em seu sistema de pilha do Azure para uma implantação pequena do Azure pilha Web Apps.  O espaço necessário é de aproximadamente 20GB de espaço em disco.
- [Um servidor que esteja executando o SQL Server](#SQL-Server).

>[AZURE.NOTE] As etapas a seguir que todos ocorram na VM cliente.

## <a name="before-you-deploy-azure-stack-web-apps"></a>Antes de implantar Azure pilha Web Apps

Para implantar um provedor de recursos, você deve executar o Environment(ISE) script integrado do PowerShell como administrador. Por esse motivo, é necessário permitir cookies e JavaScript no perfil do Internet Explorer que você usa para entrar no Active Directory do Azure.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Desativar a segurança aprimorada do Internet Explorer

1.  Entrar no computador do Azure pilha prova de conceito (VDC) como **AzureStack/administrador**e, em seguida, abra o **Gerenciador de servidor**.
2.  Desative **A configuração de segurança aprimorada do Internet Explorer** para administradores e usuários.
3.  Entre máquina virtual ClientVM.AzureStack.local como administrador e, em seguida, abra o **Gerenciador de servidor**.
4.  Desative **A configuração de segurança aprimorada do Internet Explorer** para administradores e usuários.

## <a name="enable-cookies"></a>Habilitar cookies

1.  Selecione **Iniciar**, > **todos os aplicativos**, > **Acessórios do Windows**. Clique com botão direito **Do Internet Explorer** > **mais** > **Executar como administrador**.
2.  Se você for solicitado, selecione **uso recomendado segurança**e selecione **Okey**.
3.  No Internet Explorer, selecione **Ferramentas** (o ícone de engrenagem), > **Opções da Internet** > **privacidade** > **Avançado**.
4.  Selecione **Avançado**. Certifique-se de que as duas caixas de seleção **Aceitar** são selecionadas e selecione **Okey** e **Okey** novamente.
5.  Feche o Internet Explorer e reinicie o PowerShell ISE como administrador.

## <a name="install-the-latest-version-of-azure-powershell"></a>Instalar a versão mais recente do PowerShell do Azure

1.  Entre no computador do Azure pilha VDC como **AzureStack/administrador**.
2.  Use a conexão de área de trabalho remota para entrar máquina virtual ClientVM.AzureStack.local como administrador.
3.  Abra o **Painel de controle** e selecione **desinstalar um programa**. 
4.  Clique com botão direito no **Microsoft Azure PowerShell - novembro de 2015** e selecione **desinstalar**.
5.  Após a desinstalação terminar, reinicie o computador de virtual ClientVM.AzureStack.local
6.  Baixe e instale o mais recente [Do PowerShell do Azure](http://aka.ms/azstackpsh).


## <a name="sql-server"></a>SQL Server

Por padrão, o instalador do Azure pilha Web Apps é definido para usar o SQL Server que está instalado junto com o provedor de recursos SQL Azure pilha. Quando você instala o provedor de recursos do SQL Server (SQL RP) Certifique-se de que você anote o nome de usuário de administrador de banco de dados e a senha. Você precisa de ambos quando você instala o Azure pilha Web Apps.
Observação: Você também terá a opção de implantar e usar outro servidor para executar o SQL Server. Você pode escolher a instância do SQL Server para usar quando você concluir as opções no instalador do Azure pilha Web Apps.
