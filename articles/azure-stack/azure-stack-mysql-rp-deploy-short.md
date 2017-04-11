<properties
    pageTitle="Usar bancos de dados MySQL como PaaS na pilha do Azure | Microsoft Azure"
    description="Entenda as etapas rápidas para implantar o provedor de recursos MySQL e fornecer MySQL como um serviço na pilha do Azure."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Usar bancos de dados MySQL como PaaS na pilha do Azure

> [AZURE.NOTE] As informações a seguir se aplica somente implantações de TP1 de pilha do Azure.

Você pode implantar um provedor de recursos de MySQL na pilha do Azure. Depois de implantar o provedor de recursos, você pode criar MySQL servidores e bancos de dados por meio de modelos de implantação do Gerenciador de recursos do Azure e fornecer bancos de dados MySQL como um serviço. Bancos de dados MySQL, que são comuns em sites, suportam a muitas plataformas de site. Depois de implantar o provedor de recursos, você pode criar sites WordPress da plataforma do Azure Web Apps como um complemento do serviço (PaaS) para pilha do Azure.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Etapas rápidas para implantar o provedor de recursos
Use estas etapas se você já estiver familiarizado com o Azure pilha. Se desejar mais detalhes, siga os links em cada seção ou ir direto para [implantar o adaptador de provedor do recurso de banco de dados MySQL no Azure pilha VDC](azure-stack-mysql-rp-deploy-long.md).

1.  Certifique-se de que você [Concluir as etapas de configuração todos](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) antes de implantar o provedor de recursos:

    - Framework .NET 3.5 já está configurada na imagem base do Windows Server. (Se você baixou os bits de pilha Azure após 23 de fevereiro de 2016, você pode ignorar esta etapa.)
    - [Uma versão do PowerShell do Azure que é compatível com o Azure pilha está instalada](http://aka.ms/azStackPsh).
    - Nas configurações de segurança do Internet Explorer na ClientVM, [cookies e segurança aprimorada está desativada do Internet Explorer estão habilitados](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Baixar o arquivo de binários do provedor de recurso MySQL](http://aka.ms/masmysqlrp) e extraia-la na ClientVM em sua pilha de Azure prova de conceito (VDC).

3. [Executar bootstrap.cmd e os scripts](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Um conjunto de scripts agrupados por duas guias principais abre no PowerShell Integrated Scripting ambiente (ISE). Executar todos os scripts carregados em sequência da esquerda para a direita em cada guia.

    1. Execute scripts na guia de **preparação** da esquerda para a direita para:

        - Crie um certificado curinga para proteger a comunicação entre o provedor de recursos e o Gerenciador de recursos do Azure.
        - Aceite os termos do EULA MySQL e baixar os binários MySQL.
        - Carregue os certificados e todos os outros artefatos para uma conta de armazenamento do Azure pilha.
        - Publica pacotes de galeria para que você pode implantar MySQL recursos por meio da Galeria.

        > [AZURE.IMPORTANT] Se qualquer um dos scripts travar sem motivo evidente depois que você enviar seu locatário do Active Directory do Azure, suas configurações de segurança podem estar bloqueando uma DLL necessário para a implantação executar. Para resolver esse problema, procure o Microsoft.AzureStack.Deployment.Telemetry.Dll na sua pasta de provedor do recurso, direito do mouse nele, clique em **Propriedades**e depois marque **Desbloquear** na guia **Geral** .

    2. Execute scripts na guia **implantar** da esquerda para a direita para:

        - [Implantar uma máquina virtual (VM)](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) que irá hospedar seu provedor de recursos, MySQL servidores e bancos de dados que você criará uma instância. Esse script faz referência a um arquivo de parâmetro JSON, que você precisa atualizar com alguns valores antes de executar o script.
        - [Registrar um registro DNS local](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) que será mapeada para seu provedor de recurso máquina virtual.
        - [Registrar seu provedor de recurso](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) com o Gerenciador de recursos local do Azure.

        > [AZURE.IMPORTANT] Todos os scripts presumem que a imagem do sistema operacional base preenche os pré-requisitos (.NET 3.5 instalado, JavaScript e cookies habilitados a ClientVM e a versão mais recente do PowerShell do Azure instalado). Se você receber erros quando você executar os scripts, confira o que você atendida os pré-requisitos.

5. Para [testar seu novo provedor recurso MySQL](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment), implante um banco de dados MySQL do portal do Azure pilha. Clique em **criar** &gt; **personalizado** &gt; **servidor MySQL e banco de dados**.

Isso deve obter seu provedor de recurso MySQL e executando em cerca de 25 minutos.
