<properties 
    pageTitle="Proteger o SQL Server com recuperação de dados do SQL Server e recuperação de Site do Azure | Microsoft Azure" 
    description="Este artigo descreve como replicar o SQL Server usando recursos de desastres Azure Site recuperação do SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger o SQL Server com recuperação de dados do SQL Server e recuperação de Site do Azure 


O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md).

 Este artigo descreve como proteger back-end do SQL Server de um aplicativo usando uma combinação de tecnologias do SQL Server BCDR e recuperação de Site do Azure. Você deve ter uma boa compreensão dos recursos de recuperação de desastres do SQL Server (banco de dados de cluster de failover, grupos de disponibilidade sempre ativado, espelhar, envio de log) e de recuperação de Site do Azure, antes de implantar os cenários descritos neste artigo.



## <a name="overview"></a>Visão geral

Muitas cargas de trabalho usam SQL Server como uma base. Aplicativos como o SharePoint, Dynamics e SAP usam SQL Server para implementar serviços de dados.  Aplicativos implantar o SQL Server em um número de maneiras diferentes:

- **Independente do SQL Server**: SQL Server e todos os bancos de dados são hospedados em uma única máquina (física ou uma virtual). Quando virtualizado, cluster de host é usado para alta disponibilidade local. Sem disponibilidade de alta nível de convidado é implementada.
- **Instâncias de cluster de Failover (sempre em FCI) do SQL Server**: dois ou mais nós de instâncias do SQL server com discos compartilhados são configurados em um cluster de Failover do Windows. Se qualquer uma das instâncias cluster for pressionada, o cluster pode falhar ao longo do SQL Server para outra instância. Essa configuração geralmente é usada para HA em um site principal. Ele não protege contra falha ou interrupção na camada de armazenamento compartilhado. Disco compartilhado pode ser implementado usando ISCSI, canal de fibra ou VHDx compartilhado.
- **Sempre na disponibilidade grupos SQL**: nessa configuração, dois nós são configurados no compartilhado nada cluster com bancos de dados do SQL Server configurados em um grupo de disponibilidade com replicação síncrona e failover automático.

Em edições do Enterprise, SQL Server também fornece desastres nativo tecnologias de recuperação para recuperar bancos de dados para um local remoto. Neste artigo, vamos aproveitar e integrar com essas tecnologias de recuperação de desastres nativas do SQL: 

- SQL sempre em grupos de disponibilidade de recuperação de dados para o SQL Server 2012 ou edições de empresa de 2014.
- SQL espelhar do banco de dados no modo de segurança alta para SQL Server Standard edition (qualquer versão), ou para SQL Server 2008 R2.


Recuperação de site pode proteger o SQL Server, conforme resumido na tabela.

 |**Local para o local** | **Local para o Azure** 
---|---|---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim 
**Servidor físico** | Sim | Sim


## <a name="support-and-integration"></a>Suporte e integração

Estas versões do SQL Server são suportadas pelos cenários neste artigo:


- Standard e Enterprise do SQL Server de 2014
- Standard e Enterprise do SQL Server 2012
- Padrão e SQL Server 2008 R2 Enterprise


Recuperação de site pode ser integrada com tecnologias nativas do SQL Server BCDR resumidas na tabela abaixo para fornecer uma solução de recuperação.

**Recurso** |**Detalhes** | **Versão do SQL Server** 
---|---|---
**Grupo de disponibilidade sempre ativado** | Várias instâncias de autônoma do SQL Server cada executar em um cluster de failover que tem vários nós.<br/><br/>Bancos de dados podem ser agrupados em grupos de failover que podem ser copiados (espelhada) instâncias do SQL Server para que nenhum armazenamento compartilhado é necessária.<br/><br/>Fornece recuperação de dados entre um site principal e um ou mais sites secundários. Dois nós podem ser configurados em um compartilhado nada cluster com bancos de dados do SQL Server configurado em um grupo de disponibilidade com replicação síncrona e failover automático. | SQL Server 2014 & 2012 Enterprise edition
**Failover cluster (AlwaysOn FCI)** | SQL Server utiliza cluster para alta disponibilidade das cargas de trabalho do local do SQL Server de failover do Windows.<br/><br/>Nós executando instâncias do SQL Server com discos compartilhados são configurados em um cluster de failover. Se uma instância está inoperante cluster falhará sobre para outro diferente.<br/><br/>O cluster não protege contra falha ou interrupções em armazenamento compartilhado. O disco compartilhado pode ser implementado com iSCSI, canal de fibra, ou compartilhado VHDXs. | Edições do SQL Server Enterprise<br/><br/>SQL Server Standard edition (limitado a dois nós)
**Espelhamento (modo de segurança alta) banco de dados** | Protege um único banco de dados a uma única cópia secundária. Disponível em ambos os segurança alta (síncrono) e modos de replicação (assíncrono) de alto desempenho. Não requer um cluster de failover. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise todas as edições
**Autônomo SQL Server** | O SQL Server e o banco de dados são hospedados em um único servidor (físico ou virtual). Agrupamento de host é usado para alta disponibilidade se o servidor virtual. Sem disponibilidade de alta nível de convidado. | Enterprise ou Standard edition

## <a name="deployment-recommendations"></a>Recomendações de implantação


Esta tabela resume nossas recomendações para integrar tecnologias do SQL Server BCDR a recuperação do Site.

**Versão** |**Edition** | **Implantação** | **Local para local** | **Local para o Azure** 
---|---|---|---|---
SQL Server 2012 ou 2014 | Enterprise | Instância de cluster de failover | Grupos de disponibilidade sempre ativado | Grupos de disponibilidade sempre ativado
 | Enterprise | Grupos de disponibilidade sempre ativado para alta disponibilidade | Grupos de disponibilidade sempre ativado | Grupos de disponibilidade sempre ativado
 | Padrão | Instância de cluster de failover (FCI) | Replicação de recuperação de site com espelho local | Replicação de recuperação de site com espelho local
 | Enterprise ou Standard | Autônomo | Replicação de recuperação de site | Replicação de recuperação de site
SQL Server 2008 R2 | Enterprise ou Standard | Instância de cluster de failover (FCI) | Replicação de recuperação de site com espelho local | Replicação de recuperação de site com espelho local
 | Enterprise ou Standard | Autônomo | Replicação de recuperação de site | Replicação de recuperação de site
SQL Server (qualquer versão) | Enterprise ou Standard | Instância de cluster failover - o aplicativo de DTC | Replicação de recuperação de site | Sem suporte


## <a name="deployment-prerequisites"></a>Pré-requisitos de implantação

Veja aqui o que é necessário antes de começar:

- Uma implantação do SQL Server local executando uma versão suportada do SQL Server. Normalmente você precisará também um Active Directory para o SQL server.
- Os pré-requisitos para o cenário que você deseja implantar. Pré-requisitos podem ser encontrados no artigo cada implantação. Links para esses são fornecidas na [Visão geral de recuperação do Site](site-recovery-overview.md).
- Se você quiser configurar a recuperação no Azure, você precisará executar a ferramenta de [Avaliação de prontidão de máquina Virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) nas máquinas virtuais do SQL Server para verificar se que elas são compatíveis com o Azure e recuperação do Site.


## <a name="set-up-active-directory"></a>Configurar o Active Directory

Você precisará do Active Directory no site de recuperação secundário para o SQL Server ser executado corretamente. Há algumas opções:

- **Pequena empresa**— se você tiver um pequeno número de aplicativos e um único controlador de domínio para o site local e você deseja falhar ao longo de todo o site, recomendamos que você use repication de recuperação de Site para replicar o controlador de domínio ao data center secundário ou no Azure.

- **Médias e grandes empresas**— se você tiver um grande número de aplicativo, você está executando uma floresta do Active Directory e desejar falha no novo aplicativo ou carga de trabalho, é recomendável configurar um controlador de domínio adicionais no data center secundário ou no Azure. Observe que se você estiver usando grupos de disponibilidade sempre ativado para recuperar para um local remoto é recomendável que você configurar outro controlador de domínio adicionais no site secundário ou Azure, usar para instância do SQL Server recuperada.

As instruções neste documento presumem que um controlador de domínio está disponível no local secundário. [Leia mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com recuperação do Site.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Integrar proteção com o SQL Server Always-On (local no Azure)


Recuperação de site nativamente compatível com SQL AlwaysOn. Se você criou um grupo de disponibilidade do SQL com uma máquina virtual Azure configurado como 'Secundário', então você pode usar a recuperação de Site para gerenciar o failover dos grupos de disponibilidade. 

>[AZURE.NOTE] Esse recurso está no modo de visualização e disponível quando os servidores de host Hyper-V no data center principal são gerenciados em nuvens VMM e quando a instalação do VMware é gerenciada por um [Servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Agora, esse recurso não está disponível no novo portal do Azure.

#### <a name="prerequisites"></a>Pré-requisitos

Veja aqui o que você precisa integrar SQL AlwaysOn com recuperação do Site:

- Um local do SQL Server (servidor autônomo ou um cluster de failover).
- Uma ou mais máquinas virtuais Azure com o SQL Server instalado
- Um grupo de disponibilidade do SQL configurada entre um local do SQL Server e SQL Server em execução no Azure
- Conexão remota do PowerShell deve ser habilitada no computador local do SQL Server. O VMM ou o servidor de configuração deve ser capaz de fazer chamadas de PowerShell remotas para o SQL Server.
- Uma conta de usuário deve ser adicionada no local SQL Server, esses grupos de usuário do SQL com pelo menos estas permissões:
    - GRUPO de disponibilidade ALTER: permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx)e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Banco de dados ALTER - permissões[aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Uma conta de RunAs deve ser criada no servidor VMM ou uma conta deve ser criada no servidor de configuração usando o CSPSConfigtool.exe para o usuário mencionado na etapa anterior 
- O módulo SQL PS deve ser instalado no SQL servidores que executam o local e de Azure máquinas virtuais
- O agente de máquina virtual devem ser instaladas máquinas virtuais em execução no Azure
- NTAUTHORITY\System deve ter a seguir permissões no SQL Server em execução em máquinas virtuais no Azure:
    - ALTERAR o grupo de disponibilidade - permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx)e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Banco de dados ALTER - permissões [aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Etapa 1: Adicionar um SQL Server


1. Clique em **Adicionar SQL** para adicionar um novo SQL Server. 

    ![Adicionar SQL](./media/site-recovery-sql/add-sql.png)

2. Em **Definir configurações de SQL** > **nome** fornecer um nome amigável para se referir ao SQL Server.
3. **No SQL Server (FQDN)** especificar o FQDN da fonte de SQL Server que você deseja adicionar. Caso o SQL Server estiver instalado em um Cluster de Failover, então fornece FQDN do cluster e não de qualquer um de nós do cluster.  
4. Na **Instância do SQL Server** escolher a instância padrão ou fornecer o nome da instância personalizado.
5. No **Servidor de gerenciamento** , selecione um servidor VMM ou configuração registrado no cofre de recuperação do Site. Recuperação de site usa esse servidor de gerenciamento para se comunicar com o SQL Server.
6. Na caixa **Executar como conta** fornece o nome de uma conta de RunAs que foi criada no servidor VMM especificado ou a conta que foi criada no servidor Configuraaaon. Essa conta é usada para acessar o SQL Server e deve ter permissões de leitura e Failover nos grupos de disponibilidade na máquina do SQL Server.

    ![Adicionar caixa de diálogo SQL](./media/site-recovery-sql/add-sql-dialog.png)

Após adicionar o SQL Server, ele aparecerá na guia **Servidores de SQL** . 

![Lista do SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Etapa 2: Adicionar um grupo de disponibilidade do SQL

1. Depois que o SQL Server máquina for adicionada que a próxima etapa é adicionar os grupos de disponibilidade a recuperação do Site. Para fazer isso, fazer drill down dentro do SQL Server adicionado na etapa anterior e clique em Adicionar grupo de disponibilidade do SQL. 

    ![Adicionar AG SQL](./media/site-recovery-sql/add-sqlag.png)

2. Grupo de disponibilidade do SQL podem ser replicar uma ou mais máquinas virtuais no Azure. Adicionando quando o grupo de disponibilidade do sql que é necessários para fornecer o nome e a assinatura da máquina virtual Azure onde deseja que o grupo de disponibilidade para ser falha pela recuperação de Site.

    ![Caixa de diálogo SQL AG adicionar](./media/site-recovery-sql/add-sqlag-dialog.png)

3. No exemplo acima o grupo de disponibilidade DB1-AG tornaria primário na máquina virtual SQLAGVM2 executando dentro de assinatura DevTesting2 em um failover. 

>[AZURE.NOTE] Somente os grupos de disponibilidade que são principal no SQL Server adicionado na etapa acima estão disponíveis para ser adicionada a recuperação do Site. Se você tiver feito uma disponibilidade grupo primário no SQL Server ou se você tiver adicionado mais grupos de disponibilidade no SQL Server depois que ela foi adicionada, atualize-a usando a opção de atualização disponível no SQL Server.

#### <a name="step-3-create-a-recovery-plan"></a>Etapa 3: Criar um plano de recuperação

A próxima etapa é criar um plano de recuperação usando máquinas virtuais e os grupos de disponibilidade. Selecione o mesmo VMM servidor ou configuração que você usou na etapa 1 como origem e Microsoft Azure como destino.

![Criar plano de recuperação](./media/site-recovery-sql/create-rp1.png)

![Criar plano de recuperação](./media/site-recovery-sql/create-rp2.png)

No exemplo o aplicativo do Sharepoint consiste em 3 máquinas virtuais que use um grupo de disponibilidade do SQL como seu back-end. Neste plano de recuperação que poderia selecionamos os dois o grupo de disponibilidade também a máquina virtual que constituem o aplicativo. 

Você pode personalizar o plano de recuperação movendo máquinas virtuais aos grupos de failover diferente a sequência a ordem de falha. Grupo de disponibilidade sempre Falha ao longo do primeiro como ele deve ser usado como um back-end de qualquer aplicativo. 

![Personalizar o plano de recuperação](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Etapa 4: Falhar

Opções de failover diferentes estarão disponíveis quando um grupo de disponibilidade foi adicionado a um plano de recuperação.

Failover | Detalhes
--- | ---
**Failover planejado** | Failover planejado significa um sem failover de perda de dados. Para obter o modo de disponibilidade do grupo de disponibilidade que SQL primeiro está definido como síncrono e, em seguida, um failover é acionado para tornar o grupo de disponibilidade primário na máquina virtual fornecido ao adicionar o grupo de disponibilidade à recuperação do Site. Quando o failover estiver concluído, o modo de disponibilidade é definido para o mesmo valor como ela estava antes do failover planejado foi acionado.
**Failover não planejado** | Failover não planejado pode resultar em perda de dados. Enquanto disparo failover não planejado o modo de disponibilidade do grupo de disponibilidade não é alterado e o it é feita primário na máquina virtual fornecida ao adicionar o grupo de disponibilidade à recuperação do Site. Depois de failover não planejado é concluída e servidor local que executa o SQL Server está disponível novamente, reverter replicação deve ser disparadas no grupo de disponibilidade. Observe que esta ação não está disponível em seu plano de recuperação e pode ser feita em grupo de disponibilidade do SQL na guia servidores SQL
**Failover de teste** | Não há suporte para failover de teste para o grupo de disponibilidade do SQL. Se você acionar Failover de teste de um plano de recuperação que contém o grupo de disponibilidade do SQL, failover será ignorado para o grupo de disponibilidade.


Considere estas opções de failover.

Opção | Detalhes
--- | ---
**Opção 1** | 1. realize um failover de teste do aplicativo e níveis de front-end.<br/><br/>2. Atualize a camada de aplicativo para acessar a cópia de réplica no modo somente leitura e executar um teste de somente leitura do aplicativo.
**Opção 2** | 1. Crie uma cópia da instância de máquina virtual do SQL Server réplica (usando clonar VMM para Backup de Azure ou to-site) e exibi-lo em uma rede de teste<br/><br/> 2. realize o failover de teste usando o plano de recuperação.

Etapa 5: Falha de volta

Se você quiser fazer o grupo de disponibilidade novamente primária no SQL Server local você pode fazer isso disparo Failover planejado no plano de recuperação e escolhendo a direção da Microsoft Azure ao local VMM Server.

>[AZURE.NOTE] Após um failover não planejado replicação reversa deve ser disparadas no grupo de disponibilidade para retomar a replicação. Até que isso é feito a replicação permanecerá suspensa.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Proteger máquinas sem um servidor VMM ou um servidor de configuração

Para os ambientes que não são gerenciados por um servidor VMM ou um servidor de configuração, Runbooks de automação do Azure podem ser usados para configurar um script failover de grupos de disponibilidade do SQL. Estas são as etapas para configurar que:

1.  Crie um arquivo local para o script falha ao longo de um grupo de disponibilidade. Esse script de exemplo especifica um caminho para o grupo de disponibilidade na réplica Azure e falha, ele sobre a essa instância de réplica. Esse script será executado no SQL Server réplica virtual machine passando está com a extensão de script personalizado.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Carregue o script para um blob em uma conta de armazenamento do Azure. Use este exemplo:

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Crie um runbook automação Azure para invocar os scripts na máquina virtual do SQL Server réplica no Azure. Use este script de exemplo para fazer isso. [Saiba mais](site-recovery-runbook-automation.md) sobre como usar automação runbooks nos planos de recuperação. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Quando você cria um plano de recuperação para o aplicativo Adicionar uma etapa de script "pré-grupo de 1 inicialização" que invoca runbook automação falha grupos de disponibilidade.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Integrar proteção com SQL AlwaysOn (no local para o local)

Se o SQL Server estiver usando grupos de disponibilidade para alta disponibilidade, ou uma instância de cluster de failover, é recomendável usar grupos de disponibilidade no site de recuperação. Observe que neste guia para aplicativos que não usam transações distribuídas.

1. [Configurar bancos de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
2. Crie uma nova rede virtual em local secundário.
3. Configure uma VPN to-site entre a nova rede virtual e o site principal.
4. Criar uma máquina virtual do site de recuperação e instalar o SQL Server nele.
5. Estenda os grupos de disponibilidade sempre ativado existentes para a nova máquina virtual do SQL Server. Configure esta instância do SQL Server como uma cópia de réplica assíncrona.
6. Criar um ouvinte de grupo de disponibilidade, ou atualizar o ouvinte existente para incluir a máquina virtual de réplica assíncrona.
7. Verifique se o farm de aplicativo está usando o ouvinte de configuração. Se for configurar usando o nome de servidor de banco de dados, atualize-o para usar o ouvinte, então você não precisa reconfigure-após o failover.

Para aplicativos que usam transações distribuídas podemos recomendação você usar [Recuperação de Site com replicação de SAN](site-recovery-vmm-san.md) ou de [replicação do VMWare/física server - to-site](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerações de planejamento de recuperação

1. Adicione esse script de exemplo a biblioteca do VMM nos sites primárias e secundários.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Quando você cria um plano de recuperação para o aplicativo Adicionar uma etapa de script "pré-grupo de 1 inicialização" que chama o script falha grupos de disponibilidade.



## <a name="protect-a-standalone-sql-server"></a>Proteger autônoma do SQL Server

Nessa configuração, que recomendamos que você use replicação de recuperação do Site para proteger a máquina do SQL Server. As etapas exatas dependerá se SQL Server está configurado como uma máquina virtual ou o servidor físico e se você deseja duplicar para Azure ou um secundário site local. Obtenha instruções para todos os cenários de implantação na [Visão geral de recuperação do Site](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Proteger um cluster de SQL Server (padrão ou 2008 R2)

Para um cluster executando o SQL Server Standard edition ou o SQL Server 2008 R2 recomendamos que você use replicação de recuperação do Site para proteger o SQL Server.

### <a name="on-premises-to-on-premises"></a>Local para o local

- Se o aplicativo usa transações distribuídas recomendamos que você implantar [Recuperação de Site com replicação de SAN](site-recovery-vmm-san.md) para um ambiente de Hyper-V e [servidor VMware/físico para VMware](site-recovery-vmware-to-vmware.md) para ambiente VMware.

- Para aplicativos não-DTC, utilize a abordagem acima para recuperar o cluster como um servidor autônomo utilizando uma local DB espelhada de segurança alta.

### <a name="on-premises-to-azure"></a>Local para o Azure

Recuperação de site não dá suporte a suporte a cluster convidado ao replicar no Azure. SQL Server também não oferece uma solução econômica de recuperação para Standard edition. Recomendamos que você proteja o cluster do SQL Server local autônoma do SQL Server e recuperá-lo no Azure.


1. Configure uma instância do SQL Server autônomo adicionais no site local.
2. Configure esta instância para servir como um espelho para bancos de dados que precisam de proteção. Configure o espelhamento no modo de segurança alta.
3.  Configurar a recuperação de Site no site do local com base no ambiente ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [servidor VMware/física](site-recovery-vmware-to-azure-classic.md).
4.  Use a replicação de recuperação de Site para replicar a nova instância do SQL Server para Azure. É uma cópia de espelho de segurança alta e portanto ele será sincronizado com o cluster principal, mas ele vai ser replicado para Azure usando replicação de recuperação do Site.

O gráfico a seguir ilustra essa configuração.

![Cluster padrão](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Considerações sobre failback

Para clusters padrão SQL, failback após um failover não planejado será exigem um backup SQL e restaurar a partir da instância de espelho para cluster original e restabelecer o espelho.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais](site-recovery-best-practices.md) sobre como se preparando para implantar recuperação do Site.










 