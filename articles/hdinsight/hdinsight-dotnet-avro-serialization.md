<properties
    pageTitle="Serializar dados com o Microsoft Avro Library | Microsoft Azure"
    description="Saiba como o Azure HDInsight usa Avro serializar dados grandes."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializar dados em Hadoop com a Microsoft Avro Library

Este tópico mostra como usar a <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> para serializar objetos e outras estruturas de dados em fluxos para persisti-los a memória, um banco de dados ou um arquivo e também como desserializá-los para recuperar os objetos originais.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
A <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> implementa o sistema de serialização de dados Apache Avro para o ambiente Microsoft.NET. Apache Avro fornece um formato de troca de dados binários compacta para serialização. Ele usa <a href="http://www.json.org" target="_blank">JSON</a> para definir um esquema de idioma independente que subscreve interoperabilidade de linguagem. Dados serializados em um idioma podem ser lidos em outro. Atualmente C, C++, c#, Java, PHP, Python e Ruby são suportados. Informações detalhadas sobre o formato podem ser encontradas na <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Especificação de Avro Apache</a>. Observe que a versão atual do Microsoft Avro Library não dá suporte a parte de chamadas (RPCs) de procedimento remoto desta especificação.

A representação serializada de um objeto do sistema Avro consiste em duas partes: esquema e o valor real. O esquema de Avro descreve o modelo de dados independente de idioma dos dados serializados com JSON. Ele está presente lado a lado com uma representação binária dos dados. Ter o esquema separado da representação binária permite que cada objeto sejam gravados com nenhum transparências por valor, tornando serialização rápida e a representação pequenas.

##<a name="the-hadoop-scenario"></a>O cenário de Hadoop
O formato de serialização Apache Avro é amplamente usado em outros ambientes de Apache Hadoop e Azurehdinsight. Avro oferece uma maneira conveniente para representar estruturas de dados complexas dentro de um trabalho Hadoop MapReduce. O formato dos arquivos de Avro (arquivo de contêiner de objeto Avro) foi projetado para dar suporte ao modelo de programação MapReduce distribuído. O recurso de chave que permite a distribuição é que os arquivos são "divisíveis" no sentido que um pode atingir qualquer ponto em um arquivo e iniciar a leitura de um bloco específico.

##<a name="serialization-in-avro-library"></a>Serialização na biblioteca de Avro
A biblioteca de .NET para Avro suporta duas maneiras de serializar objetos:

- **reflexo** - esquema o JSON para os tipos de é automaticamente criado a partir dos dados contrato atributos dos tipos de .NET para ser serializado.
- **registro genérico** - esquema A JSON é explicitamente especificado em um registro representado pela classe [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) quando nenhuma tipos .NET estão presentes para descrever o esquema para os dados a ser serializado.

Quando o esquema de dados for conhecido gravador tanto o leitor do fluxo, os dados podem ser enviados sem seu esquema. Em casos quando um arquivo de contêiner de objeto Avro é usado, o esquema é armazenado dentro do arquivo. Outros parâmetros, como o codec usado para a compactação de dados, podem ser especificados. Esses cenários são descritos mais detalhadamente e ilustrados nos exemplos de código abaixo.


## <a name="install-avro-library"></a>Instalar Avro biblioteca

A seguir é necessária antes de instalar a biblioteca:

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 ou posterior)

Observe que a dependência de Newtonsoft.Json.dll é baixada automaticamente com a instalação do Microsoft Avro Library. O procedimento para isso é fornecido na seção a seguir.


A Microsoft Avro Library é distribuída como um pacote de NuGet que pode ser instalado do Visual Studio via o procedimento a seguir:

1. Selecione a guia de **projeto** -> **Gerenciar pacotes de NuGet...**
2. Procurar por "Microsoft.Hadoop.Avro" na caixa **Pesquisar Online** .
3. Clique no botão **instalar** ao lado de **Biblioteca de Avro do Microsoft Azure HDInsight**.

Observe que o Newtonsoft.Json.dll (> = 6.0.4) dependência também é baixada automaticamente com a Microsoft Avro Library.

Talvez você queira visite a <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page do Microsoft Avro biblioteca</a> para ler as notas de versão atual.


O código-fonte Microsoft Avro Library está disponível na <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page do Microsoft Avro biblioteca</a>.

##<a name="compile-schemas-using-avro-library"></a>Compilar esquemas usando Avro biblioteca

A Microsoft Avro Library contém um utilitário de geração de código que permite a criação de tipos c# automaticamente com base no esquema de JSON definido anteriormente. O utilitário de geração de código não é distribuído como um executável binário, mas pode ser facilmente criado via o procedimento a seguir:

1. Baixe o arquivo. zip com a versão mais recente do código-fonte HDInsight SDK do <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK para Hadoop</a>. (Clique no ícone de **Download** , não na guia **Downloads** .)

2. Extrai o SDK do HDInsight para um diretório na máquina com o .NET Framework 4 instalado e conectado à Internet para baixar pacotes do NuGet dependência necessário. Abaixo, podemos assumirá que o código-fonte é extraído para C:\SDK.

3. Vá para a pasta C:\SDK\src\Microsoft.Hadoop.Avro.Tools e execute Build. bat. (O arquivo chamará MSBuild da distribuição de 32 bits do .NET Framework. Se você gostaria de usar a versão de 64 bits, editar Build. bat, após os comentários dentro do arquivo.) Certifique-se de que a compilação seja bem-sucedida. (Em alguns sistemas, MSBuild pode produzir avisos. Esses avisos não afetam o utilitário desde que não estão sem erros de compilação.)

4. O utilitário compilado está localizado em C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Para se familiarizar com a sintaxe da linha de comando, execute o seguinte comando da pasta onde se encontra o utilitário de geração de código:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para testar o utilitário, você pode gerar classes c# do arquivo de esquema JSON de exemplo fornecido com o código-fonte. Execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Isso deve para produzir duas c# arquivos na pasta atual: SensorData.cs e Location.cs.

Para compreender a lógica que está usando o utilitário de geração de código ao converter o esquema JSON em tipos c#, consulte o arquivo que generationverification.Feature localizado em C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Observe que os namespaces são extraídas do esquema JSON, usando a lógica descrita no arquivo mencionado no parágrafo anterior. Namespaces extraídas do esquema têm precedência sobre tudo o que é fornecido com o parâmetro /n na linha de comando utilitário. Se você quiser substituir os namespaces contidos o esquema, use o parâmetro /nf. Por exemplo, para alterar todos os namespaces a SampleJSONSchema.avsc para my.own.nspace, execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Amostras
Exemplos de seis fornecidos neste tópico ilustram cenários diferentes suportados pelo Microsoft Avro Library. A Microsoft Avro Library é projetada para funcionar com qualquer fluxo. Nesses exemplos, dados é manipulados por meio de fluxos de memória em vez de fluxos de arquivos ou bancos de dados para simplicidade e consistência. Na abordagem um ambiente de produção dependerá os requisitos de cenário exato, fonte de dados e volume, restrições de desempenho e outros fatores.

Os dois primeiros exemplos mostram como serializar e desserializar dados em buffers de fluxo de memória usando reflexão e registros genéricos. O esquema nesses dois casos será considerado ser compartilhados entre os leitores e autores fora da banda.

O terceiro e o quarto exemplos mostram como serializar e desserializar dados usando os arquivos de contêiner de objeto de Avro. Quando os dados são armazenados em um arquivo de contêiner Avro, seu esquema é sempre armazenado com ele porque o esquema deve ser compartilhado para desserialização.

O exemplo que contém os quatro primeiros exemplos pode ser baixado do site do <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">amostras de código Azure</a> .

O quinto exemplo mostra como usar um codec de compactação personalizada para arquivos de contêiner de objeto de Avro. Uma amostra que contém o código para este exemplo pode ser baixada do site do <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">amostras de código Azure</a> .

O exemplo sexto mostra como usar Avro serialização para carregar dados para o armazenamento de Blob do Azure e analisá-los usando seção com um cluster de HDInsight (Hadoop). Ele pode ser baixado do site do <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">amostras de código Azure</a> .

Aqui estão links para as amostras de seis discutidas no tópico:

 * <a href="#Scenario1">**Serialização com reflexão**</a> - esquema JSON a tipos de ser serializado é automaticamente criado a partir dos dados atributos de contrato.
 * <a href="#Scenario2">**Serialização com registro genérico**</a> - esquema o JSON explicitamente especificado em um registro quando nenhum tipo de .NET está disponível para reflexão.
 * <a href="#Scenario3">**Serialização usando arquivos de contêiner de objeto com reflexão**</a> - esquema JSON o é criado e compartilhado junto com os dados serializados por meio de um arquivo de contêiner de objeto de Avro automaticamente.
 * <a href="#Scenario4">**Serialização usando arquivos de contêiner de objeto com registro genérico**</a> - esquema JSON o é explicitamente especificado antes da serialização e compartilhadas junto com os dados por meio de um arquivo de contêiner de objeto de Avro.
 * <a href="#Scenario5">**Serialização usando arquivos de contêiner de objeto com um codec de compactação personalizada**</a> - o exemplo mostra como criar um arquivo de contêiner de objeto de Avro com uma implementação de .NET personalizada do codec de compactação de dados Deflate.
 * <a href="#Scenario6">**Usando Avro para carregar dados de serviço do Microsoft Azure HDInsight**</a> - o exemplo ilustra como Avro serialização interage com o serviço de HDInsight. Uma assinatura ativa do Azure e o acesso para um cluster de Azurehdinsight são necessários para executar este exemplo.

###<a name="Scenario1"></a>Exemplo 1: Serialização com reflexão

O esquema JSON para os tipos pode ser automaticamente criado pela Microsoft Avro Library por meio de reflexo dos dados de atributos de contrato dos objetos c# para ser serializado. A Microsoft Avro Library cria uma [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) para identificar os campos para ser serializado.

Neste exemplo, objetos (uma classe **SensorData** com uma estrutura de **localização** de membro) são serializados em um fluxo de memória e este fluxo é desserializado alternadamente. O resultado é então comparado com a instância inicial para confirmar que o objeto **SensorData** recuperado é idêntico ao original.

O esquema neste exemplo será considerado ser compartilhados entre os leitores e autores, portanto, o formato de contêiner de objeto de Avro não é necessário. Para obter um exemplo de como serializar e desserializar dados em buffers de memória usando reflexão com o formato de contêiner de objeto quando o esquema deve ser compartilhado com os dados, consulte <a href="#Scenario3">serialização usando arquivos de contêiner de objeto com reflexão</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Exemplo 2: Serialização com um registro genérico

Um esquema JSON pode ser explicitamente especificado em um registro genérico quando reflexão não pode ser usado porque os dados não podem ser representados por meio de classes .NET com um contrato de dados. Esse método é geralmente mais lento do que usando reflexão. Nesses casos, o esquema de dados também podem mais ser dinâmicos, ou seja, não ser conhecido em tempo de compilação. Dados representados como arquivos de valores separados por vírgula (CSV) cujo esquema é desconhecida até que ele é transformado no formato de Avro em tempo de execução são um exemplo desse tipo de cenário dinâmico.

Este exemplo mostra como criar e usar um [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) explicitamente especificar um esquema JSON, como preenchê-la com os dados e, em seguida, como serializar e desserializá-lo. O resultado é então comparado com a instância inicial para confirmar que o registro recuperado é idêntico ao original.

O esquema neste exemplo será considerado ser compartilhados entre os leitores e autores, portanto, o formato de contêiner de objeto de Avro não é necessário. Para obter um exemplo de como serializar e desserializar dados em buffers de memória usando um registro genérico com o formato de contêiner de objeto quando o esquema deve ser incluído com os dados serializados, consulte o exemplo de <a href="#Scenario4">serialização usando arquivos de contêiner de objeto com registro genérico</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemplo 3: Serialização usando arquivos de contêiner de objeto e serialização com reflexão

Este exemplo é semelhante ao cenário no <a href="#Scenario1">primeiro exemplo</a>, onde o esquema é implicitamente especificado com reflexão. A diferença é que aqui, o esquema não será considerado conhecido ao leitor que desserializa-lo. Os objetos de **SensorData** para ser serializado e seu esquema implicitamente especificado são armazenados em um arquivo de contêiner de objeto Avro representado pela classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

Os dados são serializados neste exemplo com [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) e desserializado com [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). O resultado é comparado às instâncias iniciais para garantir que a identidade.

Os dados no arquivo de contêiner do objeto estão compactados via padrão [**Deflate**] [ deflate-100] codec de compactação do .NET Framework 4. Consulte o <a href="#Scenario5">quinto exemplo</a> neste tópico para aprender a usar uma versão mais recente e superior do [**Deflate**] [ deflate-110] codec de compactação disponível no .NET Framework 4,5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemplo 4: Serialização usando arquivos de contêiner de objeto e serialização com registro genérico

Este exemplo é semelhante ao cenário no <a href="#Scenario2">segundo exemplo</a>, onde o esquema for especificado explicitamente com JSON. A diferença é que aqui, o esquema não será considerado conhecido ao leitor que desserializa-lo.

O conjunto de dados de teste é coletado em uma lista de objetos [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) por meio de um esquema JSON explicitamente definido e, em seguida, armazenado em um arquivo de contêiner de objeto representado pela classe [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Este arquivo contêiner cria um gravador que é usado para serializar os dados, descompactados, para um fluxo de memória que é salvo em um arquivo. O parâmetro de [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) usado para criar o leitor Especifica que esses dados não serão compactados.

Os dados são, em seguida, ler o arquivo e desserializados para um conjunto de objetos. Essa coleção é comparada com a lista inicial de registros Avro para confirmar que eles sejam idênticos.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemplo 5: Serialização usando arquivos de contêiner de objeto com um codec de compactação personalizada

O quinto exemplo mostra como usar um codec de compactação personalizada para arquivos de contêiner de objeto de Avro. Uma amostra que contém o código para este exemplo pode ser baixada do site do [amostras de código Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) .

A [Especificação de Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite a utilização de um codec de compactação opcional (além dos padrões **Nulo** e **Deflate** ). Este exemplo não está implementando um codec completamente novo como Snappy (mencionado como um codec opcional suportado na [Especificação de Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Mostra como usar a implementação do .NET Framework 4,5 do [**Deflate**] [ deflate-110] codec, que fornece um algoritmo de compactação melhor com base na biblioteca de compactação [zlib](http://zlib.net/) que a versão do .NET Framework 4 padrão.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemplo 6: Usando Avro para carregar dados de serviço do Microsoft Azure HDInsight

O exemplo sexto ilustra algumas técnicas de programação relacionadas a interação com o serviço de Azurehdinsight. Uma amostra que contém o código para este exemplo pode ser baixada do site do [amostras de código Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) .

O exemplo faz o seguinte:

* Conecta-se a um cluster de serviço existente do HDInsight.
* Serializa vários arquivos CSV e carrega o resultado para armazenamento de Blob do Azure. (Os arquivos CSV são distribuídos junto com a amostra e representam um trecho de dados históricos de estoque AMEX distribuídos por [Infochimps](http://www.infochimps.com/) para o período de 1970-2010. O exemplo lê os dados de arquivo CSV, converte os registros em instâncias da classe de **estoque** e, em seguida, serializa-las por meio de reflexão. Definição de tipo de ações é criada a partir de um esquema JSON por meio do utilitário de geração de código do Microsoft Avro Library.
* Cria uma nova tabela externa chamada **ações** na seção e links para os dados carregado na etapa anterior.
* Executa uma consulta usando a seção sobre a tabela de **ações** .

Além disso, o exemplo executa um procedimento de limpeza antes e depois executar operações principais. Durante a limpeza, todos os dados relacionados do Azure Blob e pastas são removidos e a tabela de seção for interrompida. Você também pode chamar o procedimento de limpeza da linha de comando de amostra.

O exemplo tem os seguintes pré-requisitos:

* Uma assinatura ativa do Microsoft Azure e sua ID de assinatura.
* Um certificado de gerenciamento da assinatura com a chave privada correspondente. O certificado deve ser instalado no armazenamento particular usuário atual na máquina usado para executar a amostra.
* Um cluster de HDInsight ativo.
* Uma conta de armazenamento do Azure vinculado ao cluster HDInsight o pré-requisito anterior, junto com a chave primária ou secundária acesso correspondente.

Todas as informações dos pré-requisitos devem ser inseridas no arquivo de configuração de exemplo antes da amostra é executada. Há duas maneiras de fazer isso:

* Edite o arquivo App no diretório raiz amostra e, em seguida, criar o exemplo
* Primeiro criar o exemplo e editar AvroHDISample.exe.config no diretório de compilação

Em ambos os casos, todas as edições devem ser feitas **<appSettings>** seção configurações. Siga os comentários no arquivo.
O exemplo é executado a partir da linha de comando executando o seguinte comando (caso contrário, onde o arquivo. zip com o exemplo foi considerado extraídos em C:\AvroHDISample; se usar o caminho de arquivo relevante):

    AvroHDISample run C:\AvroHDISample\Data

Para limpar o cluster, execute o seguinte comando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
