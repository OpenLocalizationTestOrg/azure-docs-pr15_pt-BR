<properties
   pageTitle="Introdução ao FreeBSD no Azure | Microsoft Azure"
   description="Saiba como usar máquinas virtuais de FreeBSD no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este tópico fornece uma visão geral da execução de uma máquina virtual de FreeBSD no Azure.

## <a name="overview"></a>Visão geral
FreeBSD do Microsoft Azure é um sistema operacional de computador avançado usado para servidores modernos de energia, desktops e incorporado plataformas. A imagem de FreeBSD 10.3 é fornecida pela Microsoft Corporation e está disponível no Azure. Baseia-se na versão 10.3 FreeBSD e Azure agente de convidado de máquina virtual [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) está instalado. O agente é responsável por comunicação entre a VM FreeBSD e a estrutura Azure para operações, como provisionar a máquina virtual na primeira utilização (nome de usuário, senha, nome do host, etc.) e ativando funcionalidade para seletivas extensões de máquina virtual.
Para futuras versões do FreeBSD, a estratégia é a se manter atualizado e disponibilizar as últimas versões logo depois que eles são lançados pela equipe de engenharia do lançamento do FreeBSD. O lançamento é [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Implantando uma máquina virtual de FreeBSD
Implantar uma máquina virtual de FreeBSD é um processo simples, usando uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Extensões de máquina virtual para FreeBSD
A seguir é compatíveis com extensões de máquina virtual em FreeBSD.

### <a name="vmaccess"></a>VMAccess

A extensão de [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

- Redefina a senha do usuário sudo original.
- Crie um novo usuário sudo com a senha especificada.
- Defina a chave pública host com a chave fornecida.
- Redefina a chave de host pública fornecida durante a configuração de máquina virtual se a chave de host não for fornecida.
- Abrir a porta SSH (22) e restaurar a sshd_config se reset_ssh for definido como true.
- Remova o usuário existente.
- Verificar o disco.
- Repare um disco adicionado.

### <a name="customscript"></a>CustomScript

A extensão de [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

- Se fornecido, baixe scripts personalizados de armazenamento do Azure ou armazenamento público externo (por exemplo, GitHub).
- Execute o script de ponto de entrada.
- Suporte a comandos embutida.
- Converta a nova linha de estilo do Windows em shell e scripts de Python automaticamente.
- Remova BOM no shell e scripts Python automaticamente.
- Proteger dados confidenciais no CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de usuário, senhas e chaves SSH
Quando você estiver criando uma máquina virtual de FreeBSD usando o portal do Azure, você deve fornecer um nome de usuário, senha ou chave pública SSH.
Nomes de usuário para implantar uma máquina virtual de FreeBSD no Azure não devem corresponder a nomes de contas do sistema (UID < 100) já presentes na máquina virtual ("raiz," por exemplo).
Atualmente, apenas a RSA SSH chave é suportada. Uma chave SSH combinada deve começar com "---Comece SSH2 chave pública---" e termina com "---END SSH2 chave pública---".

## <a name="obtaining-superuser-privileges"></a>Obtendo superusuário
A conta de usuário especificado durante a implantação de instância de máquina virtual no Azure é uma conta com privilégios. O pacote de sudo foi instalado na imagem FreeBSD publicada.
Depois que você está conectado através desta conta de usuário, você pode executar comandos como raiz usando a sintaxe de comando.

    # sudo <COMMAND>

Opcionalmente, você pode obter um shell raiz usando sudo -s.

## <a name="next-steps"></a>Próximas etapas
- Acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) para criar uma VM FreeBSD.
- Se você deseja trazer seu próprio FreeBSD para Azure, consulte [criar e carregar um VHD FreeBSD no Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).
