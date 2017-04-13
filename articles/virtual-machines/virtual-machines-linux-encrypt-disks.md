<properties
   pageTitle="Criptografar discos em uma VM Linux | Microsoft Azure"
   description="Como criptografar discos em uma VM Linux usando a CLI do Azure e o modelo de implantação do Gerenciador de recursos"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Criptografar discos em uma VM Linux utilizando a CLI do Azure
Para segurança aprimorada virtuais de máquina e conformidade, discos virtuais no Azure podem ser criptografados inativos. Discos estão criptografados com chaves de criptografia que são protegidas em um cofre de chave do Azure. Você controlar essas chaves de criptografia e pode auditar seu uso. Este artigo detalha como criptografar discos virtuais em uma VM Linux usando a CLI do Azure e o modelo de implantação do Gerenciador de recursos.


## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar a tarefa, os seguintes detalhes de seção rapidamente a base comandos para criptografar discos virtuais em sua máquina virtual. Informações mais detalhadas e o contexto para cada etapa podem ser encontradas o restante do documento, [começando aqui](#overview-of-disk-encryption).

Você precisa de [Mais recente do Azure CLI](../xplat-cli-install.md) instalados e conectado usando o modo do Gerenciador de recursos da seguinte maneira:

```
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Incluem nomes de parâmetro de exemplo `myResourceGroup`, `myKeyVault`, e `myVM`.

Primeiro, ativar o provedor do Azure chave cofre dentro de sua assinatura do Azure e criar um grupo de recursos. O exemplo a seguir cria um nome de grupo de recursos `myResourceGroup` no `WestUS` local:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Crie um cofre chave Azure. O exemplo a seguir cria um cofre de chave denominada `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Criar uma chave de criptografia no seu Cofre de chave e habilitá-lo para criptografia de disco. O exemplo a seguir cria uma chave chamada `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Crie um ponto de extremidade usando o Active Directory do Azure para lidar com a autenticação e troca de chaves de criptografia do Cofre de chave. O `--home-page` e `--identifier-uris` não precisam ser endereço roteável real. No nível mais alto de segurança, as senhas dos clientes devem ser usadas em vez de senhas. A CLI do Azure atualmente não pode gerar senhas dos clientes. Senhas dos clientes só podem ser geradas no portal do Azure. O exemplo a seguir cria um ponto de extremidade do Azure Active Directory chamado `myAADApp` e usa uma senha de `myPassword`. Especifica sua própria senha da seguinte maneira:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Observação a `applicationId` mostrado na saída do comando anterior. Esta ID de aplicativo é usado nas etapas a seguir:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Adicione um disco de dados para uma máquina virtual existente. O exemplo a seguir adiciona um disco de dados a uma máquina virtual chamada `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Examine os detalhes do seu Cofre de chave e a chave que você criou. É necessário o ID de chave de cofre, URI e chave URL na etapa final. O exemplo a seguir examina os detalhes de um cofre de chave denominada `myKeyVault` e chave denominada `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Criptografe seus discos da seguinte maneira, inserindo seus próprios nomes de parâmetro em todo:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

A CLI do Azure não fornecem erros detalhados durante o processo de criptografia. Para obter informações adicionais de solução de problemas, revise `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Como o comando anterior tiver muitas variáveis e você não pode obter muito indica por que o processo falha, um exemplo de comando completo seria assim:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Finalmente, examine o status de criptografia novamente para confirmar que os discos virtuais agora foram criptografados. O exemplo a seguir verifica o status de uma máquina virtual chamado `myVM` no `myResourceGroup` grupo de recursos:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Discos virtuais no Linux VMs são criptografados inativos usando [dm crypt](https://wikipedia.org/wiki/Dm-crypt). Não há nenhum custo para criptografar discos virtuais no Azure. Chaves de criptografia são armazenadas no cofre de chave do Azure usando o software de proteção, ou você pode importar ou gerar suas chaves em módulos de segurança de Hardware (HSMs) certificados para FIPS 140-2 padrões de nível 2. Manter o controle dessas chaves de criptografia e pode auditar seu uso. Essas chaves de criptografia são usadas para criptografar e descriptografar discos virtuais anexados à sua máquina virtual. Um ponto de extremidade do Active Directory do Azure fornece um mecanismo seguro para emitir essas chaves de criptografia como VMs são ativadas e desativar.

O processo para criptografar uma máquina virtual é da seguinte maneira:

1. Crie uma chave de criptografia em um cofre de chave do Azure.
2. Configure a chave de criptografia para ser usado para criptografar discos.
3. Para ler a chave de criptografia no Azure chave cofre, crie um ponto de extremidade usando o Azure Active Directory com as permissões apropriadas.
4. Execute o comando para criptografar discos virtuais, especificando o ponto de extremidade do Active Directory do Azure e a chave de criptografia apropriada a ser usado.
5. O ponto de extremidade do Azure Active Directory solicita a chave de criptografia exigida do Azure chave cofre.
6. Os discos virtuais são criptografados usando a chave de criptografia fornecida.


## <a name="supporting-services-and-encryption-process"></a>Processo de criptografia e serviços de suporte
Criptografia de disco depende os seguintes componentes adicionais:

- **Compartimento de chave do azure** - usado para proteger as chaves de criptografia e senhas usadas para o processo de criptografia/descriptografia de disco. 
  - Se houver uma, você pode usar um cofre de chave existente do Azure. Você não precisa dedicar um cofre de chave para criptografar discos.
  - Para separar os limites administrativos e visibilidade chave, você pode criar um cofre chave dedicado.
- **Active Directory do azure** - trata a troca segura de chaves de criptografia necessárias e autenticação para ações solicitadas. 
  - Normalmente, você pode usar uma instância existente do Active Directory do Azure para acomodar o seu aplicativo. 
  - O aplicativo é mais de um ponto de extremidade para os serviços de chave cofre e máquina Virtual solicitar e obter emitiu as chaves de criptografia apropriadas. Você não está desenvolvendo um aplicativo real que se integra ao Active Directory do Azure.


## <a name="requirements-and-limitations"></a>Requisitos e limitações
Requisitos de criptografia de disco e cenários com suporte:

- O seguinte servidor Linux SKUs - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.
- Todos os recursos (como chave cofre, conta de armazenamento e máquina virtual) devem estar na mesma região Azure e assinatura.
- Um padrão, D, DS, G e GS série VMs.

Criptografia de disco não é suportada atualmente nos seguintes cenários:

- Nível básico VMs.
- VMs criadas usando o modelo de implantação do clássico.
- Desativando criptografia de disco do sistema operacional em VMs Linux.
- Atualizando as chaves de criptografia em uma VM Linux já criptografados.


## <a name="create-the-azure-key-vault-and-keys"></a>Criar o Cofre de chave do Azure e chaves
Para concluir o restante deste guia, é necessário o [Mais recente do Azure CLI](../xplat-cli-install.md) instalados e conectado usando o modo do Gerenciador de recursos da seguinte maneira:

```
azure config mode arm
```

Em todo os exemplos de comando, substitua todos os parâmetros de exemplo com seus próprios nomes, local e valores-chave. Os exemplos a seguir usam uma convenção de `myResourceGroup`, `myKeyVault`, `myAADApp`, etc.

A primeira etapa é criar um cofre de chave do Azure para armazenar suas chaves de criptografia. Azure Cofre de chave pode armazenar chaves, segredos ou senhas que permitem implementar de forma segura em seus aplicativos e serviços. Para criptografia de disco virtual, você pode usar chave cofre para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais. 

Ativar o provedor do Azure chave cofre em sua assinatura do Azure e, em seguida, criar um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `WestUS` local:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

O Cofre de chave do Azure que contém as chaves de criptografia e os recursos de computação associados como armazenamento e a máquina virtual em si deve residem na mesma região. O exemplo a seguir cria um cofre de chave do Azure denominada `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Você pode armazenar chaves de criptografia usando a proteção do modelo de segurança de Hardware (HSM) ou software. Usando um HSM requer um premium chave cofre. Há um custo adicional para a criação de um premium chave cofre em vez de padrão Cofre de chave que armazena as chaves de software protegido. Para criar uma chave do cofre premium, na etapa anterior, adicione `--sku Premium` para o comando. O exemplo a seguir usa chaves protegido por software desde que criamos um cofre de chave padrão. 

Para ambos os modelos de proteção, plataforma Windows Azure precisa ter acesso para solicitar as chaves de criptografia quando a máquina virtual inicializa para descriptografar os discos virtuais. Criar uma chave de criptografia dentro de sua chave de compartimento e habilitá-lo para uso com criptografia de disco virtual. O exemplo a seguir cria uma chave chamada `myKey` e, em seguida, permite que ela para criptografia de disco:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Criar o aplicativo do Active Directory do Azure
Quando o disco virtual é criptografado ou descriptografar, você usa um ponto de extremidade para lidar com a autenticação e a troca de chaves de criptografia do Cofre de chave. Neste ponto de extremidade, um aplicativo do Active Directory do Azure, permite a plataforma Windows Azure solicitar as chaves de criptografia apropriadas em nome da máquina virtual. Uma instância do Active Directory do Azure padrão está disponível em sua assinatura, embora muitas organizações tenham dedicados diretórios do Active Directory do Azure.

Como você não estiver criando um aplicativo completo do Active Directory do Azure, o `--home-page` e `--identifier-uris` parâmetros no exemplo a seguir não precisam ser endereço roteável real. O exemplo a seguir também especifica um segredo baseada em senha em vez de gerar chaves de dentro do portal Azure. Neste momento, a geração de chaves não pode ser feito da CLI Azure. 

Crie seu aplicativo do Azure Active Directory. O exemplo a seguir cria um aplicativo chamado `myAADApp` e usa uma senha de `myPassword`. Especifica sua própria senha da seguinte maneira:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Anote o `applicationId` que é retornado na saída do comando anterior. Esta ID de aplicativo é usada em algumas das etapas restantes. Em seguida, crie um nome principal de serviço (SPN) para que o aplicativo é acessível no seu ambiente. Para criptografar ou descriptografar discos virtuais com êxito, permissões na chave de criptografia armazenada no cofre de chave devem ser definidas para permitir que o aplicativo do Active Directory do Azure para ler as chaves. 

Crie o SPN e defina as permissões apropriadas da seguinte maneira:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Adicionar um disco virtual e revisar o status de criptografia
Para criptografar realmente alguns discos virtuais, permite adicionar um disco para uma máquina virtual existente. Adicione um disco de dados de 5Gb para uma máquina virtual existente da seguinte maneira:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Os discos virtuais atualmente não são criptografados. Revise o status atual de criptografia da sua máquina virtual da seguinte maneira:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Criptografar discos virtuais
Para criptografar agora os discos virtuais, você reúnem todos os componentes anterior:

1. Especifique o aplicativo do Active Directory do Azure e a senha.
2. Especifique o cofre chave para armazenar os metadados para seus discos criptografados.
3. Especifique as chaves de criptografia para usar para a criptografia e descriptografia.
4. Especifique se deseja criptografar o disco de sistema operacional, os discos de dados ou todos.

Permite que examine os detalhes do seu Cofre de chave do Azure e a chave que você criou, quando você precisa da identificação do cofre chave, URI e, em seguida, a URL de chave na etapa final:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Criptografar discos virtuais usando a saída do `azure keyvault show` e `azure keyvault key show` comandos da seguinte maneira:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Como o comando anterior tiver muitas variáveis, o exemplo a seguir é o comando completo para referência:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

A CLI do Azure não fornecem erros detalhados durante o processo de criptografia. Para obter informações adicionais de solução de problemas, revise `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` sobre a máquina virtual que está criptografando.

Finalmente, permite revisar o status de criptografia novamente para confirmar que os discos virtuais agora foram criptografados:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Adicionar discos de dados adicionais
Depois que você tiver criptografados seus discos de dados, você pode posteriormente adicionar discos virtuais adicionais para sua máquina virtual e também criptografá-los. Quando você executa o `azure vm enable-disk-encryption` comando, incrementar a versão de sequência usando o `--sequence-version` parâmetro. Este parâmetro de versão de sequência permite executar operações repetidas na mesma VM.

Por exemplo, permite adicionar um segundo disco virtual para sua máquina virtual da seguinte maneira:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Execute novamente o comando para criptografar os discos virtuais, esse tempo adicionando o `--sequence-version` parâmetro e incrementar o valor de nossa primeira execução da seguinte maneira:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como gerenciar Cofre de chave do Azure, incluindo a exclusão de chaves de criptografia e compartimentos, consulte [Gerenciar cofre de chave usando CLI](../key-vault/key-vault-manage-with-cli.md).
- Para obter mais informações sobre criptografia de disco, como preparar uma máquina virtual personalizada criptografada para carregar no Azure, consulte [Criptografia de disco do Azure](../security/azure-security-disk-encryption.md).