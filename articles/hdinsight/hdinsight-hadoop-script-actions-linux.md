<properties
    pageTitle="Script de desenvolvimento de ação com baseado em Linux HDInsight | Microsoft Azure"
    description="Como personalizar baseados em Linux HDInsight clusters com ação de Script. Ações de script são uma maneira de personalizar Azurehdinsight clusters especificando configurações de cluster ou instalando serviços adicionais, ferramentas ou outro software no cluster. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com HDInsight

Ações de script são uma maneira de personalizar Azurehdinsight clusters especificando configurações de cluster ou instalando serviços adicionais, ferramentas ou outro software no cluster. Você pode usar ações de script durante a criação de cluster ou em um cluster em execução.

> [AZURE.NOTE] As informações neste documento são específicas para clusters HDInsight baseados em Linux. Para obter informações sobre como usar ações de script com clusters baseados no Windows, consulte [desenvolvimento de ação de Script com HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>Quais são as ações de script?

Ações de script são scripts de Bash que Azure é executado em nós do cluster para fazer alterações de configuração ou instalar o software. Uma ação de script é executada como raiz e fornece direitos de acesso completo a nós do cluster.

Ações de script podem ser aplicadas através dos seguintes métodos:

| Use essa opção para aplicar um script... | Durante a criação do cluster... | Em um cluster em execução... |
| ----- |:-----:|:-----:|
| Portal do Azure | ✓ | ✓ |
| PowerShell Azure | ✓ | ✓ |
| CLI Azure | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Modelo do Gerenciador de recursos do Azure | ✓ | &nbsp; |

Para obter mais informações sobre como usar esses métodos para aplicar ações de script, consulte [Personalizar HDInsight clusters usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Práticas recomendadas para desenvolvimento de script

Ao desenvolver um script personalizado para um cluster de HDInsight, há várias práticas recomendadas para ter em mente:

- [A versão do Hadoop de destino](#bPS1)
- [A versão do sistema operacional de destino](#bps10)
- [Fornecer estável links para recursos de script](#bPS2)
- [Usar recursos previamente compilados](#bPS4)
- [Certifique-se de que o script de personalização de cluster é idempotente](#bPS3)
- [Garantir alta disponibilidade da arquitetura de cluster](#bPS5)
- [Configurar os componentes personalizados para usar o armazenamento de Blob do Azure](#bPS6)
- [Gravar informações STDOUT e STDERR](#bPS7)
- [Salvar arquivos como ASCII com finais de linha LF](#bps8)
- [Usar a lógica de repetição para recuperar de erros temporárias](#bps9)

> [AZURE.IMPORTANT] Ações de script devem ser concluídas em 60 minutos ou que eles serão tempo limite. Durante a configuração de nó, o script é executado simultaneamente com outros processos de instalação e configuração. Concorrência para recursos como largura de banda hora ou rede CPU pode causar o script para levar mais tempo para concluir do que no seu ambiente de desenvolvimento.

### <a name="bPS1"></a>A versão do Hadoop de destino

As diferentes versões do HDInsight têm versões diferentes do Hadoop serviços e componentes instalados. Se seu script espera uma versão específica de um serviço ou componente, você só deve usar o script com a versão do HDInsight que inclua os componentes necessários. Você pode encontrar informações sobre versões de componente incluído no HDInsight usando o documento de [controle de versão de componente HDInsight](hdinsight-component-versioning.md) .

###<a name="bps10"></a>A versão do sistema operacional de destino

HDInsight baseados em Linux baseia-se na distribuição Ubuntu Linux. As diferentes versões do HDInsight dependem de diferentes versões do Ubuntu, que pode afetar como se comporta seu script. Por exemplo, HDInsight 3.4 e versões anterior são baseados em versões de Ubuntu que usam Upstart. Versão 3.5 baseia-se no Ubuntu 16.04, que usa d. VínculosD e Upstart dependem comandos diferentes, para que o script deve ser escrito para trabalhar com ambas.

Outra diferença importante entre HDInsight 3.4 e 3.5 é que `JAVA_HOME` agora aponta para Java 8.

Você pode verificar a versão do sistema operacional usando `lsb_release`. Os seguintes trechos de código do script de instalação matiz demonstra como determinar se o script está sendo executado no Ubuntu 14 ou 16:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

Você pode encontrar o script completo que contenha esses trechos em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu que é usado pelo HDInsight, consulte o documento de [versão do componente de HDInsight](hdinsight-component-versioning.md) .

Para compreender as diferenças entre d e Upstart, consulte [VínculosD para usuários Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer estável links para recursos de script

Você deverá garantir que os scripts e recursos usados pelo script permanecem disponíveis em todo o ciclo de vida do cluster e que as versões desses arquivos não altere para a duração. Esses recursos são necessários se novos nós são adicionados ao cluster durante operações de dimensionamento.

A prática recomendada é fazer o download e arquivar tudo em uma conta de armazenamento do Azure em sua assinatura.

> [AZURE.IMPORTANT] A conta de armazenamento usada deve ser a conta de armazenamento de padrão para cluster ou um contêiner de público, somente leitura em qualquer outra conta de armazenamento.

Por exemplo, as amostras fornecidas pela Microsoft são armazenadas na conta de armazenamento de [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) , que é um contêiner de público, somente leitura mantido pela equipe de HDInsight.

### <a name="bPS4"></a>Usar recursos previamente compilados

Para reduzir o tempo necessário para executar o script, evite operações que compilar recursos de código-fonte. Em vez disso, pré-compilar os recursos e armazenar a versão binária no armazenamento de Blob do Azure para que rapidamente pode ser baixado ao cluster do seu script.

### <a name="bPS3"></a>Certifique-se de que o script de personalização de cluster é idempotente

Scripts devem ser projetados para ser idempotentes no sentido de que se o script for executado várias vezes, ele deve garantir que o cluster é retornado para o mesmo estado sempre que ele é executado.

Por exemplo, se um script personalizado instala um aplicativo em /usr/local/bin em seu primeiro executar, em cada execução subsequente o script deve verificar se o aplicativo já existe no local /usr/local/bin antes de prosseguir com outras etapas no script.

### <a name="bPS5"></a>Garantir alta disponibilidade da arquitetura de cluster

Clusters de HDInsight baseados em Linux fornece dois nós de cabeça que estão ativos dentro do cluster e script ações são executados para ambos os nós. Se os componentes que instalar esperam apenas um nó principal, você deve criar um script que só instala o componente em um dos dois nós cabeça no cluster.

> [AZURE.IMPORTANT] Serviços de padrão instalados como parte do HDInsight foram projetados para alternar entre os dois nós de cabeça conforme necessário, no entanto, essa funcionalidade não é estendida para componentes personalizados instalados através de script ctions. Se você precisar os componentes instalados por meio de uma ação de script ser altamente disponível, você deve implementar seu próprio mecanismo de failover que usa os dois nós de cabeça disponíveis.

### <a name="bPS6"></a>Configurar os componentes personalizados para usar o armazenamento de Blob do Azure

Componentes que você instala em cluster podem ter uma configuração padrão que usa o armazenamento de sistema de arquivo distribuído da Hadoop (HDFS). HDInsight usa o armazenamento de Blob do Azure (WASB) como o armazenamento padrão. Isso fornece um sistema de arquivos compatíveis HDFS que persiste dados mesmo que o cluster é excluído. Você deve configurar componentes instalar para usar WASB em vez de HDFS.

Por exemplo, o seguinte copia o arquivo de giraph examples.jar de sistema de arquivos local para WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Gravar informações STDOUT e STDERR

Informações gravadas STDOUT e STDERR durante a execução de scripts estão conectadas e podem ser visualizadas usando web Ambari da interface do usuário.

> [AZURE.NOTE] Ambari só estará disponível se o cluster é criado com êxito. Se você usar uma ação de script durante a criação de cluster e criação falhar, consulte a seção de solução de problemas [que hdinsight personalizar clusters usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para outras maneiras de acessar informações registradas.

A maioria dos utilitários e pacotes de instalação já gravará informações STDOUT e STDERR, no entanto, talvez você queira adicionar adicionais de log. Para enviar texto para uso STDOUT `echo`. Por exemplo:

        echo "Getting ready to install Foo"

Por padrão, `echo` enviará a cadeia de caracteres para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo:

        >&2 echo "An error occurred installing Foo"

Isso redireciona as informações enviadas para STDOUT (1, que é padrão, então não listado aqui,) para STDERR (2). Para obter mais informações sobre redirecionamento de IO, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obter mais informações sobre como visualizar informações registradas por ações de script, consulte [clusters de personalizar HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Salvar arquivos como ASCII com finais de linha LF

Bash scripts devem ser armazenados como formato ASCII, com linhas terminadas por LF. Se os arquivos são armazenados como UTF-8, que pode incluir uma marca de ordem de Byte no início do arquivo, ou com finais de linha de CRLF, que é comum para editores do Windows, o script falhará com erros similares ao seguinte:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Usar a lógica de repetição para recuperar de erros temporárias

Quando o download de arquivos, instalar pacotes usando chance-get ou outras ações que transmitem dados pela internet, a ação pode falhar devido a erros de rede temporárias. Por exemplo, o recurso remoto que você está se comunicando pode estar no processo de deixar através de um nó de backup.

Para fazer o script e apresentam resiliência erros temporárias, você pode implementar lógica de repetição. A seguir é um exemplo de uma função que executará qualquer comando passado para ele e (se o comando falhar,) Repetir até três vezes. Ela esperará dois segundos entre cada tentativa.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

A seguir é exemplos de como usar esta função.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Métodos de auxiliar de scripts personalizados

métodos de auxiliar de ação de script são utilitários que você pode usar ao gravar scripts personalizados. Estes são definidos no [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)e podem ser incluídos em seus scripts usando o seguinte:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Isso disponibiliza os seguintes auxiliares para uso no script:

| Uso do auxiliar | Descrição |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Baixa um arquivo do URL de origem para o caminho de arquivo especificado. Por padrão, ele não substituirá um arquivo existente. |
| `untar_file TARFILE DESTDIR` | Extrai um arquivo tar (usando `-xf`,) à pasta de destino. |
| `test_is_headnode` | Se tiver executado em um nó de cabeça cluster, retorna 1; Caso contrário, 0. |
| `test_is_datanode` | Se o nó atual for um nó de dados (trabalhador), retorna 1; Caso contrário, 0. |
| `test_is_first_datanode` | Se o nó atual seja o primeiro nó de dados (trabalhador) (chamado workernode0), retorna 1; Caso contrário, 0. |
| `get_headnodes` | Retorna o nome de domínio totalmente qualificado do headnodes em cluster. Nomes são delimitado por vírgulas. Uma cadeia de caracteres vazia é retornada quando ocorre um erro. |
| `get_primary_headnode` | Obtém o nome de domínio totalmente qualificado do headnode principal. Uma cadeia de caracteres vazia é retornada quando ocorre um erro. |
| `get_secondary_headnode` | Obtém o nome de domínio totalmente qualificado do headnode secundário. Uma cadeia de caracteres vazia é retornada quando ocorre um erro. |
| `get_primary_headnode_number` | Obtém o sufixo numérico do headnode principal. Uma cadeia de caracteres vazia é retornada quando ocorre um erro. |
| `get_secondary_headnode_number` | Obtém o sufixo numérico da headnode secundário. Uma cadeia de caracteres vazia é retornada quando ocorre um erro. |

## <a name="commonusage"></a>Padrões de uso comuns

Esta seção fornece orientação sobre como implementar alguns dos padrões de uso comuns que você pode se deparar ao escrever seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passando parâmetros para um script

Em alguns casos, o script pode exigir parâmetros. Por exemplo, você pode precisa a senha de administrador para o cluster para recuperar informações da API REST Ambari.

Parâmetros passados para o script são conhecidos como _parâmetros posicionais_e atribuídos a `$1` para o primeiro parâmetro, `$2` para o segundo e portanto-on. `$0`contém o nome do próprio script.

Valores passados para o script como parâmetros devem estar entre aspas simples (') para que o valor passado será tratado como um literal e tratamento especial não está atribuído a caracteres incluídos como '!'.

### <a name="setting-environment-variables"></a>Definir variáveis de ambiente

Definir uma variável de ambiente é realizada pelo seguinte:

    VARIABLENAME=value

Onde os NOMEDAVARIÁVEL é o nome da variável. Para acessar a variável depois de fazer isso, use `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente denominada senha, você usaria o seguinte:

    PASSWORD=$1

Subsequente acesso às informações poderia usar `$PASSWORD`.

Variáveis de ambiente definidas dentro do script só existirem dentro do escopo do script. Em alguns casos, talvez você precise adicionar variáveis de ambiente ampla de sistema que serão mantidas após o script terminou. Normalmente, isso é para que os usuários se conectarem ao cluster via SSH podem usar os componentes instalados pelo seu script. Você pode fazer isso adicionando a variável de ambiente para `/etc/environment`. Por exemplo, o seguinte adiciona __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde são armazenados os scripts personalizados

Scripts usados para personalizar um cluster precisam ao ser na conta de armazenamento padrão para o cluster ou, se em outra conta de armazenamento, em um contêiner de somente leitura público. Se o script acessa recursos localizados em outro lugar elas também precisam estar em acessível publicamente (pelo menos público somente leitura). Por exemplo, você talvez queira baixar um arquivo para o cluster usando `download_file`.

Armazenar o arquivo em uma conta de armazenamento do Azure acessível pelo cluster (como a conta de armazenamento padrão), fornecerá acesso rápido, como o armazenamento está dentro da rede Azure.

### <a name="checking-the-operating-system-version"></a>Verificar a versão do sistema operacional

As diferentes versões do HDInsight confiam em versões específicas do Ubuntu. Pode haver diferenças entre as versões de sistema operacional que você precisa verificar no script. Por exemplo, talvez você precise instalar um binário que está vinculado à versão do Ubuntu.

Para verificar a versão do sistema operacional, use `lsb_release`. Por exemplo, o seguinte demonstra como fazer referência a um arquivo de tar diferentes dependendo da versão do sistema operacional:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Lista de verificação para implantar uma ação de script

Aqui estão as etapas que tivemos ao se preparar para implantar esses scripts:

- Coloque os arquivos que contêm os scripts personalizados em um local que possa ser acessado por nós de cluster durante a implantação. Isso pode ser qualquer uma das contas adicionais de armazenamento especificadas no momento de implantação de cluster ou qualquer outro contêiner de armazenamento acessíveis publicamente ou padrão.

- Adicione verificações em scripts para garantir que eles executar impotently, para que o script pode ser executado várias vezes no mesmo nó.

- Use um /tmp do diretório de arquivos temporários para manter os arquivos baixados usados pelos scripts e, em seguida, limpá-los depois de tem executado scripts.

- Que configurações no nível do sistema operacional ou arquivos de configuração de serviço do Hadoop foram alterados, você talvez queira reiniciar serviços de HDInsight para que ela podem atende quaisquer configurações de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts.

## <a name="runScriptAction"></a>Como executar uma ação de script

Você pode usar ações de script para personalizar HDInsight clusters usando o portal do Azure, PowerShell do Azure, modelos do Azure Resource Manager (ARM) ou o SDK do .NET HDInsight. Para obter instruções, consulte [como usar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes em um cluster de HDInsight. Scripts de exemplo e instruções sobre como usá-las estão disponíveis nos links abaixo:

- [Instalar e usar o matiz em clusters de HDInsight](hdinsight-hadoop-hue-linux.md)
- [Instalar e usar R em clusters de HDInsight Hadoop](hdinsight-hadoop-r-scripts-linux.md)
- [Instalar e usar Solr em clusters de HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalar e usar Giraph em clusters de HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] Os documentos vinculados acima são específicos para clusters HDInsight baseados em Linux. Para um scripts que funcionam com HDInsight baseados no Windows, consulte [desenvolvimento de ação de Script com HDInsight (Windows)](hdinsight-hadoop-script-actions.md) ou use os links disponíveis na parte superior de cada artigo.

##<a name="troubleshooting"></a>Solução de problemas

Estes são os erros que você pode encontrar quando estiver usando scripts desenvolvido:

__Error__: `$'\r': command not found`. Às vezes, seguido por `syntax error: unexpected end of file`.

_Causa_: esse erro é causado quando as linhas em um script terminam com CRLF. Sistemas UNIX esperam apenas LF como o final da linha.

Esse problema ocorre com mais frequência quando o script é criado em um ambiente do Windows, como CRLF é uma linha comum terminando para muitos editores de texto no Windows.

_Resolução_: se for uma opção no editor de texto, selecione o formato Unix ou LF para o final da linha. Você também pode usar os seguintes comandos em um sistema Unix para alterar o CRLF para um LF:

> [AZURE.NOTE] Os seguintes comandos são equivalentes em que eles devem alterar extremidades de linha CRLF para LF. Selecione uma baseia os utilitários disponíveis no seu sistema.

| Comando | Anotações |
| ------- | ----- |
| `unix2dos -b INFILE` | O arquivo original será feito backup com um. Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE conterá uma versão com apenas finais de LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Isto irá modificar o arquivo diretamente sem criar um novo arquivo |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | OUTFILE conterá uma versão com finais de LF apenas.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Causa_: esse erro ocorre quando o script foi salva como UTF-8 com uma marca de ordem de bytes (BOM).

_Resolução_: salvar o arquivo como ASCII ou como UTF-8 sem um BOM. Você também pode usar o seguinte comando em um sistema Linux ou Unix para criar um novo arquivo sem BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Para o comando acima, substitua __INFILE__ o arquivo que contém a BOM. __OUTFILE__ deve ser um novo nome de arquivo, que conterá o script sem BOM.

## <a name="seeAlso"></a>Próximas etapas

* Saiba como [Personalizar HDInsight clusters usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)

* Usar o [SDK do .NET HDInsight Referência](https://msdn.microsoft.com/library/mt271028.aspx) para saber mais sobre a criação de aplicativos .NET que gerenciar HDInsight

* Use a [API REST de HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a usar o restante para executar ações de gerenciamento em clusters de HDInsight.
