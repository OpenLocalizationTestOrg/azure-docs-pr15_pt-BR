<properties
    pageTitle="Cenários para a análise avançada processo e tecnologia de aprendizado de máquina Azure | Microsoft Azure"
    description="Selecione os cenários apropriados para fazer avançadas de análise de previsão com o processo de ciência de dados de equipe."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na" 
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários para análises avançadas de aprendizado de máquina do Azure

Este artigo descreve a variedade de fontes de dados de exemplo e cenários de destino que podem ser tratados pelo [Processo de ciência de dados da equipe (TDSP)](data-science-process-overview.md). O TDSP fornece uma abordagem sistemática para equipes colaborar em compilação de aplicativos inteligentes. Os cenários apresentados aqui ilustram opções disponíveis do fluxo de trabalho de processamento de dados que dependem as características de dados, locais de origem e repositórios de destino no Azure.

A **árvore de decisão** para selecionar os cenários de exemplo apropriado para seus dados e o objetivo é apresentado na última seção.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cada uma das seções a seguir apresenta um cenário de exemplo. Para cada cenário, uma ciência de dados possíveis ou análises avançadas fluxo e Azure recursos de suporte estão listados.

>[AZURE.NOTE]**Para todos os cenários a seguir, você precisa:**
><br/>
>* [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md)
><br/>
>* [Criar um espaço de trabalho de aprendizado de máquina do Azure](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Cenário \#1: pequeno para um conjunto de dados tabular médio em um arquivos locais

![Pequenas e médias arquivos locais][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionais de Azure: nenhum

1.  Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

2.  Carregar um conjunto de dados.

3.  Crie um fluxo de experimento de aprendizado de máquina do Azure começando com dataset(s) carregados.

## <a name="smalllocalprocess"></a>Cenário \#2: pequeno para conjunto de dados médio dos arquivos locais que exigem processamento

![Pequenas e médias arquivos locais com processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (servidor de bloco de anotações de IPython)

1.  Crie uma máquina Virtual do Azure executando o bloco de anotações de IPython.

2.  Carregar dados em um contêiner de armazenamento do Azure.

3.  Pré-processar e limpar dados no bloco de anotações de IPython, acessar dados do contêiner de armazenamento do Azure.

4.  Transforme dados limpos, formato tabular.

5.  Salve dados transformados em bolhas Azure.

6.  Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

7.  Ler os dados do Azure blobs usando os [Dados de importação] [ import-data] módulo.

8. Crie um fluxo de experimento de aprendizado de máquina do Azure começando com dataset(s) ingeridos.

## <a name="largelocal"></a>Cenário \#3: grande conjunto de dados de arquivos locais, direcionamento Blobs do Azure

![Grandes arquivos locais][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (servidor de bloco de anotações de IPython)

1.  Crie uma máquina Virtual do Azure executando o bloco de anotações de IPython.

2.  Carregar dados em um contêiner de armazenamento do Azure.

3.  Pré-processar e limpar dados no bloco de anotações de IPython, acessar dados do Azure blobs.

4.  Transforme dados limpos, formato tabular, se necessário.

5.  Explorar dados e criar recursos conforme necessário.

6.  Extraia uma amostra de dados de pequeno e médio.

7.  Salve os dados de amostrados em bolhas Azure.

8. Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

9. Ler os dados do Azure blobs usando os [Dados de importação] [ import-data] módulo.

10. Crie fluxo de experimento de aprendizado de máquina do Azure começando com dataset(s) ingeridos.


## <a name="smalllocaltodb"></a>Cenário \#4: pequeno para conjunto de dados médio dos arquivos locais, direcionamento SQL Server na máquina de Virtal Azure

![Pequenas e médias arquivos locais para o banco de dados do SQL no Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor de bloco de anotações de IPython)

1.  Crie uma máquina Virtual do Azure executando o SQL Server + IPython bloco de anotações.

2.  Carregar dados em um contêiner de armazenamento do Azure.

3.  Pré-processar e limpar dados no contêiner de armazenamento do Azure usando o bloco de anotações de IPython.

4.  Transforme dados limpos, formato tabular, se necessário.

5.  Salvar dados de arquivos de máquina virtual local (IPython bloco de anotações está em execução na máquina virtual, unidades locais consultem unidades de máquina virtual).

6.  Carregar dados ao banco de dados do SQL Server em execução em uma máquina virtual do Azure.

    Opção \#1: usar o SQL Server Management Studio.

    - Logon para o SQL Server máquina virtual
    - Execute o SQL Server Management Studio.
    - Crie tabelas de banco de dados e de destino.
    - Use um em massa métodos para carregar os dados de arquivos de máquina virtual local de importação.

    Opção \#2: usando IPython bloco de anotações não recomendável para conjuntos de dados médios e maiores<!-- -->    
    - Use a cadeia de conexão ODBC para acessar o SQL Server na máquina virtual.
    - Crie tabelas de banco de dados e de destino.
    - Use um em massa métodos para carregar os dados de arquivos de máquina virtual local de importação.

7.  Explorar dados, criar recursos conforme necessário. Observe que os recursos não precisam ser materializado nas tabelas de banco de dados. Observação somente a consulta necessária para criá-los.

8. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.

9. Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

10. Ler os dados diretamente do SQL Server usando os [Dados de importação] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados se necessário diretamente na [Importação de dados] [ import-data] consulta.

11. Crie fluxo de experimento de aprendizado de máquina do Azure começando com dataset(s) ingeridos.

## <a name="largelocaltodb"></a>Cenário \#5: grande conjunto de dados em um local arquivos, direcionar o SQL Server na máquina virtual do Azure

![Grandes arquivos locais para o banco de dados do SQL no Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor de bloco de anotações de IPython)

1.  Crie uma máquina Virtual do Azure executando o SQL Server e o servidor de bloco de anotações de IPython.

2.  Carregar dados em um contêiner de armazenamento do Azure.

3.  (Opcional) Pré-processar e limpar dados.

    a.  Pré-processar e limpar dados no bloco de anotações de IPython, acessar dados do Azure blobs.

    b.  Transforme dados limpos, formato tabular, se necessário.

    c.  Salvar dados de arquivos de máquina virtual local (IPython bloco de anotações está em execução na máquina virtual, unidades locais consultem unidades de máquina virtual).

4.  Carregar dados ao banco de dados do SQL Server em execução em uma máquina virtual do Azure.

    a.  Faça login do SQL Server máquina virtual.

    b.  Se os dados não salvos já, baixe arquivos de dados do contêiner de armazenamento do Azure para pasta local-máquina virtual.

    c.  Execute o SQL Server Management Studio.

    d.  Crie tabelas de banco de dados e de destino.

    e.  Use um em massa métodos para carregar os dados de importação.

    f.  Se tabela junções forem necessárias, crie índices para agilizar junções.

     > [AZURE.NOTE] Para carregamento mais rápido de tamanhos grandes de dados, é recomendável que você cria tabelas particionadas e os dados em paralelo de importação em massa. Para obter mais informações, consulte [Paralelas importação de dados para tabelas particionadas do SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorar dados, criar recursos conforme necessário. Observe que os recursos não precisam ser materializado nas tabelas de banco de dados. Observação somente a consulta necessária para criá-los.

6.  Escolha um tamanho de amostra de dados, se necessário e/ou desejado.

7.  Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

8. Ler os dados diretamente do SQL Server usando os [Dados de importação] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados se necessário diretamente na [Importação de dados] [ import-data] consulta.

9. Fluxo de experimento de aprendizado de máquina do Azure simples começando com dataset carregado

## <a name="largedbtodb"></a>Cenário \#6: grande conjunto de dados em um SQL Server banco de dados local, direcionamento do SQL Server em uma máquina Virtual do Azure

![SQL grande BD local ao banco de dados do SQL no Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor de bloco de anotações de IPython)

1.  Crie uma máquina Virtual do Azure executando o SQL Server e o servidor de bloco de anotações de IPython.

2.  Use um dos dados exportar métodos para exportar os dados do SQL Server para arquivos de despejo.

    > [AZURE.NOTE] Se você decidir mover todos os dados do banco de dados local, um método alternativo (mais rápido) para mover o banco de dados completo para instância do SQL Server no Azure. Ignore as etapas para exportar dados, criar banco de dados e carregar/importar dados para o banco de dados de destino e execute o método alternativo.

3.  Carregar arquivos de despejo no contêiner de armazenamento do Azure.

4.  Carregue os dados para um banco de dados do SQL Server em execução em uma máquina Virtual do Azure.

    a.  Faça login no máquina virtual do SQL Server.

    b.  Baixe arquivos de dados de um contêiner de armazenamento do Azure para a pasta local-máquina virtual.

    c.  Execute o SQL Server Management Studio.

    d.  Crie tabelas de banco de dados e de destino.

    e.  Use um em massa métodos para carregar os dados de importação.

    f.  Se tabela junções forem necessárias, crie índices para agilizar junções.

    > [AZURE.NOTE] Para carregamento mais rápido de tamanhos grandes de dados, criar tabelas particionadas e em massa importe os dados em paralelo. Para obter mais informações, consulte [Paralelas importação de dados para tabelas particionadas do SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorar dados, criar recursos conforme necessário. Observe que os recursos não precisam ser materializado nas tabelas de banco de dados. Observação somente a consulta necessária para criá-los.

6.  Escolha um tamanho de amostra de dados, se necessário e/ou desejado.

7.  Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

8. Ler os dados diretamente do SQL Server usando os [Dados de importação] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados se necessário diretamente na [Importação de dados] [ import-data] consulta.

9. Aprendizado de máquina Azure simples experimentar fluxo começando com dataset carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar um banco de dados completo de um SQL Server local para o banco de dados do SQL Azure

![Desanexar BD local e anexar a DB do SQL no Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais de Azure: Azure Máquina Virtual (SQL Server / servidor de bloco de anotações de IPython)

Para replicar o banco de dados do SQL Server inteiro em sua máquina de virtual do SQL Server, você deve copiar um banco de dados de um local/servidor para outro, supondo que o banco de dados pode ficar temporariamente off-line. Você pode fazer isso no Gerenciador de objeto do SQL Server Management Studio ou usando os comandos Transact-SQL equivalentes.

1. Desanexe o banco de dados no local de origem. Para obter mais informações, consulte [desanexar um banco de dados](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Na janela do Windows Explorer ou Prompt de comando do Windows, copie o arquivo de banco de dados desanexado ou a arquivos e o arquivo de log ou a arquivos para o local de destino na VM SQL Server no Azure.
3. Anexe os arquivos copiados a instância do SQL Server de destino. Para obter mais informações, consulte [anexar um banco de dados](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Mover um banco de dados usando desanexar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Cenário \#7: grandes dados em arquivos locais, banco de dados de seção no Azure HDInsight Hadoop clusters de destino

![Dados grandes na seção de destino local][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais de Azure: Cluster de Hadoop do Azure HDInsight e Azure Virtual Machine (servidor de bloco de anotações de IPython)

1.  Crie uma máquina Virtual do Azure executando o servidor de bloco de anotações de IPython.

2.  Crie um cluster de Azure HDInsight Hadoop.

3.  (Opcional) Pré-processar e limpar dados.

    a.  Pré-processar e limpar dados no bloco de anotações de IPython, acessar dados do Azure blobs.

    b.  Transforme dados limpos, formato tabular, se necessário.

    c.  Salvar dados de arquivos de máquina virtual local (IPython bloco de anotações está em execução na máquina virtual, unidades locais consultem unidades de máquina virtual).

4.  Carregue os dados para o recipiente padrão do cluster Hadoop selecionado na etapa 2.

5.  Carregar dados banco de dados de seção no Azure HDInsight Hadoop cluster.

    a.  Faça logon no nó principal do cluster Hadoop

    b.  Abra a linha de comando do Hadoop.

    c.  Insira o diretório raiz de seção pelo comando `cd %hive_home%\bin` na linha de comando do Hadoop.

    d.  Executar as consultas de seção para criar o banco de dados e tabelas e carregar dados do armazenamento de blob para tabelas de seção.

    > [AZURE.NOTE] Se os dados forem grandes, os usuários podem criar a tabela de seção com partições. Em seguida, os usuários podem usar um `for` loop em Hadoop linha de comando no nó principal para carregar dados na tabela de seção particionada por partição.

6.  Explorar dados e criar recursos conforme necessário na linha de comando do Hadoop. Observe que os recursos não precisam ser materializado nas tabelas de banco de dados. Observação somente a consulta necessária para criá-los.

    a.  Faça logon no nó principal do cluster Hadoop

    b.  Abra a linha de comando do Hadoop.

    c.  Insira o diretório raiz de seção pelo comando `cd %hive_home%\bin` na linha de comando do Hadoop.

    d.  Execute as consultas de seção na linha de comando do Hadoop no nó principal do cluster Hadoop para explorar os dados e criar recursos conforme necessário.

7.  Se necessário e/ou desejado, os dados para ajustá-la no Azure Studio de aprendizado de máquina de exemplo.

8.  Entrar no [Studio de aprendizado de máquina Azure](https://studio.azureml.net/).

9. Ler os dados diretamente a partir do `Hive Queries` usando os [Dados de importação] [ import-data] módulo. Cole a consulta necessária que extrai campos, cria recursos e amostras de dados se necessário diretamente na [Importação de dados] [ import-data] consulta.

10. Aprendizado de máquina Azure simples experimentar fluxo começando com dataset carregado.

## <a name="decisiontree"></a>Árvore de decisão de seleção de cenário
------------------------

O diagrama a seguir resume os cenários descritos acima e o processo de análise avançada e opções de tecnologia feitas que levam você a cada um dos cenários detalhados. Observe que o processamento de dados, exploração, engenharia de recurso e amostragem podem levar colocar em um ou mais método/ambiente--a fonte, intermediário, e/ou ambientes de destino – e pode continuar interativamente conforme necessário. O diagrama só serve como uma ilustração de alguns dos fluxos de possíveis e não fornece uma enumeração completa.

![Cenários de explicação passo a passo de processo de DS de exemplo][8]

### <a name="advanced-analytics-in-action-examples"></a>Análise avançada em ação exemplos

Para orientações de aprendizado de máquina do Azure de ponta a ponta que utilizam o processo de análise avançada e tecnologia usando conjuntos de dados públicos, consulte:


* [Processo de ciência de dados de equipe em ação: usando o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Processo de ciência de dados de equipe em ação: usando clusters de HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
