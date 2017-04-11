<properties
    pageTitle="Implantar o provedor de recursos de MySQL na pilha do Azure | Microsoft Azure"
    description="Etapas detalhadas para implantar o provedor de recursos MySQL Azure pilha."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>


# <a name="deploy-the-mysql-resource-provider-on-azure-stack-to-use-with-webapps"></a>Implantar o provedor de recursos de MySQL na pilha do Azure para usar com WebApps

> [AZURE.NOTE] As informações a seguir se aplica somente implantações de TP1 de pilha do Azure.

Use este artigo para acompanhar as etapas detalhadas para configurar o provedor de recursos MySQL no comprovante Azure pilha de conceito (VDC) para que você possa iniciar [usando bancos de dados MySQL](azure-stack-mysql-rp-deploy-short.md) na pilha do Azure, incluindo usando MySQL como back-end para WordPress sites criado com o [Azure Web Apps](azure-stack-webapps-deploy.md).

## <a name="set-up-steps-before-you-deploy"></a>Etapas de configuração antes de implantar

Antes de implantar o provedor de recursos, você precisa:

- Tem uma imagem do Windows Server padrão com .NET 3.5
- Desativar a segurança aprimorada do Internet Explorer (IE)
- Instalar a versão mais recente do PowerShell do Azure

### <a name="create-an-image-of-windows-server-including-net-35"></a>Criar uma imagem do Windows Server, incluindo .NET 3.5

Se você baixou os bits de pilha Azure após 23/2/2016 porque a imagem padrão do Windows Server 2012 R2 base inclui framework .NET 3.5 neste download e versões posteriores, você pode pular esta etapa.

Se você baixou antes de 23/2/2016, você precisa criar um VHD de data center do Windows Server 2012 R2 com imagem de .NET 3.5 e conjunto é como a imagem padrão no repositório de imagens de plataforma.

### <a name="turn-off-ie-enhanced-security-and-enable-cookies"></a>Desativar IE enhanced segurança e habilitar cookies

Para implantar um provedor de recursos, você executar o ambiente de script integrado do PowerShell (ISE) como administrador, então você precisa permitir cookies e JavaScript no perfil do Internet Explorer que você usa para entrar no Active Directory do Azure para suplementos sinal de administrador e usuário.

**Para desativar o IE segurança aprimorada:**

1. Entrar no computador do Azure pilha prova de conceito (VDC) como AzureStack/administrador e, em seguida, abra o Gerenciador de servidor.

2. Desative **A configuração de segurança aprimorada IE** para administradores e usuários.

3. Entre máquina virtual **ClientVM.AzureStack.local** como administrador e, em seguida, abra o Gerenciador de servidor.

4. Desative **A configuração de segurança aprimorada IE** para administradores e usuários.

**Para habilitar cookies:**

1. Na tela Iniciar do Windows, clique em **todos os aplicativos**, clique em **Acessórios do Windows**, clique com botão direito **Do Internet Explorer**, aponte para **mais**e, em seguida, selecione **Executar como administrador**.

2. Se solicitado, marque **uso recomendado segurança**e clique em **Okey**.

3. No Internet Explorer, clique nas **Ferramentas (ícone de engrenagem e)** &gt; **Opções da Internet** &gt; guia **privacidade** .

4. Clique em **Avançado**, certifique-se de que ambos os botões **Aceitar** são selecionados, clique **Okey**e clique em **Okey** novamente.

5. Feche o Internet Explorer e reinicie o PowerShell ISE como administrador.

### <a name="install-an-azure-stack-compatible-release-of-azure-powershell"></a>Instalar uma versão compatível do Azure pilha do PowerShell do Azure

1. Desinstale qualquer Azure PowerShell existentes de sua máquina virtual cliente.

2. Entrar na máquina Azure pilha VDC como AzureStack/administrador.

3. Usando a área de trabalho remota, entre na máquina virtual de **ClientVM.AzureStack.local** como administrador.

4. Abra o painel de controle, clique em **desinstalar um programa** &gt; clique **Azure PowerShell** &gt; clique em **desinstalar**.

5. [Baixar o PowerShell Azure mais recente que ofereça suporte a pilha do Azure](http://aka.ms/azstackpsh) e instalá-lo.

    Após a instalação do PowerShell, você pode executar essa verificação script do PowerShell para certificar-se de que você pode conectar a sua instância do Azure pilha (uma página da web de login deve aparecer).

## <a name="bootstrap-the-resource-provider-deployment-powershell"></a>Inicialização a implantação de provedor de recurso PowerShell

1. Conectar-se a área de trabalho remota do Azure pilha VDC a clientVm.AzureStack.Local e entrar como azurestack\\azurestackuser.

2. [Baixe os binários MySQL RP](http://aka.ms/masmysqlrp) arquivo e extraia-o para d:\\MySQLRP.

3. Executar o d:\\MySQLRP\\Bootstrap.cmd arquivo como um administrador (azurestack\administrator).

    Isso abre o arquivo de Bootstrap.ps1 do PowerShell ISE.

4. Quando o windows PowerShell ISE completar o carregamento, clique no botão "executar" ou pressione F5.

    Duas guias principais carregará, cada um contendo todos os scripts e arquivos que você precisa implantar o seu provedor de recurso MySQL.

## <a name="prepare-prerequisites"></a>Preparar os pré-requisitos

Clique na guia de **Preparação pré-requisitos** para:

- Criar certificados necessários
- Baixar MySQL binários à pilha do Azure
- Carregar artefatos para uma conta de armazenamento na pilha do Azure
- Publicar itens da Galeria

### <a name="create-the-required-certificates"></a>Criar os certificados necessários
Esse script **New-SslCert.ps1** adiciona a \_. Certificado SSL de AzureStack.local.pfx a d:\\MySQLRP\\pré-requisitos\\BlobStorage\\pasta do contêiner. O certificado protege a comunicação entre o provedor de recursos e a instância local do Gerenciador de recursos do Azure.

1. Na guia principal **Pré-requisitos preparar** , clique na guia **New-SslCert.ps1** e executá-lo.

2. No prompt que aparece, digite uma senha PFX que protege a chave particular e **anote essa senha**. Você precisará-la mais tarde.

### <a name="download-mysql-binaries-to-your-azure-stack"></a>Baixar MySQL binários à pilha do Azure

1. Selecione a guia **MySqlServer.ps1 de Download** e executá-lo.
2. Quando solicitado, clique em **Sim** na caixa de diálogo Confirmar para aceitar o EULA.

    Este comando adiciona dois arquivos zip na pasta D:\MySql\Prerequisites\BlobStorage\Container.

### <a name="upload-all-artifacts-to-a-storage-account-on-azure-stack"></a>Carregar todos os artefatos para uma conta de armazenamento na pilha do Azure

1. Clique na guia de **Carregamento-Microsoft.MySql-RP.ps1** e executá-lo.

2. Na caixa de diálogo de solicitação do Windows PowerShell credencial, digite as credenciais de administrador de serviço do Azure pilha.

3. Quando for solicitado a ID do Azure Active Directory locatário, digite seu nome de domínio totalmente qualificado do Active Directory do Azure locatário: por exemplo, microsoftazurestack.onmicrosoft.com.

    Uma janela pop-up solicita credenciais.

    > [AZURE.TIP] Se o pop-up não aparecer, você que ainda não desligado IE enhanced segurança para habilitar o JavaScript neste computador e o usuário ou você ainda não aceitou cookies no IE. Consulte [Configurar etapas antes de implantar](#set-up-steps-before-you-deploy).

4. Digite suas credenciais de administrador de serviço de pilha do Azure e clique em **Entrar**.

### <a name="publish-gallery-items-for-later-resource-creation"></a>Publicar itens da Galeria para a criação de recurso posterior

Selecione a guia **Publicar GalleryPackages.ps1** e executá-lo. Esse script adiciona dois itens de marketplace Marketplace do portal do Azure pilha VDC que você pode usar para implantar recursos de banco de dados como itens de marketplace.

## <a name="deploy-the-mysql-resource-provider-vm"></a>Implantar o provedor de recursos de MySQL máquina virtual

Agora que você tiver preparado a VDC de pilha Azure com os certificados necessários e os itens de marketplace, você pode implantar um provedor de recursos do SQL Server. Clique na guia de **provedor de implantar MySQL** para:

   - Forneça os valores em um arquivo JSON que referencia o processo de implantação
   - Implantar o provedor de recursos
   - Atualizar o DNS local
   - Registrar o adaptador de provedor de recurso do SQL Server

### <a name="provide-values-in-the-json-file"></a>Forneça os valores no arquivo JSON

Clique em **Microsoft.MySqlprovider.Parameters.JSON**. Este arquivo tem parâmetros que o modelo do Gerenciador de recursos do Azure precisa implantar corretamente em pilha do Azure.

1. Preencha os parâmetros **vazio** no arquivo JSON:

    - Certifique-se de que você forneça o **adminusername** e **adminpassword** para o provedor de recursos de MySQL máquina virtual.

    - Certificar-se de que você fornecer a senha para o parâmetro de **SetupPfxPassword** que você fez uma anotação na etapa [Preparar prequisites](#prepare-prerequisites) .

    - Certifique-se de que você fornecer parâmetros **basicAuthUserName** e **basicAuthPassword** . **Tome nota desses valores.** Você precisará-las posteriormente para registrar o provedor de recursos.

2. Clique em **Salvar**.

### <a name="deploy-the-resource-provider"></a>Implantar o provedor de recursos

1. Clique na guia **implantar-Microsoft.Mysql-provider.PS1** e execute o script.
2. Digite seu nome de locatário do Azure Active Directory quando solicitado.
3. Na janela pop-up, envie suas credenciais de administrador de serviço do Azure pilha.

Toda a implantação pode levar entre 15 e 45 minutos em algumas POCs de pilha Azure altamente utilizados.

### <a name="update-the-local-dns"></a>Atualizar o DNS local

1. Clique na guia **Register-Microsoft.MySQL-fqdn.ps1** e execute o script.
2. Quando solicitado para ID do Azure Active Directory locatário, seu nome de domínio totalmente qualificado do Active Directory do Azure locatário de entrada: por exemplo, **microsoftazurestack.onmicrosoft.com**.

### <a name="register-the-sql-rp-resource-provider"></a>Registrar o provedor de recursos RP SQL

1. Clique na guia **Register-Microsoft.My-provider.ps1** e execute o script.

2. Quando solicitado para credenciais, use o que você anotou como os parâmetros **basicAuthUserName** e **basicAuthPassword** .

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Verifique se a implantação usando o Portal de pilha do Azure

1. Saia do ClientVM e entrar novamente como **AzureStack\User**.

2. Na área de trabalho, clique em **Portal do Azure pilha VDC** e entrar no portal do como administrador do serviço.

3. Verificar se a implantação foi bem-sucedida. Clique em **Procurar** &gt; **Grupos de recursos**, clique em grupo de recursos que você usou (o padrão é **MySQLRP**) e, em seguida, certifique-se de que a parte essentials da lâmina (metade superior) lê **implantação bem-sucedida**.


4. Verificar se o registro foi bem-sucedida. Clique em **Procurar** &gt; **provedores de recursos**e procure **MySQL Local**.

## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>Criar seu primeiro banco de dados MySQL para testar sua implantação

1. Entre no portal do Azure pilha VDC como administrador de serviço.

2. Clique na **+** botão &gt; **personalizado** &gt; **servidor MySQL & bancos de dados**.

3. Preencha o formulário com detalhes de banco de dados.

    **Tome nota do "nome do servidor" você digitar.** A cadeia de caracteres de conexões para seu banco de dados inclui o "nome do servidor" como parte do nome do usuário: por exemplo, ** "user@ <ServerName>"**. Você precisará de um nome de usuário neste formato de entrada quando você se conecta ao banco de dados: por exemplo, quando você implanta um site de MySQL usando o provedor de recursos do Site da Web do Azure


## <a name="next-steps"></a>Próximas etapas

Experimente outros [Serviços de PaaS](azure-stack-tools-paas-services.md) como o [provedor de recursos do SQL Server](azure-stack-sql-rp-deploy-short.md) e o [provedor de recursos de aplicativos Web](azure-stack-webapps-deploy.md).
