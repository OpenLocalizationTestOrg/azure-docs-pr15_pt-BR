<properties
   pageTitle="Analisar e documentos de processo JSON com seção no HDInsight | Microsoft Azure"
   description="Saiba como usar JSON documentos e analisá-los usando a seção no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Processar e analisar documentos JSON usando a seção no HDInsight

Saiba como processar e analisar os arquivos JSON usando a seção no HDInsight. O seguinte documento JSON será usado no tutorial

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

O arquivo pode ser encontrado em wasbs://processjson@hditutorialdata.blob.core.windows.net/. Para obter mais informações sobre como usar o armazenamento de Blob do Azure com HDInsight, consulte [usar compatível com o HDFS Azure Blob armazenamento com Hadoop em HDInsight](hdinsight-hadoop-use-blob-storage.md). Você pode copiar o arquivo para o recipiente padrão do cluster se desejar.

Neste tutorial, você usará o console de seção.  Para obter instruções de abertura do console de seção, consulte [Usar seção com Hadoop em HDInsight com a área de trabalho remota](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Nivelar documentos JSON

Os métodos listados na próxima seção exigem o documento JSON em uma única linha. Portanto, você deve Nivelar documento JSON para uma cadeia de caracteres. Se seu documento JSON já ser nivelado, você pode ignorar esta etapa e ir direto para a próxima seção em dados JSON analisando.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

O arquivo JSON bruto está localizado em **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. A tabela *StudentsRaw* seção aponta para o documento JSON cancelamento plana bruto.

A tabela *StudentsOneLine* seção armazena os dados no sistema de arquivos HDInsight padrão sob o caminho */json/alunos /* .

A instrução INSERT popular a tabela de StudentOneLine com os dados JSON plana.

A instrução SELECT só deve retornar 1 linha.

Aqui está a saída da instrução SELECT:

![Nivelamento do documento JSON.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analisar JSON documentos na seção

Seção fornece três mecanismos diferentes para executar consultas em documentos JSON:

- Use a\_JSON\_objeto UDF (função definida pelo usuário)
- Use o UDF JSON_TUPLE
- usar SerDe personalizado
- Escreva que pertence UDF usando Python ou outros idiomas. Consulte [Este artigo] [ hdinsight-python] sobre como executar seu próprio código Python com seção.

### <a name="use-the-getjsonobject-udf"></a>Use a\_JSON_OBJECT UDF
Seção fornece que uma UDF interna chamada [obter objeto json](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que podem executar JSON consultando durante o tempo de execução. Este método leva dois argumentos – o nome da tabela e o nome do método que tem o documento JSON plana e o campo JSON que precisa ser analisado. Vamos examinar um exemplo para ver como funciona este UDF.

Obtenha o nome e sobrenome para cada aluno

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Aqui está a saída quando executar essa consulta na janela do console.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Existem algumas limitações do UDF get-json_object.

- Como cada campo na consulta requer análise novamente a consulta, ele afeta o desempenho.
- OBTER\_JSON_OBJECT() retorna a representação de cadeia de caracteres de uma matriz. Para converter isso em uma matriz de seção, será necessário usar expressões regulares para substituir os colchetes ' [' e ']' e, em seguida, também chamar divisão para obter a matriz.


É por isso o wiki de seção recomenda usando json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Use o UDF JSON_TUPLE

Outro UDF fornecida pela seção é chamado [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) que executa melhor do que [get _ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método usa um conjunto de chaves e uma cadeia de caracteres JSON e retorna uma tupla de valores usando uma função. A seguinte consulta retorna a identificação de aluno e a grade do documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

A saída deste script no console de seção:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLA usa a sintaxe de [modo de exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) na seção que permite json\_tupla para criar uma tabela virtual aplicando a função UDT para cada linha da tabela original.  JSONs complexos ficam muito confusa devido o uso repetido de modo de exibição LATERAL. Além disso, JSON_TUPLE não pode manipular JSONs aninhadas.


###<a name="use-custom-serde"></a>Usar SerDe personalizado

SerDe é a melhor opção para analisar documentos JSON aninhados, ele permite que você definir o esquema JSON e usar o esquema para analisar os documentos. Neste tutorial, você usará um do SerDe mais popular que foi desenvolvido pela [rcongiu](https://github.com/rcongiu).

**Para usar o SerDe personalizado:**

1. Instale o [Kit de desenvolvimento Sudeste Java 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Escolha a versão do Windows X64 do JDK se você vai estar usando a implantação do Windows do HDInsight

    >[AZURE.WARNING] JDK 1.8 não funciona com este SerDe.

    Após a instalação estiver concluída, adicione uma nova variável de ambiente de usuário:

    1. Abra o **modo de exibição de configurações avançadas do sistema** na tela do Windows.
    2. Clique em **variáveis de ambiente**.  
    3. Adicione que uma nova variável de ambiente **JAVA_HOME** está apontando para **C:\Program Files\Java\jdk1.7.0_55** ou onde seu JDK está instalado.

    ![Configuração de valores de configuração correta de JDK][image-hdi-hivejson-jdk]

2. Instalar o [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Adicionar pasta bin ao caminho, indo para o controle painel--> Editar as variáveis de sistema para as variáveis de Environment de conta. Captura de tela abaixo mostra como fazer isso.

    ![Configurando Maven][image-hdi-hivejson-maven]

3. Clone o projeto de [Seção-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github site. Você pode fazer isso clicando no botão "Download Zip" conforme mostrado na captura de tela abaixo.

    ![Clonagem do projeto][image-hdi-hivejson-serde]

4: vá para a pasta onde você baixou este e o pacote tipo "mvn". Isso deve criar os arquivos jar necessários que você pode copiar ao cluster.

5: vá para a pasta de destino na pasta raiz onde você baixou o pacote. Carregue o arquivo json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar cabeça-nó do cluster. Eu geralmente colocá-lo na pasta binário seção: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin ou algo semelhante.

6: no prompt de seção, digite "Adicionar jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Como no meu caso, o jar na pasta C:\apps\dist\hive-0.13.x\bin, posso pode adicionar diretamente o jar com o nome, conforme mostrado abaixo:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

Agora, você está pronto para usar o SerDe para executar consultas em documento JSON.

A instrução a seguir cria uma tabela com um esquema definido

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Lista o nome e sobrenome do aluno

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Aqui está o resultado do console de seção.

![Consulta de SerDe 1][image-hdi-hivejson-serde_query1]

Para calcular a soma da pontuação do documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

A consulta acima usa a [exibição lateral Detalhar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF para expandir a matriz de resultados para que eles podem ser resumidos.

Aqui está a saída do console de seção.

![Consulta de SerDe 2][image-hdi-hivejson-serde_query2]

Para localizar a selecionar quais os assuntos um determinado aluno tem marcou mais de 80 pontos  
      JT. StudentClassCollection.ClassId de json_table jt lateral exibição Detalhar (jt. Conjunto de StudentClassCollection.Score) como pontuação pontuação onde > 80;

A consulta acima retorna uma matriz de seção ao contrário get\_json\_objeto que retorna uma cadeia de caracteres.

![Consulta de SerDe 3][image-hdi-hivejson-serde_query3]

Se você quiser skil JSON mal formado, e em seguida, conforme explicado na [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) deste SerDe isso pode ser feito digitando o código a seguir:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Resumo
Portanto, o tipo de operador JSON na seção que você escolher depende de seu cenário. Se você tiver um documento JSON simples e você ter apenas um campo para procurar – você pode optar por usar a seção UDF\_json\_objeto. Se você tiver mais de um chaves para pesquisar que você pode usar json_tuple. Se você tiver um documento aninhado, você deve usar o SerDe JSON.

Para outros artigos relacionados, consulte

- [Usar seção e HiveQL com Hadoop em HDInsight para analisar um arquivo de log4j de Apache de exemplo](hdinsight-use-hive.md)
- [Analisar os dados de atrasos de voos usando a seção no HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analisar dados de Twitter usando a seção no HDInsight](hdinsight-analyze-twitter-data.md)
- [Executar um trabalho de Hadoop usando DocumentDB e HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
