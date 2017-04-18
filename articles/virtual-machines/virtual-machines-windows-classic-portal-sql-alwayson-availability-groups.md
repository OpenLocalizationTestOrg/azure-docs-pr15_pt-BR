<properties
    pageTitle="Configurar sempre no grupo de disponibilidade na máquina virtual do Azure - clássico"
    description="Crie um grupo de disponibilidade sempre com Azure máquinas virtuais. Este tutorial usa principalmente as ferramentas e a interface do usuário em vez de script."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configurar sempre no grupo de disponibilidade na máquina virtual do Azure - clássico

> [AZURE.SELECTOR]
- [Gerenciador de recursos: modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gerenciador de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clássico: interface do usuário](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este tutorial de ponta a ponta mostra como implementar grupos de disponibilidade usando o SQL Server sempre em execução no Azure máquinas virtuais.

No fim do tutorial, sua solução de SQL Server sempre ativada no Azure consiste nos seguintes elementos:

- Uma rede virtual que contém várias sub-redes, incluindo um front-end e uma sub-rede de back-end

- Um controlador de domínio com um domínio do Active Directory (AD)

- Duas VMs implantado à sub-rede back-end e ingressar no domínio do AD do servidor SQL

- Um cluster WSFC 3 nós com o modelo de quorum maioria de nós

- Um grupo de disponibilidade com duas réplicas síncrono confirmação de um banco de dados de disponibilidade

A figura a seguir é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para AG no Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Observe que se trata de uma das configurações possíveis. Por exemplo, você pode minimizar o número de VMs para um grupo de disponibilidade de dois réplica para salvar em horas de computação no Azure usando o controlador de domínio como a testemunha de compartilhamento de arquivo de quorum em um cluster WSFC de 2 nós. Esse método reduz a contagem de máquina virtual por uma na configuração da acima.

Este tutorial assume o seguinte:

- Você já tem uma conta do Azure.

- Você já sabe como provisionar uma máquina virtual Server do clássico SQL da Galeria de máquina virtual usando a interface gráfica.

- Você já tem uma compreensão sólida de sempre em grupos de disponibilidade. Para obter mais informações, consulte [Sempre em grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se você estiver interessado em usar sempre em grupos de disponibilidade com o SharePoint, consulte também [Configurar SQL Server 2012 sempre diante disponibilidade grupos do SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Criar a rede Virtual e o servidor do controlador de domínio

Você começa com uma nova conta de avaliação Azure. Depois de terminar a configuração de conta, você deve estar na tela inicial do portal clássico do Azure.

1. Clique no botão de **novo** no canto inferior esquerdo da página, conforme mostrado abaixo.

    ![Clique em novo no portal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. Clique em **Serviços de rede**, e em seguida, clique em **Rede Virtual** e clique em **Criar personalizada**, conforme mostrado abaixo.

    ![Criar rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. Na caixa de diálogo **Criar uma rede VIRTUAL** , crie uma nova rede virtual percorrendo as páginas com as configurações abaixo. 

  	|Página|Configurações|
|---|---|
|Detalhes de uma rede virtual|**NOME = ContosoNET**<br/>**REGIÃO = Oeste EUA**|
|Servidores DNS e conectividade VPN|Nenhum|
|Espaços de endereço de rede virtual|Configurações são mostradas na captura de tela abaixo: ![Criar rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. Em seguida, crie a máquina virtual será usada como o controlador de domínio (DC). Clique em **novo** novamente, e em seguida, **Calcular**, e em seguida, **Máquina Virtual**e, em seguida, **Na Galeria**, conforme mostrado abaixo.

    ![Criar uma máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. Na caixa de diálogo **criar uma máquina VIRTUAL** , configure uma nova VM percorrendo as páginas com as configurações abaixo. 

  	|Página|Configurações|
|---|---|
|Selecione o sistema operacional de máquina virtual|Centro de dados do Windows Server 2012 R2|
|Configuração de máquina virtual|**Data do lançamento de versão** = (mais recente)<br/>**Nome de máquina VIRTUAL** = ContosoDC<br/>**Nível** = padrão<br/>**Tamanho** = A2 (2 cores)<br/>**Novo nome de usuário** = AzureAdmin<br/>**Nova senha** = Contoso! 000<br/>**Confirmar** = Contoso! 000|
|Configuração de máquina virtual|**Serviço de nuvem** = criar um novo serviço de nuvem<br/>**Nome de DNS de serviço de nuvem** = um nome de serviço de nuvem exclusivo<br/>**Nome DNS** = um nome exclusivo (ex: ContosoDC123)<br/>**Região/grupo de AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**Sub-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = usar uma conta de armazenamento gerado automaticamente<br/>**Disponibilidade definida** = (nenhum)|
|Opções de máquina virtual|Usar os padrões|

Depois que terminar de configurar a máquina virtual novo, aguarde a máquina virtual seja provsioned. Esse processo leva algum tempo para ser concluída, e se você clicar para a guia de **Máquina Virtual** no portal de clássico do Azure, você pode ver ContosoDC nos Estados ciclo de **inicial (provisionamento)** **parado**, **Iniciando**, **executando (provisionamento)**e por fim **em execução**.

O servidor de DC agora está provisionado com êxito. Em seguida, você irá configurar o domínio do Active Directory neste servidor DC.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio

Nas etapas a seguir, você pode configurar a máquina ContosoDC como controlador de domínio para corp.contoso.com.

1. No portal do, selecione a máquina **ContosoDC** . Na guia **Painel de controle** , clique em **Conectar** para abrir um arquivo RDP para acesso de desktop remoto.

    ![Conectar-se a máquina Vritual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. Fazer logon com seu configurado a conta de administrador (**\AzureAdmin**) e senha (**Contoso! 000**).

1. Por padrão, o painel de controle do **Gerenciador de servidor** deve ser exibido.

1. Clique no link de **recursos e adicionar funções** no painel.

    ![Server Explorer adicionar funções](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. Selecione **Avançar** até chegar à seção **Funções de servidor** .

1. Selecione as funções de **Serviços de domínio do Active Directory** e **Servidor DNS** . Quando solicitado, adicione quaisquer recursos adicionais exigidos por essas funções.

    >[AZURE.NOTE] Você receberá uma aviso de que não há nenhum endereço IP estático de validação. Se você estiver testando a configuração, clique em continuar. Para produção cenários [usar o PowerShell para definir o endereço IP estático da máquina de controlador de domínio](./virtual-network/virtual-networks-reserved-private-ip.md).

    ![Adicionar caixa de diálogo de funções](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. Clique em **Avançar** até chegar à seção de **confirmação** . Selecione a caixa de seleção **reiniciar o servidor de destino automaticamente se necessário** .

1. Clique em **instalar**.

1. Depois que os recursos de concluir a instalação, retorne ao painel **Gerenciador do servidor** .

1. Selecione a nova opção de **AD DS** no painel à esquerda.

1. Clique no link **mais** na barra de aviso amarelo.

    ![Caixa de diálogo do AD DS na máquina virtual do servidor DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. Na coluna **ação** da caixa de diálogo **Todos os detalhes de tarefa do servidor** , clique em **promover este servidor a controlador de domínio**.

1. No **Assistente de configuração dos serviços do Active Directory Domain**, use os seguintes valores:

  	|Página|Configuração|
|---|---|
|Configuração de implantação|**Adicionar uma nova floresta** = selecionado<br/>**Nome do domínio raiz** = corp.contoso.com|
|Opções de controlador de domínio|**Senha** = Contoso! 000<br/>**Confirmar senha** = Contoso! 000|

1. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **De verificação de pré-requisitos** , verifique se que você veja a seguinte mensagem: **todas as verificações de pré-requisito passaram com êxito**. Observe que você deve examinar as mensagens de aviso aplicável, mas é possível continuar com a instalação.

1. Clique em **instalar**. Máquina virtual **ContosoDC** será inicializado automaticamente.

## <a name="configure-domain-accounts"></a>Configurar contas de domínio

As próximas etapas configuram as contas do Active Directory (AD) para uso posterior.

1. Faça logon novamente na máquina **ContosoDC** .

1. No **Gerenciador de servidor** , selecione **Ferramentas** e clique em **Central Administrativa do Active Directory**.

    ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. Na **Central Administrativa do Active Directory** selecione **corp (local)** no painel esquerdo.

1. No painel de **tarefas** à direita, selecione **novo** e clique em **usuário**. Use as seguintes configurações:

  	|Configuração|Valor|
|---|---|
|**Nome**|Instalar|
|**SamAccountName do usuário**|Instalar|
|**Senha**|Contoso! 000|
|**Confirmar senha**|Contoso! 000|
|**Outras opções de senha**|Selecionada|
|**Senha nunca expira**|Marcada|

1. Clique em **Okey** para criar o usuário **instalar** . Essa conta será usada para configurar o cluster de failover e o grupo de disponibilidade.

1. Crie dois usuários adicionais com as mesmas etapas: **CORP\SQLSvc1** e **CORP\SQLSvc2**. Essas contas serão usadas para as instâncias do SQL Server. Em seguida, você precisa dar **CORP\Install** as permissões necessárias para configurar o agrupamento de Failover de serviço do Windows (WSFC).

1. No **Central Administrativa do Active Directory**, selecione **corp (local)** no painel esquerdo. No painel de **tarefas** à direita, clique em **Propriedades**.

    ![Propriedades do usuário CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. Selecione **extensões**e clique no botão **Avançado** na guia **segurança** .

1. Na caixa de diálogo **Configurações de segurança avançadas para corp** . Clique em **Adicionar**.

1. Clique em **Selecionar uma entidade de segurança**. Procure por **CORP\Install**. Clique em **Okey**.

1. Selecione as permissões de **objetos de computador criar** e **Ler todas as propriedades** .

    ![Permissões de usuário corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. Clique **Okey**e clique em **Okey** novamente. Feche a janela de propriedades de corp.

Agora que você concluiu a configuração do Active Directory e os objetos de usuário, você criará três VMs do SQL Server e associá-los para este domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs do SQL Server

Em seguida, crie três VMs, incluindo um nó de cluster WSFC e duas VMs do SQL Server. Para criar cada uma das VMs, volte para o portal do Azure clássico, clique em **novo**, **Calcular**, **Máquina Virtual**e, em seguida, **Na Galeria**. Em seguida, use os modelos na tabela a seguir para ajudá-lo a criar VMs.

|Página|VM1|VM2|VM3|
|---|---|---|---|
|Selecione o sistema operacional de máquina virtual|**Centro de dados do Windows Server 2012 R2**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuração de máquina virtual|**Data do lançamento de versão** = (mais recente)<br/>**Nome de máquina VIRTUAL** = ContosoWSFCNode<br/>**Nível** = padrão<br/>**Tamanho** = A2 (2 cores)<br/>**Novo nome de usuário** = AzureAdmin<br/>**Nova senha** = Contoso! 000<br/>**Confirmar** = Contoso! 000|**Data do lançamento de versão** = (mais recente)<br/>**Nome de máquina VIRTUAL** = ContosoSQL1<br/>**Nível** = padrão<br/>**Tamanho** = A3 (4 cores)<br/>**Novo nome de usuário** = AzureAdmin<br/>**Nova senha** = Contoso! 000<br/>**Confirmar** = Contoso! 000|**Data do lançamento de versão** = (mais recente)<br/>**Nome de máquina VIRTUAL** = ContosoSQL2<br/>**Nível** = padrão<br/>**Tamanho** = A3 (4 cores)<br/>**Novo nome de usuário** = AzureAdmin<br/>**Nova senha** = Contoso! 000<br/>**Confirmar** = Contoso! 000|
|Configuração de máquina virtual|**Serviço de nuvem** = nome DNS de serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**Região/grupo de AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**Sub-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = usar uma conta de armazenamento gerado automaticamente<br/>**Disponibilidade definida** = criar uma disponibilidade definido<br/>**Nome do conjunto de disponibilidade** = SQLHADR|**Serviço de nuvem** = nome DNS de serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**Região/grupo de AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**Sub-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = usar uma conta de armazenamento gerado automaticamente<br/>**Disponibilidade definida** = SQLHADR (você também pode configurar a disponibilidade definida após a máquina tiver sido criada. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade de SQLHADR.)|**Serviço de nuvem** = nome DNS de serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**Região/grupo de AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**Sub-redes da rede VIRTUAL** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = usar uma conta de armazenamento gerado automaticamente<br/>**Disponibilidade definida** = SQLHADR (você também pode configurar a disponibilidade definida após a máquina tiver sido criada. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade de SQLHADR.)|
|Opções de máquina virtual|Usar os padrões|Usar os padrões|Usar os padrões|

<br/>

>[AZURE.NOTE] A configuração anterior sugere máquinas virtuais de nível padrão, como máquinas de nível básico não dão suporte a pontos de extremidade balanceamento de carga necessários para criar mais tarde um ouvintes de grupo de disponibilidade. Além disso, os tamanhos de máquina sugeridos aqui destinam-se para testar disponibilidade grupos em VMs do Azure. Para o melhor desempenho em cargas de trabalho de produção, consulte as recomendações para a configuração em [práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)e tamanhos de máquina do SQL Server.

Depois que as três VMs totalmente são provisionadas, é necessário associá-los ao domínio **corp.contoso.com** e conceder CORP\Install direitos administrativos para os computadores. Para fazer isso, use as etapas a seguir para cada um dos três VMs.

1. Primeiro, altere o endereço do servidor DNS preferencial. Comece fazendo download remoto da área de trabalho (RDP) do arquivo da cada VM para seu diretório local selecionando a máquina virtual na lista e clicando no botão **Conectar** . Para selecionar uma máquina virtual, clique em qualquer lugar mas a primeira célula na linha, conforme mostrado abaixo.

    ![Baixar o arquivo RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. Inicie o arquivo RDP que você baixou e faça logon na máquina virtual usando seu configurado a conta de administrador (**BUILTIN\AzureAdmin**) e senha (**Contoso! 000**).

1. Quando você estiver conectado, você deverá ver o painel de controle do **Gerenciador de servidor** . No painel esquerdo, clique em **Servidor Local** .

1. Selecione o link de **endereço IPv4 atribuído pelo DHCP, IPv6 ativado** .

1. Na janela **Conexões de rede** , selecione o ícone de rede.

    ![Alterar o servidor DNS preferencial da máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. Na barra de comandos, clique em **alterar as configurações desta conexão** (dependendo do tamanho da janela, você talvez precise clicar na seta dupla à direita para ver esse comando).

1. Selecione **Internet Protocol versão 4 (TCP/IPv4)** e clique em Propriedades.

1. Selecione usar o seguinte servidor DNS endereços e especifique **10.10.2.4** no **servidor DNS preferencial**.

1. O endereço **10.10.2.4** é o endereço atribuído a uma máquina virtual na sub-rede 10.10.2.0/24 em uma rede virtual Azure e essa máquina virtual é **ContosoDC**. Para verificar o endereço IP **ContosoDC**do, use o **nslookup contosodc** no prompt de comando, conforme mostrado abaixo.

    ![Use NSLOOKUP para localizar o endereço IP de DC](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. Clique O**K** e **Fechar** para confirmar as alterações. Agora você está consiga ingressar a máquina virtual para **corp.contoso.com**.

1. Novamente na janela de **Servidor Local** , clique no link de **grupo de trabalho** .

1. Na seção **Nome do computador** , clique em **Alterar**.

1. Marque a caixa de seleção de **domínio** e digite **corp.contoso.com** na caixa de texto. Clique em **Okey**.

1. Na caixa de diálogo de pop-up **Segurança do Windows** , especifique as credenciais para a conta de administrador do domínio padrão (**CORP\AzureAdmin**) e a senha (**Contoso! 000**).

1. Quando você vir a mensagem "Bem-vindo ao domínio corp.contoso.com", clique em **Okey**.

1. Clique em **Fechar**e, em seguida, clique em **Reiniciar agora** na caixa de diálogo pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Adicione o usuário Corp\Install como administrador em cada máquina virtual:

1. Aguarde até que a máquina virtual seja reiniciada e, em seguida, inicie o arquivo RDP novamente para fazer login na máquina virtual usando a conta **BUILTIN\AzureAdmin** .

1. No **Gerenciador de servidor** selecione **Ferramentas**e, em seguida, clique em **Gerenciamento de computador**.

    ![Gerenciamento do computador](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. Na janela **Gerenciamento do computador** , expanda **usuários e grupos locais**e **grupos**.

1. Clique duas vezes no grupo de **administradores** .

1. Na caixa de diálogo **Propriedades de administradores** , clique no botão **Adicionar** .

1. Insira o usuário **CORP\Install**e clique em **Okey**. Quando solicitado para credenciais, use a conta de **AzureAdmin** com o **Contoso! 000** senha.

1. Clique **Okey** para fechar a caixa de diálogo de **Propriedades de administrador** .

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Adicione o recurso **Cluster de Failover** para cada máquina virtual.

1. No painel **Server Manager** , clique em **recursos e adicionar funções**.

1. No **Assistente de recursos e adicionar funções**, clique em **Avançar** até chegar à página de **recursos** .

1. Selecione o **agrupamento de Failover**. Quando solicitado, adicione quaisquer outros recursos dependentes.

    ![Adicionar o recurso de cluster para máquina virtual de Failover](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. Clique em **Avançar**e, em seguida, clique em **instalar** na página **confirmação** .

1. Quando a instalação do recurso **Cluster de Failover** for concluída, clique em **Fechar**.

1. Faça logout da máquina virtual.

1. Repita as etapas desta seção para todos os servidores de três-- **ContosoWSFCNode**, **ContosoSQL1**e **ContosoSQL2**.

SQL Server VMs agora são provisionadas e em execução, mas eles são instalados com o SQL Server com opções padrão.

## <a name="create-the-wsfc-cluster"></a>Criar o Cluster WSFC

Nesta seção, você criar o cluster WSFC que hospedará o grupo de disponibilidade que você criará posteriormente. Agora, você deve ter feito o seguinte para cada uma das três VMs você usará no cluster WSFC:

- Totalmente provisionado no Azure

- Unidos máquina virtual para o domínio

- Adicionado **CORP\Install** ao grupo de administradores local

- Adicionou o recurso cluster de Failover

Todos esses são pré-requisitos em cada máquina virtual antes de você pode associá-la ao cluster WSFC.

Além disso, observe que a rede virtual Azure não se comportar da mesma maneira como uma rede local. Você precisa criar o cluster na seguinte ordem:

1. Crie um cluster de nó único em um de nós (**ContosoSQL1**).

1. Modificar o endereço IP do cluster para um endereço IP não utilizado (**10.10.2.101**).

1. Exiba o nome do cluster online.

1. Adicione outros nós (**ContosoSQL2** e **ContosoWSFCNode**).

Siga as etapas abaixo para realizar estas tarefas que totalmente configura o cluster.

1. Abra o arquivo RDP para **ContosoSQL1** e faça logon usando a conta de domínio **CORP\Install**.

1. No painel de tarefas do **Gerenciador de servidor** , selecione **Ferramentas**e, em seguida, clique em **Gerenciador de Cluster de Failover**.

1. No painel esquerdo, clique com botão direito **Gerenciador de Cluster de Failover**e clique em **criar um Cluster**, conforme mostrado abaixo.

    ![Criar Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. No Assistente de Cluster criar, crie um cluster de um nó percorrendo as páginas com as configurações abaixo:

  	|Página|Configurações|
|---|---|
|Antes de começar|Usar os padrões|
|Selecione servidores|Digite **ContosoSQL1** **Digite o nome do servidor** e clique em **Adicionar**|
|Aviso de validação|Selecione **n º eu não precisar de suporte da Microsoft para este cluster e, portanto, não deseja executar os testes de validação. Quando eu clicar em Avançar, continue a criar o cluster**.|
|Ponto de acesso para administrar o Cluster|Tipo **Cluster1** em **nome de Cluster**|
|Confirmação|Use os padrões, a menos que você estiver usando espaços de armazenamento. Consulte a observação após esta tabela.|

    >[AZURE.WARNING] Se você estiver usando [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739), que agrupa vários discos em pools de armazenamento, você deve desmarcar a caixa de seleção **Adicionar todo o armazenamento qualificado para o cluster** na página **confirmação** . Se você não desmarcar esta opção, os discos virtuais serão desanexados durante o processo de agrupamento. Como resultado, eles também não aparecerão no Gerenciador de disco ou Explorer até que os espaços de armazenamento são removidos do cluster e reconectado usando o PowerShell.

1. No painel esquerdo, expanda o **Gerenciador de Cluster de Failover**e clique em **Cluster1.corp.contoso.com**.

1. No painel central, role para baixo até a seção **Cluster principais recursos** e expandir a **nome: Clutser1** detalhes. Você deve ver o **nome** e os recursos de **Endereço IP** no estado **Falha** . O recurso de endereço IP não pode ser colocado online porque o cluster é atribuído o mesmo endereço IP que da máquina em si, que é um endereço duplicado.

1. O recurso de **Endereço IP** com falha de atalho e, em seguida, clique em **Propriedades**.

    ![Propriedades do cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. Selecione o **Endereço IP estático** e especifique **10.10.2.101** na caixa de texto endereço. Em seguida, clique em **Okey**.

1. Na seção **Recursos principais de Cluster** , clique com botão direito **nome: Cluster1** e clique em **Colocar on-line**. Em seguida, aguarde até que os dois recursos estão online. Quando o recurso de nome de cluster trata online, ele atualiza o servidor de DC com uma nova conta de computador do AD. Essa conta AD será usada para executar o serviço de grupo agrupado de disponibilidade mais tarde.

1. Finalmente, adicione os nós restantes ao cluster. Na árvore do navegador, clique com botão direito **Cluster1.corp.contoso.com** e clique em **Adicionar nó**, conforme mostrado abaixo.

    ![Adicionar o nó ao Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. No **Assistente para adicionar nó**, clique em **Avançar**. Na página **Selecionar servidores** , adicione **ContosoSQL2** e **ContosoWSFCNode** à lista digitando o nome do servidor em **Digite o nome do servidor** e, em seguida, clicando em **Adicionar**. Quando terminar, clique em **Avançar**.

1. Na página de **Aviso de validação** , clique em **não** (em um cenário de produção, você deve executar os testes de validação). Em seguida, clique em **Avançar**.

1. Na página **confirmação** , clique em **Avançar** para adicionar os nós.

    >[AZURE.WARNING] Se você estiver usando [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739), que agrupa vários discos em pools de armazenamento, você deve desmarcar a caixa de seleção **Adicionar todo o armazenamento qualificado para o cluster** . Se você não desmarcar esta opção, os discos virtuais serão desanexados durante o processo de agrupamento. Como resultado, eles também não aparecerão no Gerenciador de disco ou Explorer até que os espaços de armazenamento são removidos do cluster e reconectado usando o PowerShell.

1. Depois que os nós são adicionados ao cluster, clique em **Concluir**. Gerenciador de Cluster de failover agora deve mostrar que o seu cluster tem três nós e listá-las no contêiner de **nós** .

1. Faça logout da sessão de área de trabalho remota.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Preparar as instâncias do SQL Server para o grupo de disponibilidade

Nesta seção, você irá fazer o seguinte no **ContosoSQL1** e **contosoSQL2**:

- Adicionar um logon para o **Sistema/Autoridade NT** com um conjunto a instância do SQL Server padrão de permissões necessárias

- Adicionar **CORP\Install** como uma função de administrador do sistema a instância do SQL Server padrão

- Abra o firewall para acesso remoto do SQL Server

- Habilitar o recurso de sempre em grupos de disponibilidade

- Alterar a conta de serviço do SQL Server para **CORP\SQLSvc1** e **CORP\SQLSvc2**, respectivamente

Essas ações podem ser executadas em qualquer ordem. No entanto, as etapas a seguir guiará-las em ordem. Siga as etapas para **ContosoSQL1** e **ContosoSQL2**:

1. Se você não tiver conectado fora a sessão de área de trabalho remota para a máquina virtual, faça isso agora.

1. Iniciar os arquivos RDP para **ContosoSQL1** e **ContosoSQL2** e faça logon como **BUILTIN\AzureAdmin**.

1. Primeiro, adicione **NT\Sistema** para o logon do SQL Server e com as permissões necessárias. Inicie o **SQL Server Management Studio**.

1. Clique em **Conectar** para se conectar à instância do SQL Server padrão.

1. No **Pesquisador de objetos**, expanda **segurança**e, em seguida, expanda **logon**.

1. O logon do **Sistema/Autoridade NT** de atalho e clique em **Propriedades**.

1. Na página **protegíveis** , para o servidor local, selecione **conceder** para as seguintes permissões e clique em **Okey**.

    - Alterar qualquer grupo de disponibilidade

    - Conectar SQL

    - Exibir estado do servidor

1. Em seguida, adicione **CORP\Install** como uma função de **administrador do sistema** a instância do SQL Server padrão. No **Pesquisador de objetos**, clique com botão direito **logon** e clique em **Novo Login**.

1. Digite **CORP\Install** **nome de Login**.

1. Na página de **Funções de servidor** , selecione **administrador do sistema**. Em seguida, clique em **Okey**. Depois de criado o logon, você pode ver-expandindo **logon** no **Pesquisador de objetos**.

1. Em seguida, você criar uma regra de firewall para o SQL Server. Na tela **Iniciar** , abra o **Firewall do Windows com segurança avançada**.

1. No painel esquerdo, selecione **Regras de entrada**. No painel direito, clique em **Nova regra**.

1. Na página **Tipo de regra** , selecione o **programa**e clique em **Avançar**.

1. Na página de **programa** , selecione **Este caminho de programa** e digite %ProgramFiles%\Microsoft **Server\MSSQL12 de SQL. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** na caixa de texto (se você está seguindo estas instruções, mas usando o SQL Server 2012, o diretório do SQL Server é **MSSQL11. MSSQLSERVER**). Clique em **Avançar**.

1. Na página **ação** , manter **Permitir a conexão** selecionada e clique em **Avançar**.

1. Na página de **perfil** , aceite as configurações padrão e clique em **Avançar**.

1. Na página **nome** , especifique um nome de regra, como o **SQL Server (regra de programa)** na caixa de texto **nome** , clique em **Concluir**.

1. Em seguida, você habilitar o recurso de **Sempre em grupos de disponibilidade** . Na tela **Iniciar** , abra o **Gerenciador de configuração do SQL Server**.

1. Na árvore do navegador, clique em **Serviços do SQL Server**, e em seguida, clique com botão direito no serviço do **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.

1. Clique na guia **Sempre em alta disponibilidade** , selecione **Permitir sempre em grupos de disponibilidade**, conforme mostrado abaixo e clique em **Aplicar**. Clique em **Okey** na caixa de diálogo pop-up e não fechar a janela Propriedades. Você irá reiniciar o serviço SQL Server depois que você alterar a conta de serviço.

    ![Sempre habilitar em grupos de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. Em seguida, você pode alterar a conta de serviço do SQL Server. Clique **Na** guia logon, e em seguida, digite **CORP\SQLSvc1** (para **ContosoSQL1**) ou **CORP\SQLSvc2** (para **ContosoSQL2**) em **Nome da conta**, e em seguida, preencha e confirme a senha e clique em **Okey**.

1. Na janela pop-up, clique em **Sim** para reiniciar o serviço SQL Server. Depois que o serviço SQL Server for reiniciado, as alterações feitas na janela Propriedades são eficazes.

1. Faça logout do VMs.

## <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade

Agora você está pronto para configurar um grupo de disponibilidade. Abaixo está uma estrutura de tópicos do que você vai fazer:

- Criar um novo banco de dados (**MyDB1**) em **ContosoSQL1**

- Fazer um backup total e um backup de log de transação do banco de dados

- Restaurar completo e faça logon backups **ContosoSQL2** com a opção **NORECOVERY**

- Criar o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e legíveis réplicas secundárias

### <a name="create-the-mydb1-database-on-contososql1"></a>Crie o banco de dados MyDB1 em ContosoSQL1:

1. Se você não já fez fora as sessões de área de trabalho remotas para **ContosoSQL1** e **ContosoSQL2**, faça isso agora.

1. Abra o arquivo RDP para **ContosoSQL1** e faça logon como **CORP\Install**.

1. No **Explorador de arquivos**, em * *c:\**, crie um diretório chamado * *backup**. Você usará esse uso de diretório para fazer backup e restaurar o banco de dados.

1. A nova pasta de atalho, aponte para **compartilhar com**e clique em **pessoas específicas**, conforme mostrado abaixo.

    ![Criar uma pasta de Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. Adicionar **CORP\SQLSvc1** e dar a ele a permissão de **Leitura/gravação** , e em seguida, adicione **CORP\SQLSvc2** e dar a ele a permissão de **leitura** , conforme mostrado abaixo e, em seguida, clique em **compartilhar**. Quando o processo de compartilhamento de arquivos for concluído, clique em **concluído**.

    ![Conceder permissões para a pasta de Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. Em seguida, crie o banco de dados. No menu **Iniciar** , inicie o **SQL Server Management Studio**e clique em **Conectar** para se conectar à instância do SQL Server padrão.

1. No **Pesquisador de objetos**, clique com botão direito **bancos de dados** e clique em **Novo banco de dados**.

1. Em **nome do banco de dados**, digite **MyDB1**clique **Okey**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Faça uma completa backup de MyDB1 e restaurá-los no ContosoSQL2:

1. Em seguida, você levar completo backup do banco de dados. No **Pesquisador de objetos**, expanda **bancos de dados**, e em seguida, clique com botão direito **MyDB1**, e em seguida, aponte para **tarefas**e clique em **Fazer backup**.

1. Na seção **origem** , mantenha o **tipo de Backup** definido como **completo**. Na seção de **destino** , clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.

1. Na seção de **destino** , clique em **Adicionar**.

1. Na caixa de texto **nome do arquivo** , digite ** \\ContosoSQL1\backup\MyDB1.bak**. Em seguida, clique em **Okey**e clique em **Okey** novamente para fazer backup do banco de dados. Quando a operação de backup for concluído, clique em **Okey** novamente para fechar a caixa de diálogo.

1. Em seguida, você criar um log de transação backup do banco de dados. No **Pesquisador de objetos**, expanda **bancos de dados**, e em seguida, clique com botão direito **MyDB1**, e em seguida, aponte para **tarefas**e clique em **Fazer backup**.

1. Em tipo de **Backup** , selecione **Log de transação**. Manter o caminho do arquivo de **destino** definido para aquele que você especificou anteriormente e clique em **Okey**. Uma vez concluída a operação de backup, clique em **Okey** novamente.

1. Em seguida, você pode restaurar os backups de log de transação e completo em **ContosoSQL2**. Abra o arquivo RDP para **ContosoSQL2** e faça logon como **CORP\Install**. Deixe a sessão de área de trabalho remota para **ContosoSQL1** aberta.

1. No menu **Iniciar** , inicie o **SQL Server Management Studio**e clique em **Conectar** para se conectar à instância do SQL Server padrão.

1. No **Pesquisador de objetos**, clique com botão direito **bancos de dados** e clique em **Restaurar banco de dados**.

1. Na seção de **origem** , selecione o **dispositivo**e clique **…** botão.

1. Em **Selecione dispositivos de backup**, clique em **Adicionar**.

1. Em local do arquivo de Backup, digite \\ContosoSQL1\backup, clique em atualizar, selecione MyDB1.bak, e em seguida, clique em Okey e clique em Okey novamente. Agora você deve ver o backup completo e o backup de log no Backup define como para restaurar painel.

1. Vá para a página de opções, selecione RESTAURAR WITH NORECOVERY em estado de recuperação e clique em Okey para restaurar o banco de dados. Uma vez concluída a operação de restauração, clique em Okey.

### <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade:

1. Volte para a sessão de área de trabalho remota para **ContosoSQL1**. No **Pesquisador de objetos** no SSMS, **Sempre em alta disponibilidade** de atalho e clique em **Assistente de novo grupo de disponibilidade**, conforme mostrado abaixo.

    ![Iniciar o Assistente para novo grupo de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. Na página de **Introdução** , clique em **Avançar**. Na página **Especificar nome do grupo de disponibilidade** , digite **AG1** **nome do grupo de disponibilidade**, clique em **Avançar** novamente.

    ![Assistente para novo AG, especifique o nome de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. Na página **Selecionar bancos de dados** , selecione **MyDB1** e clique em **Avançar**. O banco de dados atende os pré-requisitos para um grupo de disponibilidade, pois você direcionado pelo menos um backup completo na réplica primária pretendida.

    ![Assistente para novo AG, selecione bancos de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. Na página **Especificar réplicas** , clique em **Adicionar réplica**.

    ![Assistente para novo AG, especifique réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. Caixa de diálogo **conectar ao servidor** aparece. Digite **ContosoSQL2** em **nome do servidor**e, em seguida, clique em **Conectar**.

    ![Assistente para novo AG, se conectar ao servidor](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. Novamente na página **Especificar réplicas** , agora você deve ver **ContosoSQL2** listado duplicações **Disponíveis**. Configure as réplicas conforme mostrado abaixo. Quando tiver terminado, clique em **Avançar**.

    ![Assistente para novo AG, especifique réplicas (concluído)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. Na página **Selecionar sincronização inicial de dados** , selecione **somente ingressar** e clique em **Avançar**. Você já tiver executado sincronização de dados manualmente quando você capturadas os backups completo e transação em **ContosoSQL1** e restaurados-los no **ContosoSQL2**. Em vez disso, você pode escolher não executar o backup e restaurar operações em seu banco de dados e selecione **completo** para permitir que o Assistente para criar novo grupo de disponibilidade executar a sincronização de dados para você. No entanto, isso não é recomendado para bancos de dados muito grandes que são encontrados em algumas empresas.

    ![Assistente para novo AG, selecione a sincronização de dados iniciais](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. Na página de **validação** , clique em **Avançar**. Esta página deve ser semelhante ao abaixo. Há um aviso para a configuração de ouvinte porque você não tiver configurado um ouvinte de grupo de disponibilidade. Você pode ignorar este aviso, porque este tutorial não configurar um ouvinte. Para configurar o ouvinte depois de concluir este tutorial, consulte [Configurar um ouvinte ILB para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

    ![Assistente para novo AG, validação](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. Na página **Resumo** , clique em **Concluir**, aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página de **progresso** , você pode clicar em **mais detalhes** para exibir o andamento detalhado. Depois que o assistente for concluído, inspecione a página de **resultados** para confirmar que o grupo de disponibilidade é criado com êxito, conforme mostrado abaixo, em seguida, clique em **Fechar** para sair do assistente.

    ![Assistente para novo AG, resultados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. No **Pesquisador de objetos**, expanda **Sempre em alta disponibilidade**e expandir **Grupos de disponibilidade**. Agora você deve ver o novo grupo de disponibilidade neste contêiner. Clique com botão direito **AG1 (primária)** e clique em **Mostrar painel de controle**.

    ![Mostrar painel de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. Seu **Sempre Dashboard** deve ser semelhante à mostrada abaixo. Você pode ver as réplicas, o modo de tolerância de cada réplica e o estado de sincronização.

    ![Painel de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. Retornar ao **Gerenciador do servidor**, selecione **Ferramentas**e, em seguida, iniciar o **Gerenciador de Cluster de Failover**.

1. Expanda **Cluster1.corp.contoso.com**e, em seguida, expanda **serviços e aplicativos**. Selecione **funções** e observe que a função de grupo de disponibilidade de **AG1** foi criada. Observe que AG1 não tem qualquer endereço IP pelo qual banco de dados clientes podem se conectar ao grupo de disponibilidade, porque você não configurou um ouvinte. Você pode conectar diretamente para o nó principal para operações de leitura e o nó secundário para consultas de somente leitura.

    ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] Não tente falha sobre o grupo de disponibilidade do Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas a partir de **Sempre Dashboard** no SSMS. Para obter mais informações, consulte [restrições sobre usando o WSFC Failover Cluster Manager com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).

## <a name="next-steps"></a>Próximas etapas
Você tem agora com êxito implementado SQL Server sempre em pela criação de um grupo de disponibilidade no Azure. Para configurar um ouvinte para esse grupo de disponibilidade, consulte [Configurar um ouvinte ILB para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Para obter outras informações sobre como usar o SQL Server no Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
