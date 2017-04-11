<properties 
    pageTitle="Solucionar problemas de fábrica de dados do Azure" 
    description="Saiba como solucionar problemas com o uso de fábrica de dados do Azure." 
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
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Solucionar problemas de fábrica de dados
Este artigo fornece dicas de solução de problemas ao usar o Azure dados fábrica. Este artigo não lista todos os possíveis problemas ao usar o serviço, mas abrange alguns problemas e dicas de solução de problemas gerais.   

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Erro: A assinatura não está registrada para usar namespace 'Microsoft.DataFactory'
Se você receber esse erro, o provedor de recursos de fábrica de dados do Azure não tiver sido registrado no seu computador. Faça o seguinte: 

1. Abra o PowerShell Azure. 
2. Faça logon em sua conta do Azure usando o seguinte comando.
        AzureRmAccount de logon 
3. Execute o seguinte comando para registrar o provedor de fábrica de dados do Azure.
        Register-AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: O erro não autorizado durante a execução de um cmdlet fábrica de dados
Você provavelmente não está usando a conta de direita Azure ou uma assinatura com o PowerShell do Azure. Use os seguintes cmdlets para selecionar a conta do Azure à direita e assinatura para usar com o PowerShell do Azure. 

1. Login-AzureRmAccount - usar a ID de usuário corretas e a senha
2. Get-AzureRmSubscription - exibir todas as assinaturas para a conta. 
3. Selecione AzureRmSubscription &lt;nome da assinatura&gt; -selecione a assinatura à direita. Use o mesmo que você usar para criar uma fábrica de dados no portal do Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: Falha ao iniciar o Express configuração de Gateway de gerenciamento de dados do Azure portal
A configuração da Express para o Gateway de gerenciamento de dados requer o Internet Explorer ou um navegador da web compatível Microsoft ClickOnce. Se a configuração Express falha ao iniciar, siga um destes procedimentos: 

- Use o Internet Explorer ou um navegador da web compatível Microsoft ClickOnce.

    Se você estiver usando o Chrome, vá para a [loja do Chrome na web](https://chrome.google.com/webstore/), pesquisar com palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instalá-lo. 
    
    Faça o mesmo para o Firefox (instalação do suplemento). Clique em botão de Menu aberto na barra (três linhas horizontais no canto superior direito), clique em complementos, pesquisar com a palavra-chave "ClickOnce", escolha uma das extensões ClickOnce e instalá-lo. 

- Use o link de **Configuração Manual** mostrado na mesma lâmina no portal. Você pode usar essa abordagem para baixar o arquivo de instalação e executá-lo manualmente. Depois que a instalação for bem-sucedida, você verá a caixa de diálogo de configuração do Gateway de gerenciamento de dados. Copie a **chave** na tela do portal e usá-lo no Gerenciador de configuração para registrar o gateway manualmente com o serviço.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: Não conseguir se conectar ao local SQL Server 
Iniciar o **Gerenciador de configuração do Gateway de gerenciamento de dados** no computador do gateway e use a guia de **solução de problemas** para testar a conexão com o SQL Server do computador do gateway. Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: Fatias de entrada são aguardar estado por vez

As fatias poderiam estar em estado **Aguardando** devido a vários motivos. Um dos motivos comuns é que a propriedade **externos** não é definida como **true**. Qualquer conjunto de dados que é produzido fora do escopo do Azure dados fábrica deve ser marcado com propriedade **external** . Esta propriedade indica que os dados são externos e não foi feito por qualquer canais dentro da fábrica de dados. As fatias de dados são marcadas como **pronto** quando os dados estão disponíveis no repositório do respectivo. 

Consulte o exemplo a seguir para o uso da propriedade **externos** . Opcionalmente, você pode especificar **externalData*** quando você define externos como true.

Consulte o artigo de [conjuntos de dados](data-factory-create-datasets.md) para obter mais detalhes sobre essa propriedade.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Para resolver o erro, adicione a propriedade **externos** e a seção opcional **externalData** para a definição de JSON da tabela de entrada e crie novamente a tabela. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: Falha de operação de cópia híbrido
Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter as etapas para solucionar problemas com a cópia de/para um armazenamento de dados local usando o Gateway de gerenciamento de dados. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: Sob demanda HDInsight falha de provisionamento
Ao usar um serviço vinculado do tipo HDInsightOnDemand, você precisa especificar um linkedServiceName que aponta para um armazenamento de Blob do Azure. Serviço de fábrica dados usa esse armazenamento para armazenar logs e arquivos de suporte para o seu cluster de HDInsight sob demanda.  Às vezes, provisionamento de um cluster de HDInsight sob demanda falha com o seguinte erro:

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Esse erro geralmente indica que o local da conta de armazenamento especificado na linkedServiceName não está no mesmo local do Centro de dados onde o provisionamento de HDInsight está acontecendo. Exemplo: se sua fábrica de dados no Oeste EUA e o armazenamento do Azure é Leste EUA, o provisionamento de sob demanda falhará Oeste EUA.

Além disso, há uma segunda additionalLinkedServiceNames de propriedade JSON onde contas de armazenamento adicional podem ser especificadas em HDInsight sob demanda. As contas adicionais de armazenamento vinculado devem estar no mesmo local que o cluster HDInsight ou falhar com o mesmo erro.

### <a name="problem-custom-net-activity-fails"></a>Problema: Personalizado .NET atividade falhar
Para obter etapas detalhadas, consulte [Depurar um pipeline com atividade personalizada](data-factory-use-custom-activities.md#debug-the-pipeline) . 

## <a name="use-azure-portal-to-troubleshoot"></a>Usar o portal do Azure para solucionar 

### <a name="using-portal-blades"></a>Usando o portal lâminas
Consulte [pipeline de Monitor](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) para as etapas. 

### <a name="using-monitor-and-manage-app"></a>Usando o Monitor e gerenciar aplicativos
Consulte [Monitor e gerenciar canais de fábrica de dados usando o Monitor e gerenciar App](data-factory-monitor-manage-app.md) para obter detalhes. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Usar o PowerShell do Azure para solucionar problemas

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Usar o PowerShell do Azure para solucionar problemas de erro  
Consulte [canais de fábrica de dados do Monitor usando o PowerShell do Azure](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) para obter detalhes. 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 