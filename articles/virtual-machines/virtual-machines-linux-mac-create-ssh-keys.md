<properties
    pageTitle="Criar chaves SSH no Linux e Mac | Microsoft Azure"
    description="Gerar e usar teclas SSH no Linux e Mac para o gerente de recursos e modelos de implantação clássico no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Criar chaves SSH no Linux e Mac para VMs Linux no Azure

Com um par de chaves SSH, você pode criar máquinas virtuais no Azure padrão usando as teclas de SSH para autenticação, eliminando a necessidade de senhas efetuar login.  As senhas podem ser adivinhadas e abra suas VMs até contínuo força bruta tenta descobrir sua senha. VMs criadas com modelos do Azure ou o `azure-cli` pode incluir sua chave pública SSH como parte da implantação, removendo uma configuração de implantação de postagem.  Se você estiver se conectando a uma VM Linux do Windows, consulte [neste documento.](virtual-machines-linux-ssh-from-windows.md)

O artigo requer:

- uma conta do Microsoft Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- o [Azure CLI](../xplat-cli-install.md) conectado com`azure login`

- o modo de Gerenciador de recursos do Azure _deve estar no_ CLI do Azure`azure config mode arm`

## <a name="quick-commands"></a>Comandos rápidos

Nos comandos a seguir, substitua os exemplos por suas próprias opções.

SSH teclas são por padrão mantido na `.ssh` diretório.  

```bash
cd ~/.ssh/
```

Se você não tiver um `~/.ssh` directory a `ssh-keygen` comando criará para você com as permissões corretas.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Insira o nome do arquivo que foi salvo na `~/.ssh/` diretório:

```bash
id_rsa
```

Digite a senha para id_rsa:

```bash
correct horse battery staple
```

Agora há um `id_rsa` e `id_rsa.pub` par de chaves SSH no `~/.ssh` diretório.

```bash
ls -al ~/.ssh
```

Adicionar a chave recém-criado para `ssh-agent` no Linux e Mac (também adicionados ao repositório de chaves OSX):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copie a chave pública SSH para seu servidor Linux:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Teste o logon usando as teclas em vez de uma senha:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada

Usar as chaves pública e privada SSH é a maneira mais fácil para efetuar login no servidores Linux. [Criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) fornece uma maneira mais segura para efetuar login no seu Linux ou BSD VM no Azure que as senhas, que podem ser forçado bruta muito mais facilmente. Sua chave pública pode ser compartilhada com qualquer pessoa; mas somente você (ou sua infraestrutura de segurança local) possuem sua chave privada.  A chave privada SSH deverá ter uma [senha bastante segura](https://www.xkcd.com/936/) (fonte:[xkcd.com](https://xkcd.com)) para proteger a ele.  Esta senha é apenas acessar a chave SSH particular e **não é** a senha da conta de usuário.  Quando você adicionar uma senha a sua chave SSH, ele criptografa a chave privada para que a chave privada é inútil sem a senha para desbloqueá-lo.  Se um invasor roubar sua chave privada e chave não tinha uma senha, eles poderão usar essa chave privada para fazer logon todos os servidores que têm a chave pública correspondente.  Se uma chave privada estiver protegido por senha-não podem ser usados por esse invasor, fornecendo uma camada adicional de segurança para sua infraestrutura no Azure.

Este artigo cria arquivos chave *ssh rsa* formatado, que são recomendados para implantações do Gerenciador de recursos.  chaves *SSH rsa* são necessárias no [portal](https://portal.azure.com) para implantações clássico e Gerenciador de recursos.

## <a name="create-the-ssh-keys"></a>Criar as chaves SSH

Ssh-rsa Azure exige pelo menos 2048 bits, formate chaves pública e privada. Para criar o uso de chaves `ssh-keygen`, que faz uma série de perguntas e, em seguida, grava uma chave privada e uma chave pública correspondente. Quando uma máquina virtual do Azure é criado, a chave pública é copiada para `~/.ssh/authorized_keys`.  Teclas de SSH em `~/.ssh/authorized_keys` são usados para o cliente para corresponder a chave privada correspondente em uma conexão de login SSH um desafio.

## <a name="using-ssh-keygen"></a>Usando ssh keygen

Esse comando cria uma senha protegida (criptografadas) SSH par de chaves usando RSA de 2048 bits e ela é comentada para identificá-lo facilmente.  

Inicie alterando diretórios, para que todos os seus ssh teclas são criadas no diretório.

```bash
cd ~/.ssh
```

Se você não tiver um `~/.ssh` directory a `ssh-keygen` comando criará para você com as permissões corretas.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Comando explicado_

`ssh-keygen`= o programa usado para criar as chaves

`-t rsa`= tipo de tecla para criar o que é o [RSA formato](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bits da chave

`-C "myusername@myserver"`= um comentário acrescentado ao final do arquivo de chave pública para identificá-lo facilmente.  Normalmente um email é usado como o comentário, mas você pode usar tudo o que funciona melhor para sua infraestrutura.

### <a name="using-pem-keys"></a>Usando as teclas de PEM

Se você estiver usando o clássico implantar modelo (Portal clássico do Azure ou da CLI de gerenciamento de serviço de Azure `asm`), você talvez precise usar PEM formatado SSH teclas para acessar suas VMs Linux.  Veja aqui como criar uma chave PEM de uma chave SSH público existente e um x509 existente certificado.

Para criar um PEM formatado chave de uma chave pública de SSH existente:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemplo de ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Salvar arquivos de chave:

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

O nome de par de chaves para este artigo.  Ter um par de chaves denominado **id_rsa** é o padrão e algumas ferramentas podem esperar o nome de arquivo de chave privada **id_rsa** para que ter uma é uma boa ideia. Diretório `~/.ssh/` é o local padrão para pares de chave SSH e o arquivo de configuração do SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
Uma listagem do `~/.ssh` diretório. `ssh-keygen`cria o `~/.ssh` diretório se ele não estiver presente e também define os modos de propriedade e arquivo corretos.

Senha da chave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`se refere a uma senha como "uma senha".  É *altamente* recomendada para adicionar uma senha ao seu pares de chaves. Sem uma senha protegendo o par de chaves, qualquer pessoa com o arquivo de chave privado pode usá-lo para efetuar login no qualquer servidor que tenha a chave pública correspondente. Adicionando uma senha oferece mais proteção caso alguém é capaz de acessar seu arquivo de chave privada, concedeu tempo para alterar as teclas usado para autenticar você.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Usando ssh agente para armazenar sua senha de chave privada

Para evitar digitar sua senha do arquivo de chave privada com cada login SSH, você pode usar `ssh-agent` em cache sua senha do arquivo de chave privada. Se você estiver usando um Mac, repositório de chaves OSX armazena de forma segura as senhas de chave particular quando você invoca `ssh-agent`.

Primeiro verifique `ssh-agent` está em execução

```bash
eval "$(ssh-agent -s)"
```

Agora, adicione a chave particular `ssh-agent` usando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A senha da chave privada agora é armazenada no `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um arquivo de configuração do SSH

É uma prática recomendada para criar e configurar um `~/.ssh/config` arquivo para acelerar a fazer logon ins e para otimizar o comportamento de cliente SSH.

O exemplo a seguir mostra uma configuração padrão.

### <a name="create-the-file"></a>Criar o arquivo

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite o arquivo para adicionar a nova configuração de SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Exemplo `~/.ssh/config` arquivo:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Esta configuração SSH fornece seções para cada servidor para habilitar cada ter seu próprio par de chaves dedicado. As configurações padrão (`Host *`) são para quaisquer hosts que não correspondem a nenhum dos hosts específicos superiores no arquivo de configuração.


### <a name="config-file-explained"></a>Arquivo de configuração explicado

`Host`= o nome do host sendo chamado no terminal.  `ssh fedora22`informa `SSH` usar os valores no bloco configurações rotulada `Host fedora22` Observação: isso pode ser qualquer rótulo lógica para seu uso e que não representa o nome de host real de qualquer servidor.

`Hostname 102.160.203.241`= o endereço IP ou o nome DNS para o servidor que está sendo acessado.

`User myusername`= a conta de usuário remoto para usar ao fazer logon no servidor.

`PubKeyAuthentication yes`= informa SSH que você deseja usar uma chave SSH para efetuar login.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= a chave privada SSH e chave pública correspondente para usar para autenticação.


## <a name="ssh-into-linux-without-a-password"></a>SSH em Linux sem uma senha

Agora que você tem um par de chaves SSH e um arquivo de configuração do SSH configurado, você é capazes de fazer logon no seu VM Linux rapidamente e com segurança. Na primeira vez que você entrar um servidor usando uma chave SSH os prompts de comando você para a senha para esse arquivo de chave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Comando explicado

Quando `ssh fedora22` é executado SSH primeiro localiza e carrega quaisquer configurações do `Host fedora22` bloco e, em seguida, carrega todas as configurações restantes do último bloco, `Host *`.

## <a name="next-steps"></a>Próximas etapas

Próximo é criar Azure Linux VMs usando a nova chave pública de SSH.  Azure VMs que são criadas com uma chave pública SSH como login são mais seguras que VMs criadas com o método de login padrão, as senhas.  Azure VMs criadas usando as teclas de SSH são por padrão configurado com senhas desabilitadas, evitando forçado bruta adivinhação tentativas.

- [Criar uma VM Linux seguro usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM Linux seguro usando o portal do Azure](virtual-machines-linux-quick-create-portal.md)
- [Criar uma VM Linux seguro utilizando a CLI do Azure](virtual-machines-linux-quick-create-cli.md)
