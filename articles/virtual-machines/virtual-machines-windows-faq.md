<properties
    pageTitle="Perguntas Frequentes sobre o Windows VMs | Microsoft Azure"
    description="Fornece respostas para algumas perguntas comuns sobre máquinas virtuais do Windows criadas com o modelo do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais do Windows 


Este artigo aborda algumas perguntas comuns sobre máquinas virtuais do Windows criadas no Azure usando o modelo de implantação do Gerenciador de recursos. Para a Linux versão deste tópico, consulte [Perguntas frequentes sobre máquinas virtuais do Linux](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que pode executar em uma máquina virtual do Azure?

Todos os assinantes podem executar o software de servidor em uma máquina virtual Azure. Para obter informações sobre a política de suporte para a execução de software de servidor Microsoft no Azure, consulte o [software de servidor da Microsoft oferece suporte para máquinas virtuais do Azure](https://support.microsoft.com/kb/2721672)

Certas versões do Windows 7 e Windows 8.1 estão disponíveis para os assinantes do MSDN Azure benefício e assinantes MSDN desenvolvimento e testar pré-pago, para tarefas de desenvolvimento e teste. Para obter detalhes, incluindo instruções e limitações, consulte [imagens de cliente do Windows para os assinantes do MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento pode usar com uma máquina virtual?

Cada disco de dados pode ser até 1 TB. O número de discos de dados, que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).

Uma conta de armazenamento do Azure fornece armazenamento para o disco de sistema operacional e qualquer disco de dados. Cada disco é um arquivo. vhd armazenado como um blob de página. Para obter detalhes sobre preços, consulte [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Como é possível acessar minha máquina virtual?

Estabelece uma conexão remota usando Conexão de área de trabalho remota (RDP) para uma máquina virtual Windows. Para obter instruções, consulte [como conectar e faça logon uma máquina virtual Azure executando o Windows](virtual-machines-windows-connect-logon.md). Um máximo de duas conexões simultâneas são suportados, a menos que o servidor está configurado como um host de sessão de serviços de área de trabalho remota.  


Se você estiver enfrentando problemas com a área de trabalho remota, consulte [Solucionar problemas de área de trabalho remota conexões para uma baseado no Windows Azure Máquina Virtual](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Se você estiver familiarizado com Hyper-V, você pode estar procurando por uma ferramenta semelhante a VMConnect. Azure não oferece uma ferramenta semelhante porque o acesso a uma máquina virtual console não é compatíveis.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Pode usar o disco temporário (a unidade d: por padrão) para armazenar dados?

Não use o disco temporário para armazenar dados. É só armazenamento temporário, seria o risco de perda de dados que não poderão ser recuperados. Perda de dados pode ocorrer quando a máquina virtual move para um host diferente. Redimensionando uma máquina virtual, atualizar o host ou uma falha de hardware no host é alguns dos motivos que pode mover uma máquina virtual.

Se você tiver um aplicativo que precisa usar a letra da unidade d:, você pode reatribuir letras de unidade para que o disco temporário usa algo diferente de d:. Para obter instruções, consulte [alterar a letra da unidade do disco temporário Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra da unidade do disco temporário?

Você pode alterar a letra da unidade movendo o arquivo de página e reatribuição letras de unidade, mas você precisa certificar-se de que você siga as etapas em uma ordem específica. Para obter instruções, consulte [alterar a letra da unidade do disco temporário Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Pode adicionar uma máquina virtual existente a um conjunto de disponibilidade?

Não. Se quiser que sua máquina virtual seja parte de um conjunto de disponibilidade, você precisa criar a máquina virtual dentro do conjunto. Atualmente não há uma maneira de adicionar uma máquina virtual a uma disponibilidade definir depois que ela foi criada.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Pode carregar uma máquina virtual no Azure?

Sim. Para obter instruções, consulte [carregar uma imagem máquina virtual do Windows Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>Posso redimensionar o disco de sistema operacional?

Sim. Para obter instruções, consulte [como expandir a unidade do sistema operacional de uma máquina Virtual em um grupo de recursos do Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>É possível copiar ou clonar uma VM Azure existente?

Sim. Para obter instruções, consulte [como criar uma cópia de um computador de virtual do Windows no modelo de implantação do Gerenciador de recursos](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que não estou vendo Canadá Central e regiões Leste Canadá através do Gerenciador de recursos do Azure?

Duas novas regiões de Canadá Central e Oriental Canadá não são automaticamente registradas para a criação de máquina virtual para assinaturas do Azure existentes. Esse registro é feito automaticamente quando uma máquina virtual é implantada através do portal do Azure para qualquer outra região usando o Gerenciador de recursos do Azure. Depois que uma máquina virtual for implantada em qualquer outra região Azure, novas regiões devem estar disponíveis para subsequentes máquinas virtuais.

## <a name="does-azure-support-linux-vms"></a>Azure suporta VMs Linux?

Sim. Para criar rapidamente uma VM Linux para experimentar, consulte [criar uma máquina virtual de Linux no Azure usando o Portal](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma NIC para minha máquina virtual após sua criação?

Não. Adicionando uma NIC só pode ser feito no momento da criação.

## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome do computador?

Sim. O nome do computador pode ter no máximo 15 caracteres. Consulte [diretrizes de nomenclatura de infraestrutura](virtual-machines-windows-infrastructure-naming-guidelines.md) para obter mais informações em torno de nomenclatura seus recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de usuário ao criar uma máquina virtual?

Nomes de usuário podem ter no máximo 20 caracteres de comprimento e não pode terminar em um período ("."). 

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

As senhas devem ser 123 de 8 caracteres e atender 3 fora os seguintes requisitos de 4 complexidade:

- Ter caracteres inferiores
- Ter caracteres superiores
- Ter um dígito
- Ter um caractere especial (Regex coincidir [\W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">PA$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Senha!</td><td style="text-align:center">Senha1</td><td style="text-align:center">Password22</td><td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
