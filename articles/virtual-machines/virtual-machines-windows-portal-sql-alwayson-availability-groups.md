<properties
    pageTitle="Configurar sempre no grupo de disponibilidade na máquina virtual do Azure automaticamente - Gerenciador de recursos"
    description="Crie um grupo de disponibilidade sempre com Azure máquinas virtuais no modo do Gerenciador de recursos do Azure. Este tutorial principalmente usa a interface do usuário para criar automaticamente a solução inteira."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurar sempre no grupo de disponibilidade na máquina virtual do Azure automaticamente - Gerenciador de recursos

> [AZURE.SELECTOR]
- [Gerenciador de recursos: modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gerenciador de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clássico: interface do usuário](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Este tutorial de ponta a ponta mostra como criar um grupo de disponibilidade do SQL Server com máquinas virtuais de Gerenciador de recursos do Azure. O tutorial usa blades Azure para configurar um modelo. Você irá examinar as configurações padrão, digite configurações necessárias e atualize lâminas no portal do conforme você percorrer este tutorial.

No fim do tutorial, sua solução de grupo de disponibilidade do SQL Server no Azure consiste nos seguintes elementos:

- Uma rede virtual que contém várias sub-redes, incluindo um front-end e uma sub-rede de back-end

- Controlador de domínio dois com um domínio do Active Directory (AD)

- Duas VMs implantado à sub-rede back-end e ingressar no domínio do AD do servidor SQL

- Um cluster WSFC 3 nós com o modelo de quorum maioria de nós

- Um grupo de disponibilidade com duas réplicas síncrono confirmação de um banco de dados de disponibilidade

A figura a seguir é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para AG no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos nesta solução pertencem a um grupo de recursos único.

Este tutorial assume o seguinte:

- Você já tem uma conta do Azure. Se você não tiver um, [Inscreva-se em uma conta de avaliação](http://azure.microsoft.com/pricing/free-trial/).

- Você já sabe como provisionar uma máquina virtual Server de SQL da Galeria de máquina virtual usando a interface gráfica. Para obter mais informações, consulte [provisionar uma máquina virtual de SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Você já tem uma compreensão sólida de grupos de disponibilidade. Para obter mais informações, consulte [sempre em grupos de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se você estiver interessado em usar grupos de disponibilidade com o SharePoint, consulte também [Configurar o SQL Server 2012 sempre em grupos de disponibilidade do SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

Neste tutorial, você usará o portal do Azure para:

- Selecione o o modelo sempre a partir do portal

- Examinar as configurações de modelo e atualizar algumas configurações do seu ambiente

- Monitor Azure como ela cria o ambiente inteiro

- Conectar a um dos controladores de domínio e, em seguida, para um dos servidores do SQL

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Provisionar o cluster da Galeria

Azure fornece uma imagem da Galeria para toda a solução. Para localizar o modelo:

1.  Faça logon no portal do Azure usando sua conta.
1.  No Azure portal, clique em **+ nova.** O portal abrirá a lâmina de novo.
1.  Na nova blade procurar **sempre ativado**.
![Localizar modelo sempre ativado](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Nos resultados da pesquisa localize **Cluster do SQL Server sempre ativado**.
![Modelo de sempre ativado](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  Em **Selecione um modelo de implantação** escolha **Gerenciador de recursos**.

### <a name="basics"></a>Noções básicas

Clique em **Noções básicas** e configure o seguinte:

- **Nome de usuário de administrador** é uma conta de usuário com permissões de administrador de domínio e um membro da função de servidor fixa de administrador do sistema do SQL Server em ambas as instâncias do SQL Server. Para este tutorial use **DomainAdmin**.

- **Senha** é a senha da conta de administrador de domínio. Use uma senha complexa. Confirme a senha.

- **Assinatura** é a assinatura que fará a cobrança para executar todos os recursos implantados para o grupo de disponibilidade do Azure. Você pode especificar uma assinatura diferente se sua conta tem várias assinaturas.

- **Grupo de recursos** é o nome para o grupo que todos os recursos Azure criado por este tutorial pertence. Para este tutorial use **SQL-HA-RG**. Para obter mais informações, consulte (visão geral do Gerenciador de recursos do Azure) [recurso-grupo-overview.md / #-grupos de recursos].

- **Local** é o Azure região onde os recursos para este tutorial serão criados. Selecione uma região Azure para hospedar a infraestrutura.

Abaixo está a aparência a lâmina **Noções básicas** :

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Clique em **Okey**.

### <a name="domain-and-network-settings"></a>Configurações de domínio e da rede

Este modelo de galeria Azure cria um novo domínio com novos controladores de domínio. Ele também cria uma nova rede e duas sub-redes. O modelo não permite criar os servidores em um domínio existente ou rede virtual. A próxima etapa é configurar as configurações de domínio e da rede.

Configurações de **domínio e da rede** blade Revise os valores predefinidos para as configurações de domínio e da rede:

- **Nome do domínio raiz da floresta** é o nome de domínio que será usado para o domínio AD que hospedará o cluster. Para o tutorial use **contoso.com**.

- **Nome de rede virtual** é o nome de rede para a rede virtual Azure. Para este tutorial use **autohaVNET**.

- **Nome de sub-rede do controlador de domínio** é o nome de uma parte da rede virtual que hospeda o controlador de domínio. Para este tutorial use **sub-rede-1**. Esta sub-rede usará o prefixo de endereço **10.0.0.0/24**.

- **Nome de sub-rede do SQL Server** é o nome de uma parte da rede virtual que hospeda os servidores de SQL e o arquivo compartilham testemunho. Para este tutorial use **sub-rede-2**. Esta sub-rede usará o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre redes virtuais no [Azure consulte Visão geral de rede Virtual](../virtual-network/virtual-networks-overview.md).  

As **configurações de domínio e da rede** deve ter esta aparência:

![Configurações de domínio e da rede](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, você pode alterar esses valores. Para este tutorial usamos os valores predefinidos.

- Examinar as configurações e clique em **Okey**.

###<a name="availability-group-settings"></a>configurações de grupo de disponibilidade

Configurações de **grupo** de disponibilidade, examine os valores predefinidos para o grupo de disponibilidade e o ouvinte.

- **Nome do grupo de disponibilidade** é o nome do recurso de cluster para o grupo de disponibilidade. Para este tutorial use **ag Contoso**.

- **nome do ouvinte de grupo de disponibilidade** é usado pelo cluster e balanceador de carga interno. Os clientes se conectam ao SQL Server podem usar esse nome para se conectar à réplica apropriada do banco de dados. Para este tutorial use **ouvinte Contoso**.

-  **porta de ouvinte do grupo de disponibilidade** Especifica que usará a porta TCP o ouvinte do SQL Server. Para este tutorial, use a porta padrão, **1433**.

Se necessário, você pode alterar esses valores. Para este tutorial, use os valores predefinidos.  

![configurações de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Clique em **Okey**.

###<a name="vm-size-storage-settings"></a>Tamanho da máquina virtual, configurações de armazenamento

Em **tamanho da máquina virtual, configurações de armazenamento** escolher um tamanho de máquina virtual do SQL Server e examine as outras configurações.

- **Tamanho da máquina virtual do SQL Server** é o tamanho do Azure máquina virtual para os servidores de SQL. Escolha um tamanho de máquina virtual apropriado para sua carga de trabalho. Se você estiver criando esse ambiente para o tutorial use **DS2**. Para cargas de trabalho de produção escolha um tamanho de máquina virtual que pode oferecer suporte a carga de trabalho. Muitas cargas de trabalho de produção exigirão **DS4** ou maior. O modelo irá criar duas máquinas virtuais desse tamanho e instalar o SQL Server em cada um deles. Para obter mais informações, consulte [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure serão instalados Enterprise Edition do SQL Server. O custo depende da edição e o tamanho da máquina virtual. Para obter informações detalhadas sobre custos atuais, consulte [máquinas virtuais preços](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Tamanho de máquina virtual do controlador de domínio** é o tamanho da máquina virtual para controladores de domínio. Para este tutorial use **D2**.

- **Tamanho da máquina virtual Testemunho de compartilhamento de arquivo** é o tamanho da máquina virtual para a testemunha de compartilhamento de arquivo. Para este tutorial use **A1**.

- **Conta de armazenamento de SQL** é o nome da conta de armazenamento para armazenar os dados do SQL Server e discos do sistema operacional. Para este tutorial use **alwaysonsql01**.

- **Conta de armazenamento de DC** é o nome da conta de armazenamento dos controladores de domínio. Para este tutorial use **alwaysondc01**.

- O **tamanho do disco de dados do SQL Server** em TB é o tamanho do disco de dados do SQL Server em TB. Especifica um número de 1 a 4. Esse é o tamanho do disco dados que será anexado à cada SQL Server. Para este tutorial use **1**.

- **Otimização de armazenamento** define configurações específicas de armazenamento para as máquinas virtuais do SQL Server com base no tipo de carga de trabalho. Todos os servidores de SQL neste cenário usar o armazenamento de premium com cache de host de disco Azure definido como somente leitura. Além disso, você pode otimizar as configurações do SQL Server para a carga de trabalho, escolhendo uma dessas três configurações:

    - **Carga de trabalho geral** define sem configurações específicas

    - **Processamento transacional** define o sinalizador de rastreamento 1117 e 1118

    - **Data warehouse** define o sinalizador de rastreamento 1117 e 610

Para este tutorial use **carga de trabalho geral**.

![Configurações de armazenamento de tamanho de máquina virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Examinar as configurações e clique em **Okey**.

####<a name="a-note-about-storage"></a>Uma observação sobre armazenamento

Outras otimizações dependem do tamanho dos discos dados do SQL Server. Para cada terabytes do disco de dados, o Azure adiciona um adicionais 1 TB de armazenamento de premium (SSD). Quando um servidor requer 2 TB ou mais, o modelo cria um pool de armazenamento em cada servidor SQL. Um pool de armazenamento é uma forma de virtualização de armazenamento onde vários discos são configurados para fornecer maior capacidade, resiliência e desempenho.  O modelo cria um espaço de armazenamento no pool de armazenamento e apresenta isso como um único de dados para o sistema operacional. O modelo designa este disco como o disco de dados para o SQL Server. O modelo ajusta o pool de armazenamento para o SQL Server com as seguintes configurações:

- Tamanho de faixa é a configuração de interleave do disco virtual. Para cargas de trabalho transações isso é definido para 64 KB. Para data warehouse cargas de trabalho a configuração é 256 KB.

- Resiliência é simple (sem resiliência).

>[AZURE.NOTE] Armazenamento do Azure premium é localmente redundante e mantém três cópias dos dados dentro de uma única região, portanto, mais resistência ao pool de armazenamento não é necessária.

- Número de colunas é igual ao número de discos no pool de armazenamento.

Para obter informações adicionais sobre o armazenamento pools de armazenamento e espaço, consulte:

- [Visão geral de espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx).

- [Backup do Windows Server e Pools de armazenamento](http://technet.microsoft.com/library/dn390929.aspx)

Para obter mais informações sobre práticas recomendadas de configuração do SQL Server, consulte [práticas recomendadas de desempenho para SQL Server no Azure máquinas virtuais](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Configurações do SQL Server

Em **configurações do SQL Server** Revise e modifique o prefixo de nome de máquina virtual do SQL Server, versão do SQL Server, conta de serviço do SQL Server e senha e automático SQL patches agenda de manutenção.

- **Prefixo do nome do SQL Server** é usado para criar um nome para cada SQL Server. Para este tutorial use **ag Contoso**. Os nomes do SQL Server será *Contoso-ag-0* e *Contoso-ag-1*.

- **Versão do SQL Server** é a versão do SQL Server. Para este tutorial use **2014 do SQL Server**. Você também pode escolher o **SQL Server 2012** ou **SQL Server 2016**.

- **Nome de usuário da conta de serviço do SQL Server** é o nome de conta de domínio para o serviço SQL Server. Para este tutorial use **sqlservice**.

- **Senha** é a senha da conta de serviço do SQL Server.  Use uma senha complexa. Confirme a senha.

- **Agenda de manutenção SQL automático patches** identifica o dia da semana que o Azure corrigirá automaticamente os servidores de SQL. Para este tutorial digite **domingo**.

- **SQL automático patches a hora de início de manutenção** é a hora do dia para a região Azure quando a correção automática começará.

>[AZURE.NOTE]A janela patch para cada máquina virtual é selecionar uma hora. Uma única máquina virtual está corrigida por vez para evitar interrupção dos serviços.

![Configurações do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Examinar as configurações e clique em **Okey**.

###<a name="summary"></a>Resumo

Na página de resumo Azure valida as configurações. Você também pode baixar o modelo. Revise o resumo. Clique em **Okey**.

###<a name="buy"></a>Comprar

Este final blade contém **termos de uso**e **política de privacidade**. Examine essas informações. Quando você estiver pronto para Azure começar a criar as máquinas virtuais e todos os recursos necessários para o grupo de disponibilidade, clique em **criar**.

O portal do Azure criará o grupo de recursos e todos os recursos.

##<a name="monitor-deployment"></a>Implantação do monitor

Monitore o progresso de implantação do portal do Azure. Um ícone que representa a implantação automaticamente é fixado no painel de portal Azure.

![Painel do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Conectar ao SQL Server

As novas instâncias do SQL Server são executados em máquinas virtuais que não tem conexões com a internet. No entanto, controladores de domínio tiver um opostas conexão de internet. Para se conectar aos servidores do SQL com área de trabalho remota, primeiro RDP para um dos controladores de domínio. Controlador de domínio abra uma segunda RDP para o SQL Server.

Ao RDP para o controlador de domínio primário, siga estas etapas:

1.  No Azure portal painel muito que a implantação foi bem-sucedido.

1.  Clique em **recursos**.

1.  Na lâmina **recursos** , clique em **ad-principal-dc** que é o nome do computador da máquina virtual do controlador de domínio primário.

1.  Na lâmina para **ad-principal-dc** , clique em **Conectar**. Seu navegador perguntará se você deseja abrir ou salvar o objeto de conexão remota. Clique em **Abrir**.
![Conectar ao DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Conexão de desktop remoto** poderá avisá-lo a que o Editor desta conexão remota não pode ser identificado. Clique em **Conectar**.

1.  Segurança do Windows solicitará que você insira suas credenciais para se conectar ao endereço IP do controlador de domínio primário. Clique em **usar outra conta**. Nome de **usuário** , digite **contoso\DomainAdmin**. Esta é a conta que você escolheu para nome de usuário de administrador. Use a senha complexa que você escolheu quando você configurou o modelo.

1.  **Área de trabalho remota** pode avisá-lo a que o computador remoto não pôde ser autenticado devido a problemas com o certificado de segurança. Ele mostrará o nome do certificado de segurança. Se você seguiu o tutorial o nome será **ad-principal-dc.contoso.com**. Clique em **Sim**.

Agora você está conectado ao controlador de domínio primário. Ao RDP para o SQL Server, siga estas etapas:

1.  No controlador de domínio, abra a **Conexão de área de trabalho remota**.

1.  Para o **computador**, digite o nome de um dos servidores do SQL. Neste tutorial, digite **0 do SQL Server**.

1.  Use a mesma conta de usuário e senha que você usou para RDP ao controlador de domínio.

Agora você está conectado com RDP para o SQL Server. Você pode abra o SQL Server management studio, conectar-se a instância padrão do SQL Server e verifique se que o grupo availabilty está configurado.


