<properties
    pageTitle="Instalar um controlador de domínio do Active Directory de réplica no Azure | Microsoft Azure"
    description="Um tutorial que explica como instalar um controlador de domínio de uma floresta do Active Directory local em uma máquina virtual Azure."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar um controlador de domínio do Active Directory de réplica em uma rede virtual Azure

Este tópico mostra como instalar controladores de domínio adicionais (também conhecido como réplica controladores de domínio) para um domínio do Active Directory local no Azure VMs (máquinas virtuais) em uma rede virtual Azure.

Você também pode estar interessado nestes tópicos relacionados:

-  Opcionalmente, você pode instalar uma nova floresta do Active Directory em uma rede virtual Azure. Para essas etapas, consulte [instalar uma nova floresta do Active Directory em uma rede virtual Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Para obter orientação conceitual sobre como instalar os serviços de domínio Active Directory (AD DS) em uma rede virtual Azure, consulte [diretrizes para implantação do Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Diagrama de cenário

Neste cenário, os usuários externos precisam acessar aplicativos que são executados em servidores de domínio. As VMs que executam os servidores de aplicativo e controladores de domínio réplica são instaladas em uma rede virtual Azure. A rede virtual pode ser conectada à rede local por uma conexão de [-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) , conforme mostrado no diagrama a seguir, ou você pode usar [rota expressa](../expressroute/expressroute-locations-providers.md) para uma conexão mais rápido.

Os servidores de aplicativo e controladores de domínio são implantados em serviços de nuvem separada para distribuir o processamento de computação e dentro de [conjuntos de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md) para melhorar a tolerância.
Controladores de domínio duplicam uns com os outros e com controladores de domínio local usando replicação do Active Directory. Nenhuma ferramenta de sincronização é necessários.

![Controlador de domínio Active Directory do réplica em pf diagrama um vnet Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Criar um site do Active Directory para a rede virtual Azure

É uma boa ideia criar um site do Active Directory que representa a região de rede correspondente à rede virtual. Que ajuda a otimizar autenticação, replicação e outras operações de localização de DC. As etapas a seguir explicam como criar um site e para obter mais informações, consulte [Adicionando um novo Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra Serviços e Sites do Active Directory: **Gerenciador de servidor** > **Ferramentas** > **serviços e Sites do Active Directory**.
2. Criar um site para representar a região onde você criou uma rede virtual Azure: clique em **Sites** > **ação** > **novo site** > digite o nome do novo site, como o Azure conosco Oeste > Selecionar um link de site > **Okey**.
3. Criar uma sub-rede e associar o novo site: clique duas vezes em **Sites** > **sub-redes**de atalho > **nova sub-rede** > digite o intervalo de endereços IP da rede virtual (como 10.1.0.0/16 no diagrama de cenário) > Selecionar o novo site Azure > **Okey**.

## <a name="create-an-azure-virtual-network"></a>Criar uma rede virtual Azure

1. No [portal do Azure clássico](https://manage.windowsazure.com), clique em **novo** > **Serviços de rede** > **Rede Virtual** > valores**Personalizados criar** e usar o seguinte para concluir o assistente.

    Nesta página do assistente...  | Especificar esses valores
    ------------- | -------------
    **Detalhes de uma rede virtual**  | <p>Nome: Digite um nome para a rede virtual, como WestUSVNet.</p><p>Região: Escolha a região mais próxima.</p>
    **Conectividade VPN e DNS**  | <p>Servidores DNS: Especifique o nome e o endereço IP de um ou mais servidores DNS local.</p><p>Conectividade: Selecione **Configurar uma VPN to-site**.</p><p>Rede local: especificar uma nova rede local.</p><p>Se você estiver usando a rota expressa em vez de uma VPN, consulte [Configurar uma Conexão de rota expressa através de um provedor do Exchange](../expressroute/expressroute-locations-providers.md).</p>
    **Conectividade de-to-site**  | <p>Nome: Digite um nome para a rede local.</p><p>Endereço IP do dispositivo de VPN: especificar o endereço IP público do dispositivo que irão se conectar à rede virtual. O dispositivo VPN não pode ser localizado atrás de um NAT.</p><p>Endereço: Especifique os intervalos de endereços para sua rede local (como 192.168.0.0/16 no diagrama de cenário).</p>
    **Espaços de endereço de rede virtual**  | <p>Espaço de endereço: Especifique o intervalo de endereços IP para VMs que você deseja executar no Azure rede virtual (como 10.1.0.0/16 no diagrama de cenário). Este intervalo de endereços não pode sobrepor os intervalos de endereços da rede local.</p><p>Sub-redes: Especifique um nome e endereço de uma sub-rede para os servidores de aplicativo (como Frontend, 10.1.1.0/24) e para controladores de domínio (como back-end, 10.1.2.0/24).</p><p>Clique em **Adicionar sub-rede do gateway**.</p>

2. Em seguida, você irá configurar o gateway de rede virtual para criar uma conexão de VPN-to-site seguro. Consulte [Configurar um Gateway de rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obter as instruções.
3. Crie a conexão de VPN-to-site entre a nova rede virtual e um dispositivo VPN de local. Consulte [Configurar um Gateway de rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obter as instruções.


## <a name="create-azure-vms-for-the-dc-roles"></a>Criar VMs do Azure para as funções de DC

Repita estas etapas para criar VMs para hospedar a função de DC conforme necessário. Você deve implantar pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância. Se a rede virtual Azure inclui pelo menos dois controladores de domínio que estão configurados da mesma forma (ou seja, eles são os dois GCs, servidor DNS execução, e nenhum detém qualquer função FSMO e assim por diante) coloque as VMs que executam os controladores de domínio em uma disponibilidade definida para melhorar a tolerância.
Para criar VMs usando o Windows PowerShell em vez de interface do usuário, consulte [Usar o PowerShell para criar e configurar previamente máquinas virtuais baseadas no Windows Azure](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. No [portal do Azure clássico](https://manage.windowsazure.com), clique em **novo** > **Calcular** > **Máquina Virtual** > **Da Galeria**. Use os seguintes valores para concluir o assistente. Aceite o valor padrão para uma configuração, a menos que outro valor é sugerida ou obrigatório.

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

## <a name="install-ad-ds-on-azure-vms"></a>Instalar o AD DS em VMs Azure

Entrar em uma máquina virtual e verifique se você tem conectividade entre a conexão de VPN ou rota expressa-to-site para os recursos na sua rede local. Em seguida, instale o AD DS nas VMs Azure. Você pode usar o mesmo processo que você usa para instalar um DC adicional em sua rede local (interface do usuário, o Windows PowerShell ou um arquivo de resposta). Como instalar o AD DS, certifique-se de que você especifique o novo volume para o local do banco de dados AD, logs e SYSVOL. Se precisar de uma atualização sobre a instalação do AD DS, consulte [Instalar o Active Directory Domain Services (nível 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [instalar um controlador de domínio réplica Windows Server 2012 em um domínio existente (nível 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigurar o servidor DNS para a rede virtual

1. No [portal do Azure clássico](https://manage.windowsazure.com), clique no nome da rede virtual e clique na guia **Configurar** para [reconfigurar os endereços IP do servidor DNS para sua rede virtual](../virtual-network/virtual-networks-manage-dns-in-vnet.md) para usar os endereços IP estáticos atribuídos à réplica controladores de domínio em vez dos endereços IP dos servidores DNS local.

2. Para garantir que todas as réplica DC VMs em uma rede virtual são configurados com a usar os servidores DNS em uma rede virtual, clique em **máquinas virtuais**, clique na coluna status para cada máquina virtual e clique em **Reiniciar**. Aguarde até que a máquina virtual mostra **executando o** estado antes de tentar entrar.

## <a name="create-vms-for-application-servers"></a>Criar VMs para servidores de aplicativo

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

## <a name="additional-resources"></a>Recursos adicionais

-  [Diretrizes para implantar o servidor Active Directory do Windows Azure máquinas virtuais](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Como carregar controladores de domínio do Hyper-V locais existentes no Azure usando o PowerShell do Azure](http://support.microsoft.com/kb/2904015)
-  [Instalar uma nova floresta do Active Directory em uma rede virtual Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IT Pro IaaS: conceitos básicos (01) máquina Virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e conectividade entre locais](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlets de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
