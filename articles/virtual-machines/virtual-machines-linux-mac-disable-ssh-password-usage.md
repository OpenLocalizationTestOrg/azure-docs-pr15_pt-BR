<properties
    pageTitle="Desabilitar senhas SSH em sua VM Linux Configurando SSHD | Microsoft Azure"
    description="Proteger sua VM Linux no Azure desabilitando logon de senha para o SSH."
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
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Desabilitar senhas SSH em sua VM Linux Configurando SSHD

Este artigo aborda como bloquear a segurança de login de sua VM Linux.  Assim que a porta SSH 22 é aberta para o início de robôs do mundo tentando login adivinhando senhas.  O que faremos neste artigo é desabilitar logon de senha via SSH.  Removendo completamente a capacidade de usar senhas podemos Proteja a VM Linux esse tipo de ataque de força bruta.  O bônus é que podemos configurará Linux SSHD para permitir logon através de chaves públicas e privadas SSH, somente de longe a maneira mais segura de fazer login no Linux.  As combinações possíveis de ele precisaria supor a chave privada é enorme e, portanto, não recomenda robôs de incomodar par para tentar força bruta SSH teclas.


## <a name="goals"></a>Metas

- Configure SSHD para proibir:
  - Logon de senha
  - Logon do usuário raiz
  - Autenticação de desafio-resposta
- Configure SSHD para permitir que:
  - Somente logons de chave SSH
- Reinicie o SSHD enquanto ainda conectado
- Testar a nova configuração SSHD

## <a name="introduction"></a>Introdução

[SSH definido](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD é SSH servidor que executa na VM Linux.  SSH é um cliente que executa a partir de um shell na estação de trabalho MacBook ou Linux.  SSH também é o protocolo usado para proteger e criptografar a comunicação entre a estação de trabalho e a VM Linux.

Para este artigo é muito importante manter um logon para sua VM Linux abrir para a percorrer todo.  Por esse motivo podemos abrirá dois terminais e SSH VM Linux de ambos.  Usaremos o terminal primeiro para fazer as alterações ao arquivo de configuração de SSHDs e reinicie o serviço SSHD.  Usaremos o segundo terminal testar essas alterações depois que o serviço for reiniciado.  Como podemos está desativando SSH senhas e depender terminantemente SSH chaves, se suas chaves SSH não estão corretos e fechar a conexão para a máquina virtual, a máquina virtual serão permanentemente bloqueado e ninguém serão capaz de login a ele exigir que ele seja excluído e recriado.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar chaves SSH no Linux e Mac para VMs Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Conta do Azure
  - [inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)
  - [Portal do Azure](http://portal.azure.com)
- Linux VM em execução no azure
- SSH pública e privada par de chaves no`~/.ssh/`
- Chave pública SSH `~/.ssh/authorized_keys` na VM Linux
- Direitos de sudo sobre a máquina virtual
- Porta 22 aberto

## <a name="quick-commands"></a>Comandos rápidos

_Experiente Linux Admins que queiram apenas a versão TLDR comece aqui.  Para todos os outros que deseja que a explicação detalhada e percorrer ignorar esta seção._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Percorrer detalhada

Faça login VM Linux em terminal 1 (T1).  Faça login VM Linux no terminal 2 (T2).

No T2 vamos editar o arquivo de configuração de SSHD.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

A partir daqui, podemos irá editar apenas as configurações para desativar as senhas e ativar logon chave SSH.  Há muitas configurações no arquivo que você deve pesquisar e alterar tornar Linux & SSH mais seguro conforme necessário.

#### <a name="disable-password-logins"></a>Desabilitar logon de senha

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Habilitar a autenticação de chave pública

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Desabilitar logon de raiz

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Desabilitar a autenticação de desafio-resposta

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Reinicie o SSHD

Do shell T1, verifique se que você ainda está conectado.  Isso é fundamental para que você não obtenha bloqueada fora de sua máquina virtual se suas chaves SSH não estiverem corretas, desde que as senhas agora estão desabilitadas.  Se qualquer configuração está incorreta em sua VM Linux que você pode usar T1 para corrigir sshd_config conforme você ainda serão registrados em e SSH manterá a conexão ativa durante o serviço SSHD reinicie.

De T2 executar:

##### <a name="on-the-debian-family"></a>Sobre a família Debian

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Na família RedHat

```
username@macbook$ sudo service sshd restart
```

As senhas agora estão desabilitadas em sua máquina virtual protegendo-a de tentativas de login de senha de força bruta.  Com apenas SSH chaves permitidas que será capaz de fazer logon com mais rapidez e muito mais segura.
