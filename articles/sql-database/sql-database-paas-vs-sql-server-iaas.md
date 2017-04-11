<properties
    pageTitle="SQL (PaaS) banco de dados versus SQL Server na nuvem em VMs (IaaS) | Microsoft Azure"
    description="Saiba qual opção de SQL Server nuvem caiba seu aplicativo: banco de dados do SQL Azure (PaaS) ou SQL Server na nuvem em máquinas virtuais do Azure."
    services="sql-database, virtual-machines"
    keywords="Nuvem de nuvem do SQL Server, SQL Server na nuvem, banco de dados de PaaS, SQL Server, DBaaS"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Escolha uma opção do SQL Server de nuvem: banco de dados do SQL Azure (PaaS) ou SQL Server no Azure VMs (IaaS)

Azure tem duas opções para hospedar cargas de trabalho do SQL Server no Microsoft Azure:

* [Banco de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/): banco de dados do SQL de uma nativo na nuvem, também conhecido como uma plataforma como um banco de dados de serviço (PaaS) ou um banco de dados como um serviço (DBaaS) que é otimizado para desenvolvimento de aplicativo do software como serviço (SaaS). Ele oferece compatibilidade com a maioria dos recursos do SQL Server. Para obter mais informações sobre PaaS, consulte [o que é PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server instalado e hospedados na nuvem no Windows Server máquinas virtuais (VMs) em execução no Azure, também conhecido como uma infraestrutura como um serviço (IaaS).
SQL Server no Azure máquinas virtuais é otimizado para migrar aplicativos existentes do SQL Server. Todas as versões e edições do SQL Server estão disponíveis. Ele oferece compatibilidade de 100% com SQL Server, permitindo que você hospedar quantos bancos de dados como transações de bancos de dados necessários e em execução. Ele oferece controle total no SQL Server e Windows.

Saiba como cada opção se encaixa a plataforma de dados da Microsoft e obter ajuda correspondente a opção certa às suas necessidades de negócios. Se você priorizar economia ou administração mínima com antecedência tudo, este artigo pode ajudá-lo a decidir qual abordagem cumpre os requisitos de negócios que importa para você maioria.


## <a name="microsofts-data-platform"></a>Plataforma de dados da Microsoft

Uma das primeiras coisas entender em qualquer discussão do Azure versus bancos de dados do SQL Server local é que você pode usar tudo. Plataforma de dados da Microsoft utiliza a tecnologia do SQL Server e o disponibiliza em máquinas locais físicos, ambientes de nuvem privada, ambientes de nuvem privada hospedada de terceiros e nuvem pública. SQL Server no Azure mchines virtual permite atender às necessidades de negócios exclusivo e diverso por meio de uma combinação de locais e implantações hospedado na nuvem, enquanto estiver usando o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e especialização nos seguintes ambientes.

   ![Opções do SQL Server em nuvem: banco de dados do SQL server em IaaS ou SaaS SQL na nuvem.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como visto no diagrama, cada oferta pode ser caracterizada pelo nível de administração que você tem sobre a infraestrutura (no eixo X) e pelo grau de eficiência de custo obtido com a consolidação de nível de banco de dados e de automação (no eixo Y).

Ao criar um aplicativo, quatro opções básicas estão disponíveis para a parte do SQL Server do aplicativo de hospedagem:

- SQL Server em máquinas físicas não virtualizado
- SQL Server nas máquinas locais virtualizados (nuvem privada)
- SQL Server na máquina Virtual do Azure (nuvem pública da Microsoft)
- Azure SQL Database (nuvem pública da Microsoft)

As seções a seguir, você vai aprender sobre o SQL Server na nuvem pública Microsoft: banco de dados do SQL Azure e SQL Server em VMs do Azure. Além disso, você explorar motivadores de negócios comuns para determinar qual opção funciona melhor para seu aplicativo.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Uma olhada mais atenta Azure SQL Database e SQL Server em VMs do Azure

**Banco de dados do SQL Azure** é um relacional banco de dados-como um serviço (DBaaS) hospedado na nuvem Azure que se encaixam as categorias do setor de *Software-como um serviço (SaaS)* e *Plataforma-como um serviço (PaaS)*. [Banco de dados SQL](sql-database-technical-overview.md) baseia-se em padronizado de hardware e software que é pertencente, hospedado e mantido pela Microsoft. Com o banco de dados SQL, você pode desenvolver diretamente no serviço usando a funcionalidade e os recursos internos. Ao usar o banco de dados SQL, você flexível com opções para expandir ou check-out para power maior sem interrupção.

**SQL Server em máquinas virtuais do Azure (VMs)** caem na categoria setor *Infraestrutura-como um serviço (IaaS)* e permite que você executar o SQL Server em uma máquina virtual na nuvem. Semelhante ao banco de dados SQL, ele é criado no hardware padronizado que é pertencente, hospedado e mantido pela Microsoft. Ao usar o SQL Server em uma máquina virtual, você pode qualquer pagamento-como você-ir para uma licença do SQL Server já incluído em uma imagem do SQL Server ou facilmente usar uma licença existente. Você pode também facilmente escala para cima/para baixo e Pausar/continuar a máquina virtual conforme necessário.

Em geral, essas duas opções de SQL são otimizadas para diferentes finalidades:

- **Banco de dados SQL** é otimizado para reduzir os custos gerais para o mínimo para provisionar e gerenciar muitos bancos de dados. Ele reduz os custos de administração contínua porque você não precisa gerenciar qualquer máquinas virtuais, o sistema operacional ou o software de banco de dados. Você não precisa gerenciar atualizações, alta disponibilidade ou [backups](sql-database-automated-backups.md). Em geral, o banco de dados do Azure SQL pode aumentar drasticamente o número de bancos de dados gerenciados por um único IT ou recurso de desenvolvimento.
- **SQL Server em execução no Azure VMs** é otimizada para Migrando aplicativos existentes para Azure ou estender aplicativos locais existentes na nuvem em implantações híbridas. Além disso, você pode usar o SQL Server em uma máquina virtual para desenvolver e testar aplicativos tradicionais do SQL Server. Com o SQL Server em VMs do Azure, você terá os direitos administrativos completos sobre uma instância do SQL Server dedicada e uma máquina virtual baseado na nuvem. É uma opção ideal quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Esses recursos permitem que você crie um sistema altamente personalizado para tratar de desempenho específico e requisitos de disponibilidade do seu aplicativo.

A tabela a seguir resume as características principais do banco de dados SQL e SQL Server em VMs Azure:

|       | Banco de dados SQL | SQL Server em uma máquina Virtual Azure|
| -------------- | ------------ | ---------------------- |
| **Melhor para:** | Novos projetado nuvem aplicativos que têm restrições de tempo em desenvolvimento e marketing. |Aplicativos existentes que requerem migração rápida para a nuvem com mínimas alterações. Cenários de desenvolvimento e teste de rápida quando não quiser comprar locais hardware do SQL Server não seja de produção. |
|| Equipes que precisam de alta disponibilidade interna, recuperação e atualização do banco de dados. |Equipes que podem configurar e gerenciar alta disponibilidade, recuperação de dados e patches para o SQL Server. Alguns fornecidos recursos automatizados drasticamente simplificam isso. |
||Equipes que não deseja gerenciar as configurações de configuração e o sistema operacional subjacente.| Se você precisar de um ambiente personalizado com direitos administrativos completos.|
||Até 1 TB ou maiores bancos de dados que podem ser [particionados horizontalmente ou verticalmente](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) usando um padrão de escala-out.|Instâncias do SQL Server com até 64 TB de armazenamento. A instância pode oferecer suporte a tantos bancos de dados conforme necessário. |
||[Aplicativos de construção Software-como um serviço (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Migrando e criar aplicativos corporativos e híbridos.|
|||||
|**Recursos:**|Você não deseja utilizar os recursos de TI para configuração e gerenciamento da infraestrutura subjacente, mas deseja se concentrar em camada de aplicativos.|Você tem alguns recursos de TI para configuração e gerenciamento. Alguns fornecidos recursos automatizados drasticamente simplificam isso.|
|**Custo total de propriedade:**|Elimina custos de hardware e reduz os custos administrativos.|Elimina os custos de hardware.|
|**Continuidade de negócios:**|Além dos recursos de infraestrutura de tolerância de falhas internos, banco de dados do SQL Azure fornece recursos, como [backups automatizados](sql-database-automated-backups.md), [Restaurar no momento](sql-database-recovery-using-backups.md#point-in-time-restore), [Localização geográfica restauração](sql-database-recovery-using-backups.md#geo-restore)e [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md) para aumentar a continuidade de negócios. Para obter mais informações, consulte [Visão geral de continuidade de negócios do banco de dados SQL](sql-database-business-continuity.md).|SQL Server no Azure VMs permite que você configure uma solução de recuperação de desastres e disponibilidade alta para necessidades específicas de seu banco de dados. Portanto, você pode ter um sistema altamente otimizada para seu aplicativo. Você pode testar e executar failovers sozinho quando necessário. Para obter mais informações, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Nuvem híbrida:**|Seu aplicativo de local pode acessar dados no Azure SQL Database.|Com o SQL Server em VMs do Azure, você pode ter aplicativos que executar parcialmente na nuvem e parcialmente no local. Por exemplo, você pode estender sua rede local e um domínio do Active Directory para a nuvem por meio de [Uma rede Virtual Azure](../virtual-network/virtual-networks-overview.md). Além disso, você pode armazenar arquivos de dados locais em armazenamento Azure usando [Arquivos de dados do SQL Server no Azure](http://msdn.microsoft.com/library/dn385720.aspx). Para obter mais informações, consulte [Introdução à nuvem híbrida do SQL Server de 2014](http://msdn.microsoft.com/library/dn606154.aspx).|
||Como um assinante para duplicar dados é compatível com [replicação de transação do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) .|Totalmente compatível com [replicação de transação do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Grupos de disponibilidade sempre ativado](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), serviços de integração e envio de Log para duplicar dados. Além disso, os backups tradicionais do SQL Server são totalmente suportados|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações empresariais para escolher o banco de dados do Azure SQL ou SQL Server nas VMs do Azure

### <a name="cost"></a>Custo

Se você estiver uma inicialização que está com poucos para dinheiro ou uma equipe em uma empresa estabelecida que operam em restrições de orçamento apertado, fundos limitados costuma driver primário quando decidir como hospedar seus bancos de dados. Nesta seção, você aprenderá a cobrança e licenciamento Noções básicas no Azure com relação a essas duas opções de banco de dados relacional: banco de dados SQL e SQL Server em VMs do Azure. Você também pode aprender sobre como calcular o custo total do aplicativo.

#### <a name="billing-and-licensing-basics"></a>Faturamento e Noções básicas de licenciamento

**Banco de dados SQL** é vendido aos clientes como um serviço, não com uma licença.  [SQL Server no Azure VMs](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) é vendido com uma licença incluída que você paga por minuto. Se você tiver uma licença existente, você também pode usá-lo.  

Atualmente, o **Banco de dados SQL** está disponível em vários níveis de serviço, todas elas são cobradas por hora a uma taxa fixa baseada no nível de serviço e nível de desempenho que você escolheu. Além disso, você será cobrado para tráfego de Internet de saída em regular [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/). Os níveis de serviço Basic, Standard e Premium foram projetados para oferecer desempenho previsível com vários níveis de desempenho para atender às necessidades de pico do seu aplicativo. Você pode alterar entre os níveis de serviço e os níveis de desempenho para atender às necessidades de produtividade variadas do seu aplicativo. Se seu banco de dados tiver alto volume de transações e necessidades para dar suporte a vários usuários simultâneos, recomendamos o nível de serviço Premium. Para as informações mais recentes sobre os níveis de serviço permitido atual, consulte [Níveis de serviço de banco de dados do Azure SQL](sql-database-service-tiers.md). Você também pode criar [pools de banco de dados elástica](sql-database-elastic-pool.md) para compartilhar recursos de desempenho entre instâncias de banco de dados.

Com o **Banco de dados SQL**, o software de banco de dados é configurado automaticamente, corrigido e atualizado pela Microsoft, que reduz os custos de administração. Além disso, seus recursos de [backup interno](sql-database-automated-backups.md) ajudarão-lo a obter economia significativa, especialmente quando você tem um grande número de bancos de dados.

Com o **SQL Server em VMs do Azure**, você pode usar qualquer uma das fornecida pela plataforma do SQL Server imagens (que inclui uma licença) ou trazer sua licença do SQL Server. Todas as versões com suporte do SQL Server (2008R2, 2012, 2014, 2016) e edições (desenvolvedor, Express, da Web, padrão, Enterprise) estão disponíveis. Além disso, versões de trazer seu-proprietário-licença (BYOL) das imagens estão disponíveis. Quando usar o Azure fornecido imagens, o custo operacional depende do tamanho da máquina virtual e a edição do SQL Server que você escolher. Independentemente de tamanho de máquina virtual ou edição do SQL Server, você pagar custo de licenciamento por minuto do SQL Server e Windows Server, juntamente com o custo de armazenamento do Azure para os discos de máquina virtual. A opção de cobrança por minuto permite que você use o SQL Server para desde que você precisa sem adição de compra do SQL Server licenças. Se você colocar seu próprio licença do SQL Server para o Azure, cobrado para o Windows Server e apenas os custos de armazenamento. Para obter mais informações sobre trazer seu-própria licenciamento, consulte [Mobilidade de licença por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total do aplicativo

Quando você começar a usar uma plataforma de nuvem, o custo da execução de seu aplicativo inclui os custos de desenvolvimento e administração, além dos custos de serviço de plataforma de nuvem pública.

Aqui está o cálculo do custo detalhadas para seu aplicativo em execução no banco de dados SQL e SQL Server no Azure VMs:

**Ao usar o banco de dados do Azure SQL:**

*Custo total de aplicativo = os custos de administração altamente minimizado, os custos de desenvolvimento de software + custos de serviço de banco de dados SQL*

**Ao usar o SQL Server em VMs Azure:**

*Custo total de aplicativo = custo de desenvolvimento de software altamente minimizado + custos de administração + custos de licenciamento do SQL Server e Windows Server + custos de armazenamento do Azure*

Para obter mais informações sobre preços, consulte os seguintes recursos:

- [Preços de banco de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços de máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Há um pequeno subconjunto dos recursos que não são aplicáveis ou não está disponível com o banco de dados SQL no SQL Server. Consulte [limitações gerais de banco de dados do SQL e diretrizes](sql-database-general-limitations.md) e [informações de banco de dados de SQL Transact-SQL](sql-database-transact-sql-information.md) para obter mais informações. Se você estiver movendo uma solução existente do SQL Server para a nuvem, consulte [migrar um banco de dados do SQL Server Azure SQL Database](sql-database-cloud-migrate.md). Quando você migra um aplicativo do SQL Server local existente banco de dados SQL, considere a possibilidade de atualização de aplicativos para tirar proveito dos recursos que a oferta de serviços de nuvem. Por exemplo, você pode considerar usando o [Serviço de aplicativo Web do Azure](https://azure.microsoft.com/services/app-service/web/) ou [Serviços de nuvem do Azure](https://azure.microsoft.com/services/cloud-services/) para hospedar sua camada de aplicativo para aumentar os benefícios de custo.

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de transição para um serviço de nuvem é como muito sobre transferindo complexidade da administração como ela custo. Com o **Banco de dados SQL**, Microsoft administra o hardware subjacente. Microsoft automaticamente replica todos os dados para fornecer alta disponibilidade, configura e atualiza o software de banco de dados, gerencia balanceamento e faz failover transparente, se houver uma falha de servidor. Você pode continuar a administrar seu banco de dados, mas você não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional do servidor ou o hardware.  Bancos de dados e logon, índice e ajuste de consulta e auditoria e segurança são exemplos de itens que você pode continuar a administrar.

Com o **SQL Server no Azure VMs**, você tem controle total sobre o sistema operacional e a configuração da instância do SQL Server. Com uma máquina virtual, ele é a decidir quando atualizar/atualizar o sistema operacional e o software de banco de dados e instalar qualquer software adicional como o antivírus. Alguns recursos automatizados são fornecidos para simplificar drasticamente a disponibilidade de patches, backup e alta. Além disso, você pode controlar o tamanho da máquina virtual, o número de discos e suas configurações de armazenamento. Azure permite que você alterar o tamanho de uma máquina virtual, conforme necessário. Para obter informações, consulte [Máquina Virtual e tamanhos de serviço de nuvem do Azure](../virtual-machines/virtual-machines-linux-sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

Para muitos departamentos de TI, o cumprimento de obrigações de tempo de um contrato de nível de serviço (SLA) é uma prioridade. Nesta seção, examinaremos qual SLA aplica cada opção de hospedagem do banco de dados.

Para níveis de serviço de **Banco de dados SQL** Basic, Standard e Premium Microsoft fornece uma SLA de 99,99% de disponibilidade. As informações mais recentes, consulte o [Contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/sql-database/). Para obter as informações mais recentes sobre níveis de serviço de banco de dados SQL e os planos de continuidade de negócios com suporte, consulte [Níveis de serviço](sql-database-service-tiers.md).

Para o **SQL Server em execução no Azure VMs**, a Microsoft fornece uma disponibilidade SLA de 99,95% que abrange apenas na máquina Virtual. Este SLA não aborda os processos (como o SQL Server) em execução na máquina virtual e requer que você pelo menos duas instâncias de máquina virtual em um conjunto de disponibilidade do host. Para obter as informações mais recentes, consulte o [SLA de máquina virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para banco de dados alta disponibilidade (HA) dentro de VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como [Grupos de disponibilidade sempre ativado](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Usando uma opção de alta disponibilidade compatível não oferece um SLA adicional, mas permite obter > 99,99% de disponibilidade de banco de dados.

### <a name="market"></a>Tempo de entrega

**Banco de dados SQL** é a solução certa para aplicativos projetados nuvem quando produtividade do desenvolvedor e a hora de entrega rápida são críticas. Com a funcionalidade de programação DBA semelhante, é ideal para desenvolvedores e arquitetos de nuvem como diminui a necessidade de gerenciamento do sistema operacional e o banco de dados subjacente. Por exemplo, você pode usar a [API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) e [Cmdlets do PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) para automatizar e gerenciar operações administrativas para milhares de bancos de dados. Recursos como [Elástica Pools de banco de dados](sql-database-elastic-pool.md) permitem que você se concentre na camada de aplicativo e fornecer a solução no mercado com mais rapidez.

**SQL Server em execução no Azure VMs** é perfeito se seus aplicativos existentes ou novos exigem grandes bancos de dados, inter-relacionados ou para todos os recursos no SQL Server ou no Windows. Também é uma boa opção quando você deseja migrar existente local aplicativos e bancos de dados do Azure como-é. Como você não precisar alterar a apresentação, de aplicativo e camadas de dados, você economiza tempo e orçamento em rearchitecting sua solução existente. Em vez disso, você pode focalizar migrando todas as suas soluções para o Azure e fazer algumas otimizações de desempenho que podem ser necessárias por plataforma Windows Azure. Para obter mais informações, consulte [Práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Resumo

Este artigo explorados banco de dados SQL e SQL Server em máquinas virtuais do Azure (VMs) e discutido motivadores de negócios comuns que podem afetar sua decisão. Este é um resumo das sugestões para você considerar:

Escolha o **banco de dados do SQL Azure** se:

- Você está criando novos aplicativos baseado em nuvem para tirar proveito da economia e forneça de otimização de desempenho que serviços em nuvem. Essa abordagem fornece os benefícios de um serviço de nuvem totalmente gerenciados, ajuda a reduzir inicial hora de entrega e pode fornecer a longo prazo otimização de custo.

- Você deseja ter o Microsoft realizar operações de gerenciamento comuns em seus bancos de dados e exigir mais forte disponibilidade SLAs para bancos de dados.



Escolha o **SQL Server em VMs Azure** se:

- Você tiver aplicativos existentes do local que você deseja migrar ou estender na nuvem, ou se você quiser criar aplicativos empresariais maiores do que 1 TB. Essa abordagem oferece o benefício de compatibilidade SQL de 100%, capacidade de banco de dados grande, controle total sobre o SQL Server e o Windows e seguro túnel para locais. Essa abordagem minimiza custos de desenvolvimento e modificações de aplicativos existentes.

- Você tem recursos de TI existentes e finalmente pode ter patches, backups e alta disponibilidade do banco de dados. Observe que alguns recursos automatizados simplificam drasticamente essas operações. 


## <a name="next-steps"></a>Próximas etapas
- Consulte [tutorial do banco de dados SQL: criar um banco de dados do SQL em minutos usando o portal do Azure](sql-database-get-started.md) para começar a usar o banco de dados SQL.
- Consulte [preço de banco de dados SQL] (https://azure.microsoft.com/pricing/details/sql-database/).
- Consulte [provisionar uma máquina virtual do SQL Server Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) para começar a usar o SQL Server em VMs do Azure.
- Consulte [SQL Server em uma máquina Virtual Azure: caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
