<properties 
    pageTitle="Selecionando nomes de usuário para Linux | Microsoft Azure" 
    description="Saiba como selecionar nomes de usuário para um computador virtual Linux no Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



#<a name="selecting-user-names-for-linux-on-azure"></a>Selecionando nomes de usuário para Linux no Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Quando você provisionar uma máquina virtual de Linux no Azure especifique o nome de um usuário não raiz que você pode usar para fazer login a máquina virtual mais tarde. Você pode escolher o nome do novo usuário, ou se provisionamento através do portal de clássico Azure você pode aceitar o nome padrão "azureuser".

Na maioria dos casos, esse usuário não existe na imagem base e será criado durante o processo de provisionamento. Se o usuário existir na imagem base máquina virtual, em seguida, o agente do Azure Linux simplesmente configura a senha e/ou chave SSH para esse usuário com base nas informações que você especificou ao criar a máquina virtual.

**No entanto**, Linux define um conjunto de nomes de usuário que não deve ser usado. O processo de provisionamento irá **falhar** se você tentar provisiona uma VM Linux usando um usuário existente do sistema, que é definido como um usuário com UID 0-99. Um exemplo típico é o `root` usuário, que tem UID 0.

 - Consulte também: [intervalos padrão de Linux Base - ID de usuário](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

A seguir está uma lista de usuários comuns do sistema interno para CentOS e Ubuntu que você evite usar ao provisionar o computador virtual Linux no Azure. Esta lista é apenas um exemplo, consulte a documentação para a sua distribuição garantir que o nome de usuário que você escolher não entram em conflito com um usuário existente do sistema.


## <a name="centos"></a>CentOS
- Abrt
- ADM
- áudio
- Lixeira
- CD-ROM
- cgred
- daemon
- dbus
- discagem
- DIP
- disco
- disquete
- FTP
- FTP
- jogos
- Gopher
- haldaemon
- interromper
- kmem
- bloquear
- LP
- mail
- Homem
- memória
- nfsnobody
- ninguém
- NTP
- operador
- oprofile
- postdrop
- sufixo
- qpidd
- raiz
- RPC
- rpcuser
- saslauth
- desligamento
- slocate
- sshd
- stapdev
- stapusr
- sincronização
- sistema
- fita
- teste
- tcpdump
- TTY
- usuários
- utempter
- utmp
- UUCP
- vcsa
- vídeo
- roda


## <a name="ubuntu"></a>Ubuntu
- ADM
- administração
- áudio
- backup
- Lixeira
- CD-ROM
- crontab
- daemon
- discagem
- DIP
- disco
- fax
- disquete
- unir
- jogos
- gnats
- IRC
- kmem
- orientação paisagem
- libuuid
- lista
- LP
- mail
- Homem
- MessageBus
- mlocate
- netdev
- notícias
- ninguém
- nogroup
- operador
- plugdev
- proxy
- raiz
- SASL
- sombra
- src
- SSH
- sshd
- equipe
- sudo
- sincronização
- sistema
- syslog
- fita
- TTY
- usuários
- utmp
- UUCP
- vídeo
- voz
- whoopsie
- dados de www

 