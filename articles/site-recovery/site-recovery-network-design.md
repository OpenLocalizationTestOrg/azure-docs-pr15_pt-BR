<properties
    pageTitle="Projetar sua infraestrutura de rede para recuperação | Microsoft Azure"
    description="Este artigo descreve as considerações de design de rede para recuperação de Site do Azure"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>Projetar sua infraestrutura de rede para recuperação de dados

Este artigo é direcionado para profissionais de TI que são responsáveis pela arquitetura, implementar e suporte continuidade de negócios e infraestrutura de recuperação (BCDR) de desastres e que desejam aproveitar Microsoft Azure Site ASR (recuperação) para oferecer suporte e aprimorar seus serviços BCDR. Este artigo discute Considerações práticas para implantação de servidor de Gerenciador de máquina Virtual do System Center, os prós e contras de sub-redes alongadas versus failover sub-redes e como estruturar recuperação aos sites virtuais no Microsoft Azure.

## <a name="overview"></a>Visão geral

[Recuperação de Site do Azure (ASR)](https://azure.microsoft.com/services/site-recovery/) é um serviço do Microsoft Azure que organiza a proteção e a recuperação de seus aplicativos virtualizados fins de negócios continuidade recuperação (BCDR). Este documento destina-se para orientar o leitor pelo processo de criação de redes, concentrando-se na arquitetura de intervalos IP e sub-redes no site de recuperação de desastres, ao replicar máquinas virtuais (VMs) usando a recuperação do Site.

Além disso, este artigo demonstra como recuperação de Site permite projetar e implementar um data center virtual multissite BCDR aos serviços de suporte a hora do teste ou desastres.

Em um mundo onde todos espera conectividade 24/7, é mais importante do que nunca manter sua infraestrutura e aplicativos para cima e em execução. A finalidade de continuidade de negócios e recuperação de desastres (BCDR) é restaurar componentes com falha para que a organização possa reiniciar rapidamente operações normais. Desenvolver estratégias de recuperação de desastres para lidar com eventos devastadores provavelmente não é desafiador. Isso é devido a dificuldade inerente de prever o futuro, particularmente como ele se relaciona a eventos improvável e o alto custo para fornecer as medidas adequadas de proteção contra catástrofes abrangente. 

Fundamentais para BCDR planejamento, objetivo de tempo de recuperação (RTO) e objetivo de ponto de recuperação (RPO) devem ser definidos como parte de um plano de recuperação de desastres. Quando um acidente Centro de dados do cliente, usando a recuperação de Site do Azure, o cliente pode rapidamente (RTO baixa) colocar online suas máquinas virtuais replicadas localizadas do Centro de dados secundária ou do Microsoft Azure com perda de dados mínima (RPO baixa). 

Failover é feita possível pela ASR que inicialmente copia designadas máquinas virtuais do data center principal para o Centro de dados secundária ou Azure (dependendo do cenário) e, em seguida, atualiza periodicamente as réplicas. Durante o planejamento de infraestrutura, design da rede deve ser considerada como gargalo potencial que pode impedir que você da empresa de reunião RTO e RPO.  

Quando administradores estiver planejando implantar uma solução de recuperação, uma das principais perguntas na sua mente é como a máquina virtual seria acessível após o failover. Recuperação automatizada do sistema permite que o administrador escolha a rede à qual uma máquina virtual será conectada ao após failover. Se o site principal é gerenciado por um servidor VMM isso é feito usando o mapeamento de rede. Consulte [preparar para mapeamento de rede](site-recovery-network-mapping.md) para obter mais detalhes.

Ao criar a rede para o site de recuperação, o administrador tem duas opções:

- Use um intervalo de endereço IP diferente para a rede no local de recuperação. Neste cenário, a máquina virtual após failover obterá um novo endereço IP e o administrador teria de fazer uma atualização do DNS. Leia mais sobre como fazer o DNS atualizar [aqui](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Use o mesmo intervalo de endereços IP para a rede no local de recuperação. Em determinados cenários administradores prefere manter os endereços IP que eles têm no local principal, mesmo após o failover. Em um cenário de normal, um administrador precisa atualizar as rotas para indicar o novo local dos endereços IP. Mas o cenário onde uma VLAN alongada é implantada entre o principal e os sites de recuperação, manter os endereços IP para as máquinas virtuais se torna uma opção atraente. Mantendo o mesmo IP endereços simplifica o processo de recuperação fazendo ausente qualquer rede relacionados postagem failover etapas.


Quando os administradores estiver planejando implantar uma solução de recuperação, uma das principais perguntas em mente é como os aplicativos serão acessíveis após o failover. Aplicativos modernos quase sempre são dependentes rede até certo ponto, portanto física movendo que um serviço de um site para outro representa um desafio de rede. Há duas maneiras principais que esse problema é resolvido em soluções de recuperação de desastres. A primeira abordagem é manter endereços IP fixos. Apesar dos serviços movendo e os servidores de hospedagem sendo em diferentes locais físicos, aplicativos levar a configuração de endereço IP com elas para o novo local. A segunda abordagem envolve completamente alterando o endereço IP durante a transição para o site recuperado. Cada abordagem tem diversas variações de implementação que estão resumidas abaixo.

Ao criar a rede para o site de recuperação, o administrador tem duas opções:

## <a name="option-1-retain-ip-addresses"></a>Opção 1: Reter endereços IP 

De uma perspectiva de processo de recuperação de desastres, usando IP fixo endereços parece ser o método mais fácil para implementar, mas ele possui um número de desafios possíveis que facilitam a abordagem menos popular na prática. Recuperação de Site Azure fornece a capacidade para manter os endereços IP em todos os cenários. Antes de um decide reter IP, pensamento apropriado deve ser dada as restrições que ele impõe os recursos de failover. Vamos examine os fatores que podem ajudar você a tomar uma decisão para reter endereços IP, ou não. Isso pode ser feito de duas maneiras, usando uma sub-rede alongada ou seguindo um failover sub-rede completo.

### <a name="stretched-subnet"></a>Sub-rede ampliada

Veja a sub-rede é disponibilizada simultaneamente em primário e locais de DR. Em termos simples, isso significa que você pode mover um servidor e sua configuração de IP (camada 3) para o segundo local e a rede será rotear o tráfego para o novo local automaticamente. Isso é simples de lidar com a partir de uma perspectiva do servidor, mas tem uma série de desafios:

- De uma perspectiva de (camada de vínculo de dados) de camada 2, exigirá equipamento de rede que pode gerenciar uma VLAN ampliada, mas isso se tornou menos de um problema, pois agora é amplamente disponível. O segundo e mais difícil problema é alongando VLAN o domínio da falha potencial é estendido para ambos os sites, essencialmente se tornar um único ponto de falha. Embora seja provável, aconteceu que uma difusão tempestade iniciado, mas não pôde ser isolada. Estamos descobriram mistos opiniões sobre esse problema e viu muitos implementações bem-sucedidas, bem como "podemos nunca irá implementar essa tecnologia aqui".
- Sub-rede alongada não é possível se você estiver usando o Microsoft Azure como o local de DR.


### <a name="subnet-failover"></a>Failover de sub-rede

É possível implementar failover de sub-rede para obter os benefícios da solução sub-rede alongada descrito acima sem esticando sub-rede entre vários sites. Veja qualquer determinada sub-rede seria presente no Site 1 ou 2 de Site, mas nunca em ambos os sites simultaneamente. Para manter o espaço de endereço IP em caso de um failover, é possível organizar por programação para a infraestrutura de roteador mover as sub-redes de um site para outro. Em um cenário de failover que as sub-redes serão movidas com associada protegido VMs. A principal desvantagem dessa abordagem é em caso de uma falha que você precisa mover toda a sub-rede, o que pode ser Okey, mas ele pode afetar as considerações de detalhamento de failover. 

Vamos examinar como uma empresa fictícia chamada Contoso é capaz de replicar suas VMs para um local de recuperação enquanto falha em sub-rede inteira. Primeiro veremos como Contoso é capaz de gerenciar suas sub-redes enquanto replicar VMs entre dois locais locais e, em seguida, falaremos sobre como sub-rede failover funciona ao [que Azure é usado como o local de recuperação de desastres](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Failover para um site local secundário

Vamos examine um cenário onde queremos manter o IP de cada uma das VMs e falhas sobre a sub-rede completa juntos. O site principal tem aplicativos executados em sub-rede 192.168.1.0/24. Quando ocorre o failover, todas as máquinas virtuais que fazem parte de nesta sub-rede vai ser falhou para o site de recuperação e manterão seus endereços IP. Rotas terão que adequadamente ser modificada para refletir o fato de que todas as máquinas virtuais pertencentes a sub-rede 192.168.1.0/24 agora foram movidos para o site de recuperação. 

Na ilustração a seguir as rotas entre local principal e local de recuperação, terceiro local e site principal e site de terceiro e site de recuperação precisará ser modificada adequadamente. 

As imagens a seguir mostra as sub-redes antes do failover. Sub-rede 192.168.0.1/24 está ativo no local principal antes do failover e fica ativa do Site recuperação após o failover 

![Antes do Failover](./media/site-recovery-network-design/network-design2.png)

Antes do failover


A imagem abaixo mostra redes e sub-redes após failover.
    
![Após Failover](./media/site-recovery-network-design/network-design3.png)

Após failover

No seu site secundário é local e você estiver usando um servidor VMM para gerenciá-lo depois ao habilitar proteção para uma máquina virtual específica, ASR será alocar recursos de rede de acordo com o fluxo de trabalho o seguir:

- Recuperação automatizada do sistema aloca um endereço IP para cada interface de rede na máquina virtual do endereço IP estático definido na rede relevante para cada instância do System Center VMM.
- Se o administrador define o mesmo pool de endereços IP para a rede no site de recuperação do pool de endereços IP da rede no site principal, enquanto alocar o endereço IP na máquina virtual de réplica ASR seria alocar o mesmo endereço IP que da máquina virtual primária.  O IP está reservado no VMM, mas não definido como failover IP. IP de Failover é definida logo antes do failover.

![Manter o endereço IP](./media/site-recovery-network-design/network-design4.png)
    
Figura 5

Figura 5 mostra as configurações de TCP/IP Failover de máquina virtual réplica (no console do Hyper-V). Essas configurações poderiam ser preenchidas logo antes da máquina virtual é iniciada após um failover

Se o mesmo IP não estiver disponível, ASR seria alocar alguns outro endereço IP disponível do pool de endereços IP definido. 

Depois que a máquina virtual está habilitada para proteção, que você pode usar o seguinte script de exemplo para verificar o IP que foi alocado na máquina virtual. Mesmo IP seria definido como Failover IP e atribuído para a máquina virtual no momento do failover:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] O cenário onde máquinas virtuais usar DHCP, o gerenciamento de endereços IP é completamente fora do controle da recuperação automatizada do sistema. Um administrador tem que garantir que o servidor DHCP servir os endereços IP do site de recuperação sirva partir do mesmo intervalo do local principal.

#### <a name="failover-to-azure"></a>Failover do Azure

Recuperação de Site Azure (ASR) permite que a Microsoft Azure para ser usado como um local de recuperação de desastres para suas máquinas virtuais. Nesse caso, você precisará lidar com uma restrição mais. 

Vamos examinar um cenário onde uma empresa fictícia denominada Banco Woodgrove tem infraestrutura local que hospeda seus aplicativos de linha de negócios, e eles estiver hospedando seus aplicativos móveis no Azure. Conectividade entre Woodgrove bancária VMs em servidores do Azure e local é fornecida por uma-to-site (S2S) rede Virtual privada (VPN). S2S VPN permite que a rede virtual do Banco Woodgrove no Azure para ser visto como uma extensão de rede de local do Banco Woodgrove. Essa comunicação é habilitada por VPN S2S entre borda do Banco Woodgrove e Azure rede virtual. Agora Woodgrove deseja usar a ASR para replicar suas cargas de trabalho em execução no seu centro de dados no Azure. Esta opção atende às necessidades dos Woodgrove, que quer uma opção de DR econômica e é capaz de armazenar dados em ambientes de nuvem pública. Woodgrove tem de lidar com aplicativos e configurações que dependem de endereços IP embutido, portanto tiverem reter endereços IP para seus aplicativos após falhar ao Azure.

Woodgrove decidiu atribuir endereços IP do intervalo de endereços IP (172.16.1.0/24, 172.16.2.0/24) a seus recursos em execução no Azure.

Para Woodgrove sejam capazes de duplicar suas máquinas virtuais para Azure enquanto mantém os endereços IP, uma rede Virtual do Azure precisa ser criado. Deve ser uma extensão da rede local para que aplicativos possam failover do site local para o Azure perfeitamente. Azure permite que você adicione-to-site bem como ponto-a-site conectividade VPN para as redes virtuais criadas no Azure. Ao configurar sua conexão de-to-site, rede Azure permite direcionar o tráfego para o local de local (Azure chama-rede local) somente se o intervalo de endereços IP é diferente do intervalo de endereço IP local, como Azure não dá suporte a sub-redes alongamento.  Isso significa que se você tiver um 192.168.1.0/24 sub-rede local, você não pode adicionar um 192.168.1.0/24 rede local na rede do Azure. Isso é previsto porque Azure não souber que não há nenhum VMs ativas na sub-rede e que a sub-rede está sendo criada somente para fins de DR. Para poder corretamente rotear o tráfego de rede fora de uma rede Azure as sub-redes na rede e a rede de local não deve ter conflito. 

![Antes do Failover de sub-rede](./media/site-recovery-network-design/network-design7.png)

Antes do failover

Para ajudar o Woodgrove atender suas necessidades de negócios, precisamos implementar os seguintes fluxos de trabalho:

- Criar uma rede adicional, deixe-no chamada de rede de recuperação, onde máquinas virtuais sobre falha seria criadas.
- Para garantir que o IP de uma máquina virtual é mantido após um failover, vá para a guia Configurar em Propriedades de máquina virtual, especifique o mesmo IP que a máquina virtual tem local e clique em Salvar. Quando a máquina virtual é Falha ao longo, recuperação de Site do Azure atribuirá o IP fornecido na máquina virtual. 

![Propriedades de rede](./media/site-recovery-network-design/network-design8.png)

Depois que o failover é disparado e as máquinas virtuais são criadas na rede recuperação com o IP desejado, conectividade a esta rede pode ser estabelecida usando um [Vnet para Conexão de Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Se necessário esta ação pode ser por script.  Como discutimos na seção anterior sobre failover sub-rede, mesmo no caso de failover para Azure, rotas teria adequadamente ser modificada para refletir que essa 192.168.1.0/24 agora foi movido para o Azure. 

![Após Failover sub-rede](./media/site-recovery-network-design/network-design9.png)

Após failover

Se você não tiver um 'rede Azure' conforme mostrado na imagem acima. Você pode criar uma conexão vpn site a site entre o 'Site principal' e 'Recuperação de rede' após o failover.  


## <a name="option-2-changing-ip-addresses"></a>Opção 2: Alterar endereços IP

Essa abordagem parece ser mais comuns com base no que temos visto. Ele assume a forma de alterar o endereço IP de cada máquina virtual envolvido no failover. Uma desvantagem dessa abordagem requer a rede entrada ' saber ' que agora é o aplicativo que estava em IPx em IPy. Mesmo se IPx e IPy nomes lógicos, geralmente têm entradas DNS sejam alterados ou limpo toda a rede e entradas em cache nas tabelas de rede precisam ser atualizada ou limpo, portanto um tempo de inatividade pode ser visto dependendo de como a infraestrutura de DNS foi configurado. Esses problemas podem ser minimizados usando valores TTL baixos no caso de aplicativos de intranet e [Gerenciador de tráfego Azure com recuperação automatizada do sistema](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) para aplicativos de internet com base

### <a name="changing-the-ip-addresses---illustration"></a>Alterando os endereços IP - ilustração

Vamos examine o cenário onde você planeja usar IPs diferentes entre o principal e os sites de recuperação. No exemplo a seguir, podemos também têm um terceiro local de onde os aplicativos hospedados no principal ou recuperação site pode ser acessado.

![IP diferentes - antes do Failover](./media/site-recovery-network-design/network-design10.png)

Figura 11

Figura 11 há alguns aplicativos hospedados em sub-rede de 192.168.1.0/24 sub-rede no local principal, e eles foram configurados para aparecem no site de recuperação sub-rede 172.16.1.0/24 após um failover. Rotas de conexões/rede VPN foram configuradas adequadamente para que podem acessar todos os três sites uns aos outros.
 
Como Figura 12 mostra, após falhar um ou mais aplicativos, eles serão restaurados na sub-rede recuperação. Nesse caso nós não são restritos a failover sub-rede inteira ao mesmo tempo. Nenhuma alteração é necessárias para reconfigurar rotas VPN ou na rede. Um failover e algumas atualizações DNS serão garantir que os aplicativos permaneçam acessíveis. Se o DNS estiver configurado para permitir atualizações dinâmicas, em seguida, as máquinas virtuais faria se registram usando o novo IP depois iniciarem após um failover. 

![IP diferentes - após Failover](./media/site-recovery-network-design/network-design11.png)

Figura 12

Após a falha da máquina virtual de réplica pode ter um endereço IP que não é a mesma que o endereço IP da máquina virtual primária. Máquinas virtuais atualizará o servidor DNS que elas estiverem usando após iniciarem. Entradas DNS geralmente precisam ser alterado ou limpo em toda a rede e entradas em cache nas tabelas de rede precisam ser atualizados ou limpo, portanto, não é incomum ser enfrentados com o tempo de inatividade enquanto essas alterações de estado ocorrem. Esse problema pode ser minimizado por:

- Usando valores TTL baixos para aplicativos de intranet.
- Usando o Gerenciador de tráfego do Azure com ASR de internet com base em aplicativos.
- Usando o seguinte script em seu plano de recuperação para atualizar o servidor DNS para garantir uma atualização em tempo hábil (o script não é necessário se o registro de DNS dinâmico estiver configurado)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Alterando os endereços IP – DR para o Azure

A postagem de blog de [Instalação da infraestrutura de rede para o Microsoft Azure como um Site de recuperação de desastres](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica como configurar a infraestrutura de rede Azure necessária quando reter endereços IP não é um requisito. Ele começa com descrevendo o aplicativo e, em seguida, procure em como configurar a rede local e em Azure e concluindo com como fazer um failover de teste e um failover planejado.



## <a name="next-steps"></a>Próximas etapas

[Saiba](site-recovery-network-mapping.md) como recuperação de Site mapas de origem e destino redes quando um servidor VMM está sendo usado para gerenciar o site principal. 
