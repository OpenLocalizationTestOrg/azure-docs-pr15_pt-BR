<properties
    pageTitle="Como instalar e configurar o PowerShell do Azure"
    description="Saiba como instalar e configurar o Azure PowerShell."
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>Como instalar e configurar o PowerShell do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">CLI Azure</a></div>

##<a name="what-is-azure-powershell"></a>O que é Azure PowerShell?
PowerShell Azure é um conjunto de módulos que fornecem cmdlets para gerenciar Azure com o Windows PowerShell. Você pode usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços fornecidos por meio da plataforma Windows Azure. Na maioria dos casos, os cmdlets pode ser usados para as mesmas tarefas que o Portal do Azure, como criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e aplicativos web.

## <a name="how-versioning-works"></a>Como funciona o controle de versão

PowerShell Azure usa o controle de versão semântico, o que significa que se A versão > versão B, em seguida, versão A tem as APIs mais atualizadas. Além disso, ele significa que alterações na média de versão principal uma quebra alteram nos cmdlets de um ou mais.  Portanto, por exemplo, versão 1.7.0 é para endereçar uma alteração significativa nas versões 1. x do PowerShell do Azure.

Para obter mais informações sobre práticas recomendadas de controle de versão semântico no Azure PowerShell, consulte a especificação de controle de versão semântico em: http://semver.org
 
Para obter as APIs mais recentes, você deve usar a versão 2. x. Mas se você tiver scripts escritos em relação a versão 1. x e você não quer absorvê as alterações recentes na versão 2. x descrito a 2. x [Notas](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md), e em seguida, você deve instalar 1.7.0.

Uma incompatibilidade de versão pode resultar se a versão mais recente do módulo perfil estiver instalada, e uma versão anterior de um módulo que depende dele subsequentemente é carregada. A maneira mais simples de resolver isso é instalar a partir o arquivo. msi mais recente. O arquivo. msi limpa automaticamente versões mais antigas dos módulos.
 
###<a name="installing-module-versions-side-by-side"></a>Instalar o módulo versões lado a lado

Versão 2.1.0 (e versão 1.2.6 para AzureStack) são as primeiras versões de módulo projetadas para ser instalado e usados lado a lado. Como Azure PowerShell usa módulos binários, você deve abrir uma nova janela do PowerShell e usar o **Módulo de importação** para importar uma versão específica de cmdlets do AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Versões antes 2.1.0 (diferente de 1.2.6) não funcionam bem lado a lado com outras versões de módulo do PowerShell do Azure. Ao carregar uma versão anterior do Azure PowerShell módulos usando um comando como o exibido acima, versões incompatíveis do módulo **AzureRM.Profile** serão carregadas, resultando nos cmdlets solicitando que você faça logon em sempre que você executar um cmdlet, mesmo após você ter conectado.

A maneira mais fácil para resolver que isso é para instalar o PowerShell Azure mais recente do WebPI feed ou. msi – isso remove as versões anteriores dos módulos instalados da Galeria. 

Observe que módulos do Azure e AzureRM tem dependências em comum, portanto, se você usar ambos os módulos, quando a atualização de um, você deve atualizar ambos. Versões anteriores do módulo Azure têm o mesmo problema com módulo de lado a lado Carregando que versões anteriores do módulo AzureRM têm.

<a id="Install"></a>
## <a name="step-1-install"></a>Etapa 1: instalar

A seguir estão os dois métodos pelos quais você pode instalar o Azure PowerShell. Você pode instalá-lo da Galeria de PowerShell ou o WebPI.

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>Instalar o PowerShell Azure da Galeria do PowerShell

A melhor maneira é usar o PowerShell Galeria. É necessário o módulo PowerShellGet usar a Galeria de PowerShell. Essa opção estará disponível aqui: [PowerShellGallery.com](https://www.powershellgallery.com/)

Instalar o Azure PowerShell 1.3.0 ou maior da Galeria de PowerShell usando um ambiente de script integrado do PowerShell (ISE) ou de Windows PowerShell prompt usando os seguintes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>Mais informações sobre esses comandos

- **Módulo de instalação AzureRM** instala um módulo de rollup para os cmdlets do Gerenciador de recursos do Azure. O módulo AzureRM depende 
- um intervalo de versão específica para cada módulo do Gerenciador de recursos do Azure. O intervalo de versão incluída garante que nenhuma alteração de módulo ruptura pode ser incluídas ao instalar módulos AzureRM com a mesma versão principal. Quando você instala o módulo AzureRM, qualquer módulo do Gerenciador de recursos do Azure que anteriormente não foi instalado será baixado e instalado da Galeria do PowerShell. Para obter mais informações sobre o controle de versão semântico usada pelos módulos do Azure PowerShell, consulte [semver.org](http://semver.org). 
- **Módulo de instalação Azure** instala o módulo Azure. Este módulo é o módulo de gerenciamento de serviço do Azure PowerShell 0.9.x. Isso deve ter não principais alterações e ser intercambiáveis para a versão anterior do módulo Azure.

###<a name="installing-azure-powershell-from-webpi"></a>Instalando o PowerShell Azure partir WebPI

Instalar o Azure PowerShell 1.0 e maior de WebPI é o mesmo como ela estava para 0.9.x. Baixe [O PowerShell do Azure](http://aka.ms/webpi-azps) e iniciar a instalação. Se você tiver o Azure PowerShell 0.9.x instalado, versão 0.9.x será desinstalado como parte da atualização. Se você instalou módulos do PowerShell do Azure da Galeria do PowerShell, o instalador remove automaticamente os módulos antes da instalação para garantir um ambiente consistente do PowerShell do Azure.

> [AZURE.NOTE] Se você já tiver instalado o Azure módulos da Galeria do PowerShell, o instalador removerá automaticamente-los. Isso evita confusão sobre quais versões do módulo você instalou e onde estão localizados. Módulos do PowerShell Galeria normalmente serão instalados na **%ProgramFiles%\WindowsPowerShell\Modules**. Em contraste, o instalador de WebPI irá instalar módulos do Azure * *% ProgramFiles (x86) %\Microsoft SDKs\Azure\PowerShell\**. Se ocorrer um erro durante a instalação, você pode remover manualmente o Azure* pastas em sua pasta de **%ProgramFiles%\WindowsPowerShell\Modules** e tente a instalação novamente.

Após a conclusão da instalação, seu ```$env:PSModulePath``` configuração deve incluir pastas que contêm os cmdlets do PowerShell do Azure.

> [AZURE.NOTE] Há um problema conhecido com o PowerShell **$env: PSModulePath** que podem ocorrer durante a instalação em WebPI. Se seu computador requer a reinicialização devido a atualizações do sistema ou outras instalações, ele pode causar atualizações **$env: PSModulePath** para não incluir o caminho onde o Azure PowerShell está instalado. Nesse caso, você poderá ver uma mensagem de 'cmdlet não reconhecido' ao tentar usar cmdlets do PowerShell do Azure após a instalação ou atualização. Nesse caso, a reinicialização da máquina deve corrigir o problema.

Se você receber uma mensagem como a seguinte ao tentar carregar ou executar cmdlets:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Isso pode ser corrigido reiniciar a máquina ou importando os cmdlets de C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ seguinte (onde XXXX é a versão do PowerShell instalado:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Etapa 2: Iniciar
Você pode executar os cmdlets do console do Windows PowerShell padrão ou do ambiente de script integrado de plano de PowerShell (ISE).
O método usado para abrir o console depende da versão do Windows você está executando:

- Em um computador executando pelo menos o Windows 8 ou o Windows Server 2012, você pode usar a pesquisa interna. Na tela **Iniciar** , comece a digitar power. Isso retorna uma lista com escopo de aplicativos que inclui o Windows PowerShell. Para abrir o console, clique em qualquer aplicativo. (Para fixar o aplicativo para a tela **Iniciar** , clique com botão direito no ícone.)

- Em um computador executando uma versão anterior ao Windows 8 ou o Windows Server 2012, use o **menu Iniciar**. No menu **Iniciar** , **Todos os**programas, clique em **Acessórios**, clique na pasta do **Windows PowerShell** e clique em **Windows PowerShell**.

Você também pode executar o **Windows PowerShell ISE** para usar atalhos de teclado e itens de menu para executar muitas das mesmas tarefas que você deve executar no console do Windows PowerShell. Para usar o ISE, no console do Windows PowerShell, Cmd.exe, ou na caixa **Executar** , digite **powershell_ise.exe**.

###<a name="commands-to-help-you-get-started"></a>Comandos para ajudá-lo a começar

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Etapa 3: conectar-se
Os cmdlets necessário sua assinatura para que possam gerenciar seus serviços. Você pode adquirir uma assinatura do Azure se você ainda não tiver um. Para obter instruções, consulte [como comprar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Tipo **AzureRmAccount de logon**

2. Digite o endereço de email e a senha associada a sua conta. Azure autentica salva as informações de credencial e fecha a janela.

-OU-

Entrar em seu trabalho ou escola conta:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Se você tiver mais de um locatário associado a sua conta organizacional, especifique o parâmetro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Esse registro não interativo no método só funciona com uma conta corporativa ou escolar. Uma conta corporativa ou de estudante é um usuário que é gerenciado pelo seu trabalho ou escola e definido na instância do Active Directory do Azure para seu trabalho ou escola. Se você não tem uma conta corporativa ou escolar e estiver usando uma conta da Microsoft para efetuar logon em sua assinatura do Azure, você pode facilmente criar um usando as seguintes etapas.

> 1. Faça logon [portal clássico Azure](https://manage.windowsazure.com)e clique em **Active Directory**.

> 2. Se a pasta não existir, selecione **criar seu diretório** e forneça as informações solicitadas.

> 3. Selecione seu diretório e adicionar um novo usuário. Este novo usuário pode entrar usando uma conta corporativa ou escolar. Durante a criação do usuário, você será fornecido com um endereço de email para o usuário e uma senha temporária. Salve essas informações, como ele é usado na etapa 5 abaixo.

> 4. Azure clássico no portal do, selecione **configurações** e selecione **administradores**. Selecione **Adicionar**e adicionar o novo usuário como um administrador de colegas. Isso permite que a conta corporativa ou de estudante gerenciar sua assinatura Azure.

> 5. Por fim, faça logout do portal clássico do Azure e, em seguida, faça logon novamente usando o trabalho ou escola conta. Se esta for a primeira vez registro em log com essa conta, você será solicitado a alterar a senha.

> Para obter mais informações sobre como se inscrever para o Microsoft Azure com uma conta corporativa ou escolar, consulte [Inscreva-se no Microsoft Azure como uma organização](./active-directory/sign-up-organization.md).

> Para obter mais informações sobre o gerenciamento de autenticação e de assinatura no Azure, consulte [Gerenciar contas, assinaturas e funções administrativas](http://go.microsoft.com/fwlink/?LinkId=324796).

### <a name="view-account-and-subscription-details"></a>Exibir detalhes de conta e assinatura

Você pode ter várias contas e assinaturas disponíveis para uso pelo PowerShell do Azure. Você pode adicionar várias contas executando **AzureRmAccount de adicionar** mais de uma vez.

Para exibir as contas Azure disponíveis, digite **Get-AzureAccount**.

Para exibir suas assinaturas Azure, digite **Get-AzureRmSubscription**.

##<a id="Help"></a>Obtendo ajuda##

Estes recursos fornecem ajuda para os cmdlets específicos:


-   Console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a este sistema. 

- Para obter ajuda da comunidade, tente estas fóruns populares:

 - [Fórum Azure no MSDN]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [StackOverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>Saiba mais


Consulte os seguintes recursos para saber mais sobre como usar os cmdlets:

Para obter instruções básicas sobre como usar o Windows PowerShell, consulte [Usando o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para informações de referência sobre os cmdlets, consulte [Referência de cmdlets do Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para scripts de exemplo e instruções para ajudá-lo a aprender a usar o script para gerenciar o Azure, consulte o [Centro de Script](http://go.microsoft.com/fwlink/p/?LinkId=321940).

