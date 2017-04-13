<properties 
    pageTitle="Use os privilégios de raiz em máquinas virtuais Linux | Microsoft Azure" 
    description="Saiba como usar privilégios de raiz no computador virtual Linux no Azure." 
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


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Usando privilégios de raiz no Linux máquinas virtuais do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Por padrão, o `root` usuário está desabilitado em máquinas virtuais de Linux no Azure. Os usuários podem executar comandos com privilégios elevados usando o `sudo` comando. No entanto, a experiência pode variar dependendo de como o sistema foi provisionado.

1. **Chave SSH e senha ou senha somente** - máquina virtual foi provisionada com qualquer um certificado (`.CER` arquivo) ou chave SSH bem como uma senha, ou apenas um nome de usuário e senha. Nesse caso `sudo` solicitará a senha do usuário antes de executar o comando.

2. **Somente a chave SSH** - máquina virtual foi provisionada com um certificado (`.cer`, `.pem`, ou `.pub` arquivo) ou chave SSH, mas nenhuma senha.  Nesse caso `sudo` **não irá** pedir a senha do usuário antes de executar o comando.


## <a name="ssh-key-and-password-or-password-only"></a>SSH chave e a senha ou a senha somente

Faça logon na máquina virtual Linux usando autenticação de senha ou chave SSH, em seguida, executar comandos usando `sudo`, por exemplo:

    # sudo <command>
    [sudo] password for azureuser:

Nesse caso o usuário será solicitado uma senha. Após inserir a senha `sudo` executará o comando com `root` privilégios.

Você também pode habilitar sudo passwordless editando o `/etc/sudoers.d/waagent` arquivo, por exemplo:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Essa alteração permitirá sudo passwordless pelo usuário "azureuser".

## <a name="ssh-key-only"></a>SSH chave somente

Faça logon na máquina virtual Linux usando autenticação de chave SSH, em seguida, executar comandos usando `sudo`, por exemplo:

    # sudo <command>

Nesse caso o usuário será **não** ser for solicitada uma senha. Após pressionar `<enter>`, `sudo` executará o comando com `root` privilégios.

 
