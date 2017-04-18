<properties
    pageTitle="Padrões de aplicativos do SQL Server em VMs | Microsoft Azure"
    description="Este artigo aborda padrões de aplicativos para o SQL Server em VMs do Azure. Ele fornece solução arquitetos e desenvolvedores uma base para design e arquitetura de aplicativo bons."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Padrões de aplicativo e estratégias de desenvolvimento para SQL Server no Azure máquinas virtuais

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Resumo:
Determinar quais padrão de aplicativo ou padrões de uso de aplicativos do SQL Server são baseados no Azure ambiente é uma decisão de design importantes e requer uma compreensão sólida de como o SQL Server e cada componente de infraestrutura do Azure funcionam juntos. Com o SQL Server nos serviços de infraestrutura do Azure, você pode facilmente migrar, manter e monitorar seus aplicativos do SQL Server existentes criados no Windows Server máquinas virtuais no Azure.

O objetivo deste artigo é fornecer uma base de solução arquitetos e desenvolvedores de arquitetura de aplicativo bons e design, que eles podem seguir quando Migrando aplicativos existentes para Azure, bem como desenvolver novos aplicativos no Azure.

Para cada padrão do aplicativo, você encontrará um cenário de locais, seu respectiva solução habilitados para a nuvem e as recomendações técnicas relacionadas. Além disso, o artigo discute estratégias de desenvolvimento do Azure específicas para que você pode criar seus aplicativos corretamente. Devido a muitos padrões de aplicativo possíveis, é recomendável que arquitetos e desenvolvedores devem escolher o padrão mais apropriado para seus aplicativos e usuários.

**Colaboradores técnicos:** Luís Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Nuno se Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introdução

Você pode desenvolver vários tipos de aplicativos de n camadas, separando os componentes das camadas aplicativo diferente em máquinas diferentes, bem como nos componentes separados. Por exemplo, você pode colocar o aplicativo cliente e componentes em um computador, camada web front-end e componentes de nível de acesso de dados em outro computador e uma camada de banco de dados back-end em outra máquina de regras de negócios. Esse tipo de estruturar ajuda a isolar cada camada uns dos outros. Se você alterar a origem dos dados, você não precisa alterar o aplicativo web ou de cliente, mas apenas os componentes de nível de acesso dados.

Um aplicativo típico *fileiras* inclui a camada de apresentação, a camada de negócios e a camada de dados:


| Camada              | Descrição                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Apresentação** | A *camada de apresentação* (camada web, camadas front-end) é a camada na qual os usuários interagem com um aplicativo.                                                                      |
| **Business**     | O *nível de negócios* (camada intermediária) é a camada que a camada de apresentação e a camada de dados usam para se comunicar com os outros e inclui a funcionalidade de núcleo do sistema. |
| **Dados**         | A *camada de dados* é basicamente o servidor que armazena dados de um aplicativo (por exemplo, um servidor executando o SQL Server).                                                             |

Camadas de aplicativo descrevem os agrupamentos lógicos da funcionalidade e componentes em um aplicativo; enquanto níveis descrevem a distribuição física da funcionalidade e componentes em servidores físicos separados, computadores, redes ou locais remotos. As camadas de um aplicativo podem residem no mesmo computador físico (o mesmo nível) ou podem ser distribuídas ao longo de computadores separados (fileiras) e os componentes em cada camada se comunicar com componentes em outras camadas por meio de interfaces bem definidas. Você pode considerar a camada de termos como consultando os padrões de distribuição física como duas camadas, três camadas e fileiras. Um **padrão de aplicativo de 2 camadas** contém dois níveis de aplicativos: servidor de aplicativo e servidor de banco de dados. A comunicação direta acontece entre o servidor de aplicativo e o servidor de banco de dados. O servidor de aplicativo contém componentes da camada da web e camada de negócios. Em **padrão de aplicativo de nível 3**, existem três níveis de aplicativo: server, servidor de aplicativos, que contém a camada de lógica de negócios e/ou componentes de acesso de dados de camada de negócios e o servidor de banco de dados da web. A comunicação entre o servidor da web e o servidor de banco de dados acontece sobre o servidor de aplicativos. Para obter informações detalhadas sobre camadas e camadas de aplicativo, consulte [O guia de arquitetura de aplicativo Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar lendo este artigo, você deve ter conhecimento sobre os conceitos fundamentais do SQL Server e o Azure. Para obter informações, consulte [Manuais Online do SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve diversos padrões de aplicativos que podem ser adequados para seus aplicativos simples, bem como os aplicativos corporativos altamente complexas. Antes de Detalhar cada padrão, recomendamos que você deve se familiarizar com os serviços de armazenamento de dados disponíveis no Azure, como [O armazenamento do Azure](../storage/storage-introduction.md), [Banco de dados do SQL Azure](../sql-database/sql-database-technical-overview.md)e [SQL Server em uma máquina Virtual do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tornar as melhores decisões de design para seus aplicativos, compreenda quando usar qual serviço de armazenamento de dados claramente.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolha o SQL Server em uma máquina Virtual Azure, quando:

- Você precisa de controle no SQL Server e Windows. Por exemplo, isso pode incluir a versão do SQL Server hotfixes especiais, configuração de desempenho, etc.

- Você precisa de uma total compatibilidade com SQL Server local e deseja mover aplicativos existentes no Azure como-é.

- Você deseja aproveitar os recursos do ambiente do Azure mas Azure SQL Database não dá suporte a todos os recursos que seu aplicativo requer. Isso pode incluir as seguintes áreas:

    - **Tamanho do banco de dados**: no momento este artigo foi atualizado, o banco de dados SQL dá suporte a um banco de dados de até 1 TB de dados. Se seu aplicativo exigir mais de 1 TB de dados e você não quer implementar soluções de fragmentação personalizado, é recomendável que você usa o SQL Server em uma máquina Virtual do Azure. Para obter as informações mais recentes, consulte [Dimensionamento Out Azure bancos de dados SQL](https://msdn.microsoft.com/library/azure/dn495641.aspx) e [níveis de serviço de banco de dados do Azure SQL e níveis de desempenho](../sql-database/sql-database-service-tiers.md).
    - **Conformidade HIPAA**: saúde clientes e fornecedores de Software independentes (ISVs) podem optar [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) em vez de [Banco de dados do Azure SQL](../sql-database/sql-database-technical-overview.md) porque o SQL Server em uma máquina Virtual do Azure está coberto por contrato de associar de negócios do HIPAA (BAA). Para obter informações sobre conformidade, consulte [Microsoft Azure Trust Center: conformidade](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Recursos de nível de instância**: no momento, o banco de dados do SQL não dá suporte a recursos do live fora do banco de dados (como servidores vinculados, agente trabalhos, FileStream, agente de serviço, etc.). Para obter mais informações, consulte [diretrizes de banco de dados do SQL Azure e limitações](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>nível 1 (simples): única máquina virtual

Nesse padrão do aplicativo, você implanta seu aplicativo do SQL Server e o banco de dados para uma máquina virtual de autônomo no Azure. Mesma máquina virtual contém seu aplicativo web do cliente, componentes de negócios, camada de acesso a dados e o servidor de banco de dados. A apresentação, comerciais e código de acesso de dados são separadas logicamente mas física estão localizados em uma máquina de servidor único. Começar a maioria dos clientes com esse padrão de aplicativo e, em seguida, eles dimensionar adicionando mais funções da web ou máquinas virtuais ao seu sistema.

Esse padrão de aplicativo é útil quando:

- Você deseja executar uma migração simple para plataforma Windows Azure para avaliar se a plataforma respostas requisitos do seu aplicativo ou não.

- Você deseja manter todas as camadas de aplicativo hospedadas na mesma máquina virtual no mesmo Azure data center para reduzir a latência entre os níveis.

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Deseja executar Enfatize testes de vários níveis de carga de trabalho, mas ao mesmo tempo que você deseja não pertence e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um simples local cenário e como você pode implantar sua solução de nuvem habilitado em uma única máquina virtual no Azure.

![padrão de aplicativo de nível 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implantando a camada de negócios (lógica de negócios e componentes de acesso a dados) na mesma camada física como a camada de apresentação pode maximizar o desempenho do aplicativo, a menos que você deve usar uma camada separada devido a preocupações escalabilidade ou segurança.

Como isso é começar com um padrão de muito comum, podem ser úteis o seguinte artigo sobre migração para mover seus dados para o seu servidor de SQL máquina virtual: [migrar um banco de dados SQL Server em uma máquina virtual do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>nível 3 (simples): várias máquinas virtuais

Nesse padrão do aplicativo, você pode implantar um aplicativo de nível 3 no Azure colocando cada camada de aplicativo em uma máquina virtual diferente. Isso fornece um ambiente flexível para uma fácil cenários de escala para cima e fora de escala. Quando uma máquina virtual contém seu aplicativo web do cliente, as outras um hospeda componentes comerciais e outros aquele que hospeda o servidor de banco de dados.

Esse padrão de aplicativo é útil quando:

- Você deseja executar uma migração de aplicativos de banco de dados complexos para máquinas virtuais do Azure.

- Você quer diferentes níveis de aplicativos sejam hospedados em diferentes regiões. Por exemplo, você pode ter bancos de dados compartilhados que são implantados em várias regiões para fins de relatório.

- Você deseja mover aplicativos corporativos de plataformas de local virtualizado para máquinas virtuais do Azure. Para uma discussão detalhada sobre aplicativos corporativos, consulte [o que é um aplicativo corporativo](https://msdn.microsoft.com/library/aa267045.aspx).

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Deseja executar Enfatize testes de vários níveis de carga de trabalho, mas ao mesmo tempo que você deseja não pertence e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra como você pode colocar um aplicativo de nível 3 simples no Azure colocando cada camada de aplicativo em uma máquina virtual diferente.

![padrão de aplicativo de nível 3](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Nesse padrão do aplicativo, há apenas uma máquina virtual (VM) em cada camada. Se você tiver várias VMs no Azure, é recomendável que você configure uma rede virtual. [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) cria um limite de segurança confiável e também permite VMs para se comunicar entre si sobre o endereço IP privado. Além disso, sempre Certifique-se de que todas as conexões de Internet só ir para a camada de apresentação. Quando seguir esse padrão do aplicativo, gerencie as regras de grupo de segurança de rede para controlar o acesso. Para obter mais informações, consulte [Permitir acesso externo para sua máquina virtual usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md).

No diagrama, protocolos Internet pode ser TCP, UDP, HTTP ou HTTPS.

>[AZURE.NOTE] Configurando uma rede virtual no Azure é gratuito. No entanto, cobrado para o gateway VPN que se conecta ao local. A cobrança baseia-se na quantidade de tempo que conexão está provisionado e disponível.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>dimensionamento de nível 2 e 3-camadas com uma apresentação

Nesse padrão do aplicativo, você pode implantar o aplicativo de banco de dados 2 ou 3-camadas para máquinas virtuais do Azure colocando cada camada de aplicativo em uma máquina virtual diferente. Além disso, você dimensionar a camada de apresentação devido a maior volume de solicitações de entrada de cliente.

Esse padrão de aplicativo é útil quando:

- Você deseja mover aplicativos corporativos de plataformas de local virtualizado para máquinas virtuais do Azure.

- Você deseja dimensionar a camada de apresentação devido a maior volume de solicitações de entrada de cliente.

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Deseja executar Enfatize testes de vários níveis de carga de trabalho, mas ao mesmo tempo que você deseja não pertence e manter muitas máquinas físicas o tempo todo.

- Você deseja o proprietário de um ambiente de infraestrutura que pode ser dimensionada de cima e para baixo sob demanda.

O diagrama a seguir demonstra como você pode colocar as camadas do aplicativo em várias máquinas virtuais no Azure dimensionando a camada de apresentação devido a maior volume de solicitações de entrada de cliente. Como visto no diagrama, balanceador de carga do Azure é responsável por distribuir o tráfego em várias máquinas virtuais e também determinar qual servidor web para se conectar ao. Ter várias instâncias dos servidores web atrás de um balanceador de carga garante a alta disponibilidade da camada de apresentação.

![Padrão de aplicativo - escala de camada de apresentação check-out](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Práticas recomendadas para padrões de 2, 3 camadas ou n-camadas que têm várias VMs em um nível

É recomendável que você coloque as máquinas virtuais que pertençam a mesma camada no serviço de nuvem mesmo e na mesma a disponibilidade definida. Por exemplo, colocar um conjunto de servidores web em um conjunto de servidores de banco de dados no **CloudService2** e **AvailabilitySet2**, **AvailabilitySet1** e **CloudService1** . Uma disponibilidade definida no Azure permite que você colocar os nós de alta disponibilidade em domínios de falha separada e atualizar domínios.

Para aproveitar várias instâncias de máquina virtual de uma camada, você precisa configurar balanceador de carga do Azure entre os níveis de aplicativo. Para configurar balanceador de carga em cada camada, crie um ponto de extremidade de balanceamento de carga em VMs da cada camada separadamente. Para um nível específico, primeiro crie VMs no serviço de nuvem do mesmo. Isso garante que eles tenham o mesmo endereço IP Virtual público. Em seguida, crie um ponto de extremidade em uma das máquinas virtuais naquele nível. Em seguida, atribua o mesmo ponto de extremidade para as outras máquinas virtuais naquele nível para balanceamento de carga. Criando um conjunto de balanceamento de carga, você distribuir o tráfego em várias máquinas virtuais e também permitem que o balanceador de carga determinar qual nó para se conectar quando falha de um nó de máquina virtual de back-end. Por exemplo, com várias instâncias dos servidores web atrás de um balanceador de carga garante a alta disponibilidade da camada de apresentação.

Como prática recomendada, sempre Certifique-se de que todas as conexões de internet primeiro acessem a camada de apresentação. A camada de apresentação acessa a camada de negócios e, em seguida, a camada de negócios acessa a camada de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte [Permitir acesso externo para sua máquina virtual usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Observe que o balanceador de carga no Azure funciona semelhante ao carregar balanceadores em um ambiente local. Para obter mais informações, consulte [balanceamento de carga para serviços de infraestrutura Azure](virtual-machines-windows-load-balance.md).

Além disso, é recomendável que você configurar uma rede privada para suas máquinas virtuais usando rede Virtual do Azure. Isso permite que eles se comuniquem entre si sobre o endereço IP privado. Para obter mais informações, consulte [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>dimensionamento de nível 2 e 3-camadas com business

Nesse padrão do aplicativo, você pode implantar o aplicativo de banco de dados 2 ou 3-camadas para máquinas virtuais do Azure colocando cada camada de aplicativo em uma máquina virtual diferente. Além disso, talvez você queira distribuir os componentes do servidor de aplicativo para várias máquinas virtuais devido a complexidade do seu aplicativo.

Esse padrão de aplicativo é útil quando:

- Você deseja mover aplicativos corporativos de plataformas de local virtualizado para máquinas virtuais do Azure.

- Você deseja distribuir os componentes do servidor de aplicativo para várias máquinas virtuais devido a complexidade do seu aplicativo.

- Você deseja mover pesado de lógica de negócios para máquinas virtuais do Azure os aplicativos LOB (linha de negócios) locais. Aplicativos LOB são um conjunto de aplicativos de computador crítico que são importantes para executar uma empresa, como contabilidade, recursos humanos (RH), folha de pagamento, gerenciamento de cadeia de fornecedores e aplicativos de planejamento de recursos.

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Deseja executar Enfatize testes de vários níveis de carga de trabalho, mas ao mesmo tempo que você deseja não pertence e manter muitas máquinas físicas o tempo todo.

- Você deseja o proprietário de um ambiente de infraestrutura que pode ser dimensionada de cima e para baixo sob demanda.

O diagrama a seguir demonstra um cenário de locais e sua solução de nuvem habilitado. Neste cenário, você pode colocar as camadas do aplicativo em várias máquinas virtuais no Azure dimensionando a camada de negócios, que contém a camada de lógica de negócios e componentes de acesso de dados. Como visto no diagrama, balanceador de carga do Azure é responsável por distribuir o tráfego em várias máquinas virtuais e também determinar qual servidor web para se conectar ao. Ter várias instâncias dos servidores de aplicativo atrás de um balanceador de carga garante a alta disponibilidade da camada comercial. Para obter mais informações, consulte [práticas recomendadas para a camada 2, 3 camadas ou padrões de aplicativo de n camadas que têm várias máquinas virtuais em um nível](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicativo com escala de nível de negócios check-out](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 e 3-camadas com apresentação e níveis de negócios escala-out e HADR

Nesse padrão do aplicativo, você pode implantar o aplicativo de banco de dados 2 ou 3-camadas para máquinas virtuais do Azure distribuindo a camada de apresentação (servidor web) e os componentes de (servidor de aplicativos) de nível de negócios para várias máquinas virtuais. Além disso, você implementar soluções de recuperação de desastres e alta disponibilidade dos bancos de dados no Azure máquinas virtuais.

Esse padrão de aplicativo é útil quando:

- Você deseja mover aplicativos corporativos do plataformas virtualizado local para Azure Implementando o SQL Server alta disponibilidade e recursos de recuperação de desastres.

- Você deseja dimensionar a camada de apresentação e a camada de negócios devido a maior volume de solicitações de entrada de cliente e a complexidade do seu aplicativo.

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Deseja executar Enfatize testes de vários níveis de carga de trabalho, mas ao mesmo tempo que você deseja não pertence e manter muitas máquinas físicas o tempo todo.

- Você deseja o proprietário de um ambiente de infraestrutura que pode ser dimensionada de cima e para baixo sob demanda.

O diagrama a seguir demonstra um cenário de locais e sua solução de nuvem habilitado. Neste cenário, você dimensiona a camada de apresentação e os componentes da camada de negócios em várias máquinas virtuais no Azure. Além disso, você implementar altas técnicas de recuperação (HADR) de desastres e disponibilidade para bancos de dados do SQL Server no Azure.

A execução de várias cópias de um aplicativo em diferentes VMs Certifique-se de que você está balanceamento de carga solicitações entre elas. Quando você tem várias máquinas virtuais, você precisa certificar-se de que todas as suas VMs são acessíveis e em execução com um ponto no tempo. Se você configurar balanceamento de carga, balanceador de carga do Azure controla a integridade de VMs e direciona chamadas de entrada para os eficaz, nós de máquina virtual está funcionando corretamente. Para obter informações sobre como configurar o balanceamento de carga de máquinas virtuais, consulte [balanceamento de carga em serviços de infraestrutura Azure](virtual-machines-windows-load-balance.md). Ter várias instâncias de servidores web e aplicativo atrás de um balanceador de carga garante a alta disponibilidade das camadas de apresentação e comercial.

![Fora de escala e alta disponibilidade](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Práticas recomendadas para padrões de aplicativos que exigem SQL HADR

Quando você configurar o SQL Server alta disponibilidade e soluções de recuperação de desastres em máquinas virtuais do Azure, configurando uma rede virtual para suas máquinas virtuais usando [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) é obrigatório.  Máquinas virtuais em uma rede Virtual terá um endereço IP privado estável mesmo depois de um tempo de inatividade de serviço, portanto, você pode evitar o tempo de atualização necessário para resolução de nomes DNS. Além disso, a rede virtual permite estender sua rede local para o Azure e cria um limite de segurança confiável. Por exemplo, se seu aplicativo tem restrições de domínio corporativo (como autenticação do Windows, do Active Directory), a configuração de [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) é necessário.

A maioria dos clientes, o que estiver executando o código de produção no Azure, impedem réplicas primárias e secundárias no Azure.

Para informações completas e tutoriais em alta disponibilidade e técnicas de recuperação de desastres, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2 e 3-camadas usando VMs do Azure e os serviços de nuvem

Nesse padrão do aplicativo, você implanta aplicativo 2 ou 3-camadas no Azure usando ambos os [Serviços de nuvem do Azure](../cloud-services/cloud-services-choose-me.md#tellmecs) (funções de web e trabalhador - plataforma como um serviço (PaaS)) e [máquinas virtuais do Azure](virtual-machines-windows-about.md) (infraestrutura como um serviço (IaaS)). Usando [Os serviços de nuvem do Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para a camada de/comercial de camada de apresentação e o SQL Server nas [máquinas virtuais do Azure](virtual-machines-windows-about.md) para a camada de dados é útil para a maioria dos aplicativos em execução no Azure. O motivo é que ter uma instância de computação em execução no serviços de nuvem fornece um gerenciamento fácil, implantação, monitoramento e fora de escala.

Com os serviços de nuvem, Azure mantém a infraestrutura para você, executa a manutenção de rotina, patches os sistemas operacionais e tenta recuperar de falhas de hardware e de serviço. Quando seu aplicativo precisa escala-out, opções de escala-out automáticas e manuais estão disponíveis para seu projeto de serviço de nuvem aumentando ou diminuindo o número de instâncias ou máquinas virtuais que são usadas pelo seu aplicativo. Além disso, você pode usar o local Visual Studio para implantar seu aplicativo a um projeto de serviço de nuvem no Azure.

Em resumo, se você não quiser proprietário considerável tarefas administrativas para a apresentação/business nível e seu aplicativo não exigir qualquer configuração complexa de software ou o sistema operacional, use os serviços de nuvem do Azure. Se o banco de dados do Azure SQL não oferece suporte a todos os recursos que você está procurando, use SQL Server em uma máquina Virtual do Azure para a camada de dados. Executando um aplicativo em serviços de nuvem do Azure e armazenar dados em máquinas virtuais do Azure combina os benefícios de ambos os serviços. Para uma comparação detalhada, consulte a seção neste tópico em [estratégias de desenvolvimento comparando no Azure](#comparing-web-development-strategies-in-azure).

Nesse padrão do aplicativo, a camada de apresentação inclui uma função da web, que é um componente de serviços de nuvem executados no ambiente de execução Azure e ela é personalizada para programação de aplicativos web como suportados pelo IIS e ASP.NET. A camada de negócios ou back-end inclui uma função de trabalho, o que é um componente de serviços de nuvem executados no ambiente de execução Azure e é útil para desenvolvimento generalizado e pode executar o processamento em segundo plano para uma função web. A camada de banco de dados reside em uma máquina virtual de SQL Server no Azure. A comunicação entre a camada de apresentação e a camada de banco de dados acontece diretamente ou pela camada de negócios – componentes de função de trabalho.

Esse padrão de aplicativo é útil quando:

- Você deseja mover aplicativos corporativos do plataformas virtualizado local para Azure Implementando o SQL Server alta disponibilidade e recursos de recuperação de desastres.

- Você deseja o proprietário de um ambiente de infraestrutura que pode ser dimensionada de cima e para baixo sob demanda.

- Banco de dados do SQL Azure não oferece suporte a todos os recursos que precisa de seu aplicativo ou banco de dados.

- Deseja executar Enfatize testes de vários níveis de carga de trabalho, mas ao mesmo tempo que você deseja não pertence e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um cenário de locais e sua solução de nuvem habilitado. Neste cenário, você deve colocar a camada de apresentação em funções da web, a camada de negócios em funções de trabalho, mas a camada de dados nas máquinas virtuais no Azure. Executar várias cópias da camada de apresentação em funções da web diferente garante para carregar solicitações de saldo entre eles. Quando você combina serviços de nuvem do Azure com máquinas virtuais do Azure, é recomendável que você configure [Uma rede Virtual Azure](../virtual-network/virtual-networks-overview.md) também. Com [Uma rede Virtual Azure](../virtual-network/virtual-networks-overview.md), você pode ter estáveis e persistentes endereços IP particulares dentro do mesmo serviço de nuvem na nuvem. Depois de definir uma rede virtual para suas máquinas virtuais e serviços em nuvem, elas podem começar a comunicação entre si sobre o endereço IP privado. Além disso, ter máquinas virtuais e funções do Azure web/trabalhador na mesma [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) oferece baixa latência e conectividade mais segura. Para obter mais informações, consulte [o que é um serviço de nuvem](../cloud-services/cloud-services-choose-me.md).

Como visto no diagrama, o balanceador de carga do Azure distribui o tráfego entre várias máquinas virtuais e também determina qual servidor web ou um servidor de aplicativo para se conectar ao. Ter várias instâncias dos servidores web e aplicativo atrás de um balanceador de carga garante a alta disponibilidade da camada de apresentação e a camada de negócios. Para obter mais informações, consulte [práticas recomendadas para padrões de aplicativos que exigem HADR de SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicativo com os serviços de nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Outra abordagem para implementar esse padrão de aplicativo é usar uma função de web consolidado que contém a camada de apresentação e componentes de camada de negócios, conforme mostrado no diagrama a seguir. Esse padrão de aplicativo é útil para aplicativos que exigem design com estado. Como Azure fornece nós de computação sem estado em funções da web e trabalhador, recomendamos que você implemente uma lógica para armazenar o estado de sessão usando um dos seguintes tecnologias: [Cache do Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [O armazenamento de tabela do Azure](../storage/storage-dotnet-how-to-use-tables.md) ou [Azure SQL Database](../sql-database/sql-database-technical-overview.md).

![Padrões de aplicativo com os serviços de nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com VMs Azure, banco de dados do SQL Azure e serviço de aplicativo do Azure (Web Apps)

O objetivo principal deste padrão de aplicativo é mostram como combinar infraestrutura Azure como um componentes de serviço (IaaS) com componentes Azure plataforma como serviço (PaaS) em sua solução. Esse padrão é voltado Azure SQL Database para armazenamento de dados relacionais. Ele não inclui do SQL Server em uma máquina virtual Azure, que é parte de infraestrutura do Azure como uma oferta de serviço.

Nesse padrão do aplicativo, você pode implantar um aplicativo de banco de dados no Azure colocando as camadas de apresentação e comercial na mesma máquina virtual e acessando um banco de dados em servidores do Azure SQL Database (banco de dados do SQL). Você pode implementar a camada de apresentação usando soluções de web baseados em IIS tradicional. Ou, você pode implementar uma apresentação combinada e a camada de negócios usando [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Esse padrão de aplicativo é útil quando:

- Você já tem um servidor de banco de dados SQL existente configurado no Azure e você deseja testar seu aplicativo rapidamente.

- Você deseja testar os recursos de ambiente Azure.

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Os componentes de acesso de negócios lógica e os dados podem ser autônomos dentro de um aplicativo web.

O diagrama a seguir demonstra um cenário de locais e sua solução de nuvem habilitado. Neste cenário, você deve colocar as camadas de aplicativos em uma única máquina virtual em dados do Azure e acesso em Azure SQL Database.

![Padrão de aplicativos mistos](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se você optar por implementar um web combinada e a camada de aplicativo usando o Azure Web Apps, recomendamos que você mantenha o nível intermediário ou aplicativo como bibliotecas de vínculo dinâmico (DLLs) no contexto de um aplicativo web.

Além disso, examine as recomendações fornecidas na seção de [estratégias de desenvolvimento de web comparando no Azure](#comparing-web-development-strategies-in-azure) no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicativo de fileiras híbrido

Em padrão de aplicativo de fileiras híbridos, você implementar seu aplicativo em vários níveis distribuídos entre locais e do Azure. Portanto, você cria um sistema híbrido flexível e reutilizáveis, que você pode modificar ou adicionar um nível específico sem alterar os outros níveis. Para estender sua rede corporativa para a nuvem, você usar o serviço de [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) .

Esse padrão de aplicativo híbrido é útil quando:

- Você deseja criar aplicativos que executar parcialmente na nuvem e parcialmente no local.

- Você deseja migrar alguns ou todos os elementos de um aplicativo local existente para a nuvem.

- Você deseja mover aplicativos corporativos de plataformas de local virtualizado para Azure.

- Você deseja o proprietário de um ambiente de infraestrutura que pode ser dimensionada de cima e para baixo sob demanda.

- Você deseja rapidamente provisionar desenvolvimento e ambientes de teste para curtos períodos de tempo.

- Você quiser uma forma econômica de fazer backups para aplicativos de banco de dados corporativos.

O diagrama a seguir demonstra um padrão de aplicativo de fileiras híbrido que abrange locais e do Azure. Como mostrado no diagrama, infraestrutura local inclui controlador de domínio [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) para oferecer suporte à autorização e autenticação de usuário. Observe que o diagrama demonstra um cenário, onde algumas partes da camada de dados ao vivo em uma central de dados local enquanto algumas partes da camada de dados ao vivo no Azure. Dependendo das necessidades do seu aplicativo, você pode implementar vários outros cenários de implantação híbrida. Por exemplo, você pode manter a camada de apresentação e a camada de negócios em um ambiente local, mas a camada de dados no Azure.

![Padrão de aplicativo de N camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

No Azure, você pode usar o Active Directory como um diretório de nuvem autônomo para sua organização ou, você também pode integrar o Active Directory local existente com o [Active Directory do Azure](https://azure.microsoft.com/documentation/services/active-directory/). Como visto no diagrama, os componentes da camada de negócios podem acessar a várias fontes de dados, como para o [SQL Server no Azure](virtual-machines-windows-sql-server-iaas-overview.md) por meio de um interno endereço IP privado, para no SQL Server local por meio de [Uma rede Virtual Azure](../virtual-network/virtual-networks-overview.md), ou a de [Banco de dados SQL](../sql-database/sql-database-technical-overview.md) usando as tecnologias de provedor de dados .NET Framework. Neste diagrama, o banco de dados do SQL Azure é um serviço de armazenamento de dados opcionais.

Em padrão de aplicativo de fileiras híbridos, você pode implementar o fluxo de trabalho a seguir na ordem especificada:

1. Identifique os aplicativos de banco de dados corporativos que precisa ser movido para a nuvem usando o [Kit de ferramentas do Planning (MAP) e avaliação da Microsoft](http://microsoft.com/map). O Kit de ferramentas do mapa reúne dados de desempenho e inventário de computadores que você estiver considerando para virtualização e fornece recomendações sobre o planejamento de avaliação e a capacidade.

1. Planeje os recursos e as configurações necessárias na plataforma Windows Azure, como contas de armazenamento e máquinas virtuais.

1. Configure a conectividade de rede entre a rede corporativa local e a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar a conexão entre a rede corporativa local e uma máquina virtual no Azure, use um dos seguintes métodos:

    1. Estabelecer uma conexão entre o local e o Azure por meio de pontos de extremidade públicos em uma máquina virtual no Azure. Este método fornece uma instalação fácil e permite que você use autenticação do SQL Server na máquina virtual. Além disso, configure suas regras de grupo de segurança de rede para controlar o tráfego público para a máquina virtual. Para obter mais informações, consulte [Permitir acesso externo para sua máquina virtual usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Estabelece uma conexão entre o local e o Azure através do Azure Virtual privada túnel VPN (rede). Este método permite que você estenda diretivas de domínio para uma máquina virtual no Azure. Além disso, você pode configurar regras de firewall e usar a autenticação do Windows em sua máquina virtual. Atualmente, o Azure suporta seguro-to-site conexões VPN e ponto-a-site VPN:

        - Com conexão to-site seguro, você pode estabelecer conectividade de rede entre sua rede local e sua rede virtual no Azure. É recomendável para conectar o seu ambiente de centro de dados local no Azure.

        - Com a conexão ponto-a-site seguro, você pode estabelecer conectividade de rede entre sua rede virtual no Azure e seus computadores individuais em execução em qualquer lugar. É recomendável principalmente para fins de desenvolvimento e teste.

    Para obter informações sobre como conectar-se ao SQL Server no Azure, veja [conectar a um SQL Server computador Virtual no Azure](virtual-machines-windows-classic-sql-connect.md).

1. Configure tarefas agendadas e alertas que fazer backup de dados no local em um disco de máquina virtual no Azure. Para obter mais informações, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [Backup e restauração para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

1. Dependendo das necessidades do seu aplicativo, você pode implementar um dos três cenários comuns a seguir:

    1. Você pode manter seu servidor web, servidor de aplicativos e diferenciação de dados em um servidor de banco de dados no Azure enquanto você mantém o dados confidenciais no local.

    1. Você pode manter seu servidor web e servidor de aplicativos do local enquanto o servidor de banco de dados em uma máquina virtual no Azure.

    1. Você pode manter seu servidor de banco de dados, o servidor web e servidor de aplicativo local enquanto mantém as réplicas de banco de dados em máquinas virtuais no Azure. Essa configuração permite que os servidores do local da web ou aplicativos de relatório para acessar as réplicas de banco de dados no Azure. Portanto, você pode obter para reduzir a carga de trabalho em um banco de dados local. Recomendamos que você implementa este cenário para pesado ler cargas de trabalho e fins de desenvolvimento. Para obter informações sobre como criar réplicas de banco de dados no Azure, consulte grupos de disponibilidade sempre ativado em [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparando estratégias de desenvolvimento web no Azure

Para implementar e implantar um aplicativo baseado em SQL Server no Azure de vários níveis, você pode usar um dos dois métodos programação a seguir:

- Configure um servidor web tradicionais (IIS - serviços de informações da Internet) em acesso e Azure bancos de dados do SQL Server em máquinas virtuais do Azure.

- Implementar e implantar um serviço de nuvem para o Azure. Em seguida, certifique-se de que esse serviço de nuvem pode acessar bancos de dados no SQL Server nas máquinas virtuais do Azure. Um serviço de nuvem pode incluir várias funções de trabalho e da web.

A tabela a seguir fornece uma comparação de desenvolvimento de web tradicional com serviços de nuvem do Azure e Azure Web Apps relacionadas com SQL Server em máquinas virtuais do Azure. A tabela inclui Azure Web Apps como é possível usar SQL Server na máquina virtual do Azure como uma fonte de dados do Azure Web Apps por meio de seu endereço IP virtual público ou o nome DNS.

||Desenvolvimento de web tradicional em máquinas virtuais do Azure|Serviços de nuvem no Azure|Hospedagem ao Azure Web Apps|
|---|---|---|---|
|**Migração de aplicativos do local**|Aplicativos existentes como-é.|Aplicativos precisam funções da web e de trabalho.|Aplicativos existentes como-mas adequado para aplicativos web independentes e serviços da web que exigem escalabilidade rápida.|
|**Desenvolvimento e implantação**|O Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gerenciador do IIS, PowerShell.|Visual Studio, SDK do Azure, TFS, PowerShell. Cada serviço de nuvem tem dois ambientes aos quais você pode implantar seu pacote de serviço e a configuração: estágios e produção. Você pode implantar um serviço de nuvem para o ambiente de teste para testá-lo antes de você a promover a produção.|O Visual Studio, WebMatrix, Visual Web Developer, FTP, GITO, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web implantar, PowerShell.|
|**Instalação e administração**|Você é responsável por tarefas administrativas no aplicativo dados, regras de firewall, rede virtual e sistema operacional.|Você é responsável por tarefas administrativas no aplicativo, dados, regras de firewall e rede virtual.|Você é responsável por tarefas administrativas aplicativo e apenas os dados.|
|**Alta disponibilidade e recuperação de dados (HADR)**|Recomendamos que você coloque máquinas virtuais no mesmo conjunto de disponibilidade e no serviço de nuvem mesmo. Manter suas VMs no mesmo conjunto de disponibilidade permite Azure para colocar os nós de alta disponibilidade em domínios de falha separada e atualizar domínios. Da mesma forma, manter suas VMs no serviço de nuvem mesmo permite balanceamento e VMs possam se comunicar diretamente uns com os outros na rede local dentro de uma central de dados do Azure.<br/><br/>Você é responsável pelo implementando uma alta disponibilidade e a solução de recuperação para SQL Server em máquinas virtuais do Azure para evitar qualquer tempo de inatividade. Para tecnologias HADR compatíveis, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Você é responsável pelo backup dos seus próprios dados e o aplicativo.<br/><br/>Azure pode mover suas máquinas virtuais se máquina host no Centro de dados falhar devido a problemas de hardware. Além disso, pode haver tempo de inatividade planejado da sua máquina virtual quando a máquina host é atualizada para software de segurança ou atualizações. Portanto, recomendamos que você mantenha pelo menos duas VMs em cada camada de aplicativo para garantir a disponibilidade contínua. Azure não oferece SLA para uma única máquina virtual. Para obter mais informações, consulte [orientações técnicas resiliência Azure](../resiliency/resiliency-technical-guidance.md).|Azure gerencia as falhas resultantes do software hardware ou sistema operacional subjacente. Recomendamos que você implemente várias instâncias de uma função da web ou de trabalho para garantir a alta disponibilidade do seu aplicativo. Para obter informações, consulte [Serviços de nuvem, máquinas virtuais e contrato de nível de serviço de rede Virtual](http://www.microsoft.com/download/details.aspx?id=38427) e [recuperação de dados e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Você é responsável pelo backup dos seus próprios dados e o aplicativo.<br/><br/>Para bancos de dados que reside em um banco de dados do SQL Server em uma máquina virtual do Azure, você é responsável por implementar uma solução de recuperação de desastres e disponibilidade alta para evitar qualquer tempo de inatividade. Para tecnologias HDAR compatíveis, consulte alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure.<br/><br/>**Espelhamento de banco de dados do SQL Server**: uso com serviços de nuvem do Azure (funções da web/trabalho). SQL Server VMs e um projeto de serviço de nuvem pode ser na mesma rede Virtual Azure. Se máquina virtual do SQL Server não estiver na mesma rede Virtual, você precisa criar um Alias de servidor SQL para rotear comunicações para a instância do SQL Server. Além disso, o nome do alias deve corresponder ao nome do SQL Server.|Alta disponibilidade é herdada do Azure trabalhador funções, armazenamento de blob do Microsoft Azure e Azure SQL Database. Por exemplo, o armazenamento do Azure mantém três réplicas de todos os dados de fila, tabela e blob. Em qualquer momento, o Azure SQL Database mantém três réplicas de execução de dados — uma réplica principal e dois secundários. Para obter mais informações, consulte o [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) e [Azure SQL Database](../sql-database/sql-database-technical-overview.md).<br/><br/>Ao usar o SQL Server na máquina virtual do Azure como uma fonte de dados do Azure Web Apps, tenha em mente que Azure Web Apps não oferece suporte para rede Virtual do Azure. Em outras palavras, todas as conexões do Azure Web Apps em SQL Server VMs no Azure devem passar pelo públicos pontos de extremidade de máquinas virtuais. Isso pode causar algumas limitações de alta disponibilidade e cenários de recuperação de desastres. Por exemplo, o aplicativo cliente no Azure Web Apps conectando para máquina virtual do SQL Server com espelhamento de banco de dados não poderá conectar-se para o novo servidor primário como espelhamento de banco de dados requer que você configure uma rede Virtual Azure entre VMs de host do SQL Server no Azure. Portanto, usar o **SQL Server Database Mirroring** ao Azure Web Apps não é suportado atualmente.<br/><br/>**Grupos de disponibilidade do SQL Server AlwaysOn**: você pode configurar grupos de disponibilidade sempre ativado ao usar o Azure Web Apps com SQL Server VMs no Azure. Mas você precisa configurar ouvinte de grupo de disponibilidade sempre ativado para rotear a comunicação para a réplica principal por meio de pontos de extremidade públicos balanceamento de carga.|
|**Conectividade entre local**|Você pode usar a rede Virtual do Azure para se conectar ao local.|Você pode usar a rede Virtual do Azure para se conectar ao local.|Há suporte para rede Virtual Azure. Para obter mais informações, consulte [A integração de rede Virtual do Web Apps](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Escalabilidade**|Escala-up está disponível aumentando os tamanhos de máquina virtual ou adicionar mais discos. Para obter mais informações sobre tamanhos de máquina virtual, consulte [Tamanhos de máquina Virtual do Azure](virtual-machines-windows-sizes.md).<br/><br/>**Para servidor de banco de dados**: escala-out está disponível por meio de técnicas de partição de banco de dados e grupos de disponibilidade do AlwaysOn do SQL Server.<br/><br/>Para pesado cargas de trabalho de leitura, você pode usar [Grupos de disponibilidade sempre ativado](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como replicação do SQL Server.<br/><br/>Para cargas de trabalho pesado gravação, você pode implementar dados partição horizontais entre vários servidores físicos para fornecer o aplicativo escala-out.<br/><br/>Além disso, você pode implementar um escala-out usando o [SQL Server com roteamento dependentes de dados](https://technet.microsoft.com/library/cc966448.aspx). Com dados dependentes roteamento (DDR), que você precisa implementar o mecanismo de partição no aplicativo cliente, normalmente na camada de nível de negócios, para rotear as solicitações de banco de dados para vários nós do SQL Server. A camada de negócios contém mapeamentos para como os dados são particionados e nó que contém os dados.<br/><br/>Você pode dimensionar aplicativos que executam máquinas virtuais. Para obter mais informações, consulte [como dimensionar um aplicativo](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Observação importante**: O recurso de **escala automática** do Azure permite que você aumente ou diminua as máquinas virtuais que são usadas pelo seu aplicativo de automaticamente. Esse recurso garante que a experiência do usuário final não é afetada negativamente durante períodos de pico, e VMs são desligadas quando a demanda estiver baixa. É recomendável que você não defina a opção de escala automática de seu serviço de nuvem se ele incluir VMs do SQL Server. O motivo é que o recurso de escala automática permite Azure para ativar uma máquina virtual quando o uso da CPU em que máquina virtual for maior que algum limite e para desativar uma máquina virtual quando o uso da CPU vai inferior a ele. O recurso de escala automática é útil para aplicativos sem estado, como servidores web, onde qualquer máquina virtual pode gerenciar a carga de trabalho sem quaisquer referências para qualquer estado anterior. No entanto, o recurso de escala automática não é útil para aplicativos com estado, como o SQL Server, onde apenas uma instância permite escrita ao banco de dados.|Escala-up está disponível usando várias funções de trabalho e da web. Para obter mais informações sobre tamanhos de máquina virtual para funções da web e funções de trabalho, consulte [Configurar tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao usar **Os serviços de nuvem**, você pode definir várias funções para distribuir o processamento e também obter dimensionamento flexível do seu aplicativo. Cada serviço de nuvem inclui uma ou mais funções da web e/ou funções de trabalho, cada uma com seus próprios arquivos de aplicativo e configuração. Você pode escala para cima um serviço na nuvem, aumentando o número de instâncias de função (máquinas virtuais) implantado para uma função e um serviço de nuvem de suspensa escala diminuindo o número de instâncias de função. Para obter informações detalhadas, consulte [Modelos de execução do Azure](../cloud-services/cloud-services-choose-me.md).<br/><br/>Escala-out está disponível por meio de suporte do Azure internos de alta disponibilidade por meio de [Serviços de nuvem, máquinas virtuais e o contrato de nível de serviço de rede Virtual](http://www.microsoft.com/download/details.aspx?id=38427) e balanceador de carga.<br/><br/>Para um aplicativo de vários nível, recomendamos que você se conecte a aplicação de funções da web/trabalhador VMs por meio de uma rede Virtual Azure do servidor de banco de dados. Além disso, o Azure fornece balanceamento de carga para VMs no serviço de nuvem, ampliando solicitações de usuário entre elas. Máquinas virtuais conectadas dessa forma podem se comunicar diretamente uns com os outros na rede local dentro de uma central de dados do Azure.<br/><br/>Você pode configurar **escala automática** no portal do clássico Azure, bem como os tempos de cronograma. Para obter mais informações, consulte [como dimensionar um aplicativo](../cloud-services/cloud-services-how-to-scale.md).|**Dimensionar para cima e para baixo**: você pode aumentar/diminuir o tamanho da instância (máquina virtual) reservada para seu site.<br/><br/>Escala: você pode adicionar mais reservadas instâncias (VMs) para seu site.<br/><br/>Você pode configurar **escala automática** no portal do, bem como os tempos de cronograma. Para obter mais informações, consulte [como escala Web Apps](../app-service-web/web-sites-scale.md).|

Para obter mais informações sobre como escolher entre esses métodos de programação, consulte [Azure Web Apps, serviços de nuvem e VMs: quando usar que](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a execução do SQL Server nas máquinas virtuais do Azure, consulte [SQL Server em Visão geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
