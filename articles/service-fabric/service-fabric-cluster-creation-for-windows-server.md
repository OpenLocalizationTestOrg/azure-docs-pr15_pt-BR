<properties
   pageTitle="Criar e gerenciar um cluster de estrutura de serviço do Azure autônomo | Microsoft Azure"
   description="Criar e gerenciar um cluster de estrutura de serviço do Azure em qualquer computador (físico ou virtual) executando o Windows Server, seja ele local ou em qualquer nuvem."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>


# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Criar e gerenciar um cluster executando o Windows Server

Você pode usar tecidos de serviço do Azure para criar clusters de serviço tecidos em qualquer máquinas virtuais ou computadores que executam o Windows Server. Isso significa que você pode implantar e executar aplicativos de serviço tecidos em qualquer ambiente que contém um conjunto de computadores interconectados do Windows Server, seja no local ou em qualquer provedor de nuvem. Serviço tecidos fornece um pacote de instalação para criar clusters de serviço tecidos chamados o pacote de Windows Server autônomo.

Este artigo conduz você pelas etapas para criar um cluster usando o pacote de autônomo tecidos de serviço no local, embora ele pode ser facilmente adaptado para qualquer outro ambiente como outros provedores de nuvem.

>[AZURE.NOTE] Este pacote de Windows Server autônomo pode conter recursos que estão atualmente no modo de visualização e não há suporte para uso comercial. Para ver a lista de recursos que estão na visualização, consulte "Preview recursos incluídos neste pacote." Você também pode [baixar uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) agora.


<a id="getsupport"></a>
## <a name="get-support-for-the-service-fabric-standalone-package"></a>Obter suporte para o pacote de standalone tecidos de serviço

- Pergunte à comunidade sobre o pacote de autônomo tecidos de serviço para o Windows Server no [Fórum do Azure serviço tecidos](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).

- Abra um tíquete de [Suporte profissional tecidos de serviço](http://support.microsoft.com/oas/default.aspx?prid=16146 ).  Saiba mais sobre [O suporte profissional da Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).

<a id="downloadpackage"></a>
## <a name="download-the-service-fabric-standalone-package"></a>Baixe o pacote de standalone tecidos de serviço


[Baixe o pacote autônomo para o serviço tecidos do Windows Server 2012 R2 e posterior](http://go.microsoft.com/fwlink/?LinkId=730690), que é chamado Microsoft.Azure.ServiceFabric.WindowsServer. &lt;versão&gt;. zip.


O pacote de download, você encontrará os arquivos a seguir:

|**Nome do arquivo**|**Breve descrição**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|O arquivo. cab que contém os binários que são implantados em cada máquina no cluster.|
|ClusterConfig.Unsecure.DevCluster.json|Um arquivo de amostra de configuração de cluster que contém as configurações para um desprotegida, três nós, computador único (ou máquina virtual) desenvolvimento cluster, incluindo as informações para cada nó no cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|Um arquivo de amostra de configuração de cluster que contém as configurações para um cluster desprotegida, vários máquina (ou máquina virtual), incluindo as informações para cada máquina no cluster.|
|ClusterConfig.Windows.DevCluster.json|Um arquivo de amostra de configuração de cluster que contém todas as configurações para um seguro, três nós, computador único (ou máquina virtual) desenvolvimento cluster, inclusive as informações de cada nó no cluster. O cluster é protegido usando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Um arquivo de amostra de configuração de cluster que contém todas as configurações para um cluster de vários computadores (ou máquina virtual) seguro, usando a segurança do Windows, inclusive as informações de cada computador que esteja no cluster seguro. O cluster é protegido usando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Um arquivo de amostra de configuração de cluster que contém todas as configurações para um seguro, três nós, computador único (ou máquina virtual) desenvolvimento cluster, inclusive as informações de cada nó no cluster. O cluster é protegido usando x509 certificados.|
|ClusterConfig.x509.MultiMachine.json|Um arquivo de amostra de configuração de cluster que contém todas as configurações para o cluster seguro, vários máquina (ou máquina virtual), inclusive as informações de cada nó no cluster seguro. O cluster é protegido usando x509 certificados.|
|EULA_ENU.txt|Os termos de licença para o uso do pacote do Microsoft Azure serviço tecidos standalone Windows Server. Agora você pode [baixar uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) .|
|Leiame. txt|Um link para as notas de versão e instruções de instalação básica. É um subconjunto das instruções neste documento.|
|CreateServiceFabricCluster.ps1|Um script do PowerShell que cria o cluster usando as configurações no ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Um script PowerShell que remove um cluster usando as configurações no ClusterConfig.json.|
|ThirdPartyNotice.rtf |Notificação de software de terceiros que está no pacote.|
|AddNode.ps1|Um script do PowerShell para adicionar um nó a uma existente implantado cluster.|
|RemoveNode.ps1|Um script do PowerShell para remover um nó do existente implantado cluster.|
|CleanFabric.ps1|Um script PowerShell de limpeza autônoma instalação de Service tecidos logoff do computador atual. Instalações anteriores do MSI devem ser removidas usando suas próprias desinstaladores associados.|
|TestConfiguration.ps1|Um script do PowerShell para analisar a infraestrutura conforme especificado na Cluster.json.|


## <a name="plan-and-prepare-your-cluster-deployment"></a>Planejar e preparar sua implantação de cluster
Execute as seguintes etapas antes de criar seu cluster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Etapa 1: Planejar sua infraestrutura de cluster
Você está prestes a criar um cluster de serviço tecidos em máquinas que você possui, para que você possa decidir que tipos de falhas cluster sobreviver. Por exemplo, são necessárias separados linhas de energia ou conexões com a Internet fornecidos a essas máquinas? Além disso, considere a segurança física dessas máquinas. Onde as máquinas estão localizadas e quem precisa de acesso a eles? Após fazer essas decisões, você pode mapear logicamente as máquinas para os vários domínios de falhas (consulte a etapa 4). A infraestrutura de planejamento para clusters de produção é mais atrativo que para clusters de teste.

<a id="preparemachines"></a>
### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Etapa 2: Preparar as máquinas para cumprir os pré-requisitos
Pré-requisitos para cada máquina que você deseja adicionar ao cluster:

- É recomendável um mínimo de 16 GB de RAM.
- É recomendável um mínimo de 40 GB de espaço disponível em disco.
- É recomendável um núcleo 4 ou CPU maior.
- Conectividade com uma rede segura ou redes para todas as máquinas.
- Windows Server 2012 R2 ou Windows Server 2012 (você precisa ter [KB2858668](https://support.microsoft.com/kb/2858668) instalado).
- [.NET framework 4.5.1 ou superior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa.
- [O Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- O [serviço de RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve ser executado em todas as máquinas.

O administrador de cluster implantar e configurar o cluster deve ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada uma das máquinas. Você não pode instalar o serviço tecidos em um controlador de domínio.

### <a name="step-3-determine-the-initial-cluster-size"></a>Etapa 3: Determinar o tamanho de cluster inicial
Cada nó em um cluster de estrutura de serviço autônomo tem a estrutura de serviço runtime implantado e é um membro do cluster. Em uma implantação de produção típica, há um nó por instância do sistema operacional (físicos ou virtuais). O tamanho do cluster é determinado por suas necessidades de negócios. No entanto, você deve ter um tamanho de cluster mínimo de três nós (máquinas ou máquinas virtuais).
Para fins de desenvolvimento, você pode ter mais de um nó em uma determinada máquina. Em um ambiente de produção, serviço tecidos suporta somente um nó por física ou máquina virtual.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Etapa 4: Determinar o número de domínios de falha e atualizar domínios
Um *domínio de falha* (FD) é uma unidade física de falha e está diretamente relacionada na infraestrutura física nos data centers. Um domínio de falha consiste em componentes de hardware (computadores, opções, redes e muito mais) que compartilham um único ponto de falha. Embora não haja nenhum mapeamento 1:1 entre domínios de falha e racks, livremente falando, cada rack pode ser considerado um domínio de falha. Ao considerar os nós no seu cluster, é altamente recomendável que os nós seja distribuída entre pelo menos três domínios de falha.

Quando você especificar FDs no ClusterConfig.json, você pode escolher o nome de cada FD. Serviço tecidos suporta FDs hierárquicos, para que você possa refletir sua topologia de infraestrutura neles.  Por exemplo, os FDs a seguintes são válidas:

- "faultDomain": "fd: / Rack1/Room1/local1"
- "faultDomain": "fd: / FD1"
- "faultDomain": "fd: / Room1/Rack1/PDU1/M1"


Um *domínio de atualização* (UD) é uma unidade lógica de nós. Durante as atualizações de serviço tecidos coordenadas (uma atualização do aplicativo ou uma atualização de cluster), todos os nós em um UD são tirados para baixo para executar a atualização enquanto nós nos outros UDs permanecem disponíveis para atender às solicitações. As atualizações de firmware você executar em suas máquinas não aceitar UDs, portanto você deve segui-los um computador por vez.

A maneira mais simples de pensar sobre esses conceitos é considerar FDs como a unidade de falha planejada e UDs como a unidade de manutenção planejada.

Quando você especificar UDs no ClusterConfig.json, você pode escolher o nome de cada UD. Por exemplo, os nomes a seguir são válidos:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Azul"

Para obter informações mais detalhadas sobre atualização de domínios e falhas, consulte [descrevendo um cluster de estrutura de serviço](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Etapa 5: Baixe o pacote de standalone tecidos de serviço para o Windows Server
[Baixe o pacote de standalone tecidos de serviço para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote, para uma máquina de implantação que não faz parte do cluster, ou uma das máquinas que farão parte do cluster. Você pode renomear a pasta descompactada `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>
## <a name="create-your-cluster"></a>Criar seu cluster

Depois que você passou entre as etapas de planejamento e preparação, você está pronto para criar o cluster.

### <a name="step-1-modify-cluster-configuration"></a>Etapa 1: Modificar a configuração de cluster
O cluster é descrito em ClusterConfig.json. Para obter detalhes sobre as seções neste arquivo, consulte [configurações de configuração para cluster de autônoma do Windows](service-fabric-cluster-manifest.md).
Abra um dos arquivos ClusterConfig.json do pacote que você baixou e modificar as seguintes configurações:

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

|**Configuração**|**Descrição**|
|-----------------------|--------------------------|
|**NodeTypes**|Tipos de nós permitem separar os nós de cluster em vários grupos. Um cluster deve ter pelo menos um NodeType. Todos os nós em um grupo têm as seguintes características comuns: <br> **Nome** - este é o nome do tipo de nó. <br>**Portas de ponto de extremidade** - esses são diversos nomeados pontos de extremidade (portas) que estão associados esse tipo de nó. Você pode usar qualquer número de porta que você quer que, desde que eles não entram em conflito com mais neste manifesto nada e não ainda estão em uso por outro aplicativo em execução na máquina/máquina virtual. <br> **Propriedades de posicionamento** - esses descrevem propriedades para este tipo de nó usado como restrições de posicionamento para os serviços do sistema ou seus serviços. Essas propriedades são pares de chave/valor definido pelo usuário que fornecem dados extras de metadados para um determinado nó. Exemplos de propriedades de nó seria se o nó tem um disco rígido ou placa gráfica, o número de eixos em seu disco rígido, cores e outras propriedades físicas. <br> **Capacidades** - nó capacidades definem o nome e a quantidade de um determinado recurso que tenha um determinado nó disponível para consumo. Por exemplo, um nó pode definir que ela tem a capacidade de uma métrica chamada "MemoryInMb" e que ele possui 2048 MB disponível por padrão. Essas capacidades são usadas em tempo de execução para garantir que os serviços que exigem determinadas quantidades de recursos são colocados em nós que tenham os recursos disponíveis nos valores necessários.<br>**IsPrimary** - se você tiver mais de um NodeType definido garantir que apenas uma está definida para principal com o valor *true*, que é onde os serviços de sistema são executados. Todos os outros tipos de nó devem ser definidos como o valor *false*|
|**Nós**|Estes são os detalhes de cada um de nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de falha e domínio de atualização do nó). As máquinas que você deseja que o cluster a ser criado no precisam estar listada aqui com seus endereços IP. <br> Se você usar o mesmo endereço IP para todos os nós, em seguida, um cluster de uma caixa é criado, que você pode usar para fins de teste. Não use clusters de uma caixa para implantar cargas de trabalho de produção.|


### <a name="step-2-run-the-testconfiguration-script"></a>Etapa 2: Executar o script TestConfiguration

O script de TestConfiguration testa sua infraestrutura conforme definido na cluster.json para certificar-se de que as permissões necessárias são atribuídas, as máquinas estão conectadas uns aos outros e outros atributos são definidos para que a implantação seja bem-sucedido. Ele é basicamente um mini analisador de práticas recomendadas. Podemos vão continuar a adicionar mais validações esta ferramenta ao longo do tempo para torná-lo mais eficiente.

Esse script pode ser executado em qualquer computador que tenha acesso de administrador para todas as máquinas que são listados como nós no arquivo de configuração de cluster. Máquina que esse script é executado no não precisa ser parte do cluster.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3-run-the-create-cluster-script"></a>Etapa 3: Executar a criação de script de cluster
Depois que você modificou a configuração de cluster no documento JSON e adicionado todas as informações de nó, execute a criação de cluster script do PowerShell *CreateServiceFabricCluster.ps1* da pasta de pacote e passar no caminho para o arquivo de configuração de JSON. Quando isso estiver concluído, aceite o EULA.

Esse script pode ser executado em qualquer computador que tenha acesso de administrador para todas as máquinas que são listados como nós no arquivo de configuração de cluster. Máquina que esse script é executado no não precisa ser parte do cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] Os logs de implantação estão disponíveis localmente na máquina virtual/máquina que você executou o CreateServiceFabricCluster PowerShell em. Eles estão em uma subpasta chamada DeploymentTraces na pasta onde você executou o comando do PowerShell. Para ver se tecidos de serviço foi implantado corretamente a um computador, você pode encontrar os arquivos instalados no diretório C:\ProgramData e os processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gerenciador de tarefas.

### <a name="step-4-connect-to-the-cluster"></a>Etapa 4: Conectar ao cluster

Para se conectar a um cluster de seguro, consulte [conectar ao cluster seguro de estrutura de serviço](service-fabric-connect-to-secure-cluster.md).

Para se conectar a um cluster insegura, execute o seguinte comando do PowerShell:

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>Etapa 5: Trazer o serviço tecidos Explorer

Agora você pode se conectar ao cluster com o serviço tecidos Explorer tanto diretamente de uma das máquinas com http://localhost:19080/Explorer/index.html ou remotamente http://<;*IPAddressofaMachine*>: 19080/Explorer/index.html.



## <a name="add-and-remove-nodes"></a>Adicionar e remover nós

Você pode adicionar ou remover nós para seu cluster de estrutura de serviço autônomo conforme seus negócios mudam. Para obter etapas detalhadas, consulte [Adicionar ou remover nós a um cluster de autônomo tecidos de serviço](service-fabric-cluster-windows-server-add-remove-nodes.md) .


## <a name="remove-a-cluster"></a>Remover um cluster

Para remover um cluster, execute o script do PowerShell *RemoveServiceFabricCluster.ps1* da pasta de pacote e passar no caminho para o arquivo de configuração de JSON. Opcionalmente, você pode especificar um local para o log da exclusão.

Esse script pode ser executado em qualquer computador que tenha acesso de administrador para todas as máquinas que são listados como nós no arquivo de configuração de cluster. Máquina que esse script é executado no não precisa ser parte do cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>
## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dados de telemetria coletados e como deixar a ele

Como padrão, o produto coleta telemetria sobre o uso do serviço tecidos para aprimorar o produto. O analisador de prática recomendada que executa como parte das verificações de configuração para conectividade a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se não estiver acessível, a instalação falha a menos que você deixar de telemetria.

  1. O pipeline de telemetria tenta carregar os dados a seguir para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) uma vez diariamente. É um carregamento de esforço e não tem nenhum impacto sobre a funcionalidade de cluster. A telemetria é enviada apenas a partir do nó que executa o failover manager principal. Não há outros nós enviam telemetria.

  2. A telemetria consiste no seguinte:

-            Número de serviços
-            Número de ServiceTypes
-            Número de aplicativos
-            Número de ApplicationUpgrades
-            Número de FailoverUnits
-            Número de InBuildFailoverUnits
-            Número de UnhealthyFailoverUnits
-            Número de réplicas
-            Número de InBuildReplicas
-            Número de StandByReplicas
-            Número de OfflineReplicas
-            CommonQueueLength
-            QueryQueueLength
-            FailoverUnitQueueLength
-            CommitQueueLength
-            Número de nós
-            IsContextComplete: Verdadeiro/falso
-            ClusterId: Este é um GUID gerado aleatoriamente para cada cluster
-            ServiceFabricVersion
-             Endereço IP da máquina virtual ou da máquina a partir da qual a telemetria é carregada


Para desabilitar telemetria, adicione o seguinte ao *Propriedades* no cluster config: *enableTelemetry: false*.

<a id="previewfeatures"></a>
## <a name="preview-features-included-in-this-package"></a>Recursos de visualização incluídos neste pacote

Nenhuma.

>[AZURE.NOTE] Com o novo [versão GA do cluster autônomo para o Windows Server (versão 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), você pode atualizar seu cluster para lançamentos futuros, manual ou automaticamente. Porque este recurso não está disponível nas versões de visualização, você precisará criar um cluster usando a versão de GA e migrar seus dados e aplicativos do cluster preview. Fique atento para obter mais detalhes sobre esse recurso.


## <a name="next-steps"></a>Próximas etapas
- [Definições de configuração de cluster do Windows autônoma](service-fabric-cluster-manifest.md)
- [Adicionar ou remover nós para um cluster de estrutura de serviço autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Criar um cluster de estrutura de serviço autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Proteger um cluster de autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster de autônomo no Windows usando X509 certificados](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
