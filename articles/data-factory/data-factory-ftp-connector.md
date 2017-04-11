<properties 
    pageTitle="Mover dados do servidor FTP | Microsoft Azure" 
    description="Saiba mais sobre como mover dados de um servidor FTP usando fábrica de dados do Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Mover dados de um servidor FTP usando fábrica de dados do Azure
Este artigo descreve como usar a atividade de cópia em fábrica de dados do Azure para mover dados de um servidor FTP para um armazenamento de dados com suporte receptor. Este artigo baseia-se no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) que apresenta uma visão geral de movimentação de dados com a atividade de cópia e a lista de suporte como fontes/receptores de armazenamentos de dados. 

Fábrica de dados atualmente suporta apenas movendo os dados de um servidor FTP para outros armazenamentos de dados, mas não para mover dados de outras fontes de dados para um servidor FTP. Ele dá suporte a ambos os locais e na nuvem servidores FTP. 

Se você estiver movendo dados de um servidor **local** FTP para um armazenamento de dados de nuvem (exemplo: armazenamento de Blob do Azure), instalar e usar o Gateway de gerenciamento de dados. O Gateway de gerenciamento de dados é um agente de cliente que está instalado no seu computador local, que permite que os serviços de nuvem para se conectar ao recurso local. Consulte o [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway. Consulte o artigo de [mover dados entre locais de locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para instruções passo a passo sobre configuração do gateway e usá-lo. Use o gateway para se conectar a um servidor FTP, mesmo se o servidor estiver em uma máquina virtual do Azure IaaS (máquina virtual). 

Você pode instalar o gateway na mesma máquina local ou a máquina virtual IaaS do Azure como o servidor FTP. No entanto, recomendamos que você instale o gateway em uma máquina separada ou uma máquina virtual IaaS do separada do Azure para evitar disputa de recursos e para obter melhor desempenho. Quando você instala o gateway em uma máquina separada, máquina deve ser capaz de acessar o servidor FTP. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de um servidor FTP é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

Os exemplos a seguir fornecem definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Exemplo: Copiar dados de servidor FTP para blob do Microsoft Azure

Este exemplo mostra como copiar dados de um servidor FTP para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

- Um serviço vinculado do tipo [FtpServer](#ftp-linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Uma entrada [dataset](data-factory-create-datasets.md) de tipo de [compartilhamento de arquivos](#fileshare-dataset-type-properties).
- Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [FileSystemSource](#ftp-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um servidor FTP para um Azure blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

**FTP vinculado serviço** Este exemplo usa a autenticação básica com o nome de usuário e senha em texto sem formatação. Você também pode usar uma das seguintes maneiras: 

- Autenticação anônima 
- Autenticação básica com credenciais criptografadas
- FTP sobre SSL/TLS (FTPS)

Consulte a seção [FTP vinculado serviço](#ftp-linked-service-properties) para diferentes tipos de autenticação, você pode usar. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
    }

**Serviço de armazenamento vinculado do Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Dataset de entrada FTP** Este dataset refere-se a pasta FTP `mysharedfolder` e arquivo `test.csv`. O pipeline copia o arquivo para o destino. 

Configuração "externo": "true" informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Conjunto de dados de saída do Azure Blob**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **FileSystemSource** e **receptor** tipo está definido como **BlobSink**. 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Propriedades de serviço vinculada de FTP

A tabela a seguir fornece descrição para elementos JSON específicos FTP serviço vinculado.

| Propriedade | Descrição | Necessário | Padrão |
| -------- | ----------- | -------- | ------- | 
| tipo | A propriedade de tipo deve ser definida como FtpServer | Sim | &nbsp;
| host | Nome ou endereço IP do servidor FTP | Sim | &nbsp;
| authenticationType | Especificar o tipo de autenticação | Sim | Básica, anônima |
| nome de usuário | Usuário que tenha acesso ao servidor FTP | Não | &nbsp;
| senha | Senha do usuário (username) | Não | &nbsp;
| encryptedCredential | Credencial criptografada para acessar o servidor FTP | Não | &nbsp;
| gatewayName | Nome do gateway Gateway de gerenciamento de dados para se conectar a um servidor FTP local | Não    | &nbsp;
| porta | Porta em que o servidor FTP está atendendo | Não | 21 |
| enableSsl | Especifique se deseja usar FTP canal SSL/TLS | Não | verdadeiro | 
| enableServerCertificateValidation | Especifique se deseja habilitar validação de certificado do servidor SSL ao usar FTP sobre canal SSL/TLS | Não | verdadeiro | 

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Usando o nome de usuário e senha em texto sem formatação para autenticação básica
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Usando a porta, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Usando encryptedCredential para autenticação e gateway

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados FTP local.

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia de FTP

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, as propriedades de tipo variam dependendo dos tipos de fontes e receptores.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos: 

- [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 
