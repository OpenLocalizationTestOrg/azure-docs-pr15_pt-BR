<properties
    pageTitle="Perguntas Frequentes sobre o Linux VMs | Microsoft Azure"
    description="Fornece respostas para algumas perguntas comuns sobre máquinas virtuais de Linux criadas com o modelo do Gerenciador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais do Linux

Este artigo aborda algumas perguntas comuns sobre máquinas virtuais de Linux criadas no Azure usando o modelo de implantação do Gerenciador de recursos. Para a Windows versão deste tópico, consulte [Perguntas frequentes sobre máquinas virtuais do Windows](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que pode executar em uma máquina virtual do Azure?

Todos os assinantes podem executar o software de servidor em uma máquina virtual Azure. Para obter mais informações, consulte [Linux em Azure-Endorsed distribuições](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento pode usar com uma máquina virtual?

Cada disco de dados pode ser até 1 TB. O número de discos de dados, que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

Uma conta de armazenamento do Azure fornece armazenamento para o disco de sistema operacional e qualquer disco de dados. Cada disco é um arquivo. vhd armazenado como um blob de página. Para obter detalhes sobre preços, consulte [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Como é possível acessar minha máquina virtual?

Estabelece uma conexão remota para fazer logon na máquina virtual, usando o Secure Shell (SSH). Consulte as instruções sobre como conectar a [partir de Windows](virtual-machines-linux-ssh-from-windows.md) ou [Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md). Por padrão, SSH permite um máximo de 10 conexões simultâneas. Você pode aumentar esse número editando o arquivo de configuração.


Se você estiver enfrentando problemas, confira [solucionar Secure Shell (SSH) conexões](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Pode usar o disco temporário (/dev/sdb1) para armazenar dados?

Não use o disco temporário (/dev/sdb1) para armazenar dados. Ele existe somente para armazenamento temporário. O risco de perda de dados que não poderão ser recuperados.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar uma VM Azure existente?

Sim. Para obter instruções, consulte [como criar uma cópia de um computador de virtual Linux no modelo de implantação do Gerenciador de recursos](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que não estou vendo Canadá Central e regiões Leste Canadá através do Gerenciador de recursos do Azure?

Duas novas regiões de Canadá Central e Oriental Canadá não são automaticamente registradas para a criação de máquina virtual para assinaturas do Azure existentes. Esse registro é feito automaticamente quando uma máquina virtual é implantada através do portal do Azure para qualquer outra região usando o Gerenciador de recursos do Azure. Depois que uma máquina virtual for implantada em qualquer outra região Azure, novas regiões devem estar disponíveis para subsequentes máquinas virtuais.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma NIC para minha máquina virtual após sua criação?

Não. Adicionando uma NIC só pode ser feito no momento da criação.


## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome do computador?

Sim. O nome do computador pode ter no máximo 64 caracteres de comprimento. Consulte [diretrizes de nomenclatura de infraestrutura](virtual-machines-linux-infrastructure-naming-guidelines.md) para obter mais informações em torno de nomenclatura seus recursos.


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de usuário ao criar uma máquina virtual?

Nomes de usuário devem ser 1-64 caracteres de comprimento.

Os seguintes nomes de usuário não são permitidos:

<table>
    <tr>
        <td style="text-align:center">administrador </td><td style="text-align:center"> administração </td><td style="text-align:center"> usuário </td><td style="text-align:center"> Usuário1</td>
    </tr>
    <tr>
        <td style="text-align:center">teste </td><td style="text-align:center"> Usuário2 </td><td style="text-align:center"> Test1 </td><td style="text-align:center"> Usuário3</td>
    </tr>
    <tr>
        <td style="text-align:center">Admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> um</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> Console </td><td style="text-align:center"> David </td><td style="text-align:center"> convidado</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> proprietário </td><td style="text-align:center"> raiz </td><td style="text-align:center"> servidor</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> suporte </td><td style="text-align:center"> Support_388945a0 </td><td style="text-align:center"> sistema</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2 </td><td style="text-align:center"> realizado no mercado3 </td><td style="text-align:center"> User4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar uma máquina virtual?

As senhas devem ser 6-72 caracteres de comprimento e atender 3 fora os seguintes requisitos de 4 complexidade:

- Ter caracteres inferiores
- Ter caracteres superiores
- Ter um dígito
- Ter um caractere especial (Regex coincidir [\W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Senha!</td>
        <td style="text-align:center">Senha1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
