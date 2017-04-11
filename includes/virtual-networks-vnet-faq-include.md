## <a name="virtual-network-basics"></a>Noções básicas de rede virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma rede Virtual do Azure (VNet)?

Você pode usar VNets para provisionar e gerenciar redes virtuais privadas (VPNs) no Azure e, opcionalmente, vincular a VNets com outros VNets no Azure ou com seu local infraestrutura para criar soluções híbrido ou entre locais. Cada VNet que criar tem seu próprio bloco CIDR e pode ser vinculada a outras redes VNets e no local, desde que os blocos CIDR não coincidem. Você também tem controles de configurações do servidor DNS para VNets e segmentação da VNet em sub-redes.

Use VNets para:

- Criar uma rede privada dedicada somente na nuvem virtual

    Às vezes, você não exige uma configuração de cruz locais para sua solução. Quando você cria um VNet, seus serviços e VMs dentro de sua VNet podem se comunicar diretamente e com segurança uns com os outros na nuvem. Isso mantém o tráfego de rede com segurança dentro do VNet, mas ainda permite que você configure conexões de ponto de extremidade para as VMs e serviços que necessitam de comunicação da Internet como parte de sua solução.

- Estender com segurança seu centro de dados

    Com VNets, você pode criar VPNs de (S2S)-to-site tradicionais para dimensionar com segurança sua capacidade do data center. S2S VPNs usar IPSEC para fornecer uma conexão segura entre o gateway VPN corporativo e o Azure.

- Habilitar cenários de nuvem híbrida

    VNets dar flexibilidade para dar suporte a uma variedade de cenários de nuvem híbrida. Você pode conectar com segurança aplicativos baseados em nuvem para qualquer tipo de sistema local como mainframes e sistemas Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Como saber se eu precisar de uma rede virtual?

Visite a [Visão geral de rede Virtual](../articles/virtual-network/virtual-networks-overview.md) para ver uma tabela de decisão que ajudarão você a decidir a melhor opção de design de rede para você.

### <a name="how-do-i-get-started"></a>Como faço para começar a?

Visite [a documentação de rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para começar. Esta página contém links para as etapas de configuração comuns, bem como informações que ajudarão você a compreender as mudanças que você precisará levar em consideração ao projetar a rede virtual.

### <a name="what-services-can-i-use-with-vnets"></a>Quais serviços pode usar com VNets?

VNets pode ser usado com uma variedade de diferentes serviços do Azure, como serviços de nuvem (PaaS), máquinas virtuais e aplicativos Web. No entanto, há alguns serviços que não são suportados em um VNet. Verifique se o serviço específico que você deseja usar e verificar se ele é compatível.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Pode usar VNets sem conectividade entre locais?

Sim. Você pode usar um VNet sem usar conectividade to-site. Isso é especialmente útil se você quiser executar controladores de domínio e farms do SharePoint no Azure.

## <a name="virtual-network-configuration"></a>Configuração da rede virtual

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quais ferramentas posso usar para criar um VNet?

Você pode usar as ferramentas a seguir para criar ou configurar uma rede virtual:

- Portal do Azure (para clássico e VNets Gerenciador de recursos).

- Um arquivo de configuração de rede (netcfg - para somente VNets clássico). Consulte [Configurar uma rede virtual usando um arquivo de configuração de rede](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (para clássico e VNets Gerenciador de recursos).

- Azure CLI (para clássico e VNets Gerenciador de recursos).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quais intervalos de endereços pode usar em meu VNets?

Você pode usar intervalos de endereços IP públicos e qualquer intervalo de endereços IP definido em [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Pode ter endereços IP públicos na minha VNets?

Sim. Para obter mais informações sobre intervalos de endereços IP públicos, consulte [espaço de endereço IP público em uma rede Virtual (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Lembre-se de que seu público IPs não será acessível diretamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>Há um limite quanto ao número de sub-redes na minha rede virtual?

Não há nenhum limite no número de sub-redes que você usar dentro de um VNet. Todas as sub-redes devem estar totalmente contidas no espaço de endereço de rede virtual e não devem sobrepor uns com os outros.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições sobre o uso de endereços IP dentro essas sub-redes?

Azure reserva alguns endereços IP dentro de cada sub-rede. Os endereços IP primeiro e últimos das sub-redes são reservados para conformidade do protocolo, juntamente com 3 endereços mais usados para serviços do Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Como pequenas e como grandes podem ser VNets e sub-redes?

A sub-rede menor que oferecemos suporte é um /29 e o maior é um /8 (usando definições de sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso importar Meus VLANs no Azure usando VNets?

Não. VNets são sobreposições de camada 3. Azure não suporta qualquer semântica de camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Pode especificar políticas de roteamento personalizadas em meu VNets e sub-redes?

Sim. Você pode usar o roteamento definidas pelo usuário (UDR). Para obter mais informações sobre UDR, visite [rotas de definidas pelo usuário e encaminhamento de IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNets dão suporte difusão ou?

Não. Não oferecemos suporte difusão ou.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais protocolos pode usar dentro de VNets?

Você pode usar protocolos padrão baseado em IP dentro VNets. No entanto, seletiva, transmissão, IP-em-IP pacotes encapsulados e encapsulamento de roteamento genérico (GRE) são bloqueados em VNets. Protocolos padrão que funcionam incluem:

- TCP
- UDP
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso pode executar ping Meus roteadores padrão dentro de um VNet?

Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Pode usar tracert para diagnosticar conectividade?

Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes após o VNet é criado?

Sim. Sub-redes podem ser adicionados para VNets a qualquer momento, desde que o endereço de sub-rede não faz parte de outra sub-rede na VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Pode modificar o tamanho da minha sub-rede depois de eu criá-lo?

Você pode adicionar, remover, expandir ou reduzir uma sub-rede se existem VMs ou serviços implantados dentro dela usando cmdlets do PowerShell ou o arquivo NETCFG. Você também pode adicionar, remover, expandir ou reduzir prefixos desde que as sub-redes que contêm VMs ou serviços não são afetadas pela alteração.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Pode modificar o sub-redes depois que eu criei-las?

Sim. Você pode adicionar, remover e modificar os blocos CIDR usados por um VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Posso me conectar à internet se estou executando meus serviços em um VNet?

Sim. Todos os serviços implantados em um VNet podem se conectar à internet. Cada serviço de nuvem implantado no Azure tem um VIP publicamente endereçamento atribuído a ela. Você precisará definir pontos de extremidade de entrada para funções de PaaS e pontos de extremidade para máquinas virtuais ativar esses serviços aceitar conexões da internet.

### <a name="do-vnets-support-ipv6"></a>VNets oferece suporte a IPv6?

Não. Não é possível usar o IPv6 com VNets neste momento.

### <a name="can-a-vnet-span-regions"></a>Um VNet pode abranger regiões?

Não. Um VNet está limitado a uma única região.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso conectar um VNet para outra VNet no Azure?

Sim. Você pode criar VNet para comunicação VNet usando APIs REST ou o Windows PowerShell. Você também pode conectar VNets via VNet correspondência. Ver mais detalhes sobre correspondência [aqui.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são minhas opções de DNS para VNets?

Use a tabela de decisão na página de [Resolução de nomes para VMs e instâncias de função](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para orientá-lo todas as opções de DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Pode especificar servidores DNS para um VNet?

Sim. Você pode especificar endereços IP do servidor DNS nas configurações VNet. Isso será aplicado como servidores DNS padrão para todas as VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS pode especificar?

Você pode especificar até 12 servidores DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Pode modificar o meus servidores DNS após ter criado a rede?

Sim. Você pode alterar a lista de servidor DNS para seu VNet a qualquer momento. Se você alterar sua lista de servidores DNS, você precisará reiniciar cada uma das VMs em seu VNet sejam para retomar o novo servidor DNS.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é DNS fornecido Azure e funciona com VNets?

DNS fornecido Azure é um serviço DNS de vários locatário oferecido pela Microsoft. Azure registra todas as suas VMs e instâncias de função neste serviço. Este serviço oferece resolução de nome pelo nome do host para VMs e instâncias de função contidas o mesmo serviço de nuvem e pelo FQDN para VMs e instâncias de função no mesmo VNet.

> [AZURE.NOTE] Não há uma limitação neste momento para os serviços de 100 nuvem primeiro na rede virtual locatário cruz para resolução de nomes usando DNS fornecido Azure. Se você estiver usando seu próprio servidor DNS, essa limitação não se aplica.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Pode substituir minhas configurações de DNS em por VM / base do serviço?

Sim. Você pode definir servidores DNS em uma base de serviço por nuvem para substituir as configurações de rede padrão. No entanto, recomendamos que você use toda a rede DNS tanto quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso importar meu próprios sufixo DNS?

Não. Você não pode especificar um sufixo DNS personalizado para seu VNets.

## <a name="vnets-and-vms"></a>VNets e VMs

### <a name="can-i-deploy-vms-to-a-vnet"></a>Pode implantar VMs para um VNet?

Sim.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>Pode implantar VMs Linux para um VNet?

Sim. Você pode implantar qualquer distribuição do Linux suportado pelo Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>Qual é a diferença entre um VIP público e um endereço IP interno?

- Um endereço IP interno é um endereço IP que é atribuído a cada máquina virtual dentro de uma VNet pelo DHCP. Não é voltado ao público. Se você tiver criado uma VNet, o endereço IP interno é atribuído do intervalo que você especificou nas configurações do seu VNet sub-rede. Se você não tiver um VNet, um endereço IP interno ainda será atribuído. O endereço IP interno permanecerá com a máquina virtual para sua duração, a menos que máquina virtual é desalocada.

- Um VIP público é o endereço IP público atribuído a sua balanceador de carga ou serviço de nuvem. Ele não é atribuído diretamente ao seu NIC. VM VIP permanecerá com o serviço de nuvem que é atribuído a até que todas as VMs nesse serviço em nuvem são desalocados ou excluídas. Neste ponto, ela será lançada.

### <a name="what-ip-address-will-my-vm-receive"></a>Qual endereço IP minha máquina virtual receberá?

- **Endereço IP interno-** Se você implantar uma máquina virtual para um VNet, a máquina virtual recebe um endereço IP interno de um pool de endereços IP internos que você especificar. VMs se comunicar dentro do VNets usando endereços IP internos. Embora Azure atribui um endereço IP interno dinâmico, você pode solicitar um endereço estático para sua máquina virtual. Para saber mais sobre os endereços IP internos estáticos, visite [como definir IP estático interno](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** Sua máquina virtual também está associado um VIP, embora um VIP nunca é atribuído a máquina virtual diretamente. Um VIP é um endereço IP público que pode ser atribuído ao seu serviço de nuvem. Opcionalmente, você pode reservar um VIP de seu serviço de nuvem.

- **ILPIP-** Você também pode configurar um endereço IP público do nível de instância (ILPIP). ILPIPs são diretamente associados a máquina virtual, ao invés de serviço de nuvem. Para saber mais sobre ILPIPs, visite [Visão geral do programa de IP do nível de instância pública](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Pode reservar um endereço IP interno para uma máquina virtual que eu criará posteriormente?

Não. Você não pode reservar um endereço IP interno. Se um endereço IP interno estiver disponível ele será atribuído a uma instância de máquina virtual ou função pelo servidor DHCP. Essa máquina virtual pode ou não ser aquele que deseja que o endereço IP interno para serem atribuídos a. No entanto, você pode alterar o endereço IP interno de uma máquina virtual já criado para qualquer endereço IP interno disponível.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Fazer alterações de endereços IP interno para VMs em um VNet?

Sim. Endereços IP internos permanecem com a máquina virtual para sua vida útil, a menos que a máquina virtual é desalocada. Quando uma máquina virtual é desalocada, o endereço IP interno é lançado, a menos que você definiu um endereço IP estático interno para sua máquina virtual. Se a máquina virtual é simplesmente parada (e não colocar no status **parado (Deallocated)**), o endereço IP permanecerá atribuído para a máquina virtual.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>É possível manualmente atribuir endereços IP a NICs em VMs?

Não. Você não deve alterar as propriedades da interface de VMs. As alterações podem levar a potencialmente perder a conectividade para a máquina virtual.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>O que acontece com meus endereços IP se eu desligar uma máquina virtual?

Nada. Os endereços IP (VIP público e endereço IP interno) permanecerá com o seu serviço de nuvem ou máquina virtual.

> [AZURE.NOTE] Se você quiser simplesmente desligar a máquina virtual, não use o Portal de gerenciamento para fazê-lo. Atualmente, o botão de desligar será desalocar a máquina virtual.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Posso mover VMs de uma sub-rede para outra sub-rede em um VNet sem implantar novamente?

Sim. Você pode encontrar mais informações [aqui](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Pode configurar um endereço MAC estático para minha máquina virtual?

Não. Um endereço MAC não pode ser configurado de forma estática.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>O endereço MAC permanecerá igual para minha máquina virtual depois que ela foi criada?

Sim, o endereço MAC permanecerá igual para uma máquina virtual mesmo que a máquina virtual foi interrompida (liberada) e relançada.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso conectar à internet de uma máquina virtual em um VNet?

Sim. Todos os serviços implantados em um VNet podem se conectar à Internet. Além disso, cada serviço de nuvem implantado no Azure tem um VIP publicamente endereçamento atribuído a ela. Você precisa definir pontos de extremidade de entrada para funções de PaaS e pontos de extremidade para VMs ativar esses serviços aceitar conexões da Internet.

## <a name="vnets-and-services"></a>VNets e serviços

### <a name="what-services-can-i-use-with-vnets"></a>Quais serviços pode usar com VNets?

Você só pode usar serviços de computação em VNets. Serviços de computação limitam-se aos serviços de nuvem (funções da web e trabalhador) e VMs.

### <a name="can-i-use-web-apps-with-virtual-network"></a>Pode usar aplicativos Web com uma rede Virtual?

Sim. Você pode implantar aplicativos Web dentro de uma VNet usando ASE (ambiente de serviço de aplicativo). Adicionar, Web Apps pode com segurança conectar e acessar os recursos em seu VNet Azure se você tiver configurado para sua VNet ponto-a-site. Para obter mais informações, consulte o seguinte:


- [Criando aplicativos Web em um ambiente de serviço de aplicativo](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Integração de rede Virtual de aplicativos Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Usando conexões de híbrido e VNet integração com aplicativos Web](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrar um aplicativo web com uma rede Virtual do Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Pode implantar os serviços de nuvem com web e trabalhador funções (PaaS) em um VNet?

Sim. Você pode implantar os serviços de PaaS dentro VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Como implantar o PaaS funções para um VNet?

Você pode fazer isso, especificando o nome de VNet e os mapeamentos de /subnet de função na seção Configuração de rede da sua configuração de serviço. Você não precisa atualizar qualquer um dos seus binários.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso mover meus serviços e sair VNets?

Não. Você não pode mover serviços e sair VNets. Você precisará excluir e implantar o serviço movê-lo para outro VNet novamente.

## <a name="vnets-and-security"></a>VNets e segurança

### <a name="what-is-the-security-model-for-vnets"></a>O que é o modelo de segurança para VNets?

VNets são completamente isolados do entre si e de outros serviços hospedados em infraestrutura do Azure. Um VNet é um limite de confiança.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>Pode definir ACLs ou NSGs na minha VNets?

Não. Você não pode associar ACLs ou NSGs para VNets. Entretanto, ACLs podem ser definidas em pontos de extremidade de entrada para VMs que foram implantadas um VNets e NSGs podem ser associadas a sub-redes ou NICs.

### <a name="is-there-a-vnet-security-whitepaper"></a>Existe um whitepaper de segurança VNet?

Sim. É possível baixá-lo [aqui](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Pode gerenciar VNets do código?

Sim. Você pode usar APIs REST para gerenciar conectividade VNets e entre locais. Mais informações podem ser encontradas [aqui](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Há suporte a ferramentas para VNets?

Sim. Você pode usar as ferramentas de linha de comando e PowerShell para uma variedade de plataformas. Mais informações podem ser encontradas [aqui](http://go.microsoft.com/fwlink/?LinkId=317721).
