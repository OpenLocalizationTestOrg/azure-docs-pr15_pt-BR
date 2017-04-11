## <a name="azure-storage-linked-service"></a>Serviço de vinculado de armazenamento do Azure

O **armazenamento do Azure vinculado serviço** permite vincular uma conta de armazenamento do Azure para uma fábrica dados Azure usando a **chave da conta**. Isso fornece a fábrica de dados com acesso global ao armazenamento do Azure. A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de armazenamento do Azure vinculado.

| Propriedade | Descrição | Necessário |
| :-------- | :----------- | :-------- |
| tipo | A propriedade type deve ser definida: **AzureStorage** | Sim |
| connectionString | Especifica as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. | Sim |

Consulte o artigo a seguir para obter etapas para exibir/cópia a chave da conta para um armazenamento do Azure: [modo de exibição, copiar e teclas de acesso de armazenamento gerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Exemplo:**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Serviço de vinculado Sas de armazenamento do Azure  
Uma assinatura de acesso compartilhado (SAS) fornece acesso delegado a recursos em sua conta de armazenamento. Isso significa que você pode conceder a que um cliente limitado permissões para objetos em sua conta de armazenamento por um determinado período de tempo e com um conjunto específico de permissões, sem ter de compartilhar suas chaves de acesso de conta. As associações de segurança é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com as associações de segurança, o cliente só precisa passar as associações de segurança para o método ou construtor apropriado. Para obter informações detalhadas sobre SAS, consulte [compartilhados assinaturas de acesso: Compreendendo o modelo de SAS](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
O serviço de SAS de armazenamento do Azure vinculado permite vincular uma conta de armazenamento do Azure para uma fábrica Azure dados usando uma assinatura de acesso compartilhado (SAS). Isso fornece a fábrica de dados com acesso restrito/ligadas ao tempo recursos todos/específicos (blob/contêiner) no armazenamento. A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de SAS de armazenamento do Azure vinculado. 

| Propriedade | Descrição | Necessário |
| :-------- | :----------- | :-------- |
| tipo | A propriedade type deve ser definida: **AzureStorageSas**  | Sim |
| sasUri | Especifica o URI de assinatura de acesso compartilhado aos recursos de armazenamento do Azure como blob, contêiner ou tabela. Consulte as anotações abaixo para obter detalhes. | Sim | 


**Exemplo:**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Ao criar um **URI SAS**, considerando o seguinte:  

- Azure Data Factory suporta somente **SAS de serviço**, não SAS de conta. Consulte [Tipos de assinaturas de acesso compartilhado](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre esses dois tipos.
- **Permissões** de leitura/gravação apropriado precisam ser definidas em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado na sua fábrica de dados.
- **Tempo de expiração** deve ser definida adequadamente. Certifique-se de que o acesso aos objetos de armazenamento do Azure não expirar dentro do período ativo do pipeline.
- URI deve ser criado no nível de tabela baseados na precisam ou contêiner/blob à direita. Um Uri SAS para um blob Azure permite que o serviço de dados fábrica acessar blob específico. Um Uri SAS como um contêiner de blob do Microsoft Azure permite que o serviço de dados fábrica percorrer blobs no contêiner. Se você precisar fornecer acesso mais/menos objetos mais tarde, ou atualize o URI SAS, lembre-se de atualizar o serviço vinculado com o novo URI.   
