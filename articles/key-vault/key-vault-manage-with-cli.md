<properties
    pageTitle="Gerenciar cofre de chave usando CLI | Microsoft Azure"
    description="Use este tutorial para automatizar tarefas comuns no cofre de chave usando a CLI"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Gerenciar cofre de chave usando CLI #
Azure Cofre de chave está disponível na maioria das regiões. Para obter mais informações, consulte a [chave do cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução  
Use este tutorial para ajudá-lo a começar a usar o Cofre de chave do Azure para criar um contêiner de proteção avançado (um cofre) no Azure, armazenar e gerenciar chaves de criptografia e senhas no Azure. Ele orienta você pelo processo de usando a Interface de linha de comando de plataforma híbrida do Azure para criar um cofre que contém uma chave ou a senha que você pode usar com um aplicativo do Azure. Em seguida, mostra como um aplicativo, em seguida, pode usar essa chave ou senha.

**Tempo estimado para concluir:** 20 minutos

>[AZURE.NOTE]  Este tutorial não inclui instruções sobre como escrever o aplicativo do Azure que uma das etapas inclui, que mostra como autorizar um aplicativo para usar uma chave ou segredo no cofre de chave.
>
>Atualmente, você não pode configurar Azure chave cofre no portal do Azure. Em vez disso, use estas instruções de Interface de linha de comando entre plataformas. Ou, para obter instruções do PowerShell do Azure, consulte [Este tutorial equivalente](key-vault-get-started.md).

Para obter informações gerais sobre o Azure chave cofre, consulte [o que é Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você deve ter o seguinte:

- Uma assinatura do Microsoft Azure. Se você não tiver um, você pode inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
- Interface de linha de comando versão 0.9.1 ou posterior. Para instalar a versão mais recente e se conectar à sua assinatura do Azure, consulte [instalar e configurar a Interface de linha do Azure entre plataformas](../xplat-cli-install.md).
- Um aplicativo que será configurado para usar a chave ou a senha que você cria neste tutorial. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame que acompanha.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtendo ajuda com Interface de linha de comando de plataforma híbrida do Azure

Este tutorial supõe que você esteja familiarizado com a interface de linha (Bash, Terminal, prompt de comando)

-Parâmetro ajuda ou -h pode ser usado para exibir ajuda para comandos específicos. Como alternativa, o azure ajudar [comando] [Opções] formatação também pode ser usada para retornar as mesmas informações. Por exemplo, os seguintes comandos todos retornam as mesmas informações:

    azure account set --help

    azure account set -h

    azure help account set

Se estiver em dúvida sobre os parâmetros necessários por um comando, consulte a Ajuda usando – Ajuda, -h ou azure [comando].

Você também pode ler os seguintes tutoriais para se familiarizar com o Gerenciador de recursos do Azure na Interface de linha de comando do Azure entre plataformas:

- [Como instalar e configurar a Interface de linha de comando de plataforma híbrida do Azure](../xplat-cli-install.md)
- [Usando a Interface de linha de comando do Azure entre plataformas com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Conectar-se a suas assinaturas

Para efetuar login usando uma conta organizacional, use o seguinte comando:

    azure login -u username -p password

ou se você quiser fazer logon no digitando interativamente

    azure login

>[AZURE.NOTE]  O método login só funciona com uma conta organizacional. Uma conta organizacional é um usuário que é gerenciado pela sua organização e definido no locatário do Azure Active Directory da sua organização.


Se você não tem uma conta organizacional e estiver usando uma conta da Microsoft para efetuar logon em sua assinatura do Azure, você pode facilmente criar um usando as seguintes etapas.

1.  Faça login Login para o [Portal de gerenciamento do Azure](https://manage.windowsazure.com/)e clique em Active Directory.
2.  Se a pasta não existir, selecione Criar seu diretório e forneça as informações solicitadas.
3.  Selecione seu diretório e adicionar um novo usuário. Este novo usuário é uma conta organizacional. Durante a criação do usuário, você será fornecido com um endereço de email para o usuário e uma senha temporária. Salve essas informações como ele é usado em outra etapa.
4.  No portal do, selecione configurações e, em seguida, selecione administradores. Selecione Adicionar e adicionar o novo usuário como um administrador de colegas. Isso permite que a conta organizacional gerenciar sua assinatura Azure.
5.  Finalmente, faça logout do portal do Azure e, em seguida, faça logon novamente usando a nova conta organizacional. Se esta for a primeira vez registro em log com essa conta, você será solicitado a alterar a senha.

Para obter mais informações sobre como usar uma conta organizacional com o Microsoft Azure, consulte [Inscreva-se no Microsoft Azure como uma organização](../active-directory/sign-up-organization.md).

Se você tiver várias assinaturas e deseja especificar um grupo específico a ser usado para Azure chave cofre, digite o seguinte para ver as assinaturas para sua conta:

    azure account list

Em seguida, para especificar a assinatura usar, digite:

    azure account set <subscription name>

Para obter mais informações sobre a configuração da Interface de linha de comando de plataforma híbrida do Azure, veja [como instalar e configurar Interface de linha de comando do Azure entre plataformas](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Alternar usando o Gerenciador de recursos do Azure

O cofre chave requer o Gerenciador de recursos do Azure, digite o seguinte para alternar para modo de Gerenciador de recursos do Azure:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos

Ao usar o Gerenciador de recursos do Azure, todos os recursos relacionados são criados dentro de um grupo de recursos. Podemos criará um novo grupo de recursos 'ContosoResourceGroup' para este tutorial.

    azure group create 'ContosoResourceGroup' 'East Asia'

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é o local. Para local, use o comando `azure location list` para identificar como especificar um local alternativo neste exemplo. Se precisar de mais informações, digite:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registrar o provedor de recursos do Cofre de chave
Certifique-se de que provedor de recursos do Cofre de chave é registrado em sua assinatura:

`azure provider register Microsoft.KeyVault`

Isso só precisa ser feito uma vez por assinatura.


## <a name="create-a-key-vault"></a>Criar um cofre chave

Use o `azure keyvault create` comando para criar um chave cofre. Esse script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome de chave cofre e a localização geográfica.

Por exemplo, se você usar o nome do cofre da ContosoKeyVault, o nome do grupo de recursos de ContosoResourceGroup e o local do Leste Asiático, digite:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

A saída desse comando mostra as propriedades do cofre chave que você acabou de criar. As duas propriedades mais importantes são:

- **Nome**: no exemplo é ContosoKeyVault. Você usará esse nome para outros cmdlets de chave cofre.
- **vaultUri**: no exemplo é https://contosokeyvault.vault.azure.net. Aplicativos que usam seu cofre por meio de sua API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a realizar operações na chave cofre. Ainda, ninguém está.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou segredo ao Cofre de chave

Se você quiser Cofre de chave do Azure para criar uma chave de software protegido para você, use o `azure key create` comando e digite o seguinte:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

No entanto, se você tiver uma chave existente em um arquivo de .pem salvo como um arquivo local em um arquivo denominado softkey.pem que você deseja carregar no Azure chave cofre, digite o seguinte para importar a chave da. Arquivo PEM, que protege a chave pelo software no serviço Cofre de chave:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Agora você pode fazer referência a chave que você criou ou carregado Cofre de chave do Azure, usando seu URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão atual e usar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter a versão específica.

Para adicionar um segredo ao cofre, que é uma senha chamada SQLPassword e que tenha o valor de Pa$ $w0rd ao Azure chave cofre, digite o seguinte:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Agora você pode referenciar essa senha que você adicionou ao Azure chave cofre, usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual e usar **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter a versão específica.

Vamos ver a tecla ou segredo que você acabou de criar:

- Para exibir sua chave, digite:`azure keyvault key list --vault-name 'ContosoKeyVault'`
- Para exibir seu segredo, digite:`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Registrar um aplicativo com o Active Directory do Azure

Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ele não é específico de Cofre de chave do Azure, mas é incluído aqui, integridade.


>[AZURE.IMPORTANT] Para concluir o tutorial, sua conta, o cofre e o aplicativo que você irá registrar nesta etapa devem estar todos na mesma pasta Azure.

Aplicativos que usam um cofre chave devem autenticar usando um token do Azure Active Directory. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo em seus Azure Active Directory. No final do registro, o proprietário do aplicativo obtém os seguintes valores:


- Uma **ID de aplicativo** (também conhecido como uma ID de cliente) e a **chave de autenticação** (também conhecido como o segredo compartilhado). O aplicativo deve apresentar ambos esses valores ao Azure Active Directory, para obter um token. Como o aplicativo está configurado para fazer isso depende do aplicativo. Para o aplicativo de amostra do Cofre de chave, o proprietário do aplicativo define esses valores no arquivo App.



Para registrar o aplicativo do Azure Active Directory:

1. Entre portal do Azure.
2. À esquerda, clique em **Active Directory**e selecione o diretório em que você irá registrar seu aplicativo. <br> <br> Observação: Você deve selecionar o mesmo diretório que contém a assinatura Azure com os quais você criou seu cofre chave. Se você não souber qual diretório isto é, clique em **configurações**, identificar a assinatura com o qual você criou seu cofre chave e observe o nome da pasta exibido na última coluna.

3. Clique em **aplicativos**. Se nenhuma aplicativos foram adicionados ao seu diretório, esta página mostrará somente o link **Adicionar um aplicativo** . Clique no link ou como alternativa, você pode clicar em **Adicionar** na barra de comando.
4.  No Assistente de **Aplicativo Adicionar** , sobre o **o que você deseja fazer?** página, clique em **Adicionar um aplicativo minha organização está desenvolvendo**.
5.  Na página **Conte-nos sobre seu aplicativo** , especifique um nome para seu aplicativo e selecione **WEB APPLICATION e/ou WEB API** (padrão). Clique no ícone próximo.
6.  Na página **Propriedades de aplicativo** , especifique a **URL de SIGN-ON** e **URI de ID de aplicativo** para o aplicativo web. Se seu aplicativo não tiver esses valores, você pode torná-los para esta etapa (por exemplo, você pode especificar http://test1.contoso.com para ambas as caixas). Não importa se esses sites existirem; o que é importante é que o aplicativo de URI de identificação para cada aplicativo é diferente para cada aplicativo em seu diretório. Diretório usa essa cadeia de caracteres para identificar seu aplicativo.
7.  Clique no ícone concluído para salvar suas alterações no assistente.
8.  Na página de início rápido, clique em **Configurar**.
9.  Role até a seção de **chaves** , selecione a duração e clique em **Salvar**. A página atualiza e agora mostra um valor de chave. Você deve configurar o seu aplicativo com esse valor de chave e o valor de **ID do cliente** . (As instruções para essa configuração será específicos do aplicativo).
10. Copie o valor de ID do cliente nesta página, que você usará na próxima etapa para definir permissões em seu cofre.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorize o aplicativo para usar a tecla ou segredo

Para autorizar o aplicativo para acessar a chave ou segredo no cofre, use o `azure keyvault set-policy` comando.

Por exemplo, se o nome do cofre é ContosoKeyVault e o aplicativo que você deseja autorizar tem uma ID de cliente do 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e você deseja autorizar o aplicativo para descriptografar e entrar com chaves em seu cofre, em seguida, execute o seguinte:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Se você estiver executando no prompt de comando do Windows, você deve substituir aspas simples com aspas duplas e também escape as aspas duplas internas. Por exemplo: "[\"descriptografar\",\"entrada\"]".

Se você quiser autorizar aplicativo mesmo ler segredos no seu cofre, execute o seguinte:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se você quiser usar um módulo de segurança de hardware (HSM) ##

Para assurance adicionado, você pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca deixe o limite HSM. Os HSMs são FIPS 140-2 nível 2 validados. Se esse requisito não se aplica a você, pule esta seção e vá para [Excluir o cofre chave e associados chaves e senhas](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar estas teclas protegidos por HSM, você deve ter uma assinatura de cofre que suporta chaves protegidas por HSM.

Quando você cria o keyvault, adicione o parâmetro 'sku':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Você pode adicionar chaves protegido por software (conforme mostrado anteriormente) e protegidos por HSM para este cofre. Para criar uma chave de protegidos por HSM, defina o parâmetro de destino para 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Você pode usar o comando a seguir para importar uma chave de um arquivo de .pem em seu computador. Este comando importa a chave para HSMs no serviço Cofre de chave:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

O próximo comando importa um "levar sua própria chave" pacote (BYOK). Isso permite que você gerar sua chave em seu local HSM e transferi-la para HSMs no serviço de chave cofre, sem a chave deixando o limite HSM:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte [como usar as teclas de HSM-Protected com o Azure chave cofre](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Excluir o cofre chave e chaves associadas e segredos

Se você não precisa mais Cofre de chave e a tecla ou segredo que ele contém, você pode excluir o cofre chave usando o comando Excluir keyvault azure:

    azure keyvault delete --vault-name 'ContosoKeyVault'

Ou, você pode excluir um grupo de recurso Azure inteiro, que inclui o cofre chave e outros recursos que você incluiu no grupo:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos de Interface de linha de comando do Azure entre plataformas

Outros comandos que você talvez seja útil para gerenciar Cofre de chave do Azure.

Este comando apresenta uma exibição tabular de todas as chaves e propriedades selecionadas:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Este comando exibe uma lista completa das propriedades da chave especificada:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Este comando apresenta uma exibição tabular de todos os nomes de secreta e propriedades selecionadas:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Aqui está um exemplo de como remover uma chave específica:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Aqui está um exemplo de como remover um segredo específico:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Próximas etapas

Para programação referências, consulte [Guia do desenvolvedor do Azure chave cofre](key-vault-developers-guide.md).
