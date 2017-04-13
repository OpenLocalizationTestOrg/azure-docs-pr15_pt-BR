<properties
    pageTitle="SQL Server no Azure máquinas virtuais perguntas Frequentes | Microsoft Azure"
    description="Este artigo fornece respostas para perguntas frequentes sobre a execução do SQL Server em VMs do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server no Azure máquinas virtuais perguntas Frequentes

Este tópico fornece respostas para algumas das perguntas mais comuns sobre executando o [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. **Como criar uma máquina virtual Azure com o SQL Server?**

    Há duas maneiras de fazer isso. A solução mais fácil é criar uma máquina Virtual que inclui o SQL Server. Para um tutorial sobre se inscrevendo no Azure e criando uma VM SQL no portal do, consulte [provisionar uma máquina virtual de SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Você também tem a opção de instalação do SQL Server em uma máquina virtual manualmente e reutilizando uma licença de local com [Mobilidade de licença por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Qual é a diferença entre VMs de SQL e o serviço de banco de dados SQL?**

    Forma conceitual, executando o SQL Server em uma máquina virtual Azure não é diferente do executando o SQL Server em um data center remoto. Em contraste, o [Banco de dados SQL](../sql-database/sql-database-technical-overview.md) oferece banco de dados-como um serviço. Com o banco de dados SQL, você não tem acesso às máquinas que hospeda seus bancos de dados. Para uma comparação completa, consulte [Escolha uma opção do SQL Server de nuvem: banco de dados do SQL Azure (PaaS) ou SQL Server no Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como posso migrar meu banco de dados do SQL Server local para a nuvem?**

    Primeiro, crie uma máquina virtual Azure com uma instância do SQL Server. Migre seus bancos de dados locais a essa instância. Para estratégias de migração de dados, consulte [migrar um banco de dados do SQL Server para o SQL Server em uma máquina virtual do Azure](virtual-machines-windows-migrate-sql.md).

2. **É possível alterar os recursos instalados ou instale uma segunda instância do SQL Server na mesma VM?**

    Sim. Mídia de instalação do SQL Server está localizada em uma pasta na unidade **C** . Execute **Setup.exe** esse local para adicionar novas instâncias do SQL Server ou alterar outros recursos instalados do SQL Server na máquina.

3. **Como atualizar para uma nova versão/edição do SQL Server em uma máquina virtual do Azure?**

    Atualmente, não há nenhuma atualização in-loco para SQL Server em execução em uma máquina virtual do Azure. Criar uma nova máquina virtual Azure com o SQL Server versão/edição desejada e, em seguida, migrar seus bancos de dados para o novo servidor usando padrão [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md).

4. **Como posso instalar minha cópia licenciada do SQL Server em uma máquina virtual do Azure?**

    Copie a mídia de instalação do SQL Server para a máquina virtual Server do Windows e, em seguida, instalar o SQL Server na máquina virtual. Licenciamento motivos, você deve ter [Mobilidade de licença por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Você tem pagar os custos SQL de uma máquina virtual se ele estiver sendo usado somente para espera/failover?**

    Se você estiver criando a VM SQL por meio da galeria, você deve ter uma licença separada para a VM SQL em espera e o preço é o mesmo. Se você instalar o SQL Server manualmente em uma máquina virtual com [Mobilidade de licença](https://azure.microsoft.com/pricing/license-mobility/), você tem a opção para ter uma instância do SQL de passivo gratuita para failover. Examine os requisitos e restrições.

6. **Como são service packs e atualizações aplicados em uma máquina de virtual do SQL Server?**

    Máquinas virtuais permitem que você controle da máquina host, incluindo quando e como você aplicar atualizações. Para o sistema operacional, você pode aplicar manualmente atualizações do windows ou você pode habilitar um serviço de agendamento chamado [Automatizada patches](virtual-machines-windows-classic-sql-automated-patching.md). Instalações de patch automatizado todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server dessa categoria. Outras atualizações opcionais para o SQL Server devem ser instaladas manualmente.

7. **É possível configurar configurações não mostradas na Galeria de máquina virtual (por exemplo Windows 2008 R2 + SQL Server 2012)?**

    Não. Para imagens de galeria de máquina virtual que incluem o SQL Server, você deve selecionar uma das imagens fornecidas.

9. **Como instalar o ferramentas de dados do SQL na minha máquina virtual do Azure?**

    Baixe e instale as ferramentas de dados SQL do [Ferramentas de dados do Microsoft SQL Server - Business Intelligence para Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos

Para obter uma visão geral do SQL Server em máquinas virtuais do Azure, assista o vídeo [máquina virtual do Azure é a melhor plataforma para o SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Você também pode obter uma boa introdução no tópico, [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Outros recursos incluem:

- [Provisionar uma máquina virtual de SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migrar um banco de dados para SQL Server em uma máquina virtual Azure](virtual-machines-windows-migrate-sql.md)
- [Alta disponibilidade e recuperação de dados para SQL Server no Azure máquinas virtuais](virtual-machines-windows-sql-high-availability-dr.md)
- [Práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
- [Padrões de aplicativo e estratégias de desenvolvimento para SQL Server no Azure máquinas virtuais](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
