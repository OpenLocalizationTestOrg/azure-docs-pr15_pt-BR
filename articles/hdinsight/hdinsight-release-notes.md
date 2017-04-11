<properties
    pageTitle="Notas de versão para componentes de Hadoop em Azurehdinsight | Microsoft Azure"
    description="Notas da versão mais recentes e versões de componentes de Hadoop para Azurehdinsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, tempestade Apache e HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de versão para componentes de Hadoop no Azure HDInsight

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Notas de versão do 26/10/2016 do servidor de R em HDInsight

- Servidor de R em HDInsight cluster provisionamento foi otimizada.
- Servidor de R em HDInsight agora está disponível como HDInsight regular "R servidor" tipo de cluster e não está mais instalado como um aplicativo separado do HDInsight. O nó de borda e binários do servidor de R agora são provisionados como parte da implantação de cluster de servidores de R. Isso melhora a velocidade e confiabilidade de provisionamento. Modelo de preços para servidor de R é atualizado.
- Preço de tipo de cluster de servidor de R baseia-se agora em preço acessível padrão além de preço de sobrecarga de servidor de R. Nível de Premium agora será reservada para os recursos Premium disponíveis em todos os tipos de cluster diferentes e não será usada para tipo de cluster de servidor de R. Essa alteração não afeta a preços eficaz do servidor de R, ele apenas altera a maneira como os encargos são apresentados na fatura. Todos os clusters de servidor de R existentes continuarão a funcionar e modelos ARM continuarão a funcionar até o aviso de substituição. **É recomendável embora para atualizar as implantações de script para usar o novo modelo ARM.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Notas de versão de 30/08/2016 do servidor de R em HDInsight

Os números de versão completa para clusters baseados em Linux HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP   |Construir Ambari |
|----|----------------------|----|------------|-------------|
|3,2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2,4 |2.4.2.4-5   |2.2.1.12-4   |

Os números de versão completa para clusters baseados no Windows HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Notas de versão do 17/08/2016 do servidor de R em HDInsight

- Servidor R 8.0.5 – principalmente uma versão de correção de bug. Consulte as [Notas de versão do servidor de R](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) para obter mais informações. 
- Pacote de AzureML no nó borda – [Este pacote R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) permite modelos de R ser publicado e consumidas como um serviço da web do Azure ML.  Consulte a seção ["colocar em operação um modelo"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) nosso artigo ["Visão geral de R Server em HDInsight"](hdinsight-hadoop-r-server-overview.md) para obter mais informações.
- Dependências de Linux dos [100 superior mais populares pacotes de R](https://github.com/metacran/cranlogs) – estes Linux dependências de pacote agora são pré-instalados.  
- Opção para usar o repo CRAN quando adicionar R pacotes para os nós de dados. Consulte a seção ["Pacotes de instalação R"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) nosso artigo ["Começar a usar o servidor de R em HDInsight"](hdinsight-hadoop-r-server-get-started.md) para obter mais informações.
- Aperfeiçoada a confiabilidade de provisionamento de servidor de R quando clusters são criados.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Notas de versão do 01/08/2016 do HDInsight

Os números de versão completa para clusters baseados em Linux HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP   |Construir Ambari |
|----|----------------------|----|------------|-------------|
|3,2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2,4 |2.4.2.4-5   |2.2.1.12-4   |

Os números de versão completa para clusters baseados no Windows HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo Spark, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Alterações para HDInsight 3.4 clusters | O valor padrão para as seguintes configurações de seção são alteradas para obter melhor desempenho <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Serviço    | Todos os| N/D|
| Estas correções estão incluídas nesta versão | SEÇÃO-13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Serviço    | Todos os| N/D

## <a name="notes-for-07142016-release-of-hdinsight"></a>Notas de versão de 14/07/2016 do HDInsight

Os números de versão completa para clusters baseados em Linux HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP   |Construir Ambari |
|----|----------------------|----|------------|-------------|
|3,2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2,4 |2.4.2.0     |2.2.1.12-2   |

Os números de versão completa para clusters baseados no Windows HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Notas de versão do 07/07/2016 do HDInsight

Os números de versão completa para clusters baseados em Linux HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP   |
|----|----------------------|----|------------|
|3,2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2,4 |2.4.2.0     |

Os números de versão completa para clusters baseados no Windows HDInsight implantados com esta versão:

|HDI |Versão de cluster HDI   |HDP |Construir HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3,2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo Spark, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Ferramentas de HDInsight para IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Plug-in IntelliJ IDEIA para HDInsight Spark clusters agora está integrada com o Kit de ferramentas do Azure para IntelliJ. Ele oferece suporte v2.9.1 SDK do Azure, SDKs Java mais recente e inclui todos os recursos de autônomo HDInsight Plugin para IntelliJ.| Ferramentas    | Spark| N/D|
| [Ferramentas de HDInsight para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Kit de ferramentas de Azure para Eclipse agora dá suporte a HDInsight Spark clusters. Ele permite que os recursos a seguir. <ul><li>Criar e gravar um aplicativo Spark facilmente em Scala e Java com primeira classe criação de suporte para o IntelliSense, formatação automática, verificação de erros, etc.</li><li>Teste o aplicativo Spark localmente.</li><li>Enviar trabalhos para cluster HDInsight Spark e recuperar os resultados.</li><li>Faça logon em Azure e acessar todos os clusters Spark associados a suas assinaturas Azure.</li><li>Navegue todos os recursos de armazenamento associado do cluster HDInsight Spark.</li></ul>| Ferramentas    | Spark| N/D

Começando com esta versão, alteramos a política de patches de sistema operacional convidado para clusters HDInsight baseados em Linux. O objetivo da nova política é reduzir significativamente o número de reinicialização devido a correção. A nova política continuará patch VMs (máquinas virtuais) em clusters Linux cada segunda-feira ou quinta começando em 12: 00 UTC de maneira irregular em nós em qualquer determinado cluster. No entanto, qualquer determinada VM só será reiniciado no máximo uma vez a cada 30 dias, devido a patches de sistema operacional de convidado. Além disso, a primeira reinicialização para um cluster recém-criado não acontecerá mais cedo do que 30 dias da data de criação de cluster.

>[AZURE.NOTE] Essas alterações somente se aplicarão a clusters recém-criado iguais ou maiores que esta versão de lançamento.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Notas de versão do 06/06/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

|HDP    |Versão HDI    |Versão Spark  |Número de compilação de Ambari    |Número de compilação HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2,4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo Spark, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Spark em HDInsight geralmente está disponível | Esta versão apresenta melhorias na disponibilidade, escalabilidade e produtividade para abrir fonte Spark Apache em HDInsight. <ul><li>Líder disponibilidade SLA de 99,9%, que torna mais adequado para exigindo cargas de trabalho do enterprise no setor.</li><li>Camada de armazenamento escaláveis usando o armazenamento de Lucerne de dados do Azure.</li><li>Ferramentas de produtividade para todas as fases de desenvolvimento e exploração de dados. Blocos de anotações de Jupyter com núcleo de Spark personalizado habilitar exploração de dados interativas, integração com painéis de BI como Power BI, Tableau e Qlik é bom para compartilhamento de dados rápidos e relatórios contínuo, plug-in IntelliJ é uma opção confiável para desenvolvimento de artefato de código de longo prazo e depuração.</li></ul>| Serviço    | Spark| N/D|
| Ferramentas de HDInsight para IntelliJ | Este é um plug-in IntelliJ IDEIA para HDInsight Spark clusters. Ele permite que os recursos a seguir.<ul><li>Criar e gravar um aplicativo Spark facilmente em Scala e Java com primeira classe criação de suporte para o IntelliSense, formatação automática, verificação de erros, etc.</li><li>Teste o aplicativo Spark localmente.</li><li>Enviar trabalhos para cluster HDInsight Spark e recuperar os resultados.</li><li>Faça logon em Azure e acessar todos os clusters Spark associados a suas assinaturas Azure.</li><li>Navegue todos os recursos de armazenamento associado do cluster HDInsight Spark.</li><li>Navegue todas as informações de trabalho para o seu cluster HDInsight Spark e histórico de trabalhos.</li><li>Depure trabalhos Spark remotamente a partir de seu computador desktop.</li></ul>| Ferramentas    | Spark| N/D

## <a name="notes-for-05132016-release-of-hdinsight"></a>Notas de versão do 05/13/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo de cluster (por exemplo Spark, Hadoop, HBase ou tempestade) | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Gerar atualização de versão e outras correções de bugs | Esta versão atualiza a versão de Spark em cluster HDInsight para 1.6.1 e correções de outros bugs| Serviço    | Spark| N/D

## <a name="notes-for-04112016-release-of-hdinsight"></a>Notas de versão do 04/11/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-inalterado)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problemas de atualização de metastore personalizada para HDI 3.4 | Criação de cluster falhará se você usou um metastore personalizado, que era usado em uma versão inferior de outro cluster HDInsight. Isso era devido a um erro de atualização de script agora foi corrigido| Criação de cluster    | Todos os | N/D
| Recuperação de falhas Livy | Fornece resiliência de status de trabalho para qualquer trabalho enviado por meio de Livy | Confiabilidade | Spark no Linux| N/D
| Conteúdo de Jupyter HA | Fornece a capacidade de salvar e carregar o conteúdo do bloco de anotações de Jupyter de e para a conta de armazenamento associado ao cluster. Para obter mais informações, consulte [Kernels disponíveis para blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Blocos de anotações | Spark no Linux| N/D
| Remoção de hiveContext em blocos de anotações de Jupter | Use `%%sql` mágico em vez de `%%hive` mágico. SqlContext é equivalente a hiveContext. Para obter mais informações, consulte [Kernels disponíveis para blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Blocos de anotações    | Clusters Spark no Linux| N/D
| Substituição de versões mais antigas do Spark | Versão mais antiga Spark 1.3.1 será removido do serviço em 5/31 | Serviço | Spark clusters no Windows | N/D

## <a name="notes-for-03292016-release-of-hdinsight"></a>Notas de versão do 29/03/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - inalterado)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - inalterado)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - inalterado)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Adicionado HDInsight 3.4 atualizado HDP versões e para todos os clusters de HDInsight | Com esta versão, podemos adicionou v3.4 HDInsight (baseado em 2,4 HDP) e também atualizados outras versões HDP. Notas de versão de 2,4 HDP estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) e mais informações sobre HDInsight versões podem ser encontradas [aqui](hdinsight-component-versioning.md).| Serviço    | Todos os clusters Linux| N/D
| HDInsight Premium | HDInsight agora está disponível em duas categorias - Standard e Premium. HDInsight Premium está no modo de visualização e disponível apenas para Hadoop e Spark clusters no Linux. Para obter mais informações, consulte [aqui](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Serviço    | Hadoop e Spark no Linux| N/D
| Servidor Microsoft R | HDInsight Premium fornece Microsoft R Server que pode ser incluído em clusters Hadoop e Spark no Linux. Para obter mais informações, consulte [Visão geral de servidor de R em HDInsight](hdinsight-hadoop-r-server-overview.md).| Serviço    | Hadoop e Spark no Linux| N/D
| Spark 1.6.0 | HDInsight 3.4 clusters agora incluem Spark 1.6.0| Serviço    | Clusters Spark no Linux| N/D
| Aprimoramentos de bloco de anotações de Jupyter | Blocos de anotações de Jupyter disponíveis com clusters Spark agora fornecem Spark adicional kernels. Eles também incluem aprimoramentos como o uso de % % mágico, autovisualização e integração com bibliotecas de visualização de Python (como matplotlib). Para obter mais informações, consulte [Kernels disponíveis para blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Serviço | Clusters Spark no Linux | N/D

## <a name="notes-for-03222016-release-of-hdinsight"></a>Notas de versão de 22/03/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7 - inalterado)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8 - inalterado)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7 - inalterado)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todos os clusters de HDInsight | Com esta versão, podemos atualizou versões de HDInsight para todos os clusters de HDInsight| Serviço    | Todos os| N/D


## <a name="notes-for-03102016-release-of-hdinsight"></a>Notas de versão do 10/03/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5 - inalterado)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todos os clusters de HDInsight | Com esta versão, podemos atualizou versões de HDInsight para todos os clusters de HDInsight| Serviço    | Todos os| N/D

## <a name="notes-for-01272016-release-of-hdinsight"></a>Notas de versão de 27/01/2016 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5 - inalterado)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todos os clusters de HDInsight | Com esta versão, podemos atualizou versões de HDInsight para todos os clusters de HDInsight| Serviço    | Todos os| N/D

## <a name="notes-for-12022015-release-of-hdinsight"></a>Notas de versão de 12/02/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34 - inalterado)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34 - inalterado)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Adicionado HDInsight 3.3 atualizado HDP versões e para todos os clusters de HDInsight | Com esta versão, podemos adicionou v3.3 HDInsight (baseado em HDP 2.3) e também atualizados outras versões HDP. Notas de versão do HDP 2.3 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) e mais informações sobre HDInsight versões podem ser encontradas [aqui](hdinsight-component-versioning.md).| Serviço    | Todos os| N/D

## <a name="notes-for-11302015-release-of-hdinsight"></a>Notas de versão de 30/11/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todas as versões HDP para 3,2 HDInsight clusters (Windows e Linux) e clusters de HDInsight | Com esta versão, versões HDInsight e HDP tenham sido atualizadas | Serviço    | Todos os| N/D


## <a name="notes-for-10272015-release-of-hdinsight"></a>Notas de versão de 27/10/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374 - inalterado)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todos os clusters HDInsight (Windows e Linux) | Com esta versão, versões HDInsight e HDP tenham sido atualizadas | Serviço    | Todos os| N/D
| Clusters Jupyter para Windows Spark fixos com clusters de letras maiusculas | Clusters que tinham nomes DNS especificados em letras maiusculas tem problemas com blocos de anotações de Jupyter devido a uma seleção de solicitação de origem. A correção foi alterar o nome DNS para configuração do Jupyter para minúsculas. | Serviço    | HDInsight Spark (Windows)| N/D


## <a name="notes-for-10202015-release-of-hdinsight"></a>Notas de versão do 20/10/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versão HDP padrão alterada para HDP 2.2 | A versão padrão para clusters HDInsight Windows é alterada para HDP 2.2. HDInsight versão 3,2 (HDP 2.2) está disponível no mercado em desde fevereiro de 2015. Essa alteração somente inverte a versão de cluster padrão, quando uma seleção explícita não tenha sido feita enquanto o cluster usando o portal do Azure, cmdlets do PowerShell ou o SDK de provisionamento. | Serviço    | Todos os| N/D                  |
|Alterações no formato de nome de máquina virtual para implantar várias HDInsight no Linux clusters em uma única rede Virtual | Suporte para implantação de vários clusters HDInsight Linux em uma única rede virtual está sendo adicionado nesta versão. Como parte desse, o formato de nomes de máquina virtual em cluster foi alterado de headnode\*, workernode\* e zookeepernode\* para hn\*, wn\*e zk\* respectivamente. Não é uma prática recomendada para tirar uma dependência direta sobre o formato dos nomes de máquina virtual, pois isso está sujeita a alterações. Use "hostname -f" no computador local ou Ambari APIs para determinar a lista de hosts e o mapeamento de componentes para hosts. Você pode encontrar mais informações em [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) e [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Serviço | HDInsight clusters no Linux | N/D |
| Alterações de configuração | Para HDInsight 3.1 clusters, agora estão ativadas as seguintes configurações: <ul><li>tez.yarn.ATS.Enabled e yarn.log.server.url. Isso permite que o servidor de linha do tempo do aplicativo e o servidor de Log poder servir os logs.</li></ul>Para clusters de 3,2 HDInsight, as seguintes configurações foram modificadas: <ul><li>MapReduce.fileoutputcommitter.Algorithm.Version foi definida como 2. Isso permite o uso da versão V2 da FileOutputCommitter.</li></ul> | Serviço | Todos os | N/D |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Notas de versão do 09/09/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334 - inalterado)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012 - inalterado)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todos os clusters de HDInsight | Com esta versão, versões de HDInsight foram atualizadas | Serviço    | Todos os| N/D                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas de versão de 31/07/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334 - inalterado)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012 - inalterado)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Corrigir Spark fluxo de trabalho de recriação de imagens de nó de cluster | Corrigido um bug que estava causando Spark nós de cluster não recuperar após recriar a imagem | Serviço    | Spark| N/D                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Notas de versão de 31/07/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334 - inalterado)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012 - inalterado)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões de HDInsight atualizadas para todos os clusters de HDInsight | Com esta versão, versões de HDInsight foram atualizadas | Serviço    | Todos os| N/D                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Notas de versão do 07/07/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334 - inalterado)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


Esta versão contém as seguintes atualizações.

| Título                                           | Descrição                                          | Área afetada (por exemplo, serviço, componente ou SDK) | Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster | JIRA (se aplicável) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versões HDP atualizadas para clusters de 3,2 HDInsight | Com esta versão, HDInsight 3,2 implanta HDP 2.2.6.1-0012 | Serviço    | Todos os                                                 | N/D                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Notas de versão do 26/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334 - inalterado)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Versões HDP atualizadas para clusters de 3,2 HDInsight</td>
<td>Com esta versão, HDInsight 3,2 implanta HDP 2.2.6.1</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Notas de versão de 18/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Portas HTTPS adicionais abertas</td>
<td>Agora, o serviço de nuvem é aberto a 5 portas 8001 para 8005 no cluster E.g. em https://<clustername>.azurehdinsight.net:8001/. Solicitações para esses URLs são autenticadas usando o mesmo mecanismo de senha de autenticação básica porta 443. Essas portas vincular a na mesma porta a headnode ativa. Ações de script podem ser usadas para fazer com que o atendimento ao cliente escutam nessas portas na headnode e rota para fora do cluster.</td>
<td>Serviço de nuvem</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Problema de ordem aleatória MapReduce intermitente para 3,2 HDInsight</td>
<td>Correção para uma condição de corrida raros, intermitente no mapa reduzir aleatória em grandes clusters, resultando em falhas de tarefas ocasionais. Consulte <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a> para obter mais informações.</td>
<td>Hadoop principais</td>
<td>Todos os</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE-6361</a></td>
</tr>

<tr>
<td>Mover para o mais recente Java Azure SDK 2.2 para HDInsight 3,2</td>
<td>Movido para a versão mais recente do SDK do Azure para Java usado pelo driver do WASB. O SDK mais recente tem algumas correções e as notas de versão para a mesma estão disponíveis em https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop principais</td>
<td>Todos os</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP-11959</a></td>
</tr>

<tr>
<td>Mover para HDP 2.1.15 para HDInsight 3.1 clusters</td>
<td>Notas de versão Hortonworks a versão estão disponíveis <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">aqui</a>.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Notas de versão do 04/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003 - inalterado)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Corrigir o erro 502 do gateway incorreto para clusters de tempestade</td>
<td>Esta versão corrige um erro que afetam o envio de trabalho API que causaram o site esteja pressionada após a reinicialização.</td>
<td>Serviço</td>
<td>Tempestade</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Notas de versão do 01/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003 - inalterado))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800 - inalterado)
* SDK 1.5.8


Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Várias correções de bugs</td>
<td>Esta versão corrige bugs relacionados ao provisionar o cluster.</td>
<td>Serviço</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Notas de versão de 27/05/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Outras versões do cluster e o SDK não são implantadas como parte nesta versão.


Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Atualização de HDP 2.2</td>
<td>Esta versão do HDInsight 3,2 contém HDP 2.2.6 e traz várias correções de bugs importantes ao HDInsight. As notas de versão completa está disponível em <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 notas de versão</a>.</td>
<td>HDP</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Alterar a configuração de memória do padrão fio colorido contêiner</td>
<td>Nesta atualização, a padrão memória disponível para contêineres de fio COLORIDO (yarn.nodemanager.resource.memory mb e yarn.scheduler.maximum-alocação-mb), iniciada pelo Gerenciador de nó, é aumentado para 5632MB. Anteriormente isso era reduzido para 4608MB, mas com base em várias execuções de trabalho, o novo valor deve oferecer melhor confiabilidade e desempenho a maioria dos trabalhos, portanto é uma melhor padrão. Como normal, se você um tem dependência crítica sobre esta configuração de memória, defina-explicitamente ao criar o cluster.</td>
<td>HDP</td>
<td>Todos os tipos de cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Paridade de configuração padrão para clusters HBase e tempestade</td>
<td>Essa atualização restaura clusters Hbase e tempestade para usar os mesmos valores de configurações de fio COLORIDO como Hadoop clusters. Isso é feito para paridade em todos os tipos de cluster.</td>
<td>HDP</td>
<td>HBase, tempestade</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Notas de versão do 20/05/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>SCP.NET EventHub suporte</td>
<td>Os pacotes de cluster atualizado para HDInsight tempestade trazem novos recursos para SCP.NET. Agora, você terá acesso às novas APIs no construtor de topologia que tornam mais fácil usar EventHubSpout ou Java Spouts. Você deve atualizar seu cliente SCP.NET SDK para trabalhar com novos clusters como os contratos tenham sido atualizados. Para obter detalhes sobre as novas APIs, uso e notas de versão (incluindo correções de bugs) consultem o arquivo Leiame incluído no pacote do nuget SCP.NET.</td>
<td>VS ferramentas</td>
<td>Tempestade HDInsight 3,2 clusters</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualização de driver JDBC</td>
<td>O driver atualizado para a versão do SQL Server com suporte no sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Todos os</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Notas de versão de 27/04/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329 - inalterado)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Corrigir dependência de DLL</td>
<td>Remove dependência de HDInsight na estrutura de teste de unidade.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correção de bug condição de corrida</td>
<td>Um cluster Criar solicitação agora aguarda na solicitação de colocar aceitos antes da pesquisa sobre o status</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Notas de versão de 14/04/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - inalterado)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Correções de bugs Tez</td>
<td>Correções para Apache TEZ 2214 e TEZ 1923 são incluídas nesta versão do HDI 3,2. Especificamente, elas são necessárias para certas consultas de seção em Tez que exigem a ordem aleatória uma grande quantidade de dados.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Notas de versão do 04/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795 - inalterado)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117 - inalterado)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329 - inalterado)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Atualizações para remover algumas classes internos para HDInsight no Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Biblioteca de Avro 1.5.6</td>
<td>Adicionado <b>KnownTypeAttribute</b> para método <b>GetAllKnownTypes</b>. NullReferenceException fixo quando um tipo é nulo para GetAllKnownTypes método.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correções de bugs</td>
<td>Várias correções de bugs no serviço</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Notas de versão do 04/01/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Capacidade de ativar/desativar credenciais de desktop remotas em clusters do Windows através do .NET SDK</td>
<td>Suporte para habilitar ou desabilitar credenciais RDP em clusters do Windows.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Capacidade de habilitar credenciais de desktop remotas em clusters enquanto eles estão sendo provisionados</td>
<td>Suporte para permitindo credenciais de desktop remotas conforme o cluster está sendo criado. Isso remove o processo de duas etapas para cluster de provisionamento primeiro e, em seguida, habilitar área de trabalho remota.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Python atualizado para 2.7.8</td>
<td>Python atualizado em Clusters de HDInsight para Python 2.7.8, que contém alguns segurança importante correções de HDInsight versões 2.1, 3.0, 3.1 e 3,2</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Alteração de configuração de fio COLORIDO</td>
<td>Alterados fio COLORIDO configuração yarn.resourcemanager.max concluída-aplicativos a 1000 para todos os tipos de cluster HDInsight versões 3.1 e 3,2. Esse valor controla somente a lista de aplicativos concluídos na interface de usuário fio COLORIDO. Para obter informações sobre os aplicativos que foram enviadas antes da lista de aplicativos mostradas na interface do usuário, você pode ir diretamente para o servidor de histórico.</td>
<td>FIO COLORIDO</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Redimensionamento de nós em um cluster de HBase</td>
<td>HBase clusters agora permitem o redimensionamento de nós (para cima e para baixo) para HDInsight versões 3.1 e 3,2</td>
<td>Serviço</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualização JDBC</td>
<td>Driver SQL JDBC é atualizado para a versão sqljdbc_4.0.2206.100 para HDInsight versão 3,2. Esta versão contém aprimoramentos de segurança importantes.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualização de configuração de JVM</td>
<td>Atualizado JVM configuração networkaddress.cache.ttl para 300 segundos do valor padrão de -1 para HDInsight versões 3.1 e 3,2. Esse valor de configuração controla a política de cache de pesquisas de nome bem-sucedida do serviço de nome. Isso corrige um bug relacionado ao aumentando e diminuindo HBase clusters.</td>
<td>Serviço</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualizar para o armazenamento do Azure SDK para Java 2.0</td>
<td>HDInsight versão 3,2 é atualizado para usar a versão mais recente do SDK de armazenamento do Azure para Java. Esta página contém várias correções de bugs importantes sobre o 0.6.0 atual versão.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualizado para o código-fonte Apache WASB</td>
<td>HDInsight versão 3,2 agora usa as últimas código para o driver de sistema de arquivos WASB do Hadoop Apache. Com essa alteração, o driver WASB agora é empacotado como um jar separado. Isso é puramente uma alteração de embalagem e não contém alterações de comportamento do driver WASB. O nome do arquivo JAR é hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>JAR atualizações de nome de arquivo no HDInsight 3,2</td>
<td>Essa atualização ao HDInsight versão 3,2 contém várias correções de bugs e alguns jars internos empacotados como parte do HDP tenham sido atualizados. Observe que esses arquivos JAR são internos ao pacote HDP e não para uso direto por aplicativos cliente. Aplicativos devem empacotar sua própria versão os jars para que uma atualização para os JARs internos HDP não quebrar aplicativos cliente.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Notas de versão do 03/03/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351 - inalterado)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097 - inalterado)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290 - inalterado)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - inalterado)
* SDK 1.5.0 (inalterada)

Esta versão contém a seguinte atualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA</th>
</tr>


<tr>
<td>Melhorias de confiabilidade</td>
<td>Fizemos correções que permitem que o serviço melhor escalabilidade com o aumento de carga relacionadas com a criação do cluster.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Notas de versão de 18/02/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351 - inalterado)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097 - inalterado)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290 - inalterado)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>HDInsight 3,2 clusters</td>
<td>Hadoop 2.6/HDP2.2 está disponível com clusters 3,2 HDInsight. Ele contém principais atualizações para todos os componentes de código-fonte aberto. Para obter mais detalhes, consulte Novidades do HDInsight e <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">notas de versão do HDP 2.2.0.0</a>.</td>
<td>Software de código-fonte aberto</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>HDinsight no Linux (prévia)</td>
<td>Clusters podem ser implantados em execução no Ubuntu Linux. Para obter mais detalhes, consulte Introdução ao HDInsight no Linux.</td>
<td>Serviço</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Disponibilidade geral de tempestade</td>
<td>Apache tempestade clusters estão geralmente disponíveis. Para obter mais detalhes, consulte Introdução usando tempestade em HDInsight.</td>
<td>Serviço</td>
<td>Tempestade</td>
<td>N/D</td>
</tr>

<tr>
<td>Tamanhos de máquina virtual</td>
<td>Azure HDInsight está disponível em mais tipos de máquina virtual e tamanhos. HDInsight pode utilizar A2 em tamanhos de A7 criados para fins gerais; Nós de série D que apresentam unidades de estado sólidas (SSDs) e 60 por cento processadores mais rápidos; e tamanhos A8 e A9 que têm InfiniBand suportam para rede rápida.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Dimensionamento de cluster</td>
<td>Você pode alterar o número de nós de dados para um cluster de HDInsight em execução sem precisar excluir ou recriá-la. Atualmente, somente tipos de cluster Hadoop consulta e Apache tempestade possuem essa capacidade, mas suporte para Apache HBase tipo de cluster é cedo para acompanhar. Para obter mais informações, consulte gerenciar HDInsight clusters.</td>
<td>Serviço</td>
<td>Hadoop, tempestade</td>
<td>N/D</td>
</tr>

<tr>
<td>Ferramentas do Visual Studio</td>
<td>Além das ferramentas concluída para Apache tempestade, as ferramentas para Apache seção no Visual Studio foi atualizada para incluir a conclusão da instrução, validação local e suporte aprimorado de depuração. Para obter mais informações, consulte Introdução ao HDInsight Hadoop ferramentas para Visual Studio.</td>
<td>Ferramentas</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Conector do Hadoop para DocumentDB</td>
<td>Com o conector de Hadoop para DocumentDB, você pode executar agregações complexas, análise e manipulação sobre seus documentos JSON sem esquema armazenados entre conjuntos de DocumentDB ou em contas de banco de dados. Para obter mais informações e um tutorial, consulte trabalhos de executar Hadoop usando DocumentDB e HDInsight.</td>
<td>Serviço</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correções de bugs</td>
<td>Fizemos várias correções de bugs secundários para serviços de HDInsight. Nenhuma alteração de comportamento de atendimento é esperadas.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Notas de versão de 02/06/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351 - inalterado)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097 - inalterado)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* SDK N/D

Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Correções de bugs</td>
<td>Fizemos várias correções de bugs secundários para serviços de HDInsight. Nenhuma alteração de comportamento de atendimento é esperadas.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualização de manutenção 2.1 HDP</td>
<td>HDInsight 3.1 é atualizado para implantar HDP 2.1.10.0. Para obter mais informações, consulte <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">notas HDP-2.1.10</a>. </td>
<td>Software de código-fonte aberto</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Atualizações de binário HDP</td>
<td>Há alguns arquivos JAR HBase para qual arquivo nomes tenham sido atualizados. Esses arquivos JAR são usados internamente pelo HBase, para que não é esperado que os clientes têm uma dependência nos nomes desses arquivos JAR. Eles incluem:
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-Util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Software de código-fonte aberto</td>
<td>HBase</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Notas de versão de 1/29/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351 - inalterado)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097 - inalterado)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196 - inalterado)
* SDK N/D

Esta versão contém a seguinte atualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada (por exemplo, serviço, componente ou SDK)</p></th>
<th>Tipo (por exemplo, Hadoop, HBase ou tempestade) de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>

<td>Correções de bugs</td>
<td>Fizemos algumas correções importantes que melhoram a confiabilidade dos Clusters HDInsight durante as atualizações do Azure.</td>
<td>Serviço</td>
<td>Todos os</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Notas de versão de 1/5/2015 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão:

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351 - inalterado)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097 - inalterado)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196 - inalterado)


Esta versão contém as seguintes atualizações.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Exemplos para análise de tendência de Twitter e recomendações de filme baseada em Mahout</td>
<td><p>Nesta versão, o console de consulta de HDInsight tem duas amostras adicionais:</p>

<p><b>Análise de tendência do Twitter</b><br>
APIs públicas fornecidos pelo sites como Twitter são uma fonte útil de dados para analisar e Noções básicas sobre tendências populares. Neste tutorial, Aprenda a usar seção para obter uma lista de usuários do Twitter que enviou as maioria dos tweets contendo uma determinada palavra. </p>

<p><b>Recomendação de filme mahout</b><br>
Apache Mahout é uma biblioteca de aprendizado de máquina de Apache Hadoop. Mahout contém algoritmos para processar dados (como filtragem, classificação e agrupamento). Neste tutorial, use um mecanismo de recomendação para gerar recomendações de filme com base em filmes que viu seus amigos.</p></td>
<td>Console de consulta</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Alterar o valor padrão para a seção configuração: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Esta configuração de tamanho se aplica ao mapa automaticamente convertidas junções. O valor representa a soma de tamanhos de tabelas que podem ser convertidos em mapas de hash que cabem na memória. Em uma versão anterior, esse valor aumentado do valor padrão de 10 MB para 128 MB. No entanto, o novo valor de 128 MB estava causando trabalhos falha devido a falta de memória. Esta versão reverte o valor padrão para 10 MB. Os clientes ainda podem optar por sobrescrever esse valor durante a criação de cluster, fornecidas suas consultas e tamanhos de tabela. Para obter mais informações sobre essa configuração e como substituí-lo, consulte <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Otimizar automático ingressar conversão</a> na documentação de Hortonworks. </p></td>
<td>Seção</td>
<td>Hadoop, Hbase</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Notas de versão do 23/12/2014 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão são:

* HDInsight 2.1.10.420.1246783 (versão HDP inalterado)
* HDInsight 3.0.6.420.1246783 (versão HDP inalterado)
* HDInsight 3.1.1.420.1246783 (versão HDP inalterado)

Esta versão contém a seguinte atualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Falhas de criação de cluster intermitente devido a carga excessiva</td>
<td><p>Algoritmo aprimorado para baixar pacotes HDP durante a criação de cluster permite mais robusta manipulação de falhas devido a carga excessiva.</p></td>
<td>Serviço</td>
<td>Hadoop, Hbase, tempestade</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Notas de versão de 18/12/2014 do HDInsight

Esta versão contém a seguinte atualização de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personalização de cluster Avalability geral</a></td>
<td><p>Personalização fornece a capacidade para você personalizar sua clusters Azurehdinsight com projetos que estão disponíveis no ecossistema do Apache Hadoop. Com este novo recurso, você pode experimentar e implantar projetos de Hadoop ao Azure HDInsight. Isso é ativado por meio do recurso de **Ação de Script** , que pode modificar Hadoop clusters maneiras aleatório usando scripts personalizados. Essa personalização está disponível em todos os tipos de HDInsight clusters incluindo Hadoop, HBase e tempestade. Para demonstrar o poder desse recurso, podemos ter documentadas o processo para instalar o populares <a href = "hdinsight-hadoop-spark-install.md" target="_blank">Spark</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>e <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> módulos. Esta versão também adiciona a capacidade para clientes especificar suas ações de script personalizado por meio do portal do Azure, fornece diretrizes e práticas recomendadas sobre como criar ações de script personalizado usando métodos de auxiliar e fornece diretrizes sobre como testar a ação de script. </p></td>
<td>Disponibilidade geral do recurso</td>
<td>Todos os</td>
<td>N/D</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Notas de versão do 05/12/2014 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão são:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK n/d

Esta versão contém as seguintes atualizações de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Correção de bug: erro intermitente ao adicionar um grande número de partições a uma tabela em uma seção DDL </td>
<td><p>Se houver um erro de conexão intermitente com o banco de dados de metastore de seção ao adicionar muitas partições a uma tabela de seção, o DDL seção pode falhar. A instrução a seguir aparecerá no log de erro de seção se esta falha ocorre: </p><p>"Erro [principais]: ql. Driver (SessionState.java:printError(547)) - falha: erro de execução, o código de retorno 1 de org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException: commitTransaction foi chamado mas openTransactionCalls = 0. Provavelmente, isso indica que há equilibradas chamadas para openTransaction/commitTransaction)"</p></td>
<td>Seção</td>
<td>Hadoop, Hbase</td>
<td>SEÇÃO-482 (Isso é uma JIRA interno, para que ele não pode ser orçado externamente. Notado aqui para referência.)</td>
</tr>

<tr>
<td>Correção de bug: travar eventual no Console de consulta de HDInsight</td>
<td>Quando isso acontece, a instrução a seguir pode ser vista no log de WebHCat para o trabalho de inicializador WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): não foi possível carregar o arquivo de histórico {wasb url para o arquivo de histórico} "</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>SEÇÃO-482 (Isso é uma JIRA interno, para que ele não pode ser orçado externamente. Notado aqui para referência.)</td>
</tr>

<tr>
<td>Correção de bug: eventual pico no latência das consultas de Hbase</td>
<td>Se isso acontecer, os usuários notará um aumento eventual de 3 segundos na latência das consultas de Hbase. </td>
<td>HDInsight Cluster Gateway</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Alterações de nome de arquivo JAR HDP</td>
<td>Para HDI cluster versão 3.0, há algumas alterações para os arquivos JAR internos instalados pelo HDP. jetty 6.1.26.jar foi substituído pelo jetty-6.1.26.hwx.jar. jetty-camada-6.1.26.jar foi substituído pelo jetty-camada-6.1.26.hwx.jar. Essas alterações se aplicam a projetos Hadoop, Mahout, WebHCat e Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Notas de versão do 21/11/2014 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão são:

* HDInsight 2.1.9.382.1169709 (nenhuma alteração de 14/11/2014)
* HDInsight 3.0.5.382.1169709 (nenhuma alteração de 14/11/2014)
* HDInsight 3.1.1.382.1169709 (nenhuma alteração de 14/11/2014)
* HDINsight SDK 1.4.0

Esta versão contém as seguintes atualizações de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Ao acessar logs de aplicativos</td>
<td>Capacidade para enumerar programaticamente aplicativos que foram executados nos seus clusters e baixar relevantes logs de aplicativo contêiner-específicas ou para ajudar a depurar aplicativos problemáticos.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Capacidade de especificar o nome de região em IHdInsightClient.DeleteCluster </td>
<td>O SDK do Azure HDInsight fornece a capacidade de especificar um nome de região ao usar **DeleteCluster**. Isso ajuda a desbloquear clientes que tinham dois recursos com o mesmo nome em diferentes regiões e tiveram sido não é possível excluir qualquer um deles.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>O objeto **ClusterDetails** retorna um campo **DeploymentID** que representa um identificador exclusivo para o cluster. Ele certamente ser exclusivo em tentativas de criação de cluster com os mesmos nomes.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Notas de versão de 14/11/2014 do HDInsight

Os números de versão completa para clusters HDInsight implantados com esta versão são:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Esta versão contém os seguintes recursos novos, atualizações de componente e correções de bugs.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Ação de script (prévia)</td>
<td>Visualização do recurso de personalização de cluster que permite a modificação de Hadoop clusters maneiras aleatório usando scripts personalizados. Com esse recurso, os usuários podem experimentar e implantar projetos disponíveis do ecossistema do Apache Hadoop Azurehdinsight clusters. Este recurso de personalização está disponível em todos os tipos de HDInsight clusters, incluindo Hadoop, HBase e tempestade.</td>
<td>Novo recurso</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Trabalhos pré-criadas para análise de log de sites e armazenamento Azure</td>
<td>Console de consulta de HDInsight tem uma galeria de Introdução que ofereça suporte a soluções que funcionam em seus dados ou em dados de exemplo.
<p>**Soluções que trabalham em seus dados**:<br>
Criamos trabalhos para alguns dos cenários de análise de dados mais comuns para fornecer um ponto de partida para criar suas próprias soluções. Você pode usar seus dados com o trabalho para ver como ele funciona. Quando você estiver pronto, use o que você aprendeu a criar uma solução que é modelada após o trabalho pré-criadas.</p>
<p>**Soluções que trabalham em dados de exemplo**:<br>
Aprenda a trabalhar com HDInsight, examinando alguns cenários básicos (como analisar logs da web e dados). Você aprenderá como usar HDInsight para analisar esses dados e como você pode conectar outros aplicativos e serviços a esses dados. Visualizar dados conectando-se ao Microsoft Excel fornece um exemplo de potência dessa abordagem.</p></td>
<td>Console de consulta</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correção de perda de memória em Templeton</td>
<td>Perda de memória no Templeton que afetados clientes que tinham clusters de execução prolongadas ou foram enviar 100s do trabalho solicita que um segundo tiver sido tratado. O problema manifestado como Templeton 5xx erros e a solução alternativa foi reiniciar o serviço. A solução alternativa não é mais necessária.</td>
<td>Templeton</td>
<td>Todos os</td>
<td>https://issues.apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Observação**: para demonstrar os novos recursos disponibilizados pela personalização de cluster, os procedimentos usando a ação de Script instalar módulos Spark e R em um cluster tiveram sido documentados. Para obter mais informações, consulte:

* [Instalar e usar Spark 1.0 em clusters de HDInsight](hdinsight-hadoop-spark-install.md)
* [Instalar e usar R em clusters de HDInsight Hadoop](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Notas de versão 11/07/2014 do HDInsight

Os números de versão completa para clusters HDInsight implantado com esta versão são:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Esta versão contém as seguintes atualizações de componente.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Esta versão é baseada na plataforma de dados de Hortonworks (HDP) 2.1.7. Para obter mais informações, consulte <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Notas de versão do HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Todos os</td>
<td>N/D</td>
</tr>

<tr>
<td>Servidor de linha do tempo de fio COLORIDO</td>
<td>O servidor de linha do tempo de fio COLORIDO (também conhecido como o aplicativo histórico servidor genérico) foi habilitado por padrão. O servidor de linha do tempo fornece informações genéricas sobre aplicativos concluídas (como ID do aplicativo, nome do aplicativo, status do aplicativo, hora de envio do aplicativo e o tempo de conclusão do aplicativo).

Informações sobre esse aplicativo pode ser recuperado a partir do nó cabeça acessando o URI http://headnodehost:8188 ou executando o comando fio COLORIDO: fio colorido aplicativo – lista – appStates tudo.

Essas informações também podem ser recuperadas remotamente Embora uma API REST em https://{ClusterDnsName}. azurehdinsight.NET/WS/v1/applicationhistory/.

Para obter informações mais detalhadas, consulte <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de linha do tempo de fio COLORIDO</a>.</td>
<td>Serviço, fio COLORIDO</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Identificação de implantação de cluster</td>
<td>Começando com o SDK versão 1.3.3.1.5426.29232, os usuários podem acessar uma ID exclusiva para cada cluster, o que é emitida HDInsight. Isso permite aos clientes descobrir instâncias exclusivas de clusters quando um nome DNS está sendo reutilizado em criar ou soltar cenários.</td>
<td>SDK</td>
<td>Todos os</td>
<td>N/D</td>
</tr>
</table>
<br>

**Observação**: O erro que impedido o número de versão completa do aparecendo no portal ou de sendo retornados pelo SDK ou o Windows PowerShell foi corrigido nesta versão.

## <a name="notes-for-10152014-release"></a>Notas de versão de 15/10/2014

Esta versão de hotfix corrigido uma perda de memória em Templeton que afetados os usuários pesados de Templeton. Em alguns casos, os usuários que exercidos Templeton muito verá erros declarados como 500 códigos de erro porque as solicitações não teria memória suficiente para executar. A solução alternativa para esse problema foi reiniciar o serviço de Templeton. Esse problema foi resolvido.


## <a name="notes-for-1072014-release"></a>Notas de versão de 7/10/2014

* Ao usar o ponto de extremidade de Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *nome_do_host* retorna o nome de domínio totalmente qualificado (FQDN) do nó em vez de apenas o nome de host. Por exemplo, em vez de retornar "**headnode0**", você obterá o FQDN "**headnode0. { ClusterDNS} .azurehdinsight .net**". Essa alteração foi exigida para facilitar a cenários onde vários tipos de cluster (como HBase e Hadoop) podem ser implantados em uma rede virtual. Isso acontece, por exemplo, ao usar o HBase como uma plataforma de back-end para Hadoop.

* Fornecemos novas configurações de memória para a implantação padrão do cluster HDInsight. Configurações de memória padrão anterior não adequadamente conta para a orientação para o número de cores de CPU sendo implantado. Essas novas configurações de memória devem fornecer padrões de melhores (acordo com as recomendações de Hortonworks). Para alterar essas opções, consulte a documentação de referência SDK sobre a alteração de configuração de cluster. As novas configurações de memória que são usadas pelo cluster padrão 4 CPU core (8 contêiner) HDInsight são detalhadas na tabela a seguir. (Os valores usados antes nesta versão também são fornecidos entre parênteses.)

<table border="1">
<tr><th>Componente</th><th>Alocação de memória</th></tr>
<tr><td> alocação de yarn.Scheduler.Minimum</td><td>768 MB (anteriormente 512 MB)</td></tr>
<tr><td> alocação de yarn.Scheduler.Maximum</td><td>6144 MB (inalterada)</td></tr>
<tr><td>yarn.NodeManager.Resource.Memory</td><td>6144 MB (inalterada)</td></tr>
<tr><td>MapReduce.map.Memory</td><td>768 MB (anteriormente 512 MB)</td></tr>
<tr><td>MapReduce.map.Java.opts</td><td>opta =-Xmx512m (anteriormente - Xmx410m)</td></tr>
<tr><td>MapReduce.reduce.Memory</td><td>1536 MB (anteriormente 1024 MB)</td></tr>
<tr><td>MapReduce.reduce.Java.opts</td><td>opta =-Xmx1024m (anteriormente - Xmx819m)</td></tr>
<tr><td>yarn.App.MapReduce.AM.Resource</td><td>768 MB (anteriormente 1024 MB)</td></tr>
<tr><td>yarn.App.MapReduce.AM.Command</td><td>opta =-Xmx512m (anteriormente - Xmx819m)</td></tr>
<tr><td>MapReduce.Task.IO.Sort</td><td>256 MB (anteriormente 200 MB)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1536 MB (inalterada)</td></tr>

</table><br>

Para obter mais informações sobre as definições de configuração de memória usado pelo fio COLORIDO e MapReduce na plataforma de dados de Hortonworks que é usado pelo HDInsight, consulte [Determinar definições de configuração de memória HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks também fornecida uma ferramenta para calcular as configurações de memória PRI.

Sobre o PowerShell do Azure e a mensagem de erro de HDInsight SDK: "*Cluster não está configurado para o acesso de serviços HTTP*":

* Esse erro é um [problema de compatibilidade](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) conhecido que pode ocorrer devido uma diferença entre a versão do SDK do HDInsight ou do PowerShell do Azure e a versão do cluster. Clusters criados em 8/15 ou posterior têm suporte para o novo recurso de provisionamento em redes virtuais. Mas esse recurso não é corretamente interpretado por versões mais antigas do HDInsight SDK ou Azure PowerShell. O resultado é uma falha em algumas operações de envio de trabalho. Se você usar HDInsight SDK APIs ou os cmdlets do PowerShell do Azure (**Use AzureRmHDInsightCluster** ou **Chamar AzureRmHDInsightHiveJob**) para enviar trabalhos, essas operações podem falhar com a mensagem de erro "*Cluster <clustername> não está configurado para o acesso de serviços HTTP*." Ou (dependendo da operação), você pode receber outras mensagens de erro, como "*não é possível se conectar ao cluster*".

* Esses problemas de compatibilidade são resolvidos nas versões mais recentes do PowerShell do Azure e HDInsight SDK. Recomendamos atualizar o SDK do HDInsight a versão 1.3.1.6 ou posteriores e Azure ferramentas de PowerShell versão 0.8.8 ou posterior. Você pode obter acesso ao HDInsight SDK mais recente do [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e as ferramentas do PowerShell do Azure como [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Notas de versão de 12/9/2014 do HDInsight 3.1

* Esta versão é baseada na plataforma de dados de Hortonworks (HDP) 2.1.5. Para obter uma lista de bugs corrigidos nesta versão, consulte a página [fixo nesta versão](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) do site Hortonworks.
* Na pasta de bibliotecas porco, o arquivo "avro-mapred-1.7.4.jar" é alterado para "avro-mapred-1.7.4-hadoop2.jar." O conteúdo deste arquivo contém uma correção de bugs secundária que é incondicionais. É recomendável que os clientes não faça uma dependência direta no nome do arquivo JAR para evitar quebras quando os arquivos são renomeados.


## <a name="notes-for-8212014-release"></a>Notas de versão de 21/8/2014

* Estamos adicionando a seguinte configuração de WebHCat (seção-7155), que define o limite de memória padrão para um trabalho de controlador de Templeton para 1 GB. (O valor padrão anterior era 512 MB.)

     templeton.Mapper.Memory.MB (= 1024)

    * Essa alteração corrige o seguinte erro que determinadas consultas seção tinham tiver devido a limites de memória inferiores: "Contêiner está executando além dos limites de memória física".
    * Para reverter para os padrões antigos, você pode definir o valor de configuração para 512 através do PowerShell do Azure no momento da criação de cluster usando o seguinte comando:

        Adicionar-AzureRmHDInsightConfigValues-Core@{"templeton.mapper.memory.mb"="512";}


* O nome de host da função zookeeper foi alterado para *zookeeper*. Isso afeta a resolução de nomes dentro do cluster, mas ele não afeta as APIs REST externas. Se você tiver componentes que usam o nome do host *zookeepernode* , você precisa atualizá-los para usar o novo nome. Os novos nomes para os três nós zookeeper são:
    * zookeeper0
    * zookeeper1
    * zookeeper2
* Matriz de suporte do HBase versão é atualizada. Somente HDInsight versão 3.1 (HBase versão 0,98) tem suporte para produção para cargas de trabalho de HBase. Versão 3.0 (que estava disponível para visualização) não há suporte para movimentação para frente.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Observações sobre clusters criados antes de 8/15/2014

Uma mensagem de erro Azure PowerShell ou HDInsight SDK "Cluster <clustername> não está configurado para o acesso de serviços HTTP" (ou dependendo da operação, outro mensagens de erro como: "Não é possível conectar-se ao cluster") podem ser encontrados devido a uma diferença de versão entre Azure PowerShell ou o SDK do HDInsight e um cluster. Clusters criados em 8/15 ou posterior têm suporte para o novo recurso de provisionamento em redes virtuais. Esse recurso não está corretamente interpretado por versões mais antigas do PowerShell do Azure ou o SDK do HDInsight, que resulta em falhas de operações de envio de trabalho. Se você usar cmdlets HDInsight SDK APIs ou Azure PowerShell (como uso AzureRmHDInsightCluster ou chamar AzureRmHDInsightHiveJob) para enviar trabalhos, essas operações podem falhar com uma das mensagens de erro descritas.

Esses problemas de compatibilidade são resolvidos nas versões mais recentes do PowerShell do Azure e HDInsight SDK. Recomendamos atualizar o SDK do HDInsight a versão 1.3.1.6 ou posteriores e Azure ferramentas de PowerShell versão 0.8.8 ou posterior. Você pode obter acesso ao HDInsight SDK mais recente do [NuGet][nuget-link]. Você pode acessar as ferramentas do PowerShell do Azure usando o [Microsoft Web Platform Installer][webpi-link].


## <a name="notes-for-7282014-release"></a>Notas do lançamento de 28/7/2014

* **HDInsight disponível em novas regiões**: nós expandido HDInsight geográfica presença para três regiões. Clientes de HDInsight podem criar clusters nessas regiões:
    * Da Ásia Oriental
    * Centro Norte dos EUA
    * Centro Sul dos EUA
* HDInsight versão 1,6 (HDP 1.1 e Hadoop 1.0.3) e HDInsight versão 2.1 (HDP1.3 e Hadoop 1.2) estão sendo removidos do portal do Azure. Você pode continuar a criar clusters Hadoop para essas versões usando o cmdlet do PowerShell do Azure, [New-AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) ou usando o [SDK do HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Consulte a página de [controle de versão do HDInsight componente](hdinsight-component-versioning.md) para obter mais informações.
* Alterações de plataforma de dados de Hortonworks (HDP) nesta versão:

<table border="1">
<tr><th>HDP</th><th>Alterações</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Nenhuma alteração</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Nenhuma alteração</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Notas do lançamento de 24/6/2014

Esta versão contém aprimoramentos para o serviço de HDInsight:

* **Disponibilidade de HDP 2.1**: 3.1 HDInsight (que contém HDP 2.1) estiver disponível e é a versão padrão para novos clusters.
* **HBase – Azure melhorias portal**: estamos fazendo HBase clusters disponíveis na visualização. Você pode criar clusters de HBase a partir do portal com apenas alguns cliques. 

Com HBase, você pode criar uma variedade de cargas de trabalho em tempo real no HDInsight, de sites interativos que funcionam com grandes conjuntos de dados nos serviços do armazenamento de dados de sensor e telemetria de milhões de pontos de extremidade. A próxima etapa seria analisar os dados nestas cargas de trabalho com trabalhos de Hadoop, e isso é possível no HDInsight por meio do PowerShell do Azure e o painel de cluster de seção.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Mahout Apache pré-instalado em HDInsight 3.1

 [Mahout](http://hortonworks.com/hadoop/mahout/) é vem pré-instalado em clusters de HDInsight 3.1 Hadoop, para que você possa executar trabalhos de Mahout sem a necessidade de configuração de cluster adicionais. Por exemplo, você pode remoto em um cluster de Hadoop usando o protocolo RDP (Remote Desktop) e sem etapas adicionais, você pode executar o seguinte comando Olá mundo Mahout:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obter uma explicação mais completa deste procedimento, consulte a documentação para que o [Exemplo Breiman](https://mahout.apache.org/users/classification/breiman-example.html) no site Apache Mahout.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Consultas de seção podem usar Tez no HDInsight 3.1

Seção 0.13 está disponível no HDInsight 3.1 e é capaz de executar consultas usando Tez, que pode ser utilizada para obter melhorias de desempenho.
Tez não é habilitar por padrão para consultas de seção. Para usá-lo, você deve optar por usar em. Você pode ativar Tez executando o trecho de código a seguir:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks publicou uma divisão detalhada dos aperfeiçoamentos de desempenho de consulta de seção com Tez como entregue em avaliações padrão. Para obter detalhes, consulte [avaliações de desempenho Apache 13 de seção para Hadoop Enterprise](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Para obter mais detalhes sobre como usar a seção com Tez, consulte a [seção em Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Disponibilidade global
Com o lançamento do HDInsight em Hadoop 2.2, Microsoft disponibilizou HDInsight em todas as principais regiões onde Azure está disponível. Especificamente, os Oeste Europa e sudeste asiático dos data centers tiveram sido colocados online. Isso permite aos clientes localizar clusters em um data center fechar e possivelmente em uma zona semelhante de requisitos de conformidade.


###<a name="dos--donts-between-cluster-versions"></a>Faça x entre versões de cluster

**Metastores Oozie usados com um cluster de HDInsight 3.1 são não compatíveis com clusters HDInsight 2.1, e eles não podem ser usados com esta versão anterior**.

Um Oozie metastore banco de dados personalizado implantado com um cluster de HDInsight 3.1 não pode ser reutilizado com um cluster de HDInsight 2.1. Esse é o caso mesmo se originou a metastore com um cluster de HDInsight 2.1. Este cenário não é suportado porque o esquema de metastore obtém atualizado quando usada com um cluster de HDInsight 3.1, portanto, não é compatível com o metastore necessário clusters HDInsight 2.1. Qualquer tentativa de reutilizar um metastore Oozie que foi usado com um cluster de HDInsight 3.1 serão renderizados cluster HDInsight 2.1 inútil.

**Oozie metastores não podem ser compartilhados entre clusters.**

Oozie metastores são anexados a clusters específicos, e eles não podem ser compartilhados entre clusters.

###<a name="breaking-changes"></a>Alterações significativas

**Sintaxe de prefixo**: somente a "wasbs: / /" sintaxe é suportada no HDInsight 3.1 e 3.0 clusters. O antigo "asv: / /" sintaxe é suportada no HDInsight 2.1 e 1,6 clusters, mas ela não é suportada no HDInsight 3.1 ou 3.0 clusters. Isso significa que todos os trabalhos enviados para um 3.1 HDInsight ou cluster 3.0 que usam explicitamente a "asv: / /" sintaxe falhará. O "wasbs: / /" sintaxe deve ser usada em vez disso. Além disso, os trabalhos enviados para qualquer 3.1 HDInsight ou 3.0 clusters que são criados com um metastore existente que contém referências explícitas aos recursos usando o "asv: / /" sintaxe falhará. Estas metastores precisa ser recriado usando o "wasbs: / /" sintaxe para os recursos de endereço.


**Portas**: as portas usadas pelo serviço HDInsight foram alteradas. Os números de porta que estavam sendo usados foram dentro do intervalo de portas efêmeras de sistema operacional Windows. Portas estão alocadas automaticamente de um intervalo de efêmero predefinido para comunicações curta duração baseada em protocolo na Internet. O novo conjunto de números de porta de serviço de plataforma de dados de Hortonworks (HDP) permitidos são fora desse intervalo para evitar conflitos que podem surgir com as portas usadas por serviços em execução no nó principal. Os novos números de porta não devem causar quaisquer alterações significativas. Os números usados são da seguinte maneira:

 **HDInsight 1,6 (HDP 1.1)**
<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>DFS.HTTP.Address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.HTTP.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.HTTP.Address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.HTTP.Address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.HTTP.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>MapReduce.History.Server.HTTP.Address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 e 3.0 (HDP 2.1 e 2.0)**
<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>endereço de DFS.namenode.http</td><td>namenodehost:30070</td></tr>
<tr><td>endereço de DFS.namenode.HTTPS</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.HTTP.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>endereço de DFS.namenode.Secondary.http</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.NodeManager.WebApp.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Dependências

As seguintes dependências foram adicionadas na HDInsight 3. x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* ativação
* jsr305
* geronimo-jaspic_1.0_spec
* jul-slf4j
* xmlbuilder Java
* Ant
* compilador Commons
* jdo-api
* Commons-math3
* paranamer
* implementação de JAXB
* stringtemplate
* eigenbase-xom
* Jersey-servlet
* Commons-executivo
* api do JAXB
* jetty-all-servidor
* janino
* xercesImpl
* optiq-avatica
* JTA
* Propriedades de eigenbase
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* cliente Jersey
* aopalliance
* geronimo-annotation_1.0_spec
* iniciador de Ant
* Jersey-guice
* apis de XML
* stax-api
* ASM-commons
* árvore de ASM
* WADL
* geronimo-jta_1.1_spec
* guice
* leveldbjni-tudo
* velocidade
* jettison
* interessantes-java
* jetty-tudo
* Commons-dbcp

As seguintes dependências não existem mais no HDInsight 3. x (HDP2.x):

* jdeb
* kfs
* sqlline
* Hera
* aspectjrt
* JSON
* principais
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* JSP
* api de log de Commons
* Commons matemáticas
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* interessantes

###<a name="version-changes"></a>Alterações de versão

Foram feitas as seguintes alterações de versão entre HDInsight 2. x (HDP1.x) e HDInsight 3. x (HDP2.x):

* métricas-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper compilador: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* HSQLDB: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* daemon Commons: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']


### <a name="drivers"></a>Drivers
O driver de Connnectivity de banco de dados Java (JDBC) para o SQL Server é usado internamente pelo HDInsight e não é usado para operações externas. Se desejar se conectar ao HDInsight usando conectividade aberta de banco de dados (ODBC), use o seção driver ODBC do Microsoft. Para obter mais informações, consulte [Conectar o Excel ao HDInsight com o Driver ODBC de seção do Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Correções de bugs

Com esta versão, podemos tiver atualizado as seguintes versões de HDInsight com várias correções de bugs:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Notas de versão do Hortonworks

Notas de versão para as plataformas de dados de Hortonworks (HDPs) que são usadas pelos clusters de versão do HDInsight estão disponíveis nos seguintes locais:

* HDInsight versão 3.1 usa uma distribuição de Hadoop baseado na [Plataforma de dados Hortonworks 2.1.7][hdp-2-1-7]. Este é o cluster de Hadoop padrão criado quando usando o portal do Azure após 11/7/2014. HDInsight 3.1 clusters criados antes de 11/7/2014 foram baseados na [Plataforma de dados Hortonworks 2.1.1][hdp-2-1-1]

* HDInsight versão 3.0 usa uma distribuição de Hadoop que baseia-se no [2.0 de plataforma de dados de Hortonworks][hdp-2-0-8].

* HDInsight versão 2.1 usa uma distribuição de Hadoop que se baseia o [Hortonworks dados plataforma 1.3][hdp-1-3-0].

* HDInsight versão 1,6 usa uma distribuição de Hadoop que baseia-se no [1.1 de plataforma de dados de Hortonworks][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 
