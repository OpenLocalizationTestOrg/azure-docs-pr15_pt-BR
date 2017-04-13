<properties
    pageTitle="Estender local sempre em grupos de disponibilidade no Azure | Microsoft Azure"
    description="Este tutorial usa recursos criados com o modelo clássico de implantação e descreve como usar o Assistente de réplica adicionar no SQL Server Management Studio (SSMS) para adicionar uma réplica sempre no grupo de disponibilidade do Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Abrangem local sempre em grupos de disponibilidade do Azure

Sempre em grupos de disponibilidade fornecer alta disponibilidade para grupos de banco de dados adicionando réplicas secundárias. Essas réplicas permitem falhando sobre bancos de dados no caso de uma falha. Além disso, eles podem ser usados para transferir cargas de trabalho de leitura ou tarefas de backup.

Você pode estender grupos de disponibilidade de locais no Microsoft Azure por um ou mais VMs Azure com o SQL Server de provisionamento e adicioná-los como réplicas aos grupos de disponibilidade local.

Este tutorial supõe que você possui o seguinte:

- Uma assinatura ativa do Azure. Você pode [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Um sempre na disponibilidade grupo existente no local. Para obter mais informações sobre grupos de disponibilidade, consulte [Sempre em grupos de disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx).

- Conectividade entre a rede local e sua rede virtual Azure. Para obter mais informações sobre como criar esta rede virtual, consulte [Configurar uma VPN to-Site no portal de clássico do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Adicionar o Assistente de réplica do Azure

Esta seção mostra como usar o **Assistente de réplica do Azure adicionar** para estender sua solução sempre no grupo de disponibilidade para incluir réplicas Azure.

1. De dentro do SQL Server Management Studio, expanda **Sempre em alta disponibilidade** > **Grupos de disponibilidade** > **[nome do seu grupo de disponibilidade]**.

1. **Réplicas de disponibilidade**de atalho, clique em **Adicionar réplica**.

1. Por padrão, a **Adicionar réplica ao Assistente de grupo de disponibilidade** é exibida. Clique em **Avançar**.  Se você tiver selecionado a **não mostrar esta página novamente** opção na parte inferior da página durante uma inicialização anterior do assistente, essa tela não será exibida.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Você precisará conectar-se a todas as réplicas secundárias existentes. Você pode clicar em **conectar-se...** ao lado de cada réplica ou você pode clicar em **Conectar tudo …** na parte inferior da tela. Após a autenticação, clique em **Avançar** para ir para a próxima tela.

1. Na página **Especificar réplicas** , várias guias estão listadas na parte superior: **réplicas**, **pontos de extremidade**, **Preferências de Backup**e **ouvinte**. Na guia **réplicas** , clique em **Adicionar … réplica do Azure** para iniciar o Assistente de réplica do Azure adicionar.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Selecione um certificado de gerenciamento do Azure existente do armazenamento de certificado local do Windows se você tiver instalado um antes. Selecione ou insira a id de uma assinatura do Azure se você tiver usado um antes. Você pode clicar em baixar para baixar e instalar um certificado de gerenciamento do Azure e baixar a lista de assinaturas usando uma conta do Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Você irá preencher cada campo na página com valores que será usada para criar o Azure Máquina Virtual (VM) que hospedará a réplica.

  	|Configuração|Descrição|
|---|---|
|**Imagem**|Selecione a combinação desejada do sistema operacional e SQL Server|
|**Tamanho de máquina virtual**|Selecione o tamanho da máquina virtual que melhor atenda às suas necessidades de negócios|
|**Nome de máquina virtual**|Especifique um nome exclusivo para a máquina virtual novo. O nome deve conter entre 3 e 15 caracteres, pode conter apenas letras, números e hifens e deve começar com uma letra e termine com uma letra ou um número.|
|**Nome de usuário de máquina virtual**|Especifique um nome de usuário que se tornará a conta de administrador na máquina virtual|
|**Senha de administrador de máquina virtual**|Especificar uma senha para a nova conta|
|**Confirmar senha**|Confirme a senha da nova conta|
|**Rede virtual**|Especifica o Azure rede virtual que a máquina virtual novo deve usar. Para obter mais informações sobre redes virtuais, consulte [Visão geral de rede Virtual](../virtual-network/virtual-networks-overview.md).|
|**Sub-rede de rede virtual**|Especifique a sub-rede de rede virtual que a máquina virtual novo deve usar|
|**Domínio**|Confirme que o valor previamente preenchido para o domínio está correto|
|**Nome de usuário de domínio**|Especifique uma conta que esteja no grupo Administradores locais em nós do cluster local|
|**Senha**|Especifique a senha para o nome de usuário de domínio|

1. Clique em **Okey** para validar as configurações de implantação.

1. Termos legais são exibidos em seguida. Leia e clique **Okey** se você concordar com estes termos.

1. A página **Especificar réplicas** é exibida novamente. Verifique as configurações para a nova réplica Azure nas guias **réplicas**, **pontos de extremidade**e **Preferências de Backup** . Modificar as configurações para atender suas necessidades de negócios.  Para obter mais informações sobre os parâmetros contidos nessas guias, consulte [Especificar página réplicas (disponibilidade assistente/Adicionar réplica Assistente para novo grupo)](https://msdn.microsoft.com/library/hh213088.aspx). Observe que ouvintes não podem ser criados usando a guia ouvinte para grupos de disponibilidade que contêm réplicas Azure. Além disso, se um ouvinte já foi criado antes de iniciar o assistente, você receberá uma mensagem indicando que não é suportado no Azure. Vamos ver como criar ouvintes na seção **criar um ouvinte de grupo de disponibilidade** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Clique em **Avançar**.

1. Selecione o método de sincronização de dados que você deseja usar na página **Selecionar sincronização inicial de dados** e clique em **Avançar**. Para a maioria dos cenários, selecione **Sincronização completa de dados**. Para obter mais informações sobre os métodos de sincronização de dados, consulte [Selecionar dados sincronização página inicial (sempre na disponibilidade grupo Assistentes)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Examine os resultados na página de **validação** . Corrija problemas pendentes e execute a validação novamente se necessário. Clique em **Avançar**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Examine as configurações na página de **Resumo** e, em seguida, clique em **Concluir**.

1. Inicia o processo de provisionamento. Quando o assistente for concluído com êxito, clique em **Fechar** para sair do assistente.

>[AZURE.NOTE] O Assistente de réplica do Azure adicionar cria um arquivo de log no Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Esse arquivo de log pode ser usado para solucionar implantações de réplica Azure falha. Se o assistente falhar executar qualquer ação, todas as operações anteriores são revertidas, incluindo excluindo a máquina virtual provisionada.

## <a name="create-an-availability-group-listener"></a>Criar um ouvinte de grupo de disponibilidade

Depois que o grupo de disponibilidade tiver sido criado, você deve criar um ouvinte para os clientes se conectem às réplicas. Ouvintes direcionam conexões de entrada principal ou uma cópia secundária somente leitura. Para obter mais informações sobre ouvintes, consulte [Configurar um ouvinte ILB para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Próximas etapas

Além de usar o **Assistente de réplica do Azure adicionar** para estender sua sempre no grupo de disponibilidade no Azure, você pode também mover algumas cargas de trabalho do SQL Server completamente no Azure. Para começar, consulte [provisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados a execução do SQL Server no Azure VMs, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
