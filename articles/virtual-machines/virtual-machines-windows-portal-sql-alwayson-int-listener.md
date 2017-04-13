<properties
   pageTitle="Criar ouvinte para o grupo de availabilty AlwaysOn para SQL Server em máquinas virtuais do Azure"
   description="Instruções passo a passo sobre como criar um ouvinte para um grupo de availabilty sempre ativado para SQL Server em máquinas virtuais do Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurar um balanceador de carga interna para um grupo de disponibilidade sempre ativado no Azure

Este tópico explica como criar um balanceador de carga interna para um grupo de disponibilidade do SQL Server AlwaysOn no Azure máquinas virtuais em execução no modelo de Gerenciador de recursos. Um grupo de disponibilidade do AlwaysOn requer um balanceador de carga quando as instâncias do SQL Server no Azure máquinas virtuais. O balanceador de carga armazena o endereço IP para o ouvinte de grupo de disponibilidade. Se um grupo de disponibilidade ocupar múltipla regiões, cada região precisa de um balanceador de carga.

Para concluir essa tarefa, você precisa ter um grupo de disponibilidade do SQL Server AlwaysOn implantado em máquinas virtuais Azure no modelo de Gerenciador de recursos. As duas máquinas virtuais de SQL Server deve pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo do Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar o grupo de disponibilidade sempre ativado automaticamente em Gerenciador de recursos do Azure. Este modelo cria automaticamente o balanceador de carga interno para você. 

Se preferir, você pode [Configurar manualmente um grupo de disponibilidade sempre ativado](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico requer que seus grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

 - [Configurar grupos de disponibilidade sempre ativado no Azure máquina virtual (interface gráfica)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurar uma conexão de VNet para VNet usando o Gerenciador de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Etapas

Examinando este documento você irá criar e configurar um balanceador de carga no portal do Azure. Depois que estiver concluída, você irá configurar o cluster para usar o endereço IP do balanceador de carga para o ouvinte de grupo de disponibilidade sempre ativado.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o balanceador de carga no portal do Azure

Nesta parte da tarefa, você irá fazer as seguintes etapas no portal do Azure:

1. Criar o balanceador de carga e configurar o endereço IP

1. Configurar o pool de back-end

1. Criar o teste 

1. Definir regras de balanceamento de carga

>[AZURE.NOTE] Se os servidores de SQL estão em regiões e grupos de recursos diferentes, você fará todas essas etapas duas vezes, uma vez em cada grupo de recursos.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Crie o balanceador de carga e configure o endereço IP

A primeira etapa é criar o balanceador de carga. No portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. No grupo de recursos, clique em **Adicionar**.

- Pesquisar **balanceador de carga**. Nos resultados da pesquisa, selecione **Balanceador de carga**, que é publicada pela **Microsoft**.

- Na lâmina **Balanceador de carga** , clique em **criar**.

- Em **criar balanceador de carga**, configure o balanceador de carga da seguinte forma:

| Configuração | Valor |
| ----- | ----- |
| **Nome** | Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
| **Esquema** | **Interno** |
| **Rede virtual** | Escolha a rede virtual que os servidores de SQL estão em.   |
| **Sub-rede**  | Escolha a sub-rede que os servidores de SQL estão em. |
| **Assinatura** | Se você tiver várias assinaturas, esse campo pode aparecer. Selecione a assinatura que deseja associada a este recurso. Normalmente é a mesma subcription como todos os recursos para o grupo de disponibilidade.  |
| **Grupo de recursos** | Escolha o grupo de recursos que os servidores de SQL estão em. | 
| **Local** | Escolha o local Azure que os servidores de SQL estão em. |

- Clique em **criar**. 

Azure cria o balanceador de carga que você configurou acima. O balanceador de carga pertence a uma rede específica, sub-rede, grupo de recursos e local. Após a conclusão do Azure, verifique as configurações de Balanceador de carga no Azure. 

Agora, configure o endereço IP de Balanceador de carga.  

- Na lâmina de **configurações** de Balanceador de carga, clique em **endereço IP**. O **endereço IP** blade mostra que esse é um balanceador de carga particular na mesma rede virtual como seus servidores SQL. 

- Defina as configurações a seguir: 

| Configuração | Valor |
| ----- | ----- |
| **Sub-rede** | Escolha a sub-rede que os servidores de SQL estão em. |
| **Atribuição** | **Estático** |
| **Endereço IP** | Digite um endereço IP virtual não utilizado da sub-rede.  |

- Salve as configurações.

Agora o balanceador de carga tem um endereço IP. Grave esse endereço IP. Você usará esse endereço IP quando você cria um ouvinte no cluster. Em um script PowerShell neste artigo, use este endereço para o `$ILBIP` variável.



## <a name="2-configure-the-backend-pool"></a>2. configurar o pool de back-end

A próxima etapa é criar um pool de endereços de back-end. Azure chamadas de pool de endereços de back-end *pool de back-end*. Nesse caso, o pool de back-end é os endereços dos dois servidores SQL no seu grupo de disponibilidade. 

- No seu grupo de recursos, clique em balanceador de carga que você criou. 

- Em **configurações**, clique **em pools back-end**.

- No **back-end pools de endereços**, clique em **Adicionar** para criar um pool de endereços de back-end. 

- Em **Adicionar pool de back-end** em **nome**, digite um nome para o pool de back-end.

- Em **máquinas virtuais** clique em **+ Adicionar uma máquina virtual**. 

- Em **máquinas virtuais de escolher** clique em **Escolher um conjunto de disponibilidade** e especifique a disponibilidade conjunto que máquinas virtuais do SQL Server pertence.

- Depois que você escolheu o conjunto de disponibilidade, clique em **Escolher as máquinas virtuais**. Clique nas duas máquinas virtuais que hospedam as instâncias do SQL Server no grupo de disponibilidade. Clique em **Selecionar**. 

- Clique **Okey** para fechar as lâminas para **máquinas virtuais de escolher**e **Adicionar pool de back-end**. 

Azure atualiza as configurações para o pool de endereços de back-end. Agora o seu conjunto de disponibilidade tem um pool de dois servidores de SQL.

## <a name="3-create-a-probe"></a>3. criar um teste

A próxima etapa é criar um teste. O teste define como Azure verificará que os servidores de SQL possui o ouvinte de grupo de disponibilidade no momento. Azure será teste o serviço com base no endereço IP em uma porta que você definir quando você cria o teste.

- Na lâmina de **configurações** de Balanceador de carga, clique em **testes**. 

- Na lâmina **testes** , clique em **Adicionar**.

- Configure o teste na lâmina **teste de adicionar** . Use os valores a seguir para configurar o teste:

| Configuração | Valor |
| ----- | ----- |
| **Nome** | Um nome de texto que representa o teste. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
| **Protocolo** | **TCP** |
| **Porta** | Você pode usar qualquer porta disponível. Por exemplo, *59999*.    |
| **Intervalo**  | *5* | 
| **Limite não íntegra**  | *2* | 

- Clique em **Okey**. 

>[AZURE.NOTE] Certifique-se de que a porta que você especificar é aberta no firewall de ambos os servidores de SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usar. Consulte [Adicionar ou editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 

Azure cria o teste. Azure usará o teste para testar a qual SQL Server tem o ouvinte para o grupo de disponibilidade.

## <a name="4-set-the-load-balancing-rules"></a>4. configurar regras de balanceamento de carga

Defina a regras de balanceamento de carga. As regras de balanceamento de carga configurar como o balanceador de carga rotear o tráfego para os servidores de SQL. Para este balanceador de carga você permitirá servidor direta retorno porque apenas um dos dois servidores SQL será nunca o proprietário do recurso de ouvinte do grupo de disponibilidade por vez.

- Na lâmina de **configurações** de Balanceador de carga, clique em **regras de balanceamento de carga**. 

- Na lâmina **balanceamento de carga de regras** , clique em **Adicionar**.

- Use a lâmina **adicionar regras de balanceamento de carga** para configurar a regra de balanceamento de carga. Use as seguintes configurações: 

| Configuração | Valor |
| ----- | ----- |
| **Nome** | Um nome de texto que representa a regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
| **Protocolo** | **TCP** |
| **Porta** | *1433*   |
| **Porta de back-end** | *1433*. Observe que isso será desabilitado porque esta regra usa **IP flutuante (servidor direta retorno)**.   |
| **Teste** | Use o nome do teste que você criou para este balanceador de carga. |
| **Persistance de sessão**  | **Nenhum** | 
| **Tempo limite ocioso (minutos)**  | *4* | 
| **Flutuante IP (servidor direta retorno)**  | **Habilitado** | 

 >[AZURE.NOTE] Talvez seja necessário rolar para baixo na lâmina para ver todas as configurações.

- Clique em **Okey**. 

- Azure configura a regra balanceamento de carga. Agora o balanceador de carga está configurado para rotear o tráfego para o SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto o grupo de recursos tem um balanceador de carga, conectando ao ambas as máquinas do SQL Server. O balanceador de carga também contém um endereço IP para o ouvinte de grupo de disponibilidade do SQL Server AlwaysOn para que o computador pode responder a solicitações para os grupos de disponibilidade.

>[AZURE.NOTE] Se seus servidores SQL estiverem em duas regiões separados, repita as etapas na região outro. Cada região requer um balanceador de carga. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para usar o endereço IP de Balanceador de carga 

A próxima etapa é configurar o ouvinte no cluster e trazer o ouvinte online. Para fazer isso, faça o seguinte: 

1. Criar o ouvinte de grupo de disponibilidade no cluster failover 

1. Trazer o ouvinte on-line

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. criar o ouvinte de grupo de disponibilidade no cluster failover

Nesta etapa, você criar manualmente o ouvinte de grupo de disponibilidade no Gerenciador de Cluster de Failover e SQL Server Management Studio (SSMS).

- Use RDP para se conectar à máquina virtual Azure que hospeda a réplica principal. 

- Abra o Gerenciador de Cluster de Failover.

- Selecione o nó de **redes** e observe o nome de rede de cluster. Esse nome será usada no `$ClusterNetworkName` variável no script PowerShell.

- Expanda o nome do cluster e, em seguida, clique em **funções**.

- No painel de **funções** , clique com botão direito no nome do grupo de disponibilidade e selecione **Adicionar recurso** > **Ponto de acesso de cliente**.

- Na caixa **nome** , crie um nome para este novo ouvinte, e em seguida, clique duas vezes em **Avançar** e, em seguida, clique em **Concluir**. Não reduza o ouvinte ou recurso online neste momento.

 >[AZURE.NOTE] O nome para o novo ouvinte é o nome de rede que aplicativos usará para se conectar aos bancos de dados no grupo de disponibilidade do SQL Server.

- Clique na guia **recursos** , expanda o ponto de acesso de cliente que você acabou de criar. O recurso de IP de atalho e clique em Propriedades. Observe o nome do endereço IP. Você usará esse nome no `$IPResourceName` variável no script PowerShell.

- Em **Endereço IP** clique em **Endereço IP estático** e definir o endereço IP estático para o mesmo endereço que você usou quando você define o endereço IP de Balanceador de carga no portal do Azure. Ativar NetBIOS para este endereço e clique em **Okey**. Repita esta etapa para cada recurso IP se sua solução abrange várias VNets do Azure. 

- No nó de cluster que atualmente hospeda a réplica principal, abra um elevado PowerShell ISE e cole os seguintes comandos em um novo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Atualize as variáveis e execute o script do PowerShell para configurar o endereço IP e porta para o novo ouvinte.

 >[AZURE.NOTE] Se seus servidores SQL em regiões separadas, você precisa executar o script do PowerShell duas vezes. Na primeira vez que use o nome de rede de cluster, nome do recurso IP de cluster e carregar o endereço IP de Balanceador do primeiro grupo de recursos. Na segunda vez use o nome de rede de cluster, nome do recurso IP de cluster e carregue o endereço IP de Balanceador do grupo de recursos segundo.

Agora o cluster tem um recurso de ouvinte do grupo de disponibilidade.

## <a name="2-bring-the-listener-online"></a>2. trazer o ouvinte on-line

Com o recurso de ouvinte de grupo disponibilidade configurado, você pode trazer o ouvinte online para que os aplicativos podem se conectar aos bancos de dados no grupo de disponibilidade com o ouvinte.

- Navegar de volta ao Cluster Gerenciador de Failover. Expanda **funções** e realce seu grupo de disponibilidade. Na guia **recursos** , o nome do ouvinte de atalho e clique em **Propriedades**.

- Clique na guia **dependências** . Se houver vários recursos listados, verifique se os endereços IP tem ou não e, dependências. Clique em **Okey**.

- O nome do ouvinte de atalho e clique em **Colocar on-line**.


- Depois que o ouvinte é online, na guia **recursos** , o grupo de disponibilidade de atalho e clique em **Propriedades**.

- Crie uma dependência no recurso de nome de ouvinte (não o nome de recursos IP endereço). Clique em **Okey**.


- Inicie o SQL Server Management Studio e se conectam a réplica primária.


- Navegue até **AlwaysOn alta disponibilidade** | **grupos de disponibilidade** | **ouvintes de grupo de disponibilidade**. 


- Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. O nome do ouvinte de atalho e clique em **Propriedades**.


- Na caixa **porta** , especifique o número da porta para o ouvinte de grupo de disponibilidade usando o $EndpointPort utilizado anteriormente (1433 era o padrão), clique em **Okey**.

Agora você tem um grupo de disponibilidade do SQL Server AlwaysOn em Azure máquinas virtuais em execução no modo do Gerenciador de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão para o ouvinte

Para testar a conexão:

1. RDP a um SQL Server que está na mesma rede virtual, mas não possui a réplica. Isso pode ser a outras do SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o seguinte script estabelece uma conexão **sqlcmd** a réplica primária através do ouvinte com autenticação do Windows:

        sqlcmd -S <listenerName> -E

A conexão de SQLCMD se conectar automaticamente a qualquer instância do SQL Server hospeda a réplica principal. 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

Observe as seguintes diretrizes no ouvinte de grupo de disponibilidade no Azure usando interno balanceador de carga:

- Somente um ouvinte de grupo de disponibilidade interna é suportado por serviço de nuvem porque o ouvinte é configurado para o balanceador de carga, e há apenas um balanceador de carga interno. No entanto é possível criar multipe escutas externas. 

- Com um balanceador de carga interno só você acessar o ouvinte de dentro da mesma rede virtual.
 
