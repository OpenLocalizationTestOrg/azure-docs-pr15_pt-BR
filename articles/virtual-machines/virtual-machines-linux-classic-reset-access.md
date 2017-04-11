<properties
        pageTitle="Redefinir a senha de Linux VM e chave SSH da CLI | Microsoft Azure"
        description="Como usar a extensão de VMAccess do Azure Interface de linha de comando (CLI) para redefinir uma senha de Linux VM ou chave SSH, corrigir a configuração do SSH e verificar a consistência de disco"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Como redefinir uma senha de Linux VM ou chave SSH, corrigir a configuração do SSH e verificar a consistência de disco usando a extensão de VMAccess


Se é possível conectar ao computador virtual Linux no Azure devido a uma senha esquecida, uma tecla de Secure Shell (SSH) incorreta, ou um problema com a configuração de SSH, use a extensão de VMAccessForLinux com a CLI do Azure para redefinir a senha ou chave SSH, corrija a configuração do SSH e verificar a consistência do disco. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Com a CLI do Azure, você use o comando de **extensão de máquina virtual azure definida** da sua interface de linha de comando (Bash, Terminal, prompt de comando) para acessar comandos. Execute a **extensão de máquina virtual do azure ajuda definido** para uso da extensão detalhadas.

Com a CLI do Azure, você pode fazer as seguintes tarefas:

+ [Redefinir a senha](#pwresetcli)
+ [Redefinir a chave SSH](#sshkeyresetcli)
+ [Redefinir a senha e chave SSH](#resetbothcli)
+ [Criar uma nova conta de usuário de sudo](#createnewsudocli)
+ [Redefinir a configuração de SSH](#sshconfigresetcli)
+ [Excluir um usuário](#deletecli)
+ [Exibir o status da extensão VMAccess](#statuscli)
+ [Verificar a consistência de discos adicionados](#checkdisk)
+ [Reparar discos adicionados na sua VM Linux](#repairdisk)


## <a name="prerequisites"></a>Pré-requisitos

Você precisará fazer o seguinte:

- Você precisará [instalar o CLI do Azure](../xplat-cli-install.md) e [se conectar à sua assinatura](../xplat-cli-connect.md) use Azure recursos associados a uma conta.
- Defina o modo correto para o modelo de implantação clássico digitando o seguinte no prompt de comando:
        
        azure config mode asm
        
- Tem uma nova senha ou o conjunto de chaves SSH, se desejar redefinir deles. Você não precisa dessas se desejar redefinir a configuração de SSH.


## <a name="pwresetcli"></a>Redefinir a senha

1. Crie um arquivo denominado PrivateConf.json com essas linhas. Substitua os colchetes e & #60; espaço reservado & #62; valores com suas próprias informações.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Execute este comando, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Redefinir a chave SSH

1. Crie um arquivo denominado PrivateConf.json com esse conteúdo. Substitua os colchetes e & #60; espaço reservado & #62; valores com suas próprias informações.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Execute este comando, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Redefinir a senha e chave SSH

1. Crie um arquivo denominado PrivateConf.json com esse conteúdo. Substitua os colchetes e & #60; espaço reservado & #62; valores com suas próprias informações.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Execute este comando, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Criar uma nova conta de usuário de sudo

Se você esquecer seu nome de usuário, você pode usar VMAccess para criar um novo com a autoridade sudo. Nesse caso, o nome de usuário e a senha existente não serão modificados.

Para criar um novo usuário sudo com senha de acesso, use o script em [Redefinir a senha](#pwresetcli) e especifique o novo nome de usuário.

Para criar um novo usuário sudo com acesso às chaves SSH, use o script em [Redefinir a chave SSH](#sshkeyresetcli) e especifique o novo nome de usuário.

Você também pode usar o [Redefinir a senha e chave SSH](#resetbothcli) para criar um novo usuário com acesso às chaves SSH e senha.

## <a name="sshconfigresetcli"></a>Redefinir a configuração de SSH

Se a configuração do SSH está em um estado indesejado, você também pode perder o acesso para a máquina virtual. Você pode usar a extensão de VMAccess para redefinir a configuração para seu estado padrão. Para fazer isso, basta definir a chave "reset_ssh" para "True". A extensão será reiniciar o servidor SSH, abra a porta SSH em sua máquina virtual e redefinir a configuração de SSH para valores padrão. A conta de usuário (nome, senha ou teclas de SSH) não será alterada.

> [AZURE.NOTE] O arquivo de configuração do SSH que obtém redefinir está localizado em /etc/ssh/sshd_config.

1. Crie um arquivo denominado PrivateConf.json com esse conteúdo.

        {
        "reset_ssh":"True"
        }

2. Execute este comando, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Excluir um usuário

Se você quiser excluir uma conta de usuário sem efetuar login para a máquina virtual diretamente, você pode usar esse script.

1. Crie um arquivo denominado PrivateConf.json com esse conteúdo, substituindo o nome de usuário para remover para 60 #; usernametoremove & #62;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Execute este comando, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Exibir o status da extensão VMAccess

Para exibir o status da extensão VMAccess, execute este comando.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< um name = 'checkdisk' <</a>Verificar consistência da discos adicionados

Para executar fsck em todos os discos na máquina virtual Linux, você precisará fazer o seguinte:

1. Crie um arquivo denominado PublicConf.json com esse conteúdo. Verificar que disco leva um booliano se deseja verificar disco anexado à sua máquina virtual ou não. 

        {   
        "check_disk": "true"
        }

2. Execute este comando para executar, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Reparar discos 

Para reparar discos que não são montagem ou tem erros de configuração de montagem, use a extensão de VMAccess para redefinir a configuração de montagem no computador virtual Linux. Substituindo o nome do seu disco #60; yourdisk & #62;.

1. Crie um arquivo denominado PublicConf.json com esse conteúdo. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Execute este comando para executar, substituindo o nome de sua máquina virtual para 60 #; nome da máquina virtual & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Próximas etapas

* Se você quiser usar cmdlets do PowerShell do Azure ou modelos do Gerenciador de recursos do Azure para redefinir a senha ou chave SSH, corrigir a configuração de SSH e verificar a consistência de disco, consulte a [documentação de extensão VMAccess no GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Você também pode usar o [portal do Azure](https://portal.azure.com) para redefinir a senha ou chave SSH de uma VM Linux implantado no modelo clássico de implantação. Atualmente não é possível usar o portal para isto para uma VM Linux implantado no modelo de implantação do Gerenciador de recursos.

* Consulte [sobre os recursos e extensões de máquina virtual](virtual-machines-linux-extensions-features.md) para obter mais informações sobre como usar extensões de máquina virtual para Azure máquinas virtuais.
