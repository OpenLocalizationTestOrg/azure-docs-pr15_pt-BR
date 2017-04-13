<properties
pageTitle="Considerações para usar imagens de máquina virtual do Oracle | Microsoft Azure"
description="Saiba mais sobre as configurações suportadas e limitações para uma VM Oracle no servidor Windows Azure antes de implantar."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="09/06/2016"
ms.author="rclaus" />

#<a name="miscellaneous-considerations-for-oracle-virtual-machine-images"></a>Diversas considerações para imagens de máquinas virtuais do Oracle



Este artigo aborda considerações para máquinas virtuais Oracle no Azure, que são baseadas em imagens de software Oracle fornecidas pelo Oracle.  

-  Imagens de máquina virtual do banco de dados Oracle
-  Imagens de máquinas virtuais do servidor WebLogic Oracle
-  Imagens de máquinas virtuais do Oracle JDK

##<a name="oracle-database-virtual-machine-images"></a>Imagens de máquina virtual do banco de dados Oracle
### <a name="clustering-rac-is-not-supported"></a>Não há suporte para cluster (RAC)

Azure atualmente não oferece suporte para Oracle Real Application Clusters (RAC) do banco de dados Oracle. Somente autônomo instâncias de banco de dados Oracle são possíveis. Isso ocorre porque o Azure atualmente não oferece suporte virtual compartilhamento de disco de uma maneira de leitura/gravação entre várias instâncias de máquina virtual. UDP seletiva também não é suportada.

### <a name="no-static-internal-ip"></a>Nenhum estático IP interno

Azure atribui a cada máquina virtual um endereço IP interno. A menos que a máquina virtual faz parte de uma rede virtual, o endereço IP da máquina virtual é dinâmico e pode ser alterada após reiniciar a máquina virtual. Isso pode causar problemas porque o banco de dados do Oracle espera que o endereço IP seja estático. Para evitar o problema, considere a adição da máquina virtual a uma rede Virtual do Azure. Para obter mais informações, consulte [criar uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) e [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) .

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco anexado

Você pode colocar os arquivos de dados do disco de sistema operacional da máquina virtual ou em anexado discos, também conhecido como dados. Discos anexados podem oferecer o melhor desempenho e tamanho flexibilidade que o disco de sistema operacional. O disco de sistema operacional pode ser preferível somente para bancos de dados em 10 GB (Gigabytes).

Discos anexados contam com o serviço de armazenamento de Blob do Azure. Cada disco é capaz de teórico máximo aproximadamente 500 operações de entrada/saída por segundo (IOPS). O desempenho de discos anexados pode não estar ideal inicialmente e desempenho de IOPS pode melhorar consideravelmente após um período de "gravação-in" de aproximadamente 60-90 minutos de operação contínua. Se um disco subsequentemente permanecer ocioso, desempenho IOPS pode diminuir até outra gravação no período de operação contínua. Em resumo, o ativo mais um disco, mais provável é um desempenho ideal IOPS abordagem.

Embora a abordagem mais simples é anexar um único disco na máquina virtual e colocar arquivos de banco de dados no disco, essa abordagem também é mais limitação em termos de desempenho. Em vez disso, você pode melhorar o desempenho de IOPS eficaz com frequência se você usa vários discos anexados, difundir dados de banco de dados entre eles e em seguida, use o gerenciamento de armazenamento automático Oracle (ASM). Consulte [Visão geral de armazenamento automático do Oracle](http://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações. Embora seja possível usar a distribuição de vários discos no nível do sistema operacional, essa abordagem não é recomendada porque não é conhecido para melhorar o desempenho de IOPS.

Considere duas abordagens diferentes para anexar vários discos com base em se deseja priorizar o desempenho de operações de leitura ou operações do banco de dados de gravação:

- **Oracle ASM por conta própria** é provável resultam em melhor desempenho de operação de gravação, mas pior IOPS para operações de leitura em comparação com a abordagem usando matrizes de disco. A ilustração a seguir ilustra logicamente nessa disposição.  
    ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Avaliar a troca entre desempenho de gravação e desempenho de leitura em uma base por caso. Resultados reais podem variar quando você usá-lo.

### <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações de recuperação de desastres e disponibilidade altas

Ao usar o banco de dados Oracle em Azure máquinas virtuais, você é responsável por implementar uma solução de recuperação de desastres e disponibilidade alta para evitar qualquer tempo de inatividade. Você também é responsável pelo backup dos seus próprios dados e o aplicativo.

Alta disponibilidade e recuperação de desastres para Oracle Database Enterprise Edition (sem RAC) no Azure pode ser obtida usando [protetor de dados, Active protetor de dados](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)ou [Oracle dourada Gate](http://www.oracle.com/technetwork/middleware/goldengate), com dois bancos de dados em duas máquinas virtuais separadas. As duas máquinas virtuais deve estar no mesmo [serviço em nuvem](virtual-machines-linux-classic-connect-vms.md) e a mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que eles possam acessar uns aos outros sobre o endereço IP persistente privado.  Além disso, é recomendável colocar as máquinas virtuais no mesmo [disponibilidade definida](virtual-machines-windows-manage-availability.md) para permitir que o Azure para colocá-los em domínios de falha separada e atualizar domínios. Somente as máquinas virtuais no serviço de nuvem mesmo podem participar o mesmo conjunto de disponibilidade. Cada máquina virtual deve ter pelo menos 2 GB de memória e 5 GB de espaço em disco.

Com proteção de dados do Oracle, alta disponibilidade pode ser obtida com um banco de dados principal uma máquina virtual, um banco de dados (espera) secundário na máquina virtual do outro e replicação unidirecional configurar entre elas. O resultado é o acesso de leitura para a cópia do banco de dados. Com Oracle GoldenGate, você pode configurar replicação bidirecional entre dois bancos de dados. Para saber como configurar uma solução de alta disponibilidade para bancos de dados usando essas ferramentas, consulte a documentação do [Active protetor de dados](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site da Oracle. Se você precisar ler e gravar acesso a cópia do banco de dados, você pode usar [Active protetor de dados Oracle](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##<a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquinas virtuais do servidor WebLogic Oracle

-  **Agrupamento é suportada apenas no Enterprise Edition.** Você está licenciado para usar WebLogic clusters somente quando usando a Enterprise Edition do WebLogic Server. Não use clusters com WebLogic Server Standard Edition.

-  **Tempos limite de Conexão:** Se seu aplicativo depende de conexões com pontos de extremidade públicos de outro serviço de nuvem Azure (por exemplo, um serviço de nível de banco de dados), o Azure poderá fechar essas conexões abertas após quatro minutos de inatividade. Isso pode afetar recursos e aplicativos que dependem de pools de conexão, porque conexões que estão inativas por mais do que esse limite talvez não seja mais permanecem válidas. Se isso afeta o aplicativo, considere a habilitação lógica "manutenção de funcionamento" em seus pools de conexão.

    Se um ponto de extremidade é *interno* à sua implantação de serviço de nuvem Azure (como um aparelho de virtual de banco de dados autônomo dentro do *mesmo* serviço de nuvem como suas máquinas virtuais de WebLogic), em seguida, a conexão é direto não dependem de Balanceador de carga Azure e, portanto, não está sujeito a um tempo limite de conexão.

-  **Não há suporte para difusão seletiva UDP.** Azure suporta UDP unicast, mas não dirigida ou transmissão. Servidor WebLogic é capaz depende dos recursos de difusão ponto a ponto UDP do Azure. Para melhor resultados depender UDP difusão ponto a ponto, recomendamos que o tamanho do cluster WebLogic sejam mantidos estático, ou ser mantidos com não mais do que 10 servidores gerenciados incluídos no cluster.

-  **Servidor WebLogic espera portas públicas e privadas para ser o mesmo para acesso de T3 (por exemplo, ao usar o Enterprise JavaBeans).** Considere um cenário de vários nível onde um aplicativo de serviço de camada (EJB) está sendo executado em um cluster de servidor WebLogic consiste em duas ou mais servidores gerenciados, em um serviço de nuvem denominada **SLWLS**. A camada do cliente está em um serviço de nuvem diferentes, executando um programa Java simple tentando chamar EJB na camada de serviço. Porque ele é necessário para a camada de serviço de balanceamento de carga, um ponto de extremidade público balanceamento de carga precisa ser criada para as máquinas virtuais no cluster WebLogic Server. Se a porta particular que você especificar para esse ponto de extremidade é diferente da porta pública (por exemplo, 7006:7008), ocorrerá um erro como o seguinte:

        [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

        Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

    Isso ocorre porque para qualquer acesso remoto do T3, servidor WebLogic espera a porta de Balanceador de carga e a porta do servidor WebLogic gerenciado para ser o mesmo. No caso acima, o cliente está acessando porta 7006 (a porta de Balanceador de carga) e o servidor gerenciado é listening em 7008 (a porta particular). Essa restrição se aplica somente para acesso de T3, não HTTP.

    Para evitar esse problema, use uma das seguintes soluções:

    -  Use os mesmos números de porta pública e privada para pontos de extremidade de balanceamento de carga dedicados ao acesso de T3.

    -  Incluem o seguinte parâmetro JVM ao iniciar o servidor WebLogic:

            -Dweblogic.rjvm.enableprotocolswitch=true

Para obter informações relacionadas, consulte KB artigo **860340.1** em <http://support.oracle.com>.

-  **Dinâmico agrupamento e limitações de balanceamento de carga.** Digamos que você queira usar um cluster dinâmico no servidor WebLogic e expô-lo por meio de um único, público balanceamento de carga ponto de extremidade no Azure. Isso pode ser feito desde que você use um número de porta fixa para cada um dos servidores gerenciados (atribuídos não dinamicamente de um intervalo) e não inicia mais servidores gerenciados que há máquinas que o administrador está acompanhando (ou seja, não mais de um servidor gerenciado por máquina virtual). Se sua configuração resulta em mais servidores WebLogic sendo iniciados que existem em máquinas virtuais (ou seja, onde várias instâncias de servidor WebLogic compartilham a mesma máquina virtual), e em seguida, não é possível para mais de um às instâncias de servidores WebLogic Server para vincular a um determinado número de porta – a outras pessoas na máquina virtual falhará.

    Por outro lado, se você configurar o servidor de administrador para atribuir automaticamente os números de porta exclusivos para os servidores gerenciados, em seguida, balanceamento de carga não é possível porque Azure não dá suporte a mapeamento de uma única porta pública para várias portas particulares, como seria necessário para esta configuração.

-  **Várias instâncias Weblogic Server em uma máquina virtual.** Dependendo das necessidades da sua implantação, você pode considerar a opção de executar várias instâncias do WebLogic Server na máquina virtual, mesma se a máquina virtual for muito grande. Por exemplo, em uma máquina virtual do tamanho médio, que contém duas cores, você pode optar por executar duas instâncias do servidor WebLogic. Observe, contudo, que ainda recomendamos que você evite introduzindo pontos únicos de falha na sua arquitetura, que seria o caso se você usou apenas uma máquina virtual que esteja executando várias instâncias do servidor WebLogic. Usar pelo menos duas máquinas virtuais poderia ser uma abordagem melhor e, em seguida, cada uma dessas máquinas virtuais pode executar várias instâncias do servidor WebLogic. Cada uma dessas instâncias do servidor WebLogic ainda pode fazer parte do mesmo cluster. Observe, no entanto, atualmente não é possível usar o Azure para pontos de extremidade de balanceamento de carga que são expostos por tais implantações de servidor WebLogic dentro mesma máquina virtual, porque balanceador de carga Azure requer os servidores de balanceamento de carga a ser distribuído entre exclusivas máquinas virtuais.

##<a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquinas virtuais do Oracle JDK

-  **JDK 6 e 7 atualizações mais recentes.** Enquanto recomendamos usando a versão mais recente pública, com suporte do Java (atualmente Java 8), Azure também disponibiliza JDK 6 e 7 imagens. Isso é destinado aplicativos legados que ainda não estiver prontos para ser atualizado para JDK 8. Enquanto atualizações de imagens JDK anteriores podem não estar mais disponíveis para o público em geral, fornecido a parceria da Microsoft com Oracle, o JDK 6 e 7 imagens fornecidas pelo Azure devem conter uma atualização mais recente não público que normalmente é oferecida pela Oracle a apenas um grupo seleto de clientes com suporte da Oracle. Novas versões das imagens JDK estarão disponíveis ao longo do tempo com versões atualizadas do JDK 6 e 7.

    O JDK disponível neste JDK 6 e 7 imagens e as máquinas virtuais e imagens derivadas deles, só pode ser usado dentro do Azure.

-  **JDK de 64 bits.** As imagens de máquina virtual do Oracle WebLogic Server e as imagens de máquina virtual do Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

##<a name="additional-resources"></a>Recursos adicionais
[Imagens de máquinas virtuais do Oracle do Azure](virtual-machines-linux-classic-oracle-images.md)
