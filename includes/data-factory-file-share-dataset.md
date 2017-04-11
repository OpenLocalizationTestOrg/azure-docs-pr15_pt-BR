## <a name="fileshare-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados de compartilhamento de arquivos

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](../articles/data-factory/data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados. 

A seção **typeProperties** é diferente para cada tipo de conjunto de dados. Ele fornece informações específicas para o tipo de conjunto de dados. A seção de typeProperties para um conjunto de dados de tipo de conjunto de dados de **compartilhamento** tem as seguintes propriedades:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
folderPath | Caminho de sub para a pasta. Usar o caractere de escape ' \ ' para caracteres especiais na cadeia. Consulte o [exemplo vinculado definições de serviço e conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Você pode combinar essa propriedade com **partitionBy** ter caminhos de pasta com base na fatia tempos de data de início/término. | Sim
nome do arquivo | Especifique o nome do arquivo na **folderPath** se quiser que a tabela para se referir a um arquivo específico na pasta. Se você não especificar qualquer valor desta propriedade, a tabela aponta para todos os arquivos na pasta.<br/><br/>Quando o nome do arquivo não for especificado para um conjunto de dados de saída, o nome do arquivo gerado seria no seguinte neste formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | Não
partitionedBy | partitionedBy pode ser usado para especificar uma folderPath dinâmico, nome do arquivo de dados da série de tempo. Por exemplo, folderPath parametrizada para cada hora dos dados. | Não
Formato | Os seguintes tipos de formato são suportados: **TextFormat**, **AvroFormat**, **JsonFormat**e **OrcFormat**. Defina a propriedade de **tipo** em Formatar como um desses valores. Consulte as seções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat)e [Especificando OrcFormat](#specifying-orcformat) para obter detalhes. Se você quiser copiar arquivos como-está entre armazenamentos baseados em arquivos (cópia binário), você pode ignorar a seção de formato em ambas as definições de conjunto de dados de entrada e saída. | Não
fileFilter | Especifica um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos.<br/><br/>Valores permitidos são: `*` (vários caracteres) e `?` (caractere único).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter é aplicável de um conjunto de dados entrado do compartilhamento de arquivos. Essa propriedade não é compatível com HDFS.  | Não
| compactação | Especifica o tipo e o nível de compactação para os dados. Tipos suportados são: **GZip**, **Deflate**e **BZip2** e níveis com suporte são: **Optimal** e **mais rápido**. Atualmente, as configurações de compactação, não há suporte para dados em **AvroFormat** ou **OrcFormat**. Para obter mais informações, consulte a seção de [suporte de compactação](#compression-support) .  | Não |
| useBinaryTransfer | Especifique se usar o modo de transferência binário. True para o modo binário e ASCII false. Valor padrão: True. Essa propriedade só pode ser usada ao tipo de serviço vinculada associado será do tipo: FtpServer. | Não | 
 

> [AZURE.NOTE] nome do arquivo e fileFilter não podem ser usados simultaneamente.

### <a name="using-partionedby-property"></a>Usando a propriedade partionedBy

Conforme mencionado na seção anterior, você pode especificar uma folderPath dinâmico, nome do arquivo de dados da série de tempo com partitionedBy. Você pode fazer isso com as macros de dados fábrica e a variável de sistema SliceStart, SliceEnd que indicam o período de tempo lógico para uma fatia de dados fornecidos. 

Para saber mais sobre conjuntos de dados de série de tempo, agendamento e fatias, consulte [Criando conjuntos de dados](../articles/data-factory/data-factory-create-datasets.md), [execução & agendamento](../articles/data-factory/data-factory-scheduling-and-execution.md)e [Criar canais](../articles/data-factory/data-factory-create-pipelines.md) artigos. 

#### <a name="sample-1"></a>Exemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Neste exemplo {fatia} é substituída pelo valor da variável de sistema de Data Factory SliceStart no formato (YYYYMMDDHH) especificado. O SliceStart refere-se para começar a hora da fatia. O folderPath é diferente para cada fatia. Exemplo: wikisampledataout/wikidatagateway/2014100103 ou wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Neste exemplo, ano, mês, dia e hora da SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.
