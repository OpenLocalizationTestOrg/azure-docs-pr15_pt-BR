<properties
    pageTitle="O processo de ciência de dados de equipe em ação: usando SQL Data Warehouse | Microsoft Azure"
    description="Processo de análise avançada e tecnologia em ação"  
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
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>O processo de ciência de dados de equipe em ação: usando SQL Data Warehouse

Neste tutorial, vamos orientá-lo a criar e implantar uma modelo usando SQL Data Warehouse (SQL DW) de aprendizado de máquina para um conjunto de dados publicamente disponível – dataset [NYC táxi viagens](http://www.andresmh.com/nyctaxitrips/) . O modelo de classificação binária construído prevê estando ou não uma dica é pago por uma viagem e modelos de classificação multiclass e regressão também são discutidos que prever a distribuição dos valores de dica pago.

O procedimento a segue o fluxo de trabalho do [Processo de ciência de dados da equipe (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Vamos mostrar como configurar um ambiente de ciência de dados, como carregar os dados no SQL DW e como usar SQL DW ou um bloco de anotações de IPython para explorar os dados e engenharia reversa recursos ao modelo. Em seguida, mostraremos como criar e implantar um modelo com aprendizado de máquina do Azure.


## <a name="dataset"></a>O conjunto de dados NYC táxi viagens

Os dados de viagem de táxi NYC consistem em cerca de 20GB de arquivos CSV compactados (~ 48GB descompactado), mais de milhões de 173 individuais viagens e as tarifas de gravação pago para cada viagem. Cada registro de viagem inclui os locais retirada e redistribuição e horários, número de licença anonymized alteração (driver) e o número de medallion (identificação exclusiva do táxi). Os dados abrange todas as viagens no ano 2013 e são fornecidos em seguintes dois conjuntos de dados para cada mês:

1. O arquivo **trip_data.csv** contém detalhes de viagem, como o número de passageiros, retirada e redução pontos, duração da viagem e comprimento de viagem. Aqui estão alguns exemplos de registros:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. O arquivo **trip_fare.csv** contém detalhes da tarifa paga para cada viagem, como o tipo de pagamento, quantidade de tarifa, sobrecarga e impostos, dicas e pedágio e o valor total pago. Aqui estão alguns exemplos de registros:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A **chave exclusiva** usada para participar de viagem\_dados e viagem\_passagens é composta dos seguintes três campos:

- medallion,
- acessar ilegalmente\_licença e
- retirada\_datetime.

## <a name="mltasks"></a>Três tipos de tarefas de previsão de endereço

Podemos Formula três problemas de previsão com base na *Dica\_quantidade* para ilustrar três tipos de tarefas de modelagem:

1. **Classificação binária**: prever ou não uma dica foi pago para uma viagem, ou seja, um *Dica\_quantidade* maior de r $0 é um exemplo positivo, enquanto um *Dica\_quantidade* de r $0 é um exemplo negativo.

2. **Classificação multiclass**: prever o intervalo de dica pagado a viagem. Dividimos o *Dica\_quantidade* em cinco compartimentos ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tarefa de regressão**: prever a quantidade de dica pagada por uma viagem.  


## <a name="setup"></a>Configurar o ambiente de ciência de dados do Azure para análise avançada

Para configurar seu ambiente de ciência de dados do Azure, siga estas etapas.

**Criar sua própria conta de armazenamento de blob do Microsoft Azure**

- Quando você provisionar o seu próprio armazenamento de blob do Microsoft Azure, escolha uma localização geográfica para seu armazenamento de blob do Microsoft Azure em ou mais próximo possível ao **Centro-Sul dos EUA**, que é onde os dados de táxi NYC estão armazenados. Os dados serão copiados usando AzCopy a partir do contêiner de armazenamento de blob pública em um contêiner em sua própria conta de armazenamento. Quanto mais perto seu armazenamento de blob do Microsoft Azure é Centro Sul dos EUA, mais rápido esta tarefa (etapa 4) será concluída.
- Para criar sua própria conta de armazenamento do Azure, siga as etapas descritas no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Certifique-se de fazer anotações sobre os valores seguintes credenciais de conta de armazenamento, pois eles serão necessário posteriormente neste passo a passo.

  - **Nome da conta de armazenamento**
  - **Chave da conta de armazenamento**
  - **Nome de contêiner** (o que você deseja que os dados sejam armazenados no armazenamento de blob do Microsoft Azure)

**Provisione sua instância de DW do SQL Azure.**
Siga a documentação em [criar um depósito de dados do SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para provisionar uma instância do SQL Data Warehouse. Certifique-se de que você faça notações nas seguintes credenciais SQL Data Warehouse que serão usadas em etapas posteriores.

  - **Nome do servidor**: <server Name>. database.windows.net
  - **Nome SQLDW (banco de dados)**
  - **Nome de usuário**
  - **Senha**

**Instale o Visual Studio 2015 e ferramentas de dados do SQL Server.** Para obter instruções, consulte [instalar o Visual Studio 2015 e/ou SSDT (ferramentas de dados do SQL Server) para depósito de dados do SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Conecte a sua DW SQL Azure com o Visual Studio.** Para obter instruções, consulte as etapas 1 e 2 em [Conectar depósito de dados do SQL Azure com o Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Execute a seguinte consulta SQL no banco de dados que você criou no SQL Data Warehouse (em vez de consulta fornecida na etapa 3 do tópico conectar,) para **criar uma chave mestre**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crie um espaço de trabalho de aprendizado de máquina do Azure em sua assinatura do Azure.** Para obter instruções, consulte [criar um espaço de trabalho de aprendizado de máquina do Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Carregar os dados em um depósito de dados do SQL

Abra um console de comando do Windows PowerShell. Executar o PowerShell seguinte comandos para baixar o exemplo SQL arquivos que podemos compartilhar com você no Github em um diretório local que você especificar com o parâmetro de script *- Dirdestino*. Você pode alterar o valor do parâmetro *-Dirdestino* para qualquer diretório local. Se *- Dirdestino* não existir, ele será criado pelo script PowerShell.

>[AZURE.NOTE] Talvez seja necessário **Executar como administrador** ao executar o seguinte script do PowerShell se seu diretório *dirdestino* precisa privilégio de administrador para criar ou gravar nela.

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Após a execução bem-sucedida, seu diretório de trabalho atual é alterado para *- Dirdestino*. Você deve ser capaz de ver a tela, como abaixo:

![][19]

No seu *-Dirdestino*, execute o seguinte script do PowerShell no modo de administrador:

    ./SQLDW_Data_Import.ps1

Quando o script do PowerShell é executado pela primeira vez, você será solicitado a inserir as informações do seu DW do SQL Azure e sua conta de armazenamento de blob do Microsoft Azure. Quando concluir este script do PowerShell executando pela primeira vez, as credenciais de entrada que você será foram gravada para um arquivo de configuração SQLDW.conf no diretório de trabalho presente. Executar futuro desse arquivo de script do PowerShell tem a opção de ler que todas as necessárias parâmetros deste arquivo de configuração. Se você precisar alterar alguns parâmetros, você pode escolher para os parâmetros na tela ao prompt de entrada excluindo esse arquivo de configuração e inserindo os valores de parâmetros conforme solicitado ou alterar os valores de parâmetro editando o arquivo de SQLDW.conf no diretório *- Dirdestino* .

>[AZURE.NOTE] Para evitar conflitos de nome de esquema com aqueles que já existem no seu DW do SQL Azure, ao ler parâmetros diretamente do arquivo SQLDW.conf, um número aleatório de 3 dígitos é adicionado ao nome do esquema do arquivo SQLDW.conf o nome de esquema padrão para cada execução. O script do PowerShell pode solicitar um nome de esquema: o nome pode ser especificado critério de usuário.

Este arquivo de **script do PowerShell** conclui as seguintes tarefas:

- **Baixa e instala AzCopy**, se AzCopy já não está instalado

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Copia dados para sua conta de armazenamento de blob particular** o blob público com AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Dados de cargas usando Polybase (executando LoadDataToSQLDW.sql) para seu DW do SQL Azure** da sua conta de armazenamento de blob particular com os seguintes comandos.

    - Criar um esquema

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Criar uma credencial de banco de dados com escopo

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Criar uma fonte de dados externa para um blob de armazenamento do Azure

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Crie um formato de arquivo externo para um arquivo csv. Dados são compactados e os campos são separados com o caractere de barra vertical.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Crie passagens externas e tabelas de viagem para NYC táxi dataset no armazenamento de blob do Microsoft Azure.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Carregar dados de tabelas externas em armazenamento de blob do Microsoft Azure depósito de dados do SQL

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Criar uma tabela de dados de exemplo (NYCTaxi_Sample) e inserir dados-lo do selecionando consultas SQL nas tabelas viagem e passagens. (Algumas etapas desta explicação precisa usar esta tabela de exemplo.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

A localização geográfica de suas contas de armazenamento afeta os tempos de carregamento.

>[AZURE.NOTE] Dependendo da localização geográfica da sua conta de armazenamento de blob particular, o processo de copiar dados de um blob pública à sua conta de armazenamento particular pode levar cerca de 15 minutos, ou mais, e o processo de carregamento de dados da sua conta de armazenamento para seus DW do SQL Azure pode levar 20 minutos ou mais.  

Você precisará decidir o que fazer se você tiver arquivos duplicados de origem e destino.

>[AZURE.NOTE] Se o arquivo. csv arquivos para serem copiados do armazenamento de blob pública à sua conta de armazenamento de blob particular já existem na sua conta de armazenamento de blob particular, AzCopy perguntará se você deseja substituí-los. Se você não deseja substituí-los, entrada **n** , quando solicitado. Se você deseja sobrescrever **todos** eles, entrada **um** quando solicitado. Você também pode entrada **y** para substituir arquivos. csv individualmente.

![Plotar #21][21]

Você pode usar seus próprios dados. Se seus dados estiverem em sua máquina no local em seu aplicativo de vida real, você ainda pode usar AzCopy para carregar dados no local em seu armazenamento de blob do Microsoft Azure particular. Você só precisa alterar o local de **origem** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando AzCopy do arquivo de script do PowerShell para o diretório local que contém os dados.


>[AZURE.TIP] Se seus dados já estiverem em seu armazenamento de blob do Microsoft Azure privadas em seu aplicativo de vida real, você pode ignorar a etapa AzCopy no script PowerShell e diretamente carregar os dados em DW do SQL Azure. Isso exigirá edições adicionais do script ajustá-los para o formato dos dados.


Este script do Powershell também encaixa nas informações do Azure SQL DW os arquivos de exemplo de exploração de dados SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py para que esses três arquivos são prontos para ser testada instantaneamente após o script do PowerShell.

Após a execução com êxito, você verá a tela, como abaixo:

![][20]

## <a name="dbexplore"></a>Exploração de dados e engenharia de recurso no armazém de dados do SQL Azure

Nesta seção, podemos executar exploração de dados e geração de recurso executando consultas SQL contra DW do SQL Azure diretamente usando **Ferramentas de dados do Visual Studio**. Todas as consultas SQL usadas nesta seção podem ser encontradas no exemplo de script chamado *SQLDW_Explorations.sql*. Este arquivo já foi baixado para seu diretório local pelo script PowerShell. Você também poderá recuperá-la do [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mas o arquivo no Github não tem as informações de DW do SQL Azure conectadas.

Conectar-se a sua DW do SQL Azure usando o Visual Studio com o nome de login de DW SQL e a senha e abra o **Explorador de objetos SQL** para confirmar que as tabelas e o banco de dados foram importadas. Recupere o arquivo *SQLDW_Explorations.sql* .

>[AZURE.NOTE] Para abrir um editor de consulta de depósito de dados paralela (PDW), use o comando **Nova consulta** enquanto seu PDW é selecionada no **SQL Object Explorer**. O editor de consulta SQL padrão não é suportado pelo PDW.

Aqui estão os tipos de dados tarefas de geração de recurso e exploração executadas nesta seção:

- Explore distribuições de dados de alguns campos no windows variáveis de tempo.
- Investigar qualidade de dados dos campos longitude e latitude.
- Gerar etiquetas de classificação binárias e multiclass com base na **Dica\_quantidade**.
- Gerar recursos e computação/comparar distância de viagem.
- Associe as duas tabelas e extraia uma amostra aleatória que será usada para criar modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados

Essas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas preenchidas anteriormente usando massa paralela do Polybase importar,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Saída:** Você deve obter 173,179,759 linhas e colunas de 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion

Essa consulta de exemplo identifica os medallions (táxi números) que concluídas mais de 100 viagens dentro de um período de tempo especificado. A consulta seria beneficiar o acesso de tabela particionada desde que ele está condicionado pelo esquema de partição de **retirada\_datetime**. Consultar o conjunto de dados completo também fará com que use da tabela particionada e/ou verificação de índice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Saída:** A consulta deve retornar uma tabela com linhas especificando os 13,369 medallions (táxis) e o número de viagem concluída por-los no 2013. A última coluna contém a contagem do número de viagens concluída.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem por medallion e hack_license

Este exemplo identifica os medallions (táxi números) e hack_license números (drivers) que concluídas mais de 100 viagens dentro de um período de tempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Saída:** A consulta deve retornar uma tabela com 13,369 linhas especificando o carro/driver 13,369 IDs que concluiu mais que viagens 100 no 2013. A última coluna contém a contagem do número de viagens concluída.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: verificar registros com longitude incorreto e/ou latitude

Este exemplo investiga se qualquer um dos campos longitude e/ou latitude conter um valor inválido (graus de radian devem estar entre -90 e 90), ou ter (0, 0) coordenadas.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Saída:** A consulta retorna 837,467 viagens com campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Oblíquo versus distribuição de viagens não Oblíquo

Este exemplo localiza o número de viagens que foram Oblíquo versus o número que não foram Oblíquo em um período de tempo especificado (ou no dataset completo se abrangendo todo o ano como ela está configurada aqui). Essa distribuição reflete a distribuição de rótulo binário a ser usada mais tarde para modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Saída:** A consulta deve retornar Oblíquo as seguintes frequências de dica para o ano de 2013: 90,447,622 e 82,264,709 não ponta.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: Distribuição de classe/intervalo de dica

Este exemplo calcula a distribuição dos intervalos de dica em um determinado período de tempo (ou no dataset completo se abrangendo o ano completo). Esta é a distribuição das classes rótulo que será usado posteriormente para modelagem de classificação multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Saída:**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Calcular e comparar a distância de viagem

Este exemplo converte a longitude retirada e redistribuição e latitude para geografia SQL aponta, calcula a distância de viagem usando diferença de pontos de Geografia SQL e retorna uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados válidos coordenadas usando somente a consulta de avaliação de qualidade de dados coberta anteriormente.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Engenharia de recurso usando funções SQL

Às vezes funções SQL podem ser uma opção eficiente para engenharia de recurso. Este passo a passo, definimos uma função SQL para calcular a distância direta entre os locais retirada e redução. Você pode executar os seguintes scripts SQL no **Visual Studio Tools de dados**.

Veja aqui o script SQL que define a função de distância.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Aqui está um exemplo para chamar esta função para gerar recursos em sua consulta SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Saída:** Essa consulta gera uma tabela (com 2,803,538 linhas) com retirada e redução latitudes e longitudes e a distância direta correspondente em milhas. Aqui estão os resultados para 3 primeiras linhas:

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Preparar dados para a construção de modelo

A seguinte consulta junções a **nyctaxi\_viagem** e **nyctaxi\_passagens** tabelas, gera uma classificação binária rótulo **Oblíquo**, um rótulo de classificação de classe vários **Dica\_classe**e extrai uma amostra do dataset Unido completo. A amostragem é feita recuperando um subconjunto das viagens com base no tempo de recebimento.  Essa consulta pode ser copiada e colada diretamente na [Studio de aprendizado de máquina do Azure](https://studio.azureml.net) [Importar dados] [ import-data] módulo para inclusão de dados diretos da instância de banco de dados SQL no Azure. A consulta exclui registros com incorreto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Quando você estiver pronto para prosseguir para aprendizado de máquina do Azure, você pode:  

1. Salvar a consulta SQL final para extrair os dados de exemplo e copiar colar a consulta diretamente em um [Importar dados] [ import-data] módulo no aprendizado de máquina do Azure, ou
2. Manter os dados de amostrados e engenharia você planeja usar para o modelo de construção em uma nova tabela do SQL DW e use a nova tabela [Importar dados] [ import-data] módulo no aprendizado de máquina do Azure. O script do PowerShell na etapa anterior tiver feito isso para você. Você pode ler diretamente desta tabela no módulo importar dados.


## <a name="ipnb"></a>Engenharia de recurso no bloco de anotações de IPython e exploração de dados

Nesta seção, podemos executará exploração de dados e geração de recurso usando os dois Python e consultas SQL DW SQL criado anteriormente. Um bloco de anotações de IPython de amostra chamado **SQLDW_Explorations.ipynb** e um arquivo de script de Python **SQLDW_Explorations_Scripts.py** tiverem sido descarregadas para seu diretório local. Eles também estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Esses dois arquivos são idênticos em scripts Python. O arquivo de script Python é fornecido a você, caso você não tiver um servidor de bloco de anotações de IPython. Esses dois arquivos Python amostra destinam-se em **Python 2.7**.

As informações de DW do SQL Azure necessárias na amostra bloco de anotações de IPython e o arquivo de script Python baixadas para o computador local foi conectadas pelo script PowerShell anteriormente. Eles são executáveis sem qualquer modificação.

Se você já tiver configurado um espaço de trabalho de AzureML, diretamente carregar a amostra de bloco de anotações de IPython para o serviço de bloco de anotações de IPython AzureML e comece a executá-lo. Aqui estão as etapas para carregar no serviço de bloco de anotações de IPython AzureML:

1. Efetuar login no seu espaço de trabalho de AzureML, clique em "Studio" na parte superior e clique em "Blocos de anotações" no lado esquerdo da página da web.

    ![Plotar #22][22]

2. Clique em "Novo" no canto inferior esquerdo da página da web e selecione "Python 2". Em seguida, forneça um nome para o bloco de anotações e clique na marca de seleção para criar novo bloco de anotações IPython em branco.

    ![Plotar #23][23]

3. Clique no símbolo de "Jupyter" no canto superior esquerdo do novo bloco de anotações IPython.

    ![Plotar #24][24]

4. Arraste e solte a amostra de bloco de anotações de IPython para a página de **árvore** do seu serviço de bloco de anotações de IPython AzureML e clique em **carregar**. Em seguida, a amostra de bloco de anotações de IPython será carregada para o serviço de bloco de anotações de IPython AzureML.

    ![Plotar #25][25]

Para executar a amostra de bloco de anotações de IPython ou Python script arquivo, as seguintes Python pacotes são necessários. Se você estiver usando o serviço de bloco de anotações de IPython AzureML, esses pacotes foram previamente instalados.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

A sequência recomendada durante a criação de soluções analíticas avançadas em AzureML com dados de grandes é o seguinte:

- Lido em uma pequena amostra dos dados em um quadro de dados na memória.
- Realize algumas visualizações e explorações usando os dados de amostrados.
- Experimentar usando os dados de amostrados de engenharia de recurso.
- Para maior exploração de dados, manipulação de dados e engenharia de recurso, use Python para emitir consultas SQL diretamente contra DW SQL.
- Decida o tamanho da amostra seja adequado para a construção de modelo de aprendizado de máquina do Azure.

A seguir está alguns dados exploração, visualização de dados e exemplos de engenharia do recurso. Mais explorações de dados podem ser encontradas na amostra de bloco de anotações de IPython e o arquivo de script de Python de amostra.

### <a name="initialize-database-credentials"></a>Inicializar credenciais de banco de dados

Inicialize suas configurações de conexão de banco de dados nas variáveis a seguir:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Criar a conexão de banco de dados

Aqui está a cadeia de conexão que cria a conexão ao banco de dados.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número do relatório de linhas e colunas na tabela < nyctaxi_trip >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Número do relatório de linhas e colunas na tabela < nyctaxi_fare >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Leitura de uma amostra de dados pequeno do banco de dados de depósito de dados SQL

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo para ler que a tabela de exemplo é 14.096495 segundos.  
Número de linhas e colunas recuperado = (1000, 21).

### <a name="descriptive-statistics"></a>Estatística descritiva

Agora você está pronto para explorar os dados de amostrados. Vamos começar examinando alguns Estatística descritiva para o **viagem\_distância** (ou todos os demais campos que você escolher se quer especificar).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de plotagem de caixa

Avançar examinaremos a plotagem de caixa para a distância de viagem visualizar o quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Plotar #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de plotagem de distribuição

Plotar que visualize a distribuição e um histograma para a distância de viagem de amostra.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plotar #2][2]

### <a name="visualization-bar-and-line-plots"></a>Visualização: Barras e linhas plotar

Neste exemplo, estamos compartimento a distância de viagem em compartimentos de cinco e visualizar os resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos plotar a distribuição de compartimento acima em uma barra ou linha plotagem com:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plotar #3][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plotar #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: Exemplos de Scatterplot

Vamos mostrar dispersão entre **viagem\_tempo\_na\_seg** e **viagem\_distância** para ver se há qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plotar #6][6]

Da mesma forma, é possível verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plotar #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados nos dados de amostras usando consultas SQL no bloco de anotações de IPython

Nesta seção, exploraremos distribuições de dados usando os dados de amostrados que são mantidos na nova tabela que criamos acima. Observe que explorações semelhantes podem ser realizadas usando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: Número de linhas e colunas na tabela amostra de relatórios

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: Oblíquo/não ultrapassado distribuição

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploração: Distribuição de classe de dica

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploração: Plotar a distribuição de dica por classe

    tip_class_dist['tip_freq'].plot(kind='bar')

![Plotar #26][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: Distribuição de viagem por licença medallion e alteração

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploração: Distribuição de tempo de viagem

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploração: Distribuição de distância de viagem

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploração: Distribuição de tipo de pagamento

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique se o formulário final da tabela featurized

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Criar modelos no aprendizado de máquina do Azure

Podemos agora está prontos para prosseguir para a construção de modelo e implantação do modelo em [Aprendizado de máquina do Azure](https://studio.azureml.net). Os dados estão prontos para ser usado em qualquer um dos problemas previsão identificados anteriormente, ou seja:

1. **Classificação binária**: prever ou não uma dica foi paga uma viagem.

2. **Classificação multiclass**: prever o intervalo de dica paga, de acordo com as classes definidas anteriormente.

3. **Tarefa de regressão**: prever a quantidade de dica pagada por uma viagem.  



Para iniciar o exercício de modelagem, faça logon em seu espaço de trabalho de **Aprendizado de máquina do Azure** . Se você ainda não tiver criado uma espaço de trabalho de aprendizado de máquina, consulte [criar um espaço de trabalho do Azure ML](machine-learning-create-workspace.md).

1. Para começar a usar aprendizado de máquina do Azure, consulte [o que é Azure Studio de aprendizado de máquina?](machine-learning-what-is-ml-studio.md)

2. Faça logon no [Studio de aprendizado de máquina Azure](https://studio.azureml.net).

3. Página inicial de Studio fornece uma grande variedade de informações, vídeos, tutoriais, links para a referência de módulos e outros recursos. Para obter mais informações sobre o aprendizado de máquina do Azure, consulte o [Centro de documentação de aprendizado de máquina Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Um experimento treinamento típico consiste as seguintes etapas:

1. Crie um experimento **+ novo** .
2. Obter os dados para o Azure ML.
3. Pré-processar, transformação e manipular os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Dividir os dados entre conjuntos de dados de treinamento / / testes de validação (ou ter conjuntos de dados separados para cada).
6. Selecione um ou mais algoritmos de aprendizado de máquina dependendo o problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação multiclass, regressão.
7. Treine um ou mais modelos usando o dataset de treinamento.
8. Pontuação dataset validação usando os modelos de treinamento.
9. Avalie os modelos para calcular as métricas relevantes para o problema de aprendizado.
10. Fino ajustar os modelos e selecione o melhor modelo para implantar.

Neste exercício, podemos ter já explorados engenharia os dados no depósito de dados do SQL e decidir sobre o tamanho da amostra para inclusão no Azure ML. Este é o procedimento para criar um ou mais dos modelos de previsão:

1. Trazer os dados para ML Azure usando os [Dados de importação] [ import-data] módulo, disponível na seção **dados de entrada e saída** . Para obter mais informações, consulte [Importar dados] [ import-data] página de referência de módulo.

    ![Azure ML importar dados][17]

2. Selecione **o banco de dados do Azure SQL** como a **fonte de dados** no painel **Propriedades** .

3. Insira o nome DNS do banco de dados no campo **nome do servidor de banco de dados** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Insira o **nome do banco de dados** no campo correspondente.

5. Insira o *nome de usuário do SQL* no **nome de conta de usuário do servidor**e a *senha* a **senha de conta de usuário do servidor**.

6. Marque a opção **aceitar qualquer certificado do servidor** .

7. Na área de texto de edição de **consulta de banco de dados** , colar a consulta que extrai o necessário campos (incluindo quaisquer campos calculados como os rótulos) de banco de dados e para baixo amostras os dados que o tamanho da amostra desejada.

Um exemplo de um experimento de classificação binária ler dados diretamente do banco de dados SQL Data Warehouse é na figura abaixo (Lembre-se substituir o nyctaxi_trip de nomes de tabela e nyctaxi_fare pelo nome do esquema e os nomes de tabela que você usou sua explicação). Experiências semelhantes podem ser construídas para classificação multiclass e problemas de regressão.

![Azure trem ML][10]

> [AZURE.IMPORTANT] Nos dados de modelagem extração e exemplos de consulta de amostragem fornecido nas seções anteriores, **todas as etiquetas para os exercícios de modelagem de três incluídas na consulta**. Uma etapa importante (obrigatório) em cada um dos exercícios modelagem é **Excluir** as etiquetas desnecessárias para os outros dois problemas e quaisquer outros **vazamentos de destino**. Por exemplo, ao usar classificação binária, use a etiqueta **Oblíquo** e excluir os campos **Dica\_classe**, **Dica\_quantidade**, e **total\_quantidade**. O último é perdas de destino desde que eles sugerem a dica pago.
>
> Para excluir qualquer colunas desnecessárias ou perdas de destino, você pode usar [Selecionar colunas em Dataset] [ select-columns] módulo ou [Editar metadados][edit-metadata]. Para obter mais informações, consulte [Selecionar colunas em Dataset] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.

## <a name="mldeploy"></a>Implantar modelos no aprendizado de máquina do Azure

Quando seu modelo estiver pronto, você pode facilmente implantá-lo como um serviço web diretamente do experimento. Para obter mais informações sobre a implantação do Azure ML web services, consulte [implantar um serviço da web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implantar um novo serviço web, você precisa:

1. Crie um experimento pontuação.
2. Implante o serviço da web.

Para criar um experimento pontuação de um experimento de treinamento **concluído** , clique em **Criar PONTUAÇÃO EXPERIMENTAR** na barra de ação inferior.

![Pontuação Azure][18]

Azure aprendizado de máquina tentará criar um experimento pontuação com base nos componentes da experiência de treinamento. Em particular, ele será:

1. Salve o modelo de treinamento e remova os módulos de treinamento do modelo.
2. Identifica uma lógica **porta de entrada** para representar o esquema de dados de entrada esperado.
3. Identifica uma lógica **porta de saída** para representar o esquema de saída do serviço web esperado.

Quando o experimento pontuação for criado, revise e faça o ajuste conforme necessário. Um típico ajuste é substituir o dataset de entrada e/ou a consulta com uma que exclui campos de rótulo, como eles não estará disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho do dataset de entrada e/ou a consulta para alguns registros, apenas o suficiente para indicar que o esquema de entrada. Para a porta de saída, é comum para excluir entrados todos os campos e incluir apenas os **Rótulos de marcou** e **Probabilidades marcou** na saída usando as [Selecionar colunas em Dataset] [ select-columns] módulo.

Uma amostra pontuação experimento é fornecida na figura abaixo. Quando estiver pronto para implantar, clique no botão **Publicar serviço da WEB** na barra de ação inferior.

![Publicar ML Azure][11]


## <a name="summary"></a>Resumo
Recapitulando o que fizemos neste tutorial passo a passo, você criou um ambiente de ciências dados Azure, trabalhado com um conjunto de dados grande público, fazendo-lo através do processo de ciência de dados equipe, totalmente de aquisição de dados para modelar treinamento e, em seguida, para a implantação de um serviço da web de aprendizado de máquina do Azure.

### <a name="license-information"></a>Informações de licença

Este exemplo passo a passo e seu que acompanha scripts e IPython notebook(s) são compartilhados pela Microsoft sob a licença do MIT. Verifique o arquivo License no diretório de código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências

• [Página de Download do Andrés Monroy NYC táxi viagens](http://www.andresmh.com/nyctaxitrips/)  
• [Acabando com dados de viagem de táxi do NYC por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC táxi, pesquisa de comissão de Limousine e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
