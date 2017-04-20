<properties
    pageTitle="O processo de ciência de dados de equipe em ação: usando o SQL Server | Microsoft Azure"
    description="Processo de análises avançadas e tecnologia em ação"  
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
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O processo de ciência de dados de equipe em ação: usando o SQL Server

Neste tutorial, você passo a passo Criando e implantando um modelo de aprendizado de máquina usando o SQL Server e um dataset publicamente disponível – dataset [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) . O procedimento a segue um fluxo de trabalho de ciência de dados padrão: absorver e explorar os dados, engenheiro de recursos para facilitar o aprendizado, e em seguida, criar e implantar um modelo.


## <a name="dataset"></a>NYC Taxi retira a descrição do conjunto de dados

Os dados NYC Taxi viagem são de cerca de 20GB de arquivos compactados de CSV (descompactada de aproximadamente 48GB), que consiste em viagens de mais de milhões de 173 individuais e as tarifas pago para cada viagem. Cada registro de viagem inclui local de retirada e redistribuição e tempo, invasão anonymized (driver) número de carteira e número de medallion (identificação exclusiva do taxi). Os dados abrange todas as viagens no ano 2013 e são fornecidos nos seguintes dois conjuntos de dados para cada mês:

1. Trip_data' ' CSV contém detalhes de viagem, como o número de passageiros, retirada e pontos de redução, duração de viagem e duração da viagem. Aqui estão alguns exemplos de registros:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip_fare' CSV contém detalhes da tarifa pago para cada viagem, como tipo de pagamento, quantidade de tarifa, taxa adicional e impostos, dicas e tarifas e o valor total pago. Aqui estão alguns exemplos de registros:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para ingressar em viagem\_dados e viagem\_tarifa é composta dos campos: medallion, ataques\_licença e retirada\_datetime.

## <a name="mltasks"></a>Exemplos de tarefas de previsão

Podemos será formular três problemas de previsão com base no *Dica\_quantidade*, notadamente:

1. Classificação binária: prever ou não uma dica foi pago para uma viagem, ou seja, um *Dica\_quantidade* que for maior de US $0 é um exemplo positivo, enquanto um *Dica\_quantidade* $ 0 é um exemplo de negativo.

2. Classificação multiclass: para prever o intervalo de dica pagado para a viagem. Dividimos o *Dica\_quantidade* em cinco bins ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. Tarefa de regressão: prever a quantidade de dica pagada por uma viagem.  


## <a name="setup"></a>Ambiente de ciência de dados de configuração o Azure para análises avançadas

Como você pode ver do guia de [Planejar seu ambiente](machine-learning-data-science-plan-your-environment.md) , há várias opções para trabalhar com o conjunto de dados NYC Taxi viagens no Windows Azure:

- Trabalhar com os dados no Azure blobs e o modelo e exibir aprendizado de máquina do Windows Azure
- Carregar os dados em um banco de dados do SQL Server e o modelo e exibir aprendizado de máquina do Windows Azure

Neste tutorial Vamos demonstrar a importação em massa paralelo dos dados a um SQL Server, exploração de dados, o recurso de engenharia e para baixo de amostragem usando o SQL Server Management Studio, bem como usar o bloco de anotações IPython. [Scripts de amostra](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [IPython blocos de anotações](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) compartilhados em GitHub. Um bloco de anotações do IPython de amostra para trabalhar com os dados no Azure blobs também está disponível no mesmo local.

Para configurar o seu ambiente do Windows Azure ciência de dados:

1. [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md)

2. [Criar um espaço de trabalho de aprendizado de máquina do Windows Azure](machine-learning-create-workspace.md)

3. [Provisionar uma máquina de Virtual de ciência de dados](machine-learning-data-science-setup-sql-server-virtual-machine.md), que servirá como um SQL Server também um servidor IPython bloco de anotações.

    > [AZURE.NOTE] Os scripts de amostra e blocos de anotações IPython serão baixados em sua máquina virtual de ciência de dados durante o processo de instalação. Quando o script de pós-instalação VM for concluído, as amostras estarão na biblioteca de documentos da sua VM:  
    > - Scripts de amostra:`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - Blocos de anotações de IPython de amostra:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > onde `<user_name>` é o nome de logon do Windows da sua VM. Podemos fará referência às pastas amostra como **Scripts de amostra** e **Blocos de anotações do exemplo IPython**.


Com base no tamanho do conjunto de dados, local de fonte de dados e o ambiente de destino Azure selecionado, este cenário é semelhante ao [cenário \#5: SQL Server no Windows Azure VM de destino do conjunto de dados grande em um arquivos locais,](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obter os dados da fonte de público

Para obter o conjunto de dados [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) de seu local público, você pode usar qualquer um dos métodos descritos em [Mover dados de e para o armazenamento de Blob do Windows Azure](machine-learning-data-science-move-azure-blob.md) para copiar os dados para sua nova máquina virtual.

Para copiar os dados usando AzCopy:

1. Faça logon sua máquina virtual (VM)

2. Criar um novo diretório no disco de dados da VM (Observação: não use o disco temporário que acompanha a VM como um disco de dados).

3. Em uma janela de Prompt de comando, execute a seguinte linha de comando do Azcopy, substituindo < path_to_data_folder > com a pasta de dados criada no (2):

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    Quando o AzCopy for concluída, um total de 24 os arquivos CSV de compactados (12 para viagem\_dados e 12 para viagem\_tarifa) deve estar na pasta de dados.

4. Descompacte os arquivos baixados. Observe que a pasta onde residem os arquivos compactados. Essa pasta será ser referida como o < caminho\_para\_dados\_arquivos\>.

## <a name="dbload"></a>Dados de importação em massa no banco de dados do SQL Server

O desempenho do carregamento/transferindo grandes quantidades de dados para um banco de dados do SQL e consultas subsequentes pode ser aprimorado usando _particionados tabelas e exibições_. Nesta seção, Seguiremos as instruções descritas em [Paralelo em massa dados importação usando SQL tabelas de partição](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) para criar um novo banco de dados e carregar os dados em tabelas particionadas em paralelo.

1. Enquanto conectado à sua VM, inicie o **SQL Server Management Studio**.

2. Conecte-se usando a autenticação do Windows.

    ![Conectar SSMS][12]

3. Se você ainda não tiver alterado o modo de autenticação do SQL Server e criado um novo usuário de logon do SQL, abrir o arquivo de script denominado **alterar\_auth.sql** na pasta **Scripts de amostra** . Altere o nome de usuário padrão e a senha. Clique **! Executar** na barra de ferramentas para executar o script.

    ![Executar o Script][13]

4. Verificar e/ou alterar os SQL Server banco de dados e log pastas padrão para garantir que serão armazenadas em um disco de dados recém-criado bancos de dados. A imagem de máquina virtual do SQL Server que é otimizada para cargas datawarehousing é pré-configurado com discos de dados e de log. Se sua VM não incluiu um disco de dados e adicionado novos discos rígidos virtuais durante o processo de instalação da VM, altere as pastas padrão da seguinte maneira:

    - Nome do SQL Server no painel esquerdo do mouse em e clique em **Propriedades**.

        ![Propriedades do SQL Server][14]

    - Selecione **Configurações do banco de dados** na lista **Selecionar uma página** à esquerda.

    - Verificar e/ou alterar os **locais do banco de dados padrão** para os locais de **Disco de dados** de sua escolha. Isso é onde residem os novos bancos de dados se criado com as configurações de local padrão.

        ![Padrões de banco de dados do SQL][15]  

5. Para criar um novo banco de dados e um conjunto de grupos de arquivos para armazenar as tabelas particionadas, abra o script de exemplo **criar\_db\_default.sql**. O script criará um novo banco de dados denominado **TaxiNYC** e 12 grupos de arquivos no local de dados padrão. Cada grupo de arquivos Acomodará um mês de viagem\_dados e viagem\_se saiam dados. Modificar o nome do banco de dados, se desejado. Clique em **! Executar** para executar o script.

6. Em seguida, crie duas tabelas de partição, um para a viagem\_dados e outra para a viagem\_tarifa. Abra o script de exemplo **criar\_particionado\_table.sql**, que será:

    - Crie uma função de partição para dividir os dados por mês.
    - Crie um esquema de partição para mapear os dados de cada mês para um grupo de arquivos diferente.
    - Crie duas tabelas particionadas mapeadas para o esquema de partição: **nyctaxi\_viagem** acomodará a viagem\_dados e **nyctaxi\_tarifa** acomodará a viagem\_se saiam dados.

    Clique em **! Executar** para executar o script e criar tabelas particionadas.

7. Na pasta **Scripts de amostra** , há dois scripts do PowerShell de amostra fornecidos para demonstrar em paralela massa importa dos dados para tabelas do SQL Server.

    - **bcp\_paralelo\_generic.ps1** é um script genérico para dados de importação em massa paralelo em uma tabela. Modifica esse script para definir as variáveis de entrada e de destino, conforme indicado na linha de comentário no script.
    - **bcp\_paralelo\_nyctaxi.ps1** é uma versão pré-configurada do script genérico e pode ser usado para ambas as tabelas para os dados NYC Taxi viagens de carga.  

8. Com o botão direito do **bcp\_paralelo\_nyctaxi.ps1** script nome e clique em **Editar** para abri-lo no PowerShell. Revise as variáveis predefinidas e modificar de acordo com seu nome de banco de dados selecionado, pasta de dados de entrada, pasta de log de destino e caminhos de arquivos de formato de amostra **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (fornecido na pasta **Scripts de amostra** ).

    ![Dados de importação em massa][16]

    Você também pode selecionar o modo de autenticação, o padrão é a autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script iniciará o 24 operações de importação em massa em paralelo, 12 para cada tabela particionada. Você pode monitorar o progresso de importação de dados ao abrir a pasta de dados do SQL Server padrão como definido acima.

9. O script do PowerShell relata horas o inicial e final. Quando todos os em massa importações completas, a hora de término é relatada. Verifique a pasta de log de destino para verificar se as importações em massa foram bem-sucedidas, ou seja, nenhum erro relatado na pasta de log de destino.

10. O banco de dados está pronto para exploração, engenharia de recurso e outras operações conforme desejado. Desde que as tabelas são particionadas de acordo com o **retirada\_datetime** field, consultas que incluem **retirada\_datetime** condições na cláusula **WHERE** serão beneficiados com o esquema de partição.

11. No **SQL Server Management Studio**, explorar o script de exemplo fornecido **amostra\_queries.sql**. Para executar qualquer um dos exemplos de consultas, destaque as linhas de consulta e clique **! Executar** na barra de ferramentas.

12. Os dados de ida e volta NYC Taxi são carregados em duas tabelas separadas. Para melhorar as operações de associação, é altamente recomendável para indexar as tabelas. O script de exemplo **criar\_particionado\_index.sql** cria índices particionados pela chave join composto **medallion, ataques\_licença e retirada\_datetime**.

## <a name="dbexplore"></a>Exploração de dados e de engenharia de recurso no SQL Server

Nesta seção, nós executará exploração de dados e geração de recurso executando consultas SQL diretamente no **SQL Server Management Studio** usando o banco de dados do SQL Server criado anteriormente. Um exemplo de script chamado **amostra\_queries.sql** é fornecido na pasta **Scripts de amostra** . Modifique o script para alterar o nome do banco de dados, se for diferente do padrão: **TaxiNYC**.

Neste exercício, iremos:

- Conecte-se ao **SQL Server Management Studio** usando a autenticação do Windows ou autenticação do SQL e o nome de login do SQL e a senha.
- Explore distribuições de dados de alguns campos em variados janelas de tempo.
- Investigue a qualidade dos dados dos campos longitude e latitude.
- Gerar rótulos de binários e multiclass classificação com base no **Dica\_quantidade**.
- Recursos de gerar e compute/compare distâncias viagem.
- Junte duas tabelas e extraia uma amostra aleatória que será usada para criar modelos.

Quando estiver pronto para prosseguir para o aprendizado de máquina do Windows Azure, você pode:  

1. Salve a consulta SQL final para extrair e os dados de exemplo e copiar colar a consulta diretamente em uma [Importação de dados] [ import-data] módulo no aprendizado de máquina do Azure, ou
2. Os dados de amostrados e engenharia que você planeja usar o modelo Criando em uma nova tabela de banco de dados e usar a nova tabela na [Importação de dados] de persistência[ import-data] módulo no aprendizado de máquina do Azure.

Nesta seção, podemos salvará consulta final para extrair e os dados de exemplo. O segundo método é demonstrado na seção de [exploração de dados e de engenharia de recurso no bloco de anotações IPython](#ipnb) .

Para uma verificação rápida do número de linhas e colunas nas tabelas preenchido anteriormente usando a importação em massa paralelo,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion

Este exemplo identifica o medallion (taxi números) com mais de 100 viagens dentro de um determinado período. A consulta pode trazer benefícios contra o acesso de tabela particionada desde que ela está condicionada pelo esquema de partição de **retirada\_datetime**. Consultar o conjunto de dados completo também fará com que use da tabela particionada e/ou verificação de índice.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem medallion e hack_license

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: Verificar os registros com incorreta longitude e/ou latitude

Este exemplo investiga se qualquer um dos campos longitude e/ou latitude conter um valor inválido (graus radian devem estar entre -90 e 90), ou ter (0, 0) coordenadas.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Distribuição de Tipped versus não-Oblíquo viagens

Este exemplo localiza o número de viagens que foram Oblíquo versus não-Oblíquo em um determinado momento períodos (ou no conjunto de dados completo se que abrangem o ano completo). Essa distribuição reflete a distribuição de rótulo binário a ser usado posteriormente para modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: Distribuição da classe/intervalo de dica

Este exemplo calcula a distribuição de intervalos de dica em determinado período de tempo (ou no conjunto de dados completo se que abrangem o ano completo). Esta é a distribuição das classes rótulo que serão usados posteriormente para modelagem de classificação multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Calcular e comparar a distância de viagem

Este exemplo converte a retirada e redistribuição longitude e latitude para geografia SQL aponta, computa a distância de viagem usando a diferença de pontos de Geografia do SQL e retorna uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados para as coordenadas válidos apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Recurso de engenharia em consultas SQL

As consultas rótulo geração e Geografia conversão exploração também podem ser usadas para gerar rótulos ou esses recursos, removendo a parte de contagem. Exemplos SQL engenharia do recurso adicionais são fornecidos na seção de [exploração de dados e de engenharia de recurso no bloco de anotações IPython](#ipnb) . É mais eficiente para executar consultas de geração do recurso no conjunto de dados completo ou um grande subconjunto dos-lo usando consultas SQL que são executados diretamente no instância de banco de dados do SQL Server. As consultas podem ser executadas no **SQL Server Management Studio**, IPython bloco de anotações ou qualquer ferramenta/ambiente de desenvolvimento que pode acessar o banco de dados local ou remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparar dados para o modelo de construção

A seguinte consulta junções o **nyctaxi\_viagem** e **nyctaxi\_tarifa** tabelas, gera uma classificação binária rótulo **-Oblíquo**, um rótulo de classificação de classe várias **Dica\_classe**e extrai uma amostra aleatória de 1% do conjunto de dados unidas completo. Esta consulta pode ser copiada e colada diretamente no [Azure máquina Learning Studio](https://studio.azureml.net) [Importar dados] [ import-data] módulo para inclusão de dados diretos da instância do banco de dados do SQL Server no Windows Azure. A consulta exclui registros com incorretas (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Engenharia de recurso no bloco de anotações IPython e exploração de dados

Nesta seção, nós executará a exploração de dados e geração de recurso usando consultas Python e SQL no banco de dados do SQL Server criada anteriormente. Um bloco de anotações amostra IPython chamado **machine-Learning-data-science-process-sql-story.ipynb** é fornecido na pasta **Blocos de anotações do exemplo IPython** . Este bloco de anotações também está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A sequência recomendada ao trabalhar com dados big é o seguinte:

- Leia em uma pequena amostra dos dados em um quadro de dados na memória.
- Execute alguns visualizações e explorações usando os dados de amostrados.
- Experimentar usando os dados de amostrados de engenharia de recurso.
- Para maior exploração de dados, manipulação de dados e engenharia de recurso, use Python para emitir consultas SQL diretamente contra o banco de dados do SQL Server no Windows Azure VM.
- Decida o tamanho da amostra a ser usado para a construção de modelo de aprendizado de máquina do Azure.

Quando estiver pronto para prosseguir para o aprendizado de máquina do Windows Azure, você pode:  

1. Salve a consulta SQL final para extrair e os dados de exemplo e copiar colar a consulta diretamente em uma [Importação de dados] [ import-data] módulo no aprendizado de máquina do Azure. Esse método é demonstrado na seção [Modelos de construção no aprendizado de máquina do Azure](#mlmodel) .    
2. Manter os dados de amostrados e engenharia você planeja usar para construir em uma nova tabela de banco de dados de modelo, então usar a nova tabela na [Importação de dados] [ import-data] módulo.

A seguir estão alguns exemplos de engenharia de recurso, a visualização de dados e exploração de dados. Para obter mais exemplos, consulte o bloco de anotações do IPython SQL de amostra na pasta **Blocos de anotações do exemplo IPython** .

#### <a name="initialize-database-credentials"></a>Inicializar as credenciais de banco de dados

Inicialize as configurações de conexão do banco de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Criar a Conexão de banco de dados

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número de linhas e colunas na tabela nyctaxi_trip do relatório

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leitura-em uma amostra de dados pequeno o banco de dados do SQL Server

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo para ler que a tabela de exemplo é 6.492000 segundos  
Número de linhas e colunas recuperado = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatística descritiva

Agora está pronto para explorar os dados de amostrados. Iniciamos com olhando estatísticas descritivas para o **viagem\_distância** (ou qualquer outro) campos:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de plotagem de caixa

Em seguida procuramos plotagem a caixa para a distância de viagem visualizar o quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Plotar #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de plotagem de distribuição

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plotar #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: Barra e linha plotar

Neste exemplo, estamos bin a distância de viagem em cinco lixeiras e visualizar os resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos plotar a distribuição bin acima em uma barra ou plotagem como abaixo da linha

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plotar #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plotar #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: Exemplo de Scatterplot

Mostraremos dispersão entre **viagem\_tempo\_na\_segs** e **viagem\_distância** para ver se há qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plotar #6][6]

Da mesma forma, é possível verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plotar #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Os dados no SQL de amostragem sub-recursos

Quando o modelo de dados de Preparando para construir no [Azure máquina Learning Studio](https://studio.azureml.net), você pode decidir sobre a **consulta SQL usar diretamente no módulo importar dados** ou os dados de engenharia e amostrados em uma nova tabela, que pode ser usado na [Importação de dados] de persistência[ import-data] módulo com um simples * *Selecione* FROM < seu\_novo\_tabela\_nome >**.

Nesta seção, criaremos uma nova tabela para armazenar os dados de amostrados e projetados. Um exemplo de uma consulta SQL direto para a construção de modelo é fornecido na seção de [exploração de dados e de engenharia de recurso no SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Criar uma amostra da tabela e preencha com 1% das tabelas associadas. Descarte tabela primeiro se ela existir.

Nesta seção, podemos associar as tabelas **nyctaxi\_viagem** e **nyctaxi\_tarifa**, extrair uma amostra aleatória de 1% e manter os dados de amostrados um novo nome de tabela **nyctaxi\_um\_%**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados utilizando consultas SQL no bloco de anotações IPython

Nesta seção, nós explore distribuições de dados usando os dados de 1% de amostra que é persistente na nova tabela que criamos acima. Observe que explorações semelhantes podem ser executadas usando as tabelas originais, opcionalmente usando **TABLESAMPLE** para limitar a exploração de amostra ou limitando os resultados para um período de tempo determinado usando o **retirada\_datetime** particiona, conforme ilustrado na seção de [exploração de dados e de engenharia de recurso no SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de recurso utilizando consultas SQL no bloco de anotações IPython

Nesta seção, nós irá gerar novas etiquetas e recursos diretamente usando consultas SQL, operacional na tabela de exemplo 1% criamos na seção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de rótulo: Gerar etiquetas de classe

No exemplo a seguir, geramos dois conjuntos de rótulos a ser usado para modelagem:

1. Binário rótulos de classe **Oblíquo** (prever se uma dica receberá)
2. Rótulos de multiclass **Dica\_classe** (prevendo a bandeja de dica ou o intervalo)

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia do recurso: Recursos de contagem categóricos colunas

Este exemplo transforma um campo categóricos em um campo numérico, substituindo cada categoria com a contagem de seus ocorrências nos dados.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia do recurso: Recursos de bandeja para colunas numérico

Este exemplo transforma a um campo numérico contínuo em intervalos de categoria predefinida, ou seja, transformação de campo numérico em um campo categórica.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Engenharia do recurso: Extrair os recursos de localização de Latitude/Longitude Decimal

Este exemplo divide a representação decimal de um campo latitude e/ou longitude em vários campos de região de granularidade diferente, como, país, cidade, cidade, bloqueio, etc. Observe que os novos campos de geo não são mapeados para locais reais. Para obter informações sobre locais de geocode mapeamento, consulte [Serviços de REST do Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique se o formato final da tabela featurized

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Agora estamos prontos para prosseguir para a construção de modelo e a implantação de modelo no [Aprendizado de máquina do Azure](https://studio.azureml.net). Os dados estão prontos para qualquer um dos problemas a previsão identificados anteriormente, notadamente:

1. Classificação binária: prever ou não uma dica foi pago para uma viagem.

2. Classificação multiclass: para prever o intervalo de dica paga, de acordo com as classes definidas anteriormente.

3. Tarefa de regressão: prever a quantidade de dica pagada por uma viagem.  


## <a name="mlmodel"></a>Modelos de construção no aprendizado de máquina do Azure

Para iniciar o exercício de modelagem, faça logon em seu espaço de trabalho de aprendizado de máquina do Azure. Se você ainda não tiver criado uma espaço de trabalho de aprendizado de máquina, consulte [criar um espaço de trabalho de aprendizagem do Windows Azure máquina](machine-learning-create-workspace.md).

1. Para começar com o aprendizado de máquina do Azure, consulte [Novidades Studio de aprendizagem do Windows Azure máquina?](machine-learning-what-is-ml-studio.md)

2. Faça logon [máquina Azure aprendizagem Studio](https://studio.azureml.net).

3. Studio Home page fornece uma ampla gama de informações, vídeos, tutoriais, links para a referência módulos e outros recursos. Para obter mais informações sobre o aprendizado de máquina do Azure, consulte o [Centro de documentação de aprendizagem do Windows Azure máquina](https://azure.microsoft.com/documentation/services/machine-learning/).

Um experimento típica de treinamento consiste no seguinte:

1. Crie um experimento **+ NEW** .
2. Obtenha os dados para o aprendizado de máquina do Azure.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Dividir os dados entre conjuntos de treinamento / / testes de validação de dados (ou ter conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado para resolver. Por exemplo, classificação binária, classificação multiclass, regressão.
7. Treine um ou mais modelos usando o conjunto de dados de treinamento.
8. Pontuação o conjunto de dados de validação usando o modelo treinados (s).
9. Avalie o modelo (s) para calcular as métricas relevantes para o problema de aprendizado.
10. Contento ajustar o modelo (s) e selecione o modelo melhor implantar.

Neste exercício, podemos ter já explorado projetados os dados no SQL Server e decidir o tamanho da amostra para inclusão no aprendizado de máquina do Azure. Para criar um ou mais dos modelos de previsão decidimos:

1. Obter os dados para o aprendizado de máquina do Windows Azure usando os [Dados de importação] [ import-data] módulo, disponível na seção **dados de entrada e saída** . Para obter mais informações, consulte [Importar dados] [ import-data] página de referência do módulo.

    ![Importar dados de aprendizado de máquina do Azure][17]

2. Selecione **o banco de dados do Windows Azure SQL** como a **fonte de dados** no painel **Propriedades** .

3. Insira o nome DNS do banco de dados no campo **nome do servidor de banco de dados** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Insira o **nome do banco de dados** no campo correspondente.

5. Insira o **nome de usuário do SQL** no **aqccount nome de usuário e a senha no **Server usuário conta senha * *.

6. Marque a opção **aceitar qualquer certificado do servidor** .

7. Na área de texto de edição de **consulta do banco de dados** , colar a consulta que extrai o necessário campos (incluindo quaisquer campos calculados, como os rótulos) do banco de dados e para baixo amostras os dados para o tamanho da amostra desejada.

Um exemplo de um experimento classificação binária lendo dados diretamente do banco de dados do SQL Server é na figura a seguir. Experiências semelhantes podem ser construídas para classificação multiclass e problemas de regressão.

![Treinar de aprendizado de máquina do Azure][10]

> [AZURE.IMPORTANT] Os dados de modelagem extração e exemplos de consultas de amostragem fornecidos nas seções anteriores, **todos os rótulos para os exercícios de três modelagem estão incluídos na consulta**. Uma etapa importante de (obrigatório) em cada um dos exercícios modelagem é para **Excluir** os rótulos desnecessários para os outros dois problemas e quaisquer outros **vazamento de destino**. Por exemplo, ao usar classificação binary, use o rótulo **-Oblíquo** e exclua os campos **Dica\_classe**, **Dica\_quantidade**, e **total\_quantidade**. O último é vazamento de destino já que eles implicam a dica pagos.
>
> Para excluir as colunas desnecessárias e/ou vazamento de destino, você poderá usar as [Selecionar colunas no conjunto de dados] [ select-columns] módulo ou os [Metadados de editar][edit-metadata]. Para obter mais informações, consulte [Selecionar colunas no conjunto de dados] [ select-columns] e [Metadados Editar] [ edit-metadata] páginas de referência.

## <a name="mldeploy"></a>Implantando modelos no aprendizado de máquina do Azure

Quando seu modelo estiver pronto, você pode facilmente implantá-lo como um serviço web diretamente do experimento. Para obter mais informações sobre como implantar serviços da web de aprendizado de máquina do Azure, consulte [Deploy um serviço web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implantar um novo web service, você precisa:

1. Crie um experimento pontuação.
2. Implante o serviço web.

Para criar um experimento pontuação de um experimento de treinamento **concluído** , clique em **Criar PONTUAÇÃO EXPERIMENTAR** na barra de ação inferior.

![Pontuação Azure][18]

Aprendizado de máquina Azure tentará criar um experimento pontuação com base nos componentes da experiência treinamento. Especificamente, ele será:

1. Salvar o modelo e exibir treinado e remova os módulos de treinamento do modelo.
2. Identifica uma **entrada de porta** lógica para representar o esquema de entrada de dados esperados.
3. Identifica uma lógica **porta de saída** para representar o esquema de saída do serviço web esperado.

Quando o pontuação experimento é criado, revisá-lo e ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou a consulta com um que exclui os campos de rótulo, à medida que eles não estará disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho da entrada do conjunto de dados e/ou a consulta para alguns registros, apenas o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum para excluir entrados todos os campos e incluir apenas os **Rótulos marcados** e **Probabilidades marcados** na saída usando as [Selecionar colunas em Dataset] [ select-columns] module.

Uma amostra da pontuação experimento é na figura a seguir. Quando estiver pronto para implantar, clique no botão **Publicar WEB SERVICE** na barra de ação inferior.

![Publicar de aprendizado de máquina do Azure][11]

Recapitulando, neste tutorial passo a passo, você criou um ambiente de ciência de dados do Azure, trabalhado com um grande conjunto de dados público totalmente de aquisição de dados para modelar o treinamento e a implantação de um serviço web de aprendizado de máquina do Windows Azure.

### <a name="license-information"></a>Informações de licença

Este passo a passo de amostra e seu complementar scripts e IPython notebook(s) são compartilhados por Microsoft sob a licença MIT. Verifique o arquivo License. txt no diretório do código de exemplo no GitHub para obter mais detalhes.

### <a name="references"></a>Referências

• a [página de Download de ida e volta NYC Taxi Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
• [Acabando com dados de viagem do NYC Taxi por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi Limousine comissão pesquisa e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
