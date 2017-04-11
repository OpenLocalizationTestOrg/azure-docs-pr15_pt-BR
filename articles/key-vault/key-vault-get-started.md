<properties
    pageTitle="Introdução ao Azure chave cofre | Microsoft Azure"
    description="Use este tutorial para ajudá-lo a começar a usar o Cofre de chave do Azure para criar um contêiner robusto no Azure, armazenar e gerenciar chaves de criptografia e senhas no Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Introdução ao Azure chave cofre #
Azure Cofre de chave está disponível na maioria das regiões. Para obter mais informações, consulte a [chave do cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução  
Use este tutorial para ajudá-lo a começar a usar o Cofre de chave do Azure para criar um contêiner de proteção avançado (um cofre) no Azure, armazenar e gerenciar chaves de criptografia e senhas no Azure. Ele orienta você pelo processo de uso do PowerShell do Azure para criar um cofre que contém uma chave ou a senha que você pode usar com um aplicativo do Azure. Em seguida, mostra como um aplicativo pode usar essa chave ou senha.

**Tempo estimado para concluir:** 20 minutos

>[AZURE.NOTE]  Este tutorial não inclui instruções sobre como escrever o aplicativo do Azure que uma das etapas inclui, ou seja como autorizar um aplicativo para usar uma chave ou segredo no cofre de chave.
>
>Este tutorial usa Azure PowerShell. Para obter instruções de Interface de linha de comando de plataforma híbrida, consulte [Este tutorial equivalente](key-vault-manage-with-cli.md).

Para obter informações gerais sobre o Azure chave cofre, consulte [o que é Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter o seguinte:

- Uma assinatura do Microsoft Azure. Se você não tiver um, você pode inscrever para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- PowerShell Azure, **versão mínima do 1.1.0**. Para instalar o PowerShell do Azure e associá-lo a sua assinatura do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Se você já tiver instalado o Azure PowerShell e não souber a versão do console do PowerShell do Azure, digite `(Get-Module azure -ListAvailable).Version`. Quando você tiver Azure PowerShell versão 0.9.1 por meio de 0.9.8 instalado, você ainda pode usar este tutorial com pequenas alterações. Por exemplo, você deve usar o `Switch-AzureMode AzureResourceManager` comando e alguns dos comandos do Azure chave cofre foram alteradas. Para obter uma lista dos cmdlets chave cofre para versões 0.9.1 por meio de 0.9.8, consulte [Cmdlets de Cofre de chave do Azure](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Um aplicativo que será configurado para usar a chave ou a senha que você cria neste tutorial. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame que acompanha.


Este tutorial é projetado para iniciantes do PowerShell do Azure, mas ele pressupõe que você compreenda os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações, consulte [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada para qualquer cmdlet que você vê neste tutorial, use o cmdlet **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet **AzureRmAccount de Login** , digite:

    Get-Help Login-AzureRmAccount -Detailed

Você também pode ler os seguintes tutoriais para se familiarizar com o Gerenciador de recursos do Azure no Azure PowerShell:

- [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)
- [Usando o PowerShell Azure com o Gerenciador de recursos](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Conectar-se a suas assinaturas ##

Iniciar uma sessão do PowerShell do Azure e entrar em sua conta do Azure com o seguinte comando:  

    Login-AzureRmAccount 

Observe que, se você estiver usando uma instância específica de uso do Azure, por exemplo, o Azure governo, o ambiente parâmetro - com este comando. Por exemplo:`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Na janela do navegador pop-up, insira seu nome de usuário da conta do Azure e senha. PowerShell Azure obtém todas as assinaturas que são associados com essa conta e por padrão, usa primeiro.

Se você tiver várias assinaturas e deseja especificar um grupo específico a ser usado para Azure chave cofre, digite o seguinte para ver as assinaturas para sua conta:

    Get-AzureRmSubscription

Em seguida, para especificar a assinatura usar, digite:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obter mais informações sobre a configuração do PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Criar um novo grupo de recursos ##

Quando você usa o Gerenciador de recursos do Azure, todos os recursos relacionados são criados dentro de um grupo de recursos. Vamos criar um novo grupo de recursos denominado **ContosoResourceGroup** para este tutorial:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Criar um cofre chave ##

Use o cmdlet [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) para criar um cofre chave. Esse cmdlet tem três parâmetros obrigatórios: um **nome de grupo de recursos**, um **nome de chave cofre**e a **localização geográfica**.

Por exemplo, se você usar o nome do cofre da **ContosoKeyVault**, o nome do grupo de recursos de **ContosoResourceGroup**e o local do **Leste Asiático**, digite:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

A saída desse cmdlet mostra propriedades do cofre chave que você acabou de criar. As duas propriedades mais importantes são:

- **Nome do cofre**: no exemplo, isso é **ContosoKeyVault**. Você usará esse nome para outros cmdlets de chave cofre.
- **URI do cofre**: no exemplo, isso é https://contosokeyvault.vault.azure.net/. Aplicativos que usam seu cofre por meio de sua API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a realizar operações na chave cofre. Ainda, ninguém está.

>[AZURE.NOTE]  Se você vir o erro **a assinatura não está registrada para usar namespace 'Microsoft.KeyVault'** quando você tenta criar seu novo cofre chave, execute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` e execute o comando New-AzureRmKeyVault novamente. Para obter mais informações, consulte [AzureRmResourceProvider de registro](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Adicionar uma chave ou segredo ao Cofre de chave ##

Se você quiser Cofre de chave do Azure para criar uma chave de software protegido para você, use o cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) e digite o seguinte:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

No entanto, se você tiver uma chave de software protegido existente em um. Arquivo PFX salvado em sua unidade C:\ em um arquivo denominado softkey.pfx que você deseja carregar no Azure chave cofre, digite o seguinte para definir a variável **securepfxpwd** uma senha de **123** para o. Arquivo PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Digite o seguinte para importar a chave da. Arquivo PFX, que protege a chave pelo software no serviço Cofre de chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Agora você pode referenciar essa chave que você criou ou carregado Cofre de chave do Azure, usando seu URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão atual e usar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter a versão específica.  

Para exibir o URI para essa chave, digite:

    $Key.key.kid

Para adicionar um segredo ao cofre, que é uma senha chamada SQLPassword e tem o valor de Pa$ $w0rd Cofre de chave do Azure, primeiro converter o valor de Pa$ $w0rd em uma cadeia de caracteres segura digitando o seguinte:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Em seguida, digite o seguinte:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Agora você pode referenciar essa senha que você adicionou ao Azure chave cofre, usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual e usar **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter a versão específica.

Para exibir o URI para esse segredo, digite:

    $secret.Id

Vamos ver a tecla ou segredo que você acabou de criar:

- Para exibir sua chave, digite:`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Para exibir seu segredo, digite:`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Agora, sua chave cofre e chave ou segredo está pronto para aplicativos para usar. Você deve autorizar aplicativos usá-las.  

## <a id="register"></a>Registrar um aplicativo com o Active Directory do Azure ##

Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ele não é específico de Cofre de chave do Azure, mas é incluído aqui integridade.


>[AZURE.IMPORTANT] Para concluir o tutorial, sua conta, o cofre e o aplicativo que você irá registrar nesta etapa devem estar todos na mesma pasta Azure.

Aplicativos que usam um cofre chave devem autenticar usando um token do Azure Active Directory. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo em seus Azure Active Directory. No final do registro, o proprietário do aplicativo obtém os seguintes valores:


- Uma **ID de aplicativo** (também conhecido como uma ID de cliente) e a **chave de autenticação** (também conhecido como o segredo compartilhado). O aplicativo deve apresentar os dois esses valores ao Azure Active Directory, para obter um token. Como o aplicativo está configurado para fazer isso depende do aplicativo. Para o aplicativo de amostra do Cofre de chave, o proprietário do aplicativo define esses valores no arquivo App.

Para registrar o aplicativo do Azure Active Directory:

1. Entre portal do Azure clássico.
2. À esquerda, clique em **Active Directory**e selecione o diretório em que você irá registrar seu aplicativo. <br> <br> **Observação:** Você deve selecionar o mesmo diretório que contém a assinatura Azure com os quais você criou seu cofre chave. Se você não souber qual diretório isto é, clique em **configurações**, identificar a assinatura com o qual você criou seu cofre chave e observe o nome da pasta exibido na última coluna.

3. Clique em **aplicativos**. Se nenhuma aplicativos foram adicionados ao seu diretório, esta página mostra apenas o link **Adicionar um aplicativo** . Clique no link ou como alternativa, você pode clicar em **Adicionar** na barra de comando.
4.  No Assistente de **Aplicativo Adicionar** , sobre o **o que você deseja fazer?** página, clique em **Adicionar um aplicativo minha organização está desenvolvendo**.
5.  Na página **Conte-nos sobre seu aplicativo** , especifique um nome para seu aplicativo e, em seguida, selecione **WEB APPLICATION e/ou WEB API** (padrão). Clique no ícone **próximo** .
6.  Na página **Propriedades de aplicativo** , especifique a **URL de SIGN-ON** e **URI de ID de aplicativo** para o aplicativo web. Se seu aplicativo não tiver esses valores, você pode torná-los para esta etapa (por exemplo, você pode especificar http://test1.contoso.com para ambas as caixas). Não importa se existem estes sites. O que é importante é que o aplicativo de URI de identificação para cada aplicativo é diferente para cada aplicativo em seu diretório. Diretório usa essa cadeia de caracteres para identificar seu aplicativo.
7.  Clique no ícone de **concluído** para salvar suas alterações no assistente.
8.  Na página de **Início rápido** , clique em **Configurar**.
9.  Role até a seção de **chaves** , selecione a duração e clique em **Salvar**. A página atualiza e agora mostra um valor de chave. Você deve configurar o seu aplicativo com esse valor de chave e o valor de **ID do cliente** . (As instruções para essa configuração são específicos do aplicativo).
10. Copie o valor de ID do cliente nesta página, que você usará na próxima etapa para definir permissões em seu cofre.

## <a id="authorize"></a>Autorize o aplicativo para usar a tecla ou segredo ##

Para autorizar o aplicativo para acessar a chave ou segredo no cofre, use o cmdlet  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) .

Por exemplo, se o nome do seu cofre for **ContosoKeyVault** e o aplicativo que você deseja autorizar tem uma identificação de cliente de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo para descriptografar e entrar com chaves em seu cofre, execute o seguinte:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se você quiser autorizar aplicativo mesmo ler segredos no seu cofre, execute o seguinte:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Se você quiser usar um módulo de segurança de hardware (HSM) ##

Para assurance adicionado, você pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca deixe o limite HSM. Os HSMs são FIPS 140-2 nível 2 validados. Se esse requisito não se aplica a você, pule esta seção e vá para [Excluir o cofre chave e associados chaves e senhas](#delete).

Para criar estas teclas protegidos por HSM, você deve usar o [nível de serviço do Azure chave cofre Premium para dar suporte a chaves protegidas por HSM](https://azure.microsoft.com/pricing/free-trial/). Além disso, observe que essa funcionalidade não está disponível para a China do Azure.


Quando você cria o cofre chave, adicione o parâmetro **- SKU** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Você pode adicionar chaves protegido por software (conforme mostrado anteriormente) e protegidos por HSM a chave cofre. Para criar uma chave de protegidos por HSM, defina o **-destino** parâmetro para 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Você pode usar o comando a seguir para importar uma chave de um. Arquivo PFX em seu computador. Este comando importa a chave para HSMs no serviço Cofre de chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


O próximo comando importa um "levar sua própria chave" pacote (BYOK). Este cenário permite que você gerar sua chave em seu local HSM e transferi-la para HSMs no serviço de chave cofre, sem a chave deixando o limite HSM:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte [como gerar e transferência protegidos por HSM chaves Azure chave cofre](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Excluir o cofre chave e chaves associadas e segredos ##

Se você não precisa mais Cofre de chave e a tecla ou segredo que ele contém, você pode excluir o cofre chave usando o cmdlet [Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) :

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Ou, você pode excluir um grupo de recurso Azure inteiro, que inclui o cofre chave e outros recursos que você incluiu no grupo:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Outros Cmdlets do PowerShell Azure ##

Outros comandos que podem ser úteis para gerenciar Cofre de chave do Azure:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Esse comando obtém uma exibição tabular de todas as chaves e propriedades selecionadas.
- `$Keys[0]`: Este comando exibe uma lista completa das propriedades da chave especificada
- `Get-AzureKeyVaultSecret`: Este comando mostra uma exibição tabular de todos os nomes de secreta e propriedades selecionadas.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exemplo como remover uma chave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exemplo como remover um segredo específico.


## <a id="next"></a>Próximas etapas ##

Para um tutorial acompanhamento que usa o Azure chave cofre em um aplicativo web, consulte [Usar Azure chave Cofre de um aplicativo Web](key-vault-use-from-web-application.md).

Para ver como seu cofre chave está sendo usado, consulte [O log de Cofre de chave do Azure](key-vault-logging.md).

Para uma lista de cmdlets do PowerShell do Azure mais recente do Cofre de chave do Azure, consulte [Cmdlets de Cofre de chave do Azure](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Para programação referências, consulte [Guia do desenvolvedor do Azure chave cofre](key-vault-developers-guide.md).
