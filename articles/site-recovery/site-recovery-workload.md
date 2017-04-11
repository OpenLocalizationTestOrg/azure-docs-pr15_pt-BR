<properties
    pageTitle="Quais cargas de trabalho que você pode proteger com recuperação de Site do Azure?"
    description="A recuperação de Site Azure protege sua carga de trabalho e aplicativos por coordenar a replicação, o failover e a recuperação de máquinas virtuais de locais e servidores físicos no Azure ou para um site local secundário"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Quais cargas de trabalho que você pode proteger com recuperação de Site do Azure?


Este artigo descreve as cargas de trabalho e aplicativos que você pode replicar com o serviço de recuperação de Site do Azure.

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral

As organizações precisam um desastre e continuidade recuperação (BCDR) estratégia comercial manter cargas de trabalho e dados confiáveis e disponível durante o tempo de inatividade planejado e e recuperar a condições de trabalho regulares assim que possível.

Recuperação de site é um serviço Azure que contribui para sua estratégia BCDR. Usando a recuperação de Site, você pode implantar replicação de reconhecimento de aplicativos para a nuvem, ou para um site secundário. Se seus aplicativos são janelas ou baseado no Linux, em execução em servidores físicos, VMware ou Hyper-V, você pode usar a recuperação de Site para coordenar replicação, execute testes de recuperação de desastres e a execução failovers e failback.


Recuperação de site integra-se a aplicativos Microsoft, inclusive SharePoint, Exchange, Dynamics, SQL Server e do Active Directory. Microsoft também trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. Você pode personalizar as soluções de replicação em uma base de aplicativo por aplicativo.

## <a name="why-use-site-recovery-for-application-replication"></a>Por que usar a recuperação de Site para replicação de aplicativo?

Recuperação de site contribui para proteção de nível de aplicativo e recuperação da seguinte maneira:

- Aplicativo independente, oferecendo replicação para qualquer cargas de trabalho em execução em um computador com suporte.
- Replicação perto síncrono, com RPOs baixas como 30 segundos para atender às necessidades dos aplicativos de negócios mais importantes.
- Instantâneos consistentes com o aplicativo, para aplicativos único ou de vários níveis.
- Integração com o SQL Server AlwaysOn e parceria com outras replicação de nível de aplicativo tecnologias, incluindo replicação do AD, SQL AlwaysOn, grupos de disponibilidade de banco de dados do Exchange (DAGs) e proteção de dados do Oracle.
- Planos de recuperação flexíveis, que permitem que você recuperar uma pilha de todo o aplicativo com um único clique e incluir para incluir scripts externos e ações manuais no plano.
- Gerenciamento de rede avançada no Azure e recuperação de Site para simplificar os requisitos de rede do aplicativo, incluindo a capacidade de reservar endereços IP, configurar balanceamento de carga e integração com o Azure tráfego Manager, para baixos switchovers de rede RTO.
-  Uma biblioteca de automação avançada que fornece scripts pronto para produção, específicos do aplicativo que podem ser baixados e integrados com planos de recuperação.



## <a name="workload-summary"></a>Resumo de carga de trabalho

Recuperação de site pode replicar qualquer aplicativo executando em um computador com suporte. Além disso fizemos uma parceria com as equipes de produto para executar testes de adicionais específicas do aplicativo.

**Carga de trabalho** | **Duplicar VMs Hyper-V para um site secundário** | **Duplicar VMs Hyper-V no Azure** | **Duplicar VMs VMware para um site secundário** | **Duplicar VMs VMware para Azure**
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
Aplicativos da Web (IIS, SQL) | Y | Y | Y | Y
System Center Operations Manager | Y | Y | Y | Y
SharePoint | Y | Y | Y | Y
SAP<br/><br/>Duplicar site SAP para Azure para não-cluster | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Exchange (não-Dag mão) | Y | Em breve | Y | Y
Área de trabalho remota/VDI | Y | Y | Y | N/D
Linux (sistema operacional e aplicativos) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Em breve | Y | Em breve
Oracle | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft) | Y (testado pela Microsoft)
Servidor de arquivos do Windows | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>Duplicar DNS e Active Directory

Um Active Directory e infraestrutura DNS são essenciais para a maioria dos aplicativos empresariais. Durante a recuperação de dados, você precisará proteger e recuperar esses componentes de infraestrutura, antes de recuperar sua carga de trabalho e aplicativos.

Você pode usar a recuperação de Site para criar um plano de recuperação de desastres automatizada concluída para o Active Directory e DNS. Por exemplo, se você quiser falhar ao longo do SharePoint e SAP de um primário para um site secundário, você pode configurar um plano de recuperação falha ao longo do Active Directory primeiro e, em seguida, um plano de recuperação de aplicativo específico adicionais falha sobre outros aplicativos que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre como proteger o Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteger o SQL Server

SQL Server fornece uma base de serviços de dados dos serviços de dados para muitos aplicativos de negócios em uma central de dados local.  Recuperação de site pode ser usada em conjunto com tecnologias do SQL Server HA/DR, proteger aplicativos de vários níveis empresariais que usam o SQL Server. Fornece a recuperação do site:

- Uma solução de recuperação de desastres simples e econômica para SQL Server. Duplicar várias versões e edições do SQL Server autônomo servers e clusters, para Azure ou para um site secundário.  
- Integração com grupos de disponibilidade sempre ativado SQL, gerenciar failover e failback com planos de recuperação de recuperação de Site do Azure.
- Planos de recuperação de ponta a ponta para todos os níveis em um aplicativo, incluindo os bancos de dados do SQL Server.
- Dimensionamento do SQL Server para pico carrega com recuperação de Site, por "interrompê-los" em tamanhos maiores de máquina virtual IaaS no Azure.
- Fácil teste de recuperação de dados do SQL Server. Você pode executar failovers de teste para analisar dados e executar verificações de conformidade, sem afetar o seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre como proteger o SQL server.

##<a name="protect-sharepoint"></a>Proteger do SharePoint

Recuperação de Site Azure ajuda a proteger implantações do SharePoint, da seguinte maneira:

- Elimina a necessidade e os custos de infraestrutura associados para um farm de espera para recuperação de dados. Use a recuperação de Site para replicar um farm inteiro (níveis da Web, o aplicativo e o banco de dados) para Azure ou para um site secundário.
- Simplifica o gerenciamento e implantação do aplicativo. Atualizações implantadas ao local principal são replicadas automaticamente e, portanto, estão disponíveis após failover e recuperação de um farm de um site secundário. Também reduz a complexidade de gerenciamento e os custos associados a manter um farm espera atualizados.
- Simplifica o desenvolvimento de aplicativos do SharePoint e testes criando um ambiente de réplica sob demanda de cópia de produção para testar e depurar.
- Simplifica a transição para a nuvem usando recuperação do Site para migrar implantações do SharePoint para o Azure.

[Saiba mais](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sobre como proteger o SharePoint.


## <a name="protect-dynamics-ax"></a>Proteger Dynamics AX

Recuperação de Site Azure ajuda a proteger sua solução de ERP do Dynamics AX, por:

- Organizar replicação do Dynamics AX ambiente inteiro (Web e AOS níveis, níveis de banco de dados, SharePoint) para Azure, ou para um site secundário.
- Simplificar a migração de implantações do Dynamics AX na nuvem (Azure).
- Simplificar o desenvolvimento de aplicativos do Dynamics AX e teste criando uma cópia de produção sob demanda, para testar e depurar.

[Saiba mais](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sobre como proteger AX dinâmico.

## <a name="protect-rds"></a>Proteger RDS

Serviços de área de trabalho remota (RDS) permite (VDI) virtual desktop infrastructure, baseada em sessão desktops e aplicativos, permitindo que os usuários trabalhar em qualquer lugar. Com o Azure recuperação do Site, você pode:

- Duplicar gerenciadas ou em pool áreas de trabalho virtuais para um site secundário e aplicativos remotos e sessões a um site secundário ou Azure.
- Veja aqui o que você pode replicar:

**RDS** | **Duplicar VMs Hyper-V para um site secundário** | **Duplicar VMs Hyper-V no Azure** | **Duplicar VMs VMware para um site secundário** | **Duplicar VMs VMware para Azure** | **Duplicar servidores físicos para um site secundário** | **Duplicar servidores físicos para Azure**
---|---|---|---|---|---|---
**Área de trabalho Virtual (não gerenciado) de pool** | Sim | Não | Sim | Não | Sim | Não
**Em pool área de trabalho Virtual (gerenciado e sem UPD)** | Sim | Não | Sim | Não | Sim | Não
**Aplicativos remotos e sessões da área de trabalho (sem UPD)** | Sim | Sim | Sim | Sim | Sim | Sim


[Saiba mais](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre como proteger RDS.


## <a name="protect-exchange"></a>Proteger o Exchange

Recuperação de site ajuda a proteger o Exchange, da seguinte maneira:

- Para implantações pequenas do Exchange, como um único ou autônomo servidores, recuperação de Site pode replicar e falhar ao Azure ou um site secundário.
- Para implantações maiores, recuperação de Site integra DAGS do Exchange.
- DAGs do Exchange são a solução recomendada para recuperação do Exchange em uma empresa.  Planos de recuperação de recuperação do site podem incluir DAGs, para coordenar Dag mão failover entre sites.


[Saiba mais](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre como proteger o Exchange.

## <a name="protect-sap"></a>Proteger SAP

Use a recuperação de Site para proteger sua implantação do SAP, da seguinte maneira:

- Habilite proteção da implantação do SAP inteira, duplicar camadas de implantação diferentes para Azure, ou para um site secundário.
- Simplifica a migração de nuvem, usando a recuperação de Site para migrar sua implantação do SAP para o Azure.
- Simplificar o desenvolvimento do SAP e testes, criando um semelhante produção copie sob demanda para testar e depurar aplicativos.

[Saiba mais](http://aka.ms/asr-sap) sobre como proteger SAP.

## <a name="next-steps"></a>Próximas etapas

[Preparar para implantação de recuperação do Site](site-recovery-best-practices.md) 
