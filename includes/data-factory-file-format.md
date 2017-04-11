## <a name="specifying-formats"></a>Especificar formatos

Azure Data Factory suporta os seguintes tipos de formato: 

- [Formato de texto](#specifying-textformat)
- [Formato JSON](#specifying-jsonformat)
- [Formato de Avro](#specifying-avroformat)
- [Formato ORC](#specifying-orcformat)
- [Formato de parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Especificando TextFormat

Se o formato é definido como **TextFormat**, você pode especificar as seguintes propriedades **opcional** na seção **formato** .

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | O caractere usado para separar colunas em um arquivo. | Apenas um caractere é permitido. O valor padrão é vírgula (','). <br/><br/>Para usar um caractere Unicode, consulte [Caracteres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente ele. Por exemplo, você pode considerar para usar um caractere não imprimível raro que provavelmente não existe em seus dados: especificar "\u0001" que representa o início do título (SOH). | Não |
| rowDelimiter | O caractere usado para separar linhas em um arquivo. | Apenas um caractere é permitido. O valor padrão é qualquer um dos seguintes valores na leitura: ["\r\n", "\r", "\n"] e "\r\n" na gravação. | Não |
| escapeChar | O caractere especial usado para pular um delimitador de coluna no conteúdo do arquivo de entrada. <br/><br/>Você não pode especificar escapeChar e quoteChar para uma tabela. | Apenas um caractere é permitido. Nenhum valor padrão. <br/><br/>Exemplo: se você tiver vírgulas (', ') como o delimitador de coluna, mas você deseja que o caractere de vírgula no texto (exemplo: "Olá, mundo"), você pode definir '$' como o caractere de escape e usar cadeia de caracteres "$Olá, mundo" na fonte. | Não | 
| quoteChar | O caractere usado para um valor de cadeia de caracteres da cotação. Os delimitadores de coluna e linha dentro os caracteres de cotação seriam tratados como parte do valor de cadeia de caracteres. Essa propriedade é aplicável a conjuntos de dados de entrada e saídos.<br/><br/>Você não pode especificar escapeChar e quoteChar para uma tabela. | Apenas um caractere é permitido. Nenhum valor padrão. <br/><br/>Por exemplo, se você tiver vírgulas (', ') como o delimitador de coluna, mas você quer que o caractere de vírgula no texto (exemplo: < Olá, mundo >), você pode definir "(aspas duplas) como o caractere de aspas e use a cadeia de caracteres"Olá, mundo"na fonte. | Não |
| nullValue | Um ou mais caracteres usados para representar um valor nulo. | Um ou mais caracteres. Os valores padrão são "\N" e "Nulo" em leitura e "\N" na gravação. | Não |
| encodingName | Especifique o nome de codificação. | Um nome de codificação válido. consulte [Encoding.EncodingName propriedade](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows-1250 ou shift_jis. O valor padrão é UTF-8. | Não | 
| firstRowAsHeader | Especifica se a considerar a primeira linha como um cabeçalho. Um conjunto de dados de entrada, a Data Factory lê a primeira linha como um cabeçalho. Um conjunto de dados de saída, Data Factory grava a primeira linha como um cabeçalho. <br/><br/>Consulte [cenários para uso **firstRowAsHeader** e **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. | True<br/>FALSO (padrão) | Não |
| skipLineCount | Indica o número de linhas a serem ignorar ao ler dados dos arquivos de entrada. Se skipLineCount e firstRowAsHeader forem especificados, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho é leitura do arquivo de entrada. <br/><br/>Consulte [cenários para uso firstRowAsHeader e skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. | Número inteiro | Não | 
| treatEmptyAsNull | Especifica se tratar nulo ou sequência vazia como um valor nulo ao ler dados de um arquivo de entrada. | Verdadeiro (padrão)<br/>FALSO | Não |  

#### <a name="textformat-example"></a>Exemplo de TextFormat
O exemplo a seguir mostra algumas das propriedades de formato para TextFormat.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Para usar um escapeChar em vez de quoteChar, substitua a linha quoteChar com a seguinte escapeChar:

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Cenários para uso firstRowAsHeader e skipLineCount

- Você está copiando uma fonte não-arquivo para um arquivo de texto e quiser adicionar uma linha de cabeçalho que contém os metadados de esquema (por exemplo: esquema SQL). Especifica **firstRowAsHeader** como true no dataset saída para esse cenário. 
- Você está copiando um arquivo de texto que contém uma linha de cabeçalho para um receptor de diferentes de arquivos e gostaria de soltar nessa linha. Especifica **firstRowAsHeader** como true do dataset de entrada.
- Você está copiando um arquivo de texto e deseja ignorar algumas linhas no início que não contêm dados ou cabeçalho informações. Especifique **skipLineCount** para indicar o número de linhas a serem ignoradas. Se o resto do arquivo contém uma linha de cabeçalho, você também pode especificar **firstRowAsHeader**. Se **skipLineCount** e **firstRowAsHeader** forem especificados, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho é leitura do arquivo de entrada

### <a name="specifying-avroformat"></a>Especificando AvroFormat
Se o formato é definido como AvroFormat, você não precisa especificar qualquer propriedades na seção formato dentro da seção de typeProperties. Exemplo:

    "format":
    {
        "type": "AvroFormat",
    }

Para usar o formato de Avro em uma tabela de seção, você pode consultar a [tutorial do Apache seção](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observe os seguintes pontos:  

- Não há suporte para [tipos de dados complexos](http://avro.apache.org/docs/current/spec.html#schema_complex) (registros, enums, matrizes, mapas, uniões e fixo)

### <a name="specifying-jsonformat"></a>Especificando JsonFormat

Se o formato é definido como **JsonFormat**, você pode especificar as seguintes propriedades **opcional** na seção **formato** .

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| filePattern | Indica o padrão de dados armazenados em cada arquivo JSON. Valores permitidos são: **setOfObjects** e **arrayOfObjects**. O valor **padrão** é: **setOfObjects**. Consulte seções para obter detalhes sobre esses padrões a seguir.| Não |
| encodingName | Especifique o nome de codificação. Para a lista de nomes de codificação válidos, consulte: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) propriedade. Por exemplo: windows-1250 ou shift_jis. O valor **padrão** é: **UTF-8**. | Não | 
| nestingSeparator | Caractere que é usado para separar níveis de aninhamento. O valor padrão é '.' (ponto). | Não | 


#### <a name="setofobjects-file-pattern"></a>padrão de arquivo setOfObjects

Cada arquivo contém único objeto ou linha-delimitado/concatenados vários objetos. Quando esta opção é escolhida em um conjunto de dados de saída, atividade de cópia produz um único arquivo JSON com cada objeto por linha (separado por linha).

**único objeto** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**JSON delimitado por linha** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenado**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>arrayOfObjects padrão do arquivo. 

Cada arquivo contém uma matriz de objetos. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Exemplo de JsonFormat

Se você tiver um arquivo JSON com o seguinte conteúdo:  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

e você deseja copiá-la para uma tabela do SQL Azure no seguinte formato: 

ID  | Name.First | Name.Middle | . Sobrenome | Marcas
--- | ---------- | ----------- | --------- | ----
1 | John | nulo | Cunha | ["Dados Factory", "Azure"]

O conjunto de dados de entrada com tipo de JsonFormat é definido da seguinte maneira: (definição parcial com apenas as partes relevantes)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Se a estrutura não estiver definida, a atividade de cópia nivela a estrutura por padrão e copie cada coisa. 

#### <a name="supported-json-structure"></a>Estrutura JSON com suporte
Observe os seguintes pontos: 

- Cada objeto com uma coleção de pares de valor do nome é mapeado para uma linha de dados em um formato tabular. Objetos podem ser aninhados e você pode definir como nivelar a estrutura em um conjunto de dados com o separador de aninhamento (.) por padrão. Consulte o [exemplo JsonFormat](#jsonformat-example) anterior seção para obter um exemplo.  
- Se a estrutura não está definida no dataset fábrica de dados, a atividade de cópia detecta o esquema do primeiro objeto e nivelar todo o objeto. 
- Se a entrada JSON tem uma matriz, a atividade de cópia converte o valor de matriz inteira em uma cadeia de caracteres. Você pode optar por ignorá-la usando o [mapeamento de coluna ou filtragem](#column-mapping-with-translator-rules).
- Se houver nomes duplicados no mesmo nível, a atividade de cópia escolhe o último.
- Nomes de propriedades diferenciam maiusculas de minúsculas. Duas propriedades com o mesmo nome, mas diferentes embalagens são tratadas como duas propriedades separadas. 

### <a name="specifying-orcformat"></a>Especificando OrcFormat
Se o formato é definido como OrcFormat, você não precisa especificar qualquer propriedades na seção formato dentro da seção de typeProperties. Exemplo:

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Se você não estiver copiando arquivos ORC **como-é** entre locais e nuvem armazenamentos de dados, você precisa instalar o 8 JRE (Java Runtime ambiente) no seu computador do gateway. Um gateway de 64 bits requer JRE de 64 bits e 32 bits gateway requer JRE de 32 bits. Você pode encontrar as duas versões dos [aqui](http://go.microsoft.com/fwlink/?LinkId=808605). Escolha o apropriado.

Observe os seguintes pontos:

-   Dados complexos tipos não são suportados (estrutura, mapa, lista, união)
-   Arquivo ORC tem três [Opções relacionadas a compactação](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): nenhum, ZLIB, SNAPPY. Fábrica de dados compatível com dados de leitura do arquivo ORC em qualquer um desses formatos compactado. Ele usa a compactação codec é nos metadados ler os dados. No entanto, ao escrever para um arquivo de ORC, Data Factory escolhe ZLIB, que é o padrão para ORC. Atualmente, não há nenhuma opção para substituir esse comportamento. 

### <a name="specifying-parquetformat"></a>Especificando ParquetFormat
Se o formato é definido como ParquetFormat, você não precisa especificar qualquer propriedades na seção formato dentro da seção de typeProperties. Exemplo:

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Se você não estiver copiando arquivos de Parquet **como-é** entre locais e nuvem armazenamentos de dados, você precisa instalar o 8 JRE (Java Runtime ambiente) no seu computador do gateway. Um gateway de 64 bits requer JRE de 64 bits e 32 bits gateway requer JRE de 32 bits. Você pode encontrar as duas versões dos [aqui](http://go.microsoft.com/fwlink/?LinkId=808605). Escolha o apropriado.

Observe os seguintes pontos:

-   Dados complexos tipos não são compatíveis (mapa, lista)
-   Arquivo de parquet tem as seguintes opções de compactação: nenhum, SNAPPY, GZIP e LZO. Fábrica de dados compatível com dados de leitura do arquivo ORC em qualquer um desses formatos compactado. Ele usa o codec de compactação nos metadados para ler os dados. No entanto, ao gravar um arquivo de Parquet, Data Factory escolhe SNAPPY, que é o padrão para o formato de Parquet. Atualmente, não há nenhuma opção para substituir esse comportamento. 
