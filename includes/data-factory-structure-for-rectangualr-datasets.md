## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especifica a definição de estrutura de conjuntos de dados retangulares
A seção estrutura os conjuntos de dados JSON é uma seção **opcional** para tabelas retangulares (com linhas e colunas) e contém um conjunto de colunas para a tabela. Você usará a seção de estrutura de um dos fornecendo informações de tipo para conversões de tipo ou fazendo mapeamentos de coluna. As seções a seguir descrevem esses recursos detalhadamente. 

Cada coluna contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome | Nome da coluna. | Sim |
| tipo | Tipo de dados da coluna. Consulte a seção de conversões de tipo abaixo para obter mais detalhes sobre quando deve você especificar informações do tipo | Não |
| cultura | .NET com base cultura a ser usada ao tipo especificado e é o tipo de .NET Datetime ou Datetimeoffset. O padrão é "en-us".  | Não |
| formato | Formatar a cadeia de caracteres a ser usada ao tipo especificado e é o tipo de .NET Datetime ou Datetimeoffset. | Não |

O exemplo a seguir mostra a seção de estrutura JSON para uma tabela com três colunas de ID de usuário, nome e lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Use as seguintes diretrizes para quando incluir informações de "estrutura" e o que incluir na seção **estrutura** .

- **Para fontes de dados estruturados** que armazenam informações de esquema e tipo de dados junto com os dados em si (fontes como tabela do Microsoft Azure SQL Server, Oracle, etc.), você deverá especificar a seção "estrutura" apenas se você quiser fazer o mapeamento de colunas específicas de colunas de origem para colunas específicas em receptor e seus nomes não são os mesmos (consulte detalhes na seção de mapeamento de coluna abaixo). 

    Conforme mencionado acima, o tipo de informação é opcional seção "estrutura". Para fontes estruturadas, informações de tipo já estão disponíveis como parte da definição de conjunto de dados no repositório de dados, assim você não deve incluir informações de tipo de quando você inclui a seção "estrutura".
- **Para o esquema de fontes de dados de leitura (especificamente Azure blob)** você pode optar por armazenar dados sem armazenar qualquer informação de esquema ou tipo com os dados. Para esses tipos de fontes de dados, você deve incluir "estrutura" nos seguintes 2 casos:
    - Você deseja fazer o mapeamento de coluna.
    - Quando o conjunto de dados é uma fonte em uma atividade de cópia, você pode fornecer informações de tipo em "estrutura" e fábrica de dados usará essas informações de tipo para a conversão em tipos nativos para o receptor. Consulte o artigo [mover dados para e do Azure Blob](../articles/data-factory/data-factory-azure-blob-connector.md) para obter mais informações.

### <a name="supported-net-based-types"></a>Suporte. Tipos baseados em líquido 
Fábrica de dados suporta os seguintes CLS compatível com .NET com base tipo valores para fornecer informações de tipo em "estrutura" para o esquema de fontes de dados de leitura como BLOB do Microsoft Azure.

- Int16
- Int32 
- Int64
- Único
- Duplo
- Decimal
- Byte]
- Bool
- Cadeia de caracteres 
- GUID
- DateTime
- DateTimeOffset
- Período de tempo 

Para Datetime e Datetimeoffset também, opcionalmente, você pode especificar "cultura" & "Formatar" cadeia de caracteres para facilitar a análise de sua cadeia de caracteres de Datetime personalizada. Veja abaixo a conversão do tipo de exemplo.

