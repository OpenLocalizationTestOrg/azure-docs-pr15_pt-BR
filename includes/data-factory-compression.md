### <a name="compression-support"></a>Suporte de compactação  
Processamento de grandes conjuntos de dados pode causar gargalos de rede e e/s. Portanto, os dados compactados em armazenamentos podem não apenas acelerar a transferência de dados pela rede e economize espaço em disco, mas também trazer melhorias de desempenho significativas no processamento de grande volume. Atualmente, a compactação tem suporte para armazenamentos de dados baseado em arquivo como Azure Blob ou sistema de arquivos local.  

> [AZURE.NOTE] Configurações de compactação não são suportadas para os dados no **AvroFormat**, **OrcFormat**ou **ParquetFormat**. 

Para especificar a compactação de um conjunto de dados, use a propriedade de **compactação** no dataset JSON como no exemplo seguinte:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
Seção **compactação** tem duas propriedades:  
  
- **Tipo:** o codec de compactação, que pode ser **GZIP**, **Deflate** ou **BZIP2**.  
- **Nível:** a taxa de compactação, que pode ser **Optimal** ou **mais rápido**. 
    - **Mais rápido:** A operação de compactação deve concluir assim que possível, mesmo se o arquivo resultante não é compactado ideal. 
    - **Optimal**: A operação de compactação deve ser otimizada compactada, mesmo se a operação demora mais para ser concluída. 
    
    Para obter mais informações, consulte o tópico de [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Suponha que o conjunto de dados de exemplo acima é usado como a saída de uma atividade de cópia, a atividade de cópia compacta os dados de saída com codec GZIP usando melhor proporção e, em seguida, escrever os dados compactados em um arquivo denominado pagecounts.csv.gz do armazenamento de Blob do Azure.   

Quando você define a propriedade compactação em um dataset entrado JSON, o pipeline pode ler dados compactados na origem e quando você especifica a propriedade em um conjunto de dados de saída JSON, a atividade de cópia pode escrever dados compactados para o destino. Aqui estão alguns cenários de exemplo: 

- Dados de leitura GZIP compactada de um blob Azure, descompactá-lo e gravar dados de resultado em um banco de dados do SQL Azure. Você pode definir o conjunto de dados do Azure Blob entrado com a compactação de propriedade JSON nesse caso. 
- Leia os dados de um arquivo de texto sem formatação local do sistema de arquivos, compactá-lo usando o formato GZip e gravar os dados compactados em um blob do Microsoft Azure. Você pode definir um conjunto de dados do Azure Blob de saída com a compactação de propriedade JSON nesse caso.  
- Ler um dados compactados GZIP de um Azure blob, descompactá-lo, compactar usando BZIP2 e gravar dados de resultado um Azure blob. Você pode definir o conjunto de dados do Azure Blob entrado com o tipo de compactação definido como GZIP e o conjunto de dados de saída com o tipo de compactação definido como BZIP2 nesse caso.   
