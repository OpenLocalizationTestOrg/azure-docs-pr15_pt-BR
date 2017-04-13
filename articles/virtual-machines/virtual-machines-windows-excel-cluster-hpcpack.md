<properties
 pageTitle="Cluster de HPC Pack para Excel e SOA | Microsoft Azure"
 description="Introdução a execução grande escala cargas de trabalho do Excel e SOA em um cluster de HPC Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Introdução a execução cargas de trabalho do Excel e SOA em um cluster de HPC Pack no Azure

Este artigo mostra como implantar um cluster Microsoft HPC Pack em Azure máquinas virtuais usando um modelo de início rápido Azure ou opcionalmente um script de implantação do PowerShell do Azure. O cluster usa imagens de máquina virtual do Azure Marketplace projetadas para executar o Microsoft Excel ou cargas de trabalho de arquitetura orientada a serviços (SOA) com HPC Pack. Você pode usar o cluster para executar HPC simples do Excel e serviços SOA de um computador de cliente local. Os serviços do Excel HPC incluem transferindo de pasta de trabalho do Excel e Excel funções definidas pelo usuário ou UDFs.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Em um alto nível, o diagrama a seguir mostra o cluster HPC Pack que você criar.

![Cluster HPC conosco executando cargas de trabalho do Excel][scenario]

## <a name="prerequisites"></a>Pré-requisitos

*   **Computador cliente** - você precisa de um computador cliente baseado no Windows para enviar trabalhos de Excel e SOA de amostra ao cluster. Você também precisa de um computador com Windows para executar o script de implantação do Azure PowerShell cluster (se você escolher esse método de implantação) e

*   **Assinatura do azure** - se você não tiver uma assinatura do Azure, você pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

*   **Cota de cores** - talvez você precise aumentar a cota de cores, especialmente se você implantar vários nós de cluster com tamanhos de máquina virtual multinucleados. Se você estiver usando um modelo de início rápido Azure, a cota de cores no Gerenciador de recursos é por região Azure. Nesse caso, talvez seja necessário aumentar a cota de uma região específica. Consulte [limites de assinatura Azure, cotas e restrições](../azure-subscription-service-limits.md). Aumentar uma cota, [Abra uma solicitação de suporte online do cliente](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sem nenhum custo.

*   **Licença do Microsoft Office** - se você implantar nós de computação usando uma imagem de máquina virtual do Marketplace HPC Pack com o Microsoft Excel, uma versão de avaliação de 30 dias do Microsoft Excel Professional Plus 2013 está instalado. Após o período de avaliação, você precisa fornecer uma licença válida do Microsoft Office para ativar o Excel para continuar a executar cargas de trabalho. Consulte [ativação do Excel](#excel-activation) mais adiante neste artigo. 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Etapa 1. Configurar um cluster de HPC Pack no Azure

Vamos mostrar duas opções para configurar o cluster: primeiro, usando um modelo de início rápido Azure e o portal do Azure; e segundo, usando um script de implantação do PowerShell do Azure.


### <a name="option-1-use-a-quickstart-template"></a>Opção 1. Usar um modelo de início rápido
Use um modelo de início rápido Azure para rapidamente e facilmente implantar um cluster de HPC Pack no portal do Azure. Quando você abre o modelo no portal do, você recebe uma interface de usuário simple, onde você digitar as configurações para o seu cluster. Aqui estão as etapas. 

>[AZURE.TIP]Se desejar, use um [modelo do Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que cria um cluster semelhante especificamente para cargas de trabalho do Excel. As etapas diferem ligeiramente a seguir.

1.  Visite a [página de modelo de criar HPC Cluster no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Se desejar, analise informações sobre o modelo e o código-fonte.

2.  Clique em **implantar no Azure** para iniciar uma implantação com o modelo no portal do Azure.

    ![Implantar o modelo para o Azure][github]

3.  No portal do, siga estas etapas para inserir os parâmetros para o modelo de cluster HPC.

    a. Na página de **parâmetros** , insira ou modifique os valores para os parâmetros de modelo. (Clique no ícone ao lado de cada configuração para obter informações de Ajuda.) Valores de exemplo são mostrados na tela a seguir. Este exemplo cria um cluster chamado *hpc01* no domínio *hpc.local* consiste de um nó principal e nós de computadores de 2. Os nós de computação são criados a partir de uma imagem de máquina virtual do HPC Pack que inclui o Microsoft Excel.

    ![Insira parâmetros][parameters]

    >[AZURE.NOTE]O nó principal máquina virtual é criada automaticamente da [imagem Marketplace mais recente](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) do HPC Pack 2012 R2 no Windows Server 2012 R2. Atualmente, a imagem se baseia em HPC Pack 2012 R2 atualização 3.
    >
    >Calcule que nó VMs são criadas a partir a imagem mais recente da família de nó computação selecionado. Selecione a opção de **ComputeNodeWithExcel** para a imagem de nó de computação HPC Pack mais recente que inclui uma versão de avaliação do Microsoft Excel Professional Plus 2013. Para implantar um cluster para sessões SOA gerais ou Excel UDF transferindo, escolha a opção de **ComputeNode** (sem o Excel instalado).

    b. Escolha a assinatura.

    c. Crie um grupo de recursos para o cluster, como *hpc01RG*.

    d. Escolha um local para o grupo de recursos, como o centro dos EUA.

    e. Na página **termos legais** , examine os termos. Se você concordar, clique em **comprar**. Quando tiver terminado de definir os valores para o modelo, clique em **criar**.

4.  Quando a implantação for concluída (normalmente leva cerca de 30 minutos), exportar o arquivo de certificado de cluster de nó principal cluster. Em uma etapa posterior, você importar este certificado público no computador cliente para fornecer a autenticação de servidor para vinculação de HTTP segura.

    a. Conecte-se para o nó principal pela área de trabalho remota do portal do Azure.

     ![Conectar-se para o nó principal][connect]

    b. Use procedimentos padrão no Gerenciador de certificados para exportar o certificado de nó principal (localizado em certificado: \LocalMachine\My) sem a chave particular. Neste exemplo, exportar *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exporte o certificado][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opção 2. Usar o script de implantação do HPC Pack IaaS

O script de implantação de HPC Pack IaaS fornece outra maneira versátil para implantar um cluster de HPC Pack. Ela cria um cluster no modelo clássico de implantação, enquanto o modelo usa o modelo de implantação do Gerenciador de recursos do Azure. Além disso, o script é compatível com uma assinatura no serviço do Azure globais ou Azure China.

**Pré-requisitos adicionais**

* **PowerShell Azure** - [instalar e configurar o Azure PowerShell](../powershell-install-configure.md) (versão 0.8.10 ou posterior) no computador cliente.

* **Script de implantação de HPC Pack IaaS** - Baixe e descompacte a versão mais recente do script a partir do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verificar a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.5.0 ou posterior do script.

**Criar o arquivo de configuração**

 O script de implantação de HPC Pack IaaS usa um arquivo de configuração de XML como entrada que descreva a infraestrutura do cluster HPC. Para implantar um cluster que consiste de um nó principal e 18 nós de computação criados a partir da imagem de nó de computação que inclui o Microsoft Excel, substitua valores para seu ambiente no arquivo de configuração de exemplo seguinte. Para obter mais informações sobre o arquivo de configuração, consulte o arquivo Manual.rtf na pasta de script e [criar um cluster de HPC com o script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Observações sobre o arquivo de configuração**

* O **VMName** do nó principal **deve** ser igual a **ServiceName**ou trabalhos SOA falham na execução.

* Verifique se que você especificar **EnableWebPortal** para que o certificado de nó principal é gerado e exportado.

* O arquivo Especifica um script do PowerShell pós-configuração PostConfig.ps1 que é executado no nó principal. O script de exemplo a seguir configura a cadeia de conexão de armazenamento do Azure, remove a função de nó de computação o nó principal e reúne todos os nós online quando eles são implantados. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Execute o script**

1.  Abra o console do PowerShell no computador cliente como administrador.

2.  Altere o diretório para a pasta de script (E:\IaaSClusterScript neste exemplo).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Para implantar o cluster HPC Pack, execute o seguinte comando. Este exemplo pressupõe que o arquivo de configuração está localizado em E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

O script de implantação de HPC Pack é executado por algum tempo. Uma coisa que o script faz é exportar e baixe o certificado de cluster e salvá-lo na pasta de documentos do usuário atual no computador cliente. O script gera uma mensagem semelhante à seguinte. Uma etapa seguinte, você importa o certificado no repositório de certificados apropriado.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Etapa 2. Transferir pastas de trabalho do Excel e executar UDFs de um cliente local

### <a name="excel-activation"></a>Ativação do Excel

Ao usar a imagem ComputeNodeWithExcel VM para cargas de trabalho de produção, você precisa fornecer uma chave de licença válida do Microsoft Office para ativar o Excel em nós de computação. Caso contrário, a versão de avaliação do Excel expira após 30 dias e executar as pastas de trabalho do Excel falhará com COMException (0x800AC472). 

Você poderá rearmar o Excel para outro 30 dias de tempo de avaliação: faça logon no nó principal e clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` em todo o Excel calcular nós via HPC Cluster Manager. Você poderá rearmar um máximo de duas vezes. Depois disso, você deve fornecer uma chave de licença válida do Office.

O Office Professional Plus 2013 instalado na imagem da máquina virtual é uma edição de volume com um genérico Volume licença chave (GVLK). Você pode ativá-lo através do serviço de gerenciamento de chaves (KMS) / ativação de Active Directory-Based (AD-BA) ou a chave de ativação múltipla (MAK). 

    * Para usar o KMS/AD-BA, usar um servidor KMS existente ou configurar um novo usando o pacote de licença de Volume do Microsoft Office 2013. (Se você quiser, configure o servidor no nó principal.) Em seguida, ative a chave de host KMS pela Internet ou por telefone. Em seguida, clusrun `ospp.vbs` para definir o servidor KMS e a porta e ativar o Office em todo o Excel nós de computadores. 
    
    * Usar MAK, primeiro clusrun `ospp.vbs` a chave de entrada e, em seguida, ativar todo o Excel calcular nós via Internet ou por telefone. 

>[AZURE.NOTE]Chaves de produto de varejo do Office Professional Plus 2013 não podem ser usadas com esta imagem de máquina virtual. Se você tiver chaves válidas e mídia de instalação para as edições do Office ou o Excel diferente nesta edição de volume do Office Professional Plus 2013, você pode usá-los em vez disso. Primeiro desinstalar esta edição de volume e instale a edição que você possui. O nó de computação reinstalado do Excel pode ser capturado como uma imagem de máquina virtual personalizada para usar em uma implantação em escala.

### <a name="offload-excel-workbooks"></a>Descarregar pastas de trabalho do Excel

Siga estas etapas para transferir uma pasta de trabalho do Excel para que seja executada no cluster HPC Pack no Azure. Para fazer isso, você deve ter o Excel 2010 ou 2013 já estiverem instalados no computador cliente.

1. Use uma das opções na etapa 1 para implantar um cluster de HPC Pack com o Excel calcular imagem nó. Obtenha o arquivo de certificado de cluster (. cer) e o nome de usuário de cluster e a senha.

2. No computador cliente, importe o certificado de cluster em certificado: \CurrentUser\Root.

3. Verifique se o que Excel está instalado. Crie um arquivo de Excel.exe.config com o seguinte conteúdo na mesma pasta como Excel.exe no computador cliente. Esta etapa garante que o HPC Pack 2012 R2 Excel suplemento carrega com êxito.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Configure o cliente para enviar trabalhos ao cluster HPC Pack. Uma opção é a completa [HPC Pack 2012 R2 atualização 3 instalação](http://www.microsoft.com/download/details.aspx?id=49922) de baixar e instalar o cliente de HPC Pack. Como alternativa, baixe e instale os [utilitários de cliente HPC Pack 2012 R2 atualização 3](https://www.microsoft.com/download/details.aspx?id=49923) e o apropriado Visual C++ 2010 redistribuível para seu computador ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  Neste exemplo, usamos uma pasta de trabalho do Excel de exemplo denominada ConvertiblePricing_Complete.xlsb. É possível baixá-lo [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copie a pasta de trabalho do Excel para uma pasta de trabalho como D:\Excel\Run.

7.  Abra a pasta de trabalho do Excel. Na faixa de **desenvolver** , clique em **Suplementos de COM** e confirme que a HPC Pack Excel suplemento é carregado com êxito.

    ![Suplemento para HPC Pack do Excel][addin]

8.  Edite a macro do VBA HPCControlMacros no Excel alterando os comentários das linhas, conforme mostrado no script a seguir. Substitua os valores adequados para seu ambiente.

    ![Macro do Excel para HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Copie a pasta de trabalho do Excel para um diretório de carregamento como D:\Excel\Upload. Este diretório especificado na constante de HPC_DependsFiles na macro VBA.

10. Para executar a pasta de trabalho no cluster no Azure, clique no botão de **Cluster** na planilha.

### <a name="run-excel-udfs"></a>Executar UDFs do Excel

Para executar o Excel UDFs, siga as etapas anteriores 1 a 3 para configurar o computador cliente. Para UDFs do Excel, você não precisa ter o aplicativo Excel instalado em nós de computação. Então, quando criar seu cluster nós de computadores, você poderá escolher um normal calcular a imagem de nó em vez da imagem de nó de computação com o Excel.

>[AZURE.NOTE] Não há um limite de 34 caracteres no Excel 2010 e a caixa de diálogo de conector de cluster de 2013. Você pode usar esta caixa de diálogo para especificar o cluster que executa os UDFs. Se o nome do cluster completo for maior (por exemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com), ela não cabe na caixa de diálogo. A solução alternativa é definir uma variável de máquina como *CCP_IAASHN* com o valor do nome do cluster longa. Em seguida, digite *% CCP_IAASHN %* na caixa de diálogo como o nome de nó principal do cluster. 

Após o cluster é implantado com êxito, continue com as etapas a seguir para executar uma amostra interna UDF do Excel. Para UDFs personalizadas do Excel, consulte estes [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para criar os XLLs e implantá-los no cluster IaaS.

1.  Abra uma nova pasta de trabalho do Excel. Na faixa de **desenvolver** , clique em **Suplementos**. Em seguida, na caixa de diálogo, clique em **Procurar**, navegue até a pasta %CCP_HOME%Bin\XLL32 e selecione a amostra de ClusterUDF32.xll. Se o ClusterUDF32 não existir no computador cliente, copie-o da pasta %CCP_HOME%Bin\XLL32 no nó principal.

    ![Selecione o UDF][udf]

2.  Clique em **arquivo** > **Opções** > **avançadas**. Em **fórmulas**, marque **Permitir funções definidas pelo usuário XLL para executar um cluster de cálculo**. Em seguida, clique em **Opções** e digite o nome completo cluster no **nome de nó principal do Cluster**. (Como observado anteriormente essa caixa de entrada está limitada aos 34 caracteres, para que um nome de cluster longo poderá não caber. Você pode usar uma variável de máquina aqui um nome de cluster longo.)

    ![Configurar o UDF][options]

3.  Para executar o cálculo de UDF no cluster, clique na célula com valor =XllGetComputerNameC() e pressione Enter. A função simplesmente recupera o nome do nó computação no qual o UDF é executado. Para a primeira execução, uma caixa de diálogo credenciais solicita o nome de usuário e senha para se conectar ao cluster IaaS.

    ![Executar UDF][run]

    Quando houver várias células para calcular, pressione Alt-Shift-Ctrl + F9 para executar o cálculo em todas as células.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Etapa 3. Executar uma carga de trabalho SOA de um cliente local

Para executar aplicativos SOA geral no cluster HPC Pack IaaS, use um dos métodos na etapa 1 para implantar o cluster. Especifica um genérico calcular imagem nó nesse caso, porque você não precisa Excel em nós de computação. Em seguida, siga estas etapas.

1. Depois de recuperar o certificado de cluster, importá-lo no computador cliente em certificado: \CurrentUser\Root.

2. Instale o [SDK do HPC Pack 2012 R2 atualização 3](http://www.microsoft.com/download/details.aspx?id=49921) e [utilitários de cliente HPC Pack 2012 R2 atualização 3](https://www.microsoft.com/download/details.aspx?id=49923). Essas ferramentas permitem que você desenvolver e executar aplicativos de cliente SOA.

3. Baixe o HelloWorldR2 [código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633). Abra o HelloWorldR2.sln no Visual Studio 2010 ou 2012.

4. Crie o projeto EchoService primeiro. Em seguida, implante o serviço ao cluster IaaS da mesma maneira que você implanta em um cluster de local. Para obter etapas detalhadas, consulte o Leiame no HelloWordR2. Modificar e criar a HellWorldR2 e outros projetos, conforme descrito na seção a seguir para gerar os aplicativos de cliente SOA que são executadas em um cluster de IaaS do Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Usar vinculação Http com fila do armazenamento do Azure

Para usar vinculação Http com uma fila de armazenamento do Azure, fazer algumas alterações no código de amostra.

* Atualize o nome de cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Opcionalmente, use o padrão TransportScheme em SessionStartInfo ou explicitamente definido como Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Use vinculação de padrão para o BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Ou explicitamente usando basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Opcionalmente, defina o sinalizador UseAzureQueue como verdadeiro na SessionStartInfo. Se não estiver definida, ela será definida como true por padrão quando o nome do cluster tem sufixos de domínio Azure e o TransportScheme é Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Usar vinculação de Http sem fila do armazenamento do Azure

Para usar a ligação de Http sem uma fila do armazenamento do Azure, explicitamente definir o sinalizador UseAzureQueue para false na SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Usar ligação NetTcp

Para usar NetTcp vinculação, a configuração é semelhante ao se conectar a um cluster de local. Você precisa abrir alguns pontos de extremidade no nó principal máquina virtual. Se você usou o script de implantação de HPC Pack IaaS para criar o cluster, por exemplo, defina os pontos de extremidade no portal de clássico do Azure da seguinte maneira.


1. Pare a máquina virtual.

2. Adicione as portas TCP 9090, 9087, 9091, 9094 para a sessão, atuar como intermediário, atuar como intermediário de trabalho e serviços de dados, respectivamente

    ![Configurar pontos de extremidade][endpoint]

3. Inicie a máquina virtual.

O aplicativo de cliente SOA não requer alterações, exceto alterar o nome de cabeça para o nome completo do IaaS cluster.

## <a name="next-steps"></a>Próximas etapas

* Consulte [estes recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para obter mais informações sobre como executar cargas de trabalho do Excel com HPC Pack.

* Consulte [Gerenciar serviços SOA no Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) para obter mais informações sobre como implantar e gerenciar serviços SOA com HPC Pack.

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
