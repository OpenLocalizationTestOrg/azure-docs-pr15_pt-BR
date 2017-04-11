<properties
    pageTitle="Instalar uma floresta do Active Directory em uma rede virtual Azure | Microsoft Azure"
    description="Um tutorial que explica como criar uma nova floresta do Active Directory em uma máquina virtual (VM) em uma rede Virtual do Azure."
    services="active-directory, virtual-network"
    keywords="do Active directory máquina virtual, instalar floresta do active directory, vídeos do active directory do azure "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Instalar uma nova floresta do Active Directory em uma rede virtual Azure

Este tópico mostra como criar um novo ambiente do Active Directory do Windows Server em uma rede virtual Azure em uma máquina virtual (VM) em uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md). Nesse caso, a rede virtual Azure não está conectada a uma rede local.

Você também pode estar interessado nestes tópicos relacionados:

- Para obter um vídeo que mostra essas etapas, consulte [como instalar uma nova floresta do Active Directory em uma rede virtual Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Opcionalmente, você pode [Configurar uma VPN to-site](../vpn-gateway/vpn-gateway-site-to-site-create.md) e, em seguida, instale uma nova floresta ou estender uma floresta local para uma rede virtual Azure. Para essas etapas, consulte [instalar um controlador de domínio réplica Active Directory em uma rede Virtual do Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Para obter orientação conceitual sobre como instalar os serviços de domínio Active Directory (AD DS) em uma rede virtual Azure, consulte [diretrizes para implantação do Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama de cenário

Neste cenário, os usuários externos precisam acessar aplicativos que são executados em servidores de domínio. VMs que executam os servidores de aplicativo e as VMs que são executadas controladores de domínio são instaladas em seu próprios serviço de nuvem em uma rede virtual Azure. Eles também são incluídos dentro de uma disponibilidade definida para melhorar a tolerância.

![Floresta do Active Directory em um máquinas virtuais em uma rede Virtual Azure ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>Como isso difere do local?

Não há muita diferença entre instalando um controlador de domínio no Azure versus local. As principais diferenças estão listadas na tabela a seguir.

Para configurar...  | Local  | Rede virtual Azure
------------- | -------------  | ------------
**Endereço IP do controlador de domínio**  | Atribuir endereço IP estático nas propriedades do adaptador de rede   | Execute o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático
**Resolução de cliente DNS**  | Definir o endereço do servidor DNS alternativo e preferencial na rede propriedades de adaptador de membros do domínio   | Configurar o endereço do servidor DNS nas propriedades da rede virtual
**Armazenamento de banco de dados do Active Directory**  | Opcionalmente, altere o local de armazenamento padrão de C:\  | Você precisa alterar o local padrão de armazenamento de C:\



## <a name="create-an-azure-virtual-network"></a>Criar uma rede virtual Azure

1. Entre portal do Azure clássico.
2. Crie uma rede virtual. Clique em **redes** > **criar uma rede virtual**. Use os valores da tabela a seguir para concluir o assistente.

    Nesta página do assistente...  | Especificar esses valores
    ------------- | -------------
    **Detalhes de uma rede virtual**  | <p>Nome: Digite um nome para sua rede virtual</p><p>Região: Escolha a região mais próxima</p>
    **VPN e DNS**  | <p>Deixe o servidor DNS em branco</p><p>Não selecione a opção VPN</p>
    **Espaços de endereço de rede virtual**  | <p>Nome de sub-rede: insira um nome para a sua sub-rede</p><p>IP inicial: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Criar VMs para executar o controlador de domínio e funções de servidor DNS

Repita estas etapas para criar VMs para hospedar a função de DC conforme necessário. Você deve implantar pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância. Se a rede virtual Azure inclui pelo menos dois controladores de domínio que estão configurados da mesma forma (ou seja, eles são os dois GCs, servidor DNS execução, e nenhum detém qualquer função FSMO e assim por diante) coloque as VMs que executam os controladores de domínio em uma disponibilidade definida para melhorar a tolerância.

Para criar VMs usando o Windows PowerShell em vez de interface do usuário, consulte [Usar o PowerShell para criar e configurar previamente máquinas virtuais baseadas no Windows Azure](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. No portal do clássico, clique em **novo** > **Calcular** > **Máquina Virtual** > **Da Galeria**. Use os seguintes valores para concluir o assistente. Aceite o valor padrão para uma configuração, a menos que outro valor é sugerida ou obrigatório.

    Nesta página do assistente...  | Especificar esses valores
    ------------- | -------------
    **Escolher uma imagem**  | Centro de dados do Windows Server 2012 R2
    **Configuração de máquina virtual**  | <p>Nome de máquina virtual: Digite um nome de rótulo único (como AzureDC1).</p><p>Novo nome de usuário: Digite o nome de um usuário. Esse usuário será um membro do grupo de administradores local na máquina virtual. Você precisará esse nome entrar para a máquina virtual pela primeira vez. A conta interna chamada administrador não funcionará.</p><p>Nova senha/confirmar: Digite uma senha</p>
    **Configuração de máquina virtual**  | <p>Serviço de nuvem: Escolha <b>criar um novo serviço de nuvem</b> para a máquina virtual primeira e selecione esse mesmo nome de serviço de nuvem quando você cria mais VMs que hospedarão a função DC.</p><p>Nome DNS do serviço de nuvem: Especifique um nome exclusivo</p><p>Região/grupo de afinidade/rede Virtual: especifique o nome de rede virtual (como WestUSVNet).</p><p>Conta de armazenamento: Escolha <b>usar uma conta de armazenamento gerado automaticamente</b> para a máquina virtual primeira e selecione esse nome de conta de armazenamento mesmo quando você cria mais VMs que hospedarão a função DC.</p><p>Conjunto de disponibilidade: Escolha <b>criar um conjunto de disponibilidade</b>.</p><p>Disponibilidade definir nome: digite um nome para a disponibilidade definido quando você cria a máquina virtual primeira e, em seguida, selecione mesmo nome quando você cria mais VMs.</p>
    **Configuração de máquina virtual**  | <p>Selecione <b>instalar o agente de máquina virtual</b> e quaisquer outras extensões que necessárias.</p>
2. Anexe um disco para cada máquina virtual que executará a função de servidor de DC. O disco adicional é necessária para armazenar o banco de dados, logs e SYSVOL AD. Especifique um tamanho para o disco (como 10 GB) e deixe a **Preferência de Cache de Host** definida em **Nenhum**. Para ver as etapas, veja [como anexar um disco de dados para uma máquina Virtual do Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Depois de entrar pela primeira vez para a máquina virtual, abra o **Gerenciador de servidor** > **Serviços de armazenamento de arquivos e** criar um volume no disco usando o NTFS.
4. Reserve um endereço IP estático para VMs que executarão a função de DC. Para reservar um endereço IP estático, baixe o Microsoft Web Platform Installer e [Instale o PowerShell do Azure](../powershell-install-configure.md) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    ' Get-AzureVM - ServiceName AzureDC1-nome AzureDC1 | Conjunto AzureStaticVNetIP - endereço IP 10.0.0.4 | Atualização AzureVM

Para obter mais informações sobre a configuração de um endereço IP estático, consulte [Configurar um endereço de IP estático interno para uma máquina virtual](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Instalar o Active Directory do Windows Server

Use a mesma rotina para [instalar o AD DS](https://technet.microsoft.com/library/jj574166.aspx) que você use local (ou seja, você pode usar a interface do usuário, um arquivo de resposta ou o Windows PowerShell). Você precisa fornecer credenciais de administrador para instalar uma nova floresta. Para especificar o local para o banco de dados do Active Directory, logs e SYSVOL, altere o local de armazenamento padrão de unidade do sistema operacional para o disco de dados adicionais que você anexou para a máquina virtual.

Após concluir a instalação de DC, conectar-se para a máquina virtual novamente e faça logon no DC. Lembre-se especifique as credenciais de domínio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Redefinir o servidor DNS para a rede virtual Azure

1. Redefina a configuração de encaminhadores DNS no novo servidor DNS/DC.
  1. No Gerenciador do servidor, clique em **Ferramentas** > **DNS**.
  2. No **Gerenciador DNS**, clique com botão direito no nome do servidor DNS e clique em **Propriedades**.
  3. Na guia **encaminhadores** , clique no endereço IP do encaminhador e clique em **Editar**.  Selecione o endereço IP e clique em **Excluir**.
  4. Clique em **Okey** para fechar o editor e **Okey** novamente para fechar as propriedades do servidor DNS.
2. Atualize a configuração de servidor DNS para a rede virtual.
  1. Clique em **Redes virtuais** > duas vezes na rede virtual que você criou > **Configurar** > **servidores DNS**, digite o nome e o DIP de uma das VMs que executa a função de servidor DNS/DC e clique em **Salvar**.
  2. Selecione a máquina virtual e clique em **Reiniciar** para disparar a máquina virtual para definir as configurações de resolução DNS com o endereço IP do novo servidor DNS.


## <a name="create-vms-for-domain-members"></a>Criar VMs para membros do domínio

1. Repita estas etapas para criar VMs para executar como servidores de aplicativo. Aceite o valor padrão para uma configuração, a menos que outro valor é sugerida ou obrigatório.

    Nesta página do assistente...  | Especificar esses valores
    ------------- | -------------
    **Escolher uma imagem**  | Centro de dados do Windows Server 2012 R2
    **Configuração de máquina virtual**  | <p>Nome de máquina virtual: Digite um nome de rótulo único (como AppServer1).</p><p>Novo nome de usuário: Digite o nome de um usuário. Esse usuário será um membro do grupo de administradores local na máquina virtual. Você precisará esse nome entrar para a máquina virtual pela primeira vez. A conta interna chamada administrador não funcionará.</p><p>Nova senha/confirmar: Digite uma senha</p>
    **Configuração de máquina virtual**  | <p>Serviço de nuvem: Escolha **criar um novo serviço de nuvem** para a máquina virtual primeira e selecione esse mesmo nome de serviço de nuvem quando você cria mais VMs que hospedarão o aplicativo.</p><p>Nome DNS do serviço de nuvem: Especifique um nome exclusivo</p><p>Região/grupo de afinidade/rede Virtual: especifique o nome de rede virtual (como WestUSVNet).</p><p>Conta de armazenamento: Escolha **usar uma conta de armazenamento gerado automaticamente** para a máquina virtual primeira e selecione esse nome de conta de armazenamento mesmo quando você cria mais VMs que hospedarão o aplicativo.</p><p>Conjunto de disponibilidade: Escolha **criar um conjunto de disponibilidade**.</p><p>Disponibilidade definir nome: digite um nome para a disponibilidade definido quando você cria a máquina virtual primeira e, em seguida, selecione mesmo nome quando você cria mais VMs.</p>
    **Configuração de máquina virtual**  | <p>Selecione <b>instalar o agente de máquina virtual</b> e quaisquer outras extensões que necessárias.</p>
2. Depois de cada máquina virtual está provisionado, entrar e ingressar no domínio. No **Gerenciador de servidor**, clique em **Servidor Local** > **grupo de trabalho** > **alterar...** Selecione o **domínio** e digite o nome do seu domínio local. Fornecer credenciais de um usuário de domínio e reinicie a máquina virtual para concluir a junção de domínio.

Para criar VMs usando o Windows PowerShell em vez de interface do usuário, consulte [Usar o PowerShell para criar e configurar previamente máquinas virtuais baseadas no Windows Azure](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Para obter mais informações sobre como usar o Windows PowerShell, consulte [Referência de Cmdlet do Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)e [Começar a usar Cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) .


## <a name="see-also"></a>Consulte também

-  [Como instalar uma nova floresta do Active Directory em uma rede virtual Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Diretrizes para implantar o servidor Active Directory do Windows Azure máquinas virtuais](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurar uma VPN to-Site](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Instalar um controlador de domínio Active Directory réplica em uma rede virtual Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS: conceitos básicos (01) máquina Virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e conectividade entre locais](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Visão geral de rede virtual](../virtual-network/virtual-networks-overview.md)
-  [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Referência de cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Definir o endereço IP estático de máquina virtual Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Como atribuir IP estático para máquina virtual do Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Instalar uma nova floresta do Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introdução ao Active Directory Domain Services (AD DS) virtualização (nível 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
