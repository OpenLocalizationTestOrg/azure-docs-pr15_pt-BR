<properties
    pageTitle="Script de desenvolvimento de ação com HDInsight | Microsoft Azure"
    description="Saiba como personalizar Hadoop clusters com ação de Script. Ação de script pode ser usada para instalar o software adicional em execução em um cluster de Hadoop ou para alterar a configuração de aplicativos instalados em um cluster."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Desenvolver scripts de ação de Script para clusters baseados no Windows de HDInsight

Saiba como escrever scripts de ação de Script para HDInsight. Para obter informações sobre como usar scripts de ação de Script, consulte [Personalizar HDInsight clusters usando a ação de Script](hdinsight-hadoop-customize-cluster.md). Para o mesmo artigo escrito para clusters baseados em Linux HDInsight, consulte [scripts de ação de Script desenvolver para HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] As informações neste documento são específicas para clusters HDInsight baseados no Windows. Para obter informações sobre como usar ações de script com clusters baseados no Windows, consulte [desenvolvimento de ação de Script com HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Ação de script pode ser usada para instalar o software adicional em execução em um cluster de Hadoop ou para alterar a configuração de aplicativos instalados em um cluster. Ações de script são scripts executados em nós do cluster quando HDInsight clusters são implantados, e eles são executados quando nós no cluster concluir a configuração de HDInsight. Uma ação de script é executada em privilégios de conta de administrador do sistema e fornece direitos de acesso completo a nós do cluster. Cada cluster pode ser fornecido com uma lista de ações de script a serem executadas na ordem em que eles são especificados.

> [AZURE.NOTE] Se você tiver a seguinte mensagem de erro:
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> É porque você não incluir os métodos de auxiliar.  Consulte [métodos de auxiliar de scripts personalizados](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Exemplos de scripts

Para criar clusters de HDInsight no sistema operacional Windows, a ação de Script é script do PowerShell do Azure. A seguir é um exemplo de script para configurar os arquivos de configuração do site:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

O script leva quatro parâmetros, o nome do arquivo de configuração, a propriedade que você deseja modificar, o valor que você deseja definir, e uma descrição. Por exemplo:

    hive-site.xml hive.metastore.client.socket.timeout 90

Esses parâmetros definirá o valor de hive.metastore.client.socket.timeout como 90 no arquivo site.xml de seção.  O valor padrão é 60 segundos.

Neste exemplo de script também pode ser encontrado no [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight fornece vários scripts para instalar componentes adicionais em clusters de HDInsight:

Nome | Script
----- | -----
**Instalar Spark** | https://hdiconfigactions.blob.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Consulte [instalação e uso ramo em clusters de HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalação e uso R em clusters de HDInsight][hdinsight-r-scripts].
**Instalar o Solr** | https://hdiconfigactions.blob.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Consulte [instalação e uso Solr em HDInsight clusters](hdinsight-hadoop-solr-install.md).
- **Instalar o Giraph** | https://hdiconfigactions.blob.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Consulte [instalação e uso Giraph em HDInsight clusters](hdinsight-hadoop-giraph-install.md).

Ação de script pode ser implantada em portal do Azure, Azure PowerShell ou usando o SDK do .NET HDInsight.  Para obter mais informações, consulte [clusters de personalizar HDInsight usando a ação de Script][hdinsight-cluster-customize].

> [AZURE.NOTE] Os scripts de exemplo funcionam apenas com a versão de cluster HDInsight 3.1 ou acima. Para obter mais informações sobre versões de cluster HDInsight, consulte [versões de cluster HDInsight](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Métodos de auxiliar de scripts personalizados

Métodos de auxiliar de ação de script são utilitários que você pode usar ao gravar scripts personalizados. Estes são definidos no [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)e podem ser incluídos em seus scripts usando o seguinte:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Aqui estão os métodos de auxiliar que são fornecidos por este script:

Método auxiliar | Descrição
-------------- | -----------
**Salvar HDIFile** | Baixe um arquivo do especificado URI Uniform Resource Identifier () para um local no disco local que está associado com o nó de máquina virtual do Azure atribuído ao cluster.
**HDIZippedFile expandir** | Descompacte o arquivo compactado.
**HDICmdScript invocar** | Execute um script cmd.exe.
**HDILog de gravação** | Gravar a saída do script personalizado usado para uma ação de script.
**Get-serviços** | Obter uma lista de serviços em execução no computador onde o script executa.
**Get-Service** | Com o nome de serviço específico como entrada, obter informações detalhadas para um serviço específico (nome do serviço, processar ID, estado, etc.) no computador onde o script executa.
**Get-HDIServices** | Obter uma lista de serviços de HDInsight em execução no computador onde o script executa.
**Get-HDIService** | Com o nome de serviço de HDInsight específico como entrada, obter informações detalhadas para um serviço específico (nome do serviço, processar ID, estado, etc.) no computador onde o script executa.
**Get-ServicesRunning** | Obter uma lista dos serviços que estejam em execução no computador onde o script executa.
**Get-ServiceRunning** | Verifique se um serviço específico (por nome) está em execução no computador onde o script executa.
**Get-HDIServicesRunning** | Obter uma lista de serviços de HDInsight em execução no computador onde o script executa.
**Get-HDIServiceRunning** | Verifique se um serviço de HDInsight específico (por nome) está em execução no computador onde o script executa.
**Get-HDIHadoopVersion** | Obter a versão do Hadoop instalada no computador onde o script executa.
**Teste-IsHDIHeadNode** | Verifique se o computador onde o script executa é um nó principal.
**Teste-IsActiveHDIHeadNode** | Verifique se o computador onde o script executa é um nó de cabeça ativo.
**Teste-IsHDIDataNode** | Verifique se o computador onde o script executa é um nó de dados.
**Editar HDIConfigFile** | Edite a seção-site.xml de arquivos de configuração, core-site.xml, hdfs site.xml, mapred-site.xml ou fio colorido-site.xml.


## <a name="best-practices-for-script-development"></a>Práticas recomendadas para desenvolvimento de script

Ao desenvolver um script personalizado para um cluster de HDInsight, há várias práticas recomendadas para ter em mente:

- Verificar a versão do Hadoop

    Somente HDInsight versão 3.1 (Hadoop 2,4) e acima usando a ação de Script instalar componentes personalizados em um cluster de suporte. No script personalizado, você deve usar o método de auxiliar **Get-HDIHadoopVersion** para verificar a versão do Hadoop antes de prosseguir com a execução de outras tarefas no script.


- Fornecer estável links para recursos de script

    Usuários deverão garantir que todos os scripts e outros artefatos usados na personalização de um cluster permanecem disponíveis em todo o ciclo de vida do cluster e que as versões desses arquivos não forem alterados para a duração. Esses recursos são necessários se as imagens novamente de nós no cluster é necessária. A prática recomendada é fazer o download e arquivar tudo em uma conta de armazenamento que controla o usuário. Isso pode ser a conta de armazenamento padrão ou qualquer uma das contas de armazenamento adicionais especificadas no momento de implantação de um cluster personalizado.
    Na Spark e R personalizados amostras de cluster fornecidos na documentação, por exemplo, fizemos uma cópia local dos recursos nesta conta de armazenamento: https://hdiconfigactions.blob.core.windows.net/.


- Certifique-se de que o script de personalização de cluster é idempotente

    Você deve esperar que os nós de um cluster de HDInsight será criados novamente durante o ciclo de vida do cluster. O script de personalização de cluster é executado sempre que um cluster é criado novamente. Esse script deve ser projetado para ser idempotentes no sentido que, durante a geração de novas imagens, o script deve garantir que o cluster é retornado para o mesmo personalizou o estado em que se encontrava, logo após o script foi executado pela primeira vez em que o cluster inicialmente foi criado. Por exemplo, se um script personalizado instalado um aplicativo em D:\AppLocation no seu primeiro executar, em cada execução subsequente, durante a geração de novas imagens, o script deve verificar se o aplicativo existe no local D:\AppLocation antes de prosseguir com outras etapas no script.


- Instalar componentes personalizados no local ideal

    Quando nós de cluster são criadas novamente, a unidade de recurso C:\ e a unidade de sistema D:\ podem ser reformatados, resultando em perda de dados e aplicativos que estavam instalados essas unidades. Isso também pode acontecer se um nó Azure virtuais de máquina que faz parte do cluster desce e é substituído por um novo nó. Você pode instalar componentes na unidade D:\ ou no local c:\Apps. em cluster. Todos os outros locais na unidade C:\ são reservados. Especifique o local onde aplicativos ou bibliotecas devem ser instalados no script de personalização de cluster.


- Garantir alta disponibilidade da arquitetura de cluster

    HDInsight tem uma arquitetura de ativo-passivo para alta disponibilidade, em que um nó principal está em modo ativo (onde os serviços de HDInsight estão executando) e o outro nó cabeça está no modo de espera (no qual HDInsight services está não executando). Os nós alternar modos de ativos e passivos se os serviços de HDInsight são interrompidos. Se uma ação de script é usada para instalar os serviços em ambos os nós cabeça para alta disponibilidade, observe que o mecanismo de failover HDInsight não poderão automaticamente falha esses serviços instalados pelo usuário. Portanto instalados pelo usuário serviços em nós de cabeça HDInsight que são esperados ser altamente disponível devem ou têm seu próprio mecanismo de failover se estiver no modo de ativo-passivo ou estar no modo de ativa.

    Um comando de ação de Script HDInsight é executado em ambos os nós cabeça quando a função de cabeça-node é especificada como um valor no parâmetro *ClusterRoleCollection* . Então quando você cria um script personalizado, certifique-se de que seu script esteja ciente dessa configuração. Você não deve enfrentar problemas onde os mesmos serviços estão instalados e iniciados em ambos os nós cabeça e eles acabem que competem uns com os outros. Além disso, esteja ciente de que dados serão perdidos durante a geração de novas imagens, para que o software instalado por meio de ação de Script deve ser flexível tais eventos. Aplicativos devem ser projetados para trabalhar com dados altamente disponível distribuída entre muitos nós. Observe que até 1/5 de nós em um cluster pode ser reproduzida novamente ao mesmo tempo.


- Configurar os componentes personalizados para usar o armazenamento de Blob do Azure

    Os componentes personalizados instalados em nós de cluster podem ter uma configuração padrão para usar o armazenamento de sistema de arquivo distribuído da Hadoop (HDFS). Você deve alterar a configuração para usar o armazenamento de Blob do Azure em vez disso. Em uma cluster recriar a imagem, o sistema de arquivos HDFS obtém formatado e você pode perder os dados armazenados nesse local. Em vez disso, usando o armazenamento de Blob do Azure garante que os seus dados serão mantidos.

## <a name="common-usage-patterns"></a>Padrões de uso comuns

Esta seção fornece orientação sobre como implementar alguns dos padrões de uso comuns que você pode se deparar ao escrever seu próprio script personalizado.

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Muitas vezes no desenvolvimento de ação de script, você irá se sentir a necessidade de definir variáveis de ambiente. Por exemplo, um cenário mais provável é quando você baixar um binário de um site externo, instalá-lo em cluster e adicione o local de onde ele está instalado para a variável de ambiente 'Caminho'. O trecho a seguir mostra como definir variáveis de ambiente no script personalizado.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Esta política define a variável de ambiente **MDS_RUNNER_CUSTOM_CLUSTER** para o valor 'true' e também define o escopo dessa variável seja máquina. Às vezes é importante que variáveis de ambiente estão definidas no escopo apropriado – máquina ou usuário. Consulte [aqui] [ 1] para obter mais informações sobre como definir variáveis de ambiente.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde são armazenados os scripts personalizados

Scripts usados para personalizar um cluster precisam como estar a conta de armazenamento padrão para o cluster ou em um contêiner de somente leitura público em qualquer outra conta de armazenamento. Se o script acessa recursos localizados em outro lugar esses precisam estar em um publicamente acessível (pelo menos público somente leitura). Por exemplo, convém acessar um arquivo e salvá-lo usando o comando SaveFile-HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Neste exemplo, você deve garantir que o contêiner 'somecontainer' na conta de armazenamento 'somestorageaccount' é acessível publicamente. Caso contrário, o script irá lançar uma exceção 'Não encontrado' e falhar.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Passar parâmetros para o cmdlet Add-AzureRmHDInsightScriptAction

Para passar vários parâmetros para o cmdlet Add-AzureRmHDInsightScriptAction, você precisa formatar o valor de cadeia de caracteres para conter todos os parâmetros para o script. Por exemplo:

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

ou

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Lançar exceção para implantação de cluster falhou

Se quiser ser notificado com precisão do fato de personalização de cluster não conseguiu conforme esperado, é importante lançar uma exceção e falhar a criação de cluster. Por exemplo, talvez você queira processar um arquivo se existir e manipular o caso de erro onde o arquivo não existe. Isso garante que o script saírem normalmente e o estado do cluster é conhecido corretamente. O trecho a seguir fornece um exemplo de como fazer isso:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Neste trecho, se o arquivo não existir, ele levaria a um estado onde o script realmente sai normalmente após a mensagem de erro de impressão e cluster atinge o estado de execução presumindo que ele concluído o processo de personalização de cluster "com êxito". Se você deseja ser notificado com precisão do fato de que cluster personalização essencialmente não conseguiu conforme o esperado devido a um arquivo ausente, é mais apropriado lançar uma exceção e falham na etapa de personalização de cluster. Para fazer isso, você deve usar o seguinte trecho de código de amostra em vez disso.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista de verificação para implantar uma ação de script
Aqui estão as etapas que tivemos ao se preparar para implantar esses scripts:

1. Coloque os arquivos que contêm os scripts personalizados em um local que possa ser acessado por nós de cluster durante a implantação. Isso pode ser qualquer uma das contas adicionais de armazenamento especificadas no momento de implantação de cluster ou qualquer outro contêiner de armazenamento acessíveis publicamente ou padrão.
2. Adicione verificações em scripts para garantir que eles executar idempotently, para que o script pode ser executado várias vezes no mesmo nó.
3. Use o cmdlet do PowerShell do Azure de **Write-Output** para imprimir STDOUT, bem como STDERR. Não use **Write-Host**.
4. Usar uma pasta de arquivos temporários, como $env: TEMP, para manter o arquivo baixado usado pelos scripts e, em seguida, limpá-los depois de tem executado scripts.
5. Instale o software personalizado somente em D:\ ou c:\Apps.. Outros locais na unidade c: não devem ser usados como eles estão reservados. Observe que instalar arquivos na unidade c: fora da pasta c:\Apps. pode resultar em falhas de instalação durante novamente imagens do nó.
6. Que configurações no nível do sistema operacional ou arquivos de configuração de serviço do Hadoop foram alterados, você talvez queira reiniciar serviços de HDInsight para que ela podem atende quaisquer configurações de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts.

## <a name="debug-custom-scripts"></a>Depurar scripts personalizados

Os logs de erros de script são armazenados, juntamente com outra saída, na conta de armazenamento padrão que você especificou para o cluster em sua criação. Os logs estão armazenados em uma tabela com o nome *u < \cluster-name-fragment >< \time-stamp > setuplog*. Estas são agregados logs que têm registros de todos os nós (nó principal e nós de operador) no qual o script é executado no cluster.
Uma maneira fácil de verificar os logs é usar as ferramentas de HDInsight para Visual Studio. Para instalar as ferramentas, consulte [Introdução ao uso de ferramentas do Visual Studio Hadoop para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Para verificar o log usando o Visual Studio**

1. Abra o Visual Studio.
2. Clique em **Exibir**e, em seguida, clique em **Server Explorer**.
3. "Azure" de atalho, clique em conectar à **Assinaturas do Microsoft Azure**e insira as suas credenciais.
4. Expanda **armazenamento**, expanda a conta de armazenamento do Azure usada como o sistema de arquivo padrão, expanda **tabelas**e clique duas vezes no nome da tabela.


Você pode também remoto em nós de cluster para ver a ambos STDOUT e STDERR de scripts personalizados. Os logs em cada nó são específicos apenas para esse nó e estiver conectados ao **C:\HDInsightLogs\DeploymentAgent.log**. Esses arquivos de log registram todas as saídas do script personalizado. Um trecho de log de exemplo para uma ação de script Spark tem esta aparência:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Neste log, é claro que a ação de script Spark foi executada na VM chamado HEADNODE0 e que nenhuma exceção foi lançada durante a execução.

Se ocorrer uma falha em execução, a saída descrevendo-serão também contida esse arquivo de log. As informações fornecidas nesses logs devem ser úteis na depuração de problemas de script que podem surgir.


## <a name="see-also"></a>Consulte também

- [Personalizar clusters HDInsight usando a ação de Script][hdinsight-cluster-customize]
- [Instalar e usar Spark em clusters de HDInsight][hdinsight-install-spark]
- [Instalar e usar R em clusters de HDInsight][hdinsight-r-scripts]
- [Instalar e usar clusters Solr em HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar e usar clusters Giraph em HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
