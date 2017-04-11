
<properties 
    pageTitle="Gerenciando ativos e entidades relacionadas com os serviços de mídia SDK .NET" 
    description="Saiba como gerenciar ativos e entidades relacionadas com o SDK de serviços de mídia para .NET." 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gerenciando ativos e entidades relacionadas com os serviços de mídia SDK .NET


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [RESTANTE](media-services-rest-manage-entities.md)


Este tópico mostra como realizar as seguintes tarefas de gerenciamento de serviços de mídia:

- Obtenha uma referência de ativos 
- Obtenha uma referência de trabalho 
- Listar todos os ativos 
- Lista de trabalhos e ativos 
- Lista todas as políticas de acesso 
- Listar todos os localizadores
- Enumerar através de grandes coleções de entidades
- Excluir um ativo 
- Excluir um trabalho 
- Excluir uma política de acesso 

##<a name="prerequisites"></a>Pré-requisitos 

Consulte [Configurar o seu ambiente](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>Obtenha uma referência de ativos

Uma tarefa frequente é obter uma referência a um ativo existente nos serviços de mídia. O exemplo de código a seguir mostra como você pode obter uma referência de ativos da coleção ativos no servidor de objeto de contexto, com base em um ativo ID.
O exemplo de código a seguir usa uma consulta Linq para obter uma referência a um objeto IAsset existente.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>Obtenha uma referência de trabalho

Quando você trabalha com o processamento de tarefas em código de serviços de mídia, muitas vezes você precisa obter uma referência para um trabalho existente com base em uma ID. O exemplo de código a seguir mostra como obter uma referência a um objeto de IJob a partir do conjunto de trabalhos.
Exibição você talvez precise obter uma referência de trabalho ao iniciar um trabalho de codificação de execução demorada e precisar verificar o status de trabalho em um segmento. Em casos assim, quando o método retorna de um segmento, você precisa recuperar uma referência atualizada para um trabalho.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>Listar todos os ativos

Conforme aumenta o número de ativos que você tem no armazenamento, é útil listar os ativos. O exemplo de código a seguir mostra como percorrer a coleção de ativos no objeto de contexto do servidor. Com cada ativo, o exemplo de código também grava alguns dos seus valores de propriedade no console. Por exemplo, cada ativo pode conter muitos arquivos de mídia. O exemplo de código grava todos os arquivos associados a cada ativo.



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>Lista de trabalhos e ativos

Uma tarefa relacionada importante é ativos de lista com suas tarefas associadas em serviços de mídia. O exemplo de código a seguir mostra como cada objeto IJob, de lista e, em seguida, para cada tarefa, ela exibe propriedades sobre o trabalho, todas as tarefas relacionadas, todas as entrada ativos e todos os ativos de saída. O código neste exemplo pode ser útil para várias outras tarefas. Por exemplo, se você quiser listar os ativos de saída de um ou mais trabalhos de codificação que você executou anteriormente, este código mostra como acessar os ativos de saída. Quando você tem uma referência a um ativo de saída, você pode fornecer o conteúdo para outros usuários ou aplicativos baixá-lo ou fornecendo URLs. 

Para obter mais informações sobre as opções para fornecer ativos, consulte [Oferecer ativos com o SDK de serviços de mídia para .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>Lista todas as políticas de acesso

Em serviços de mídia, você pode definir uma política de acesso em um ativo ou seus arquivos. Uma política de acesso define as permissões para um arquivo ou um ativo (qual tipo de acesso e a duração). No seu código de serviços de mídia, normalmente você definir uma política de acesso criando um objeto de IAccessPolicy e associando-um ativo existente. Você criar um objeto ILocator, que permite que você fornecer acesso direto aos ativos nos serviços de mídia. Projeto do Visual Studio que acompanha esta série de documentação contém vários exemplos de código que mostram como criar e atribuir políticas de acesso e locators aos ativos.

O exemplo de código a seguir mostra como listar todas as diretivas de acesso no servidor e mostra o tipo de permissões associadas a cada uma. Outra maneira útil para exibir as políticas de acesso é listar todos os objetos de ILocator no servidor e, em seguida, para cada localizador, você pode listar sua política de acesso associado usando sua propriedade AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>Listar todos os localizadores

Um localizador é uma URL que fornece um caminho direto para acessar um ativo, juntamente com as permissões ao ativo, conforme definido pela diretiva de acesso associado do localizador. Cada ativo pode ter uma coleção de objetos ILocator associada a ele na sua propriedade Locators. O contexto do servidor também tem uma coleção de Locators que contém todos os localizadores.

O exemplo de código a seguir lista todas as locators no servidor. Para cada localizador, ela mostra a Id da política de acesso e ativos relacionados. Ele também exibe o tipo de permissões, a data de vencimento e o caminho completo para o ativo.

Observe que um caminho de localizador de um ativo é apenas uma URL base para o ativo. Para criar um caminho direto para arquivos individuais que um usuário ou aplicativo poderia procure, seu código deve adicionar o caminho de arquivo específico para o caminho de localizador. Para obter mais informações sobre como fazer isso, consulte o tópico [Entregar ativos com o SDK de serviços de mídia para .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerar através de grandes coleções de entidades

Ao consultar entidades, há um limite de 1000 entidades retornadas de uma vez porque público v2 restante limita os resultados de consulta aos resultados de 1000. Você precisa usar ignorar e fazer quando enumerar através de grandes coleções de entidades. 
    
A seguinte função percorre todos os trabalhos na conta de serviços de mídia fornecido. Serviços de mídia retorna trabalhos de 1000 no conjunto de trabalhos. A função utiliza de ignorar e levar para certificar-se de que todos os trabalhos são enumerados (caso você tenha mais de 1000 trabalhos em sua conta).
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>Excluir um ativo

O exemplo a seguir exclui um ativo.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>Excluir um trabalho

Para excluir um trabalho, você deve verificar o estado do trabalho conforme indicado na propriedade estado. Trabalhos que são concluídos ou cancelados podem ser excluídos, enquanto trabalhos que estão em determinados outros estados, como enfileirados, agendada ou processamento, devem ser cancelados primeiro e, em seguida, eles podem ser excluídos.
O exemplo de código a seguir mostra um método para excluir um trabalho estados de trabalho de verificação e excluindo quando o estado é concluído ou cancelado. Este código depende da seção anterior deste tópico para obter uma referência a um trabalho: Obtenha uma referência de trabalho.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>Excluir uma política de acesso

O exemplo de código a seguir mostra como obter uma referência a uma política de acesso com base em uma política de Id e, então, exclua a política.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
