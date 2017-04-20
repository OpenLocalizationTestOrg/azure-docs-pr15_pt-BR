<properties
   pageTitle="Guia do desenvolvedor do armazenamento de chave | Microsoft Azure"
   description="Os desenvolvedores podem usar o Azure chave Vault para gerenciar chaves criptográficas dentro do ambiente do Microsoft Azure. "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor do Azure Vault principais
Usando a chave Vault, você será capaz de acessar informações confidenciais em seus aplicativos de forma segura, de modo que:

- Chaves e segredos serão protegidos sem precisar escrever o código sozinho e você poderá facilmente usá-los a partir de seus aplicativos.
- Você poderá ter seus próprios de clientes e gerenciar suas próprias chaves, portanto você pode enfocam fornecendo os principais recursos do software. Dessa forma os seus aplicativos não serão proprietário a responsabilidade ou responsabilidade potencial para as teclas de locatário de seus clientes e segredos.
- Seu aplicativo pode usar as teclas para assinatura e criptografia ainda mantém o gerenciamento de chaves externa do seu aplicativo, de tal modo que a solução é adequada para um aplicativo que seja distribuído geograficamente.

- Com a versão de setembro de 2016 do Vault chave, seus aplicativos agora podem fazer uso de chave Vault certificados. Para obter mais informações, consulte o artigo **sobre chaves, segredos e certificados** na [referência do REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Para obter informações gerais no Azure chave Vault, consulte [o que é a chave Vault](key-vault-whatis.md).

## <a name="videos"></a>Vídeos
Este vídeo mostra como criar sua própria chave vault e como usá-lo a partir do aplicativo de amostra 'Hello chave Vault'.

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Links para recursos mencionados no vídeo:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de amostra do Azure Vault principais](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para saber mais pode acompanhe a [Chave Vault Blog](http://aka.ms/kvblog) e participar de [Chave Vault fórum](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando a chaves compartimentos

Antes de trabalhar com o Windows Azure chave Vault em seu código, você pode criar e gerenciar compartimentos por meio do REST, modelos de gerente de recursos, PowerShell ou CLI, conforme descrito nos artigos a seguir:

- [Criar e gerenciar a chaves compartimentos com REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Criar e gerenciar a chaves compartimentos com o PowerShell](key-vault-get-started.md)
- [Criar e gerenciar a chaves compartimentos com CLI](key-vault-manage-with-cli.md)
- [Criar um armazenamento de chave e adicionar um segredo por meio de um modelo do Gerenciador de recursos do Windows Azure](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operações contra compartimentos principais são autenticadas através do AAD e autorizadas através da política de acesso de chave do Vault próprios, definida por vault.

## <a name="coding-with-key-vault"></a>Codificação com chave Vault

O sistema de gerenciamento de chave Vault para programadores consiste em várias interfaces, com o restante como base, [Chave Vault referência à API REST](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Você pode, sujeitos a autorização bem-sucedida, faça o seguinte:

- Gerenciar chaves de criptografia usando [criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações

- Gerenciar segredos usando [obter](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Atualizar](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Excluir](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações

- Use chaves criptográficas com [sinal](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [criptografar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/operações[descriptografar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Os seguintes SDKs estão disponíveis para trabalhar com chave Vault:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node. js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentação do SDK do .NET](https://msdn.microsoft.com/library/mt765854.aspx)|[Documentação do SDK do Node. js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacote do SDK do .NET em Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Pacote Node. js SDK](https://www.npmjs.com/package/azure-keyvault)|

Para obter mais informações sobre a versão 2. x do SDK do .NET, consulte as [notas de versão](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Código de exemplo
Para exemplos completos usando a chave Vault com seus aplicativos, consulte:

- Aplicativo de exemplo .NET *HelloKeyVault* e um exemplo de serviço da web do Windows Azure. [Exemplos de códigos de tecla Vault Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Tutorial para ajudá-lo a aprender a usar o Windows Azure chave Vault de um aplicativo web no Windows Azure. [Use o Azure Vault principal de um aplicativo Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Instruções

Os artigos e os cenários a seguir fornecem orientação específica de tarefa para trabalhar com o Windows Azure chave Vault:

- [ID do inquilino vault principais de alteração após mover assinatura](key-vault-subscription-move-fix.md) - quando você mover sua assinatura do Azure de locatário uma ao locatário B, o seus compartimentos chaves existentes estão inacessíveis pelas entidades (usuários e aplicativos) no locatário corrigida B. isso usando este guia.
- [Acessando o chave Vault atrás do firewall](key-vault-access-behind-firewall.md) - para acessar uma chave vault que seu aplicativo de cliente vault chave precisa ser capaz de acessar vários pontos de extremidade para várias funcionalidades.

- [Como gerar e chaves de Transfer HSM-Protected para Azure chave Vault](key-vault-hsm-protected-keys.md) - isso ajudarão a planejar, gerar e depois transferir suas próprias teclas protegidos HSM para usar com o Azure chave Vault.
- [Como passar valores seguros (como senhas) durante a implantação](../resource-manager-keyvault-parameter.md) - quando você precisar passar um valor seguro (como uma senha) como um parâmetro durante a implantação, você pode armazenar esse valor como um segredo em um compartimento de chave do Windows Azure e referência o valor em outros modelos do Gerenciador de recursos.
- [Como usar o compartimento de chave para o gerenciamento de chaves extensível com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - o SQL Server Connector para o Windows Azure chave Vault permite que o SQL Server e SQL no VM para aproveitar o serviço do Windows Azure chave Vault como um provedor de extensível EKM (gerenciamento) para proteger suas chaves de criptografia para o link de aplicativos; Criptografia de dados transparente, a criptografia de Backup e criptografia de nível de coluna.
- [Como implantar certificados para VMs de chave compartimento](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - um aplicativo em nuvem em execução em uma VM no Azure precisa de um certificado. Como você obter esse certificado para essa VM hoje?
- [Como Vault chave com a rotação de chaves de ponta a ponta e auditoria de instalação](key-vault-key-rotation-log-monitoring.md) - este passa por toda a como configurar a rotação de chaves e auditoria com Vault de chave do Windows Azure.

Para obter mais tarefas específicas orientação sobre como integrar e usar compartimentos chave com o Azure, consulte [exemplos de modelo dos Ryan Jones Gerenciador de recursos do Windows Azure para armazenamento de chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integrado com chave Vault

Estes artigos sejam referem a outros cenários e os serviços que nos tornar do ou integram Vault chave.

- [Criptografia de disco do Windows Azure](../security/azure-security-disk-encryption.md) utiliza o recurso de [disco BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso de [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume do sistema operacional e os discos de dados. A solução é integrada com o Windows Azure Vault de chave para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e segredos em sua assinatura de chave vault, enquanto garante que todos os dados na máquina virtual discos são criptografados em repouso no seu armazenamento do Azure.


## <a name="supporting-libraries"></a>Bibliotecas de suporte

- [Biblioteca de núcleo do Microsoft Azure chave Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece `IKey` e `IKeyResolver` interfaces para localizar as chaves de identificadores e executar operações com chaves.

- [Extensões do Microsoft Azure chave Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornece recursos estendidos para Vault de chave do Windows Azure.

## <a name="other-key-vault-resources"></a>Outros recursos de armazenamento de chave
- [Blog de chave Vault](http://aka.ms/kvblog)
- [Fórum de chave Vault](http://aka.ms/kvforum)
