<properties
    pageTitle="Proteger o Active Directory e DNS com recuperação de Site Azure | Microsoft Azure"
    description="Este artigo descreve como implementar uma solução de recuperação do Active Directory usando a recuperação de Site do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteger o Active Directory e DNS com recuperação de Site Azure

Aplicativos corporativos como o SharePoint, Dynamics AX e SAP dependem do Active Directory e uma infraestrutura DNS funcione corretamente. Quando você cria uma solução de recuperação de desastres para aplicativos, é importante lembrar que você precisa proteger e recuperar o Active Directory e DNS antes dos outros componentes de aplicativo, para garantir que as coisas funcionam corretamente quando desastre.

Recuperação de site é um serviço Azure que fornece recuperação de dados por coordenação replicação, failover e recuperação de máquinas virtuais. Recuperação de site é compatível com um número de cenários de replicação consistentemente proteger, perfeitamente máquinas virtuais de failover e aplicativos para nuvens hoster, público ou particular.

Usando a recuperação de Site, você pode criar um plano de recuperação de desastres automatizada concluída para o Active Directory. Quando ocorrem interrupções, você pode iniciar um failover em segundos de qualquer lugar e obter Active Directory configurar e utilizar em poucos minutos. Se você tiver implantado o Active Directory para vários aplicativos como o SharePoint e SAP em seu site principal e quiser falhar ao longo de todo o site, você pode falhar ao longo do Active Directory usando primeiro a recuperação do Site e, em seguida, falhar sobre os outros aplicativos utilizando os planos de recuperação de aplicativos específicos.

Este artigo explica como criar uma solução de recuperação para Active Directory, como realizar planejadas, não planejadas e failovers de teste usando um plano de recuperação de um único clique, configurações suportadas e os pré-requisitos.  Antes de começar, você deve estar familiarizado com o Active Directory e recuperação de Site do Azure.

Há duas opções recomendadas baseadas na complexidade de seu ambiente.

### <a name="option-1"></a>Opção 1

Se você tiver um pequeno número de aplicativos e um único controlador de domínio e quiser falhar ao longo de todo o site, é recomendável usando a recuperação de Site para replicar o controlador de domínio para o site secundário (se você está falhando ao longo do Azure ou um site secundário). A mesma máquina virtual replicada pode ser usada para testar failover muito.

### <a name="option-2"></a>Opção 2

Se você tiver um grande número de aplicativos e há mais de um controlador de domínio no ambiente, ou se você planeja Falha ao longo de alguns aplicativos por vez, recomendamos que além de replicação na máquina virtual do domínio controlador com recuperação de Site que você também configurará um controlador de domínio adicionais no site de destino (Azure ou um data center secundário no local).

>[AZURE.NOTE] Mesmo se você estiver implementando opção-2, para fazer um failover de teste que ainda será necessário duplicar o controlador de domínio usando a recuperação do Site. Leia [Testar failover considerações](#considerations-for-test-failover) para obter mais informações.


As seções a seguir explicam como habilitar proteção para um controlador de domínio no Site de recuperação e como configurar um controlador de domínio no Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Uma implantação local do servidor do Active Directory e DNS.
- Um compartimento de serviços de recuperação de Site do Azure em uma assinatura do Microsoft Azure.
- Se você estiver replicar Azure executar a ferramenta de avaliação de prontidão de máquina Virtual do Azure em VMs para garantir que eles são compatíveis com o Azure VMs e serviços de recuperação de Site do Azure.


## <a name="enable-protection-using-site-recovery"></a>Habilitar proteção usando a recuperação de Site


### <a name="protect-the-virtual-machine"></a>Proteger a máquina virtual

Habilite proteção da máquina virtual domínio controlador/DNS no Site de recuperação. Definir configurações de recuperação de Site com base no tipo de máquina virtual (Hyper-V ou VMware). Recomendamos que uma frequência de replicação consistente falha de 15 minutos.

###<a name="configure-virtual-machine-network-settings"></a>Definir configurações de rede de máquina virtual

Para a máquina virtual DNS/controlador de domínio, definir configurações de rede na recuperação de Site para que a máquina virtual será anexada à rede correta após failover. Por exemplo, se você estiver replicar VMs Hyper-V no Azure você pode selecionar a máquina virtual de na nuvem VMM ou no grupo de proteção para definir as configurações de rede, conforme mostrado abaixo

![Configurações de rede de máquina virtual](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteger o Active Directory com replicação do Active Directory

### <a name="site-to-site-protection"></a>Proteção de-to-site

Criar um controlador de domínio no site secundário e especifique o nome do mesmo domínio que está sendo usado no local principal quando você promove o servidor para uma função de controlador de domínio. Você pode usar o snap-in **serviços e Sites do Active Directory** para definir configurações no objeto do link de site ao qual os sites serão adicionados. Definindo configurações em um link de site, você pode controlar quando a replicação ocorre entre dois ou mais sites e frequência. Consulte o [Agendamento de replicação entre Sites](https://technet.microsoft.com/library/cc731862.aspx) para obter mais detalhes.

###<a name="site-to-azure-protection"></a>Proteção de site para Azure

Siga as instruções para [criar um controlador de domínio em uma rede virtual Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando você promove o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que é usado no local principal.

[Reconfigurar o servidor DNS para a rede virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), usar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Considerações de failover de teste

Teste failover ocorre em uma rede que tem isolada da rede de produção para que não há nenhum impacto em cargas de trabalho de produção.

A maioria dos aplicativos também exigir a presença de um controlador de domínio e um servidor DNS para funcionar, portanto, antes do aplicativo falha sobre um controlador de domínio precisa ser criados em rede isolada a ser usado para failover de teste. A maneira mais fácil de fazer isso é habilitar proteção na máquina virtual domínio controlador/DNS com recuperação de Site e execute um failover de teste da máquina virtual, antes de executar um failover de teste do plano de recuperação de aplicativo. Veja aqui como fazer isso:

1. Habilite proteção no Site de recuperação da máquina de virtual de controlador/DNS do domínio.
2. Crie uma rede isolada. Qualquer rede virtual criado no Azure por padrão é isolada de outras redes. Recomendamos que o intervalo de endereços IP para esta rede é igual da sua rede de produção. Não permitem conectividade to-site nesta rede.
3. Forneça um endereço IP DNS da rede criado, como o endereço IP que você espera máquina virtual DNS obter. Se você estiver replicar no Azure, em seguida, forneça o endereço IP para a máquina virtual que será usada no failover na configuração de **IP de destino** nas propriedades de máquina virtual. Se você estiver duplicar para outro local site e você estiver usando o acompanhamento DHCP as instruções para [Configurar o DNS e DHCP para failover de teste](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] O endereço IP alocado a uma máquina virtual durante um failover de teste é igual o endereço IP-teria durante um failover planejado ou, se o endereço IP está disponível na rede de failover de teste. Se não for, a máquina virtual recebe um endereço IP diferente que está disponível na rede de failover de teste.

4. Na máquina virtual domínio controlador executar um failover de teste da rede isolada. Use o ponto de recuperação consistente mais recente do aplicativo disponível da máquina domínio controlador virtual para o failover de teste. 
5. Execute um failover de teste para o plano de recuperação de aplicativo.
6. Depois de teste for concluído, marca o trabalho de failover de teste de máquina de virtual do controlador de domínio e o plano de recuperação 'Concluída' na guia **trabalhos** no portal de recuperação do Site.

### <a name="dns-and-domain-controller-on-different-machines"></a>Controlador de DNS e de domínio em máquinas diferentes

Se o DNS não estiver na mesma máquina virtual como o controlador de domínio, você precisará criar uma VM DNS para o failover de teste. Se eles estiverem na mesma VM, você pode ignorar esta seção.

Você pode usar um servidor DNS clara e criar todas as zonas necessárias. Por exemplo, se seu domínio do Active Directory for contoso.com, você pode criar uma zona DNS com o nome contoso.com. As entradas correspondente ao Active Directory devem ser atualizadas no DNS, da seguinte maneira:

1. Certifique-se de que essas configurações estão no lugar antes de qualquer outra máquina virtual no plano de recuperação surgir:

    - A zona deve ser nomeada após o nome de raiz da floresta.
    - A zona deve ser feito backup de arquivo.
    - A zona deve estar habilitada para atualizações seguras e não seguras.
    - A resolução da máquina virtual domínio controlador deve apontar para o endereço IP da máquina virtual DNS.

2. Execute o seguinte comando na máquina de virtual do controlador de domínio:

    `nltest /dsregdns`

3. Adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para ele DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Próximas etapas

Leitura [quais cargas de trabalho posso proteger?](../site-recovery/site-recovery-workload.md) para saber mais sobre como proteger cargas de trabalho corporativo com recuperação de Site do Azure.
