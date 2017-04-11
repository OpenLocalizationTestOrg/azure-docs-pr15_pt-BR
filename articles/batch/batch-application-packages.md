<properties
    pageTitle="Aplicativo de fácil instalação e gerenciamento no lote Azure | Microsoft Azure"
    description="Use o recurso de pacotes de aplicativo do Azure lote para gerenciar com facilidade vários aplicativos e versões para instalação em lote nós de computadores."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Implantação do aplicativo com os pacotes de aplicativos de lote do Azure

O recurso de pacotes de aplicativo do Azure lote fornece fácil gerenciamento de aplicativos de tarefa e sua implantação para os nós de computação em seu pool. Com os pacotes de aplicativos, você pode carregar e gerenciar várias versões dos aplicativos que executar suas tarefas, incluindo seus arquivos de suporte. Você pode, em seguida, implantar automaticamente um ou mais desses aplicativos para os nós de computação em seu pool.

Neste artigo, você aprenderá como carregar e gerenciar pacotes de aplicativos no portal do Azure. Em seguida, você aprenderá como instalá-los em nós de computação de um pool com o [Lote .NET] [ api_net] biblioteca.

> [AZURE.NOTE] O recurso de pacotes de aplicativo descrito aqui substitui o recurso de "Lote aplicativos" disponível nas versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicativos

Você deve [vincular uma conta de armazenamento do Azure](#link-a-storage-account) à sua conta de lote para usar os pacotes do aplicativo.

O recurso de pacotes de aplicativo abordado neste artigo é compatível *apenas* com pools de lote criadas após 10 de março de 2016. Pacotes de aplicativos não serão implantados para calcular nós em pools criados antes desta data.

Este recurso foi introduzido em [API REST de lote] [ api_rest] versão 2015-12-01.2.2 e o correspondente [Lote .NET] [ api_net] versão da biblioteca 3.1.0. Recomendamos que você sempre usar a versão mais recente do API ao trabalhar com lote.

> [AZURE.IMPORTANT] Atualmente, somente os pools de *CloudServiceConfiguration* suportam pacotes de aplicativos. Você não pode usar pacotes de aplicativos em pools criados usando o VirtualMachineConfiguration imagens. Consulte a seção [configuração de máquina Virtual](batch-linux-nodes.md#virtual-machine-configuration) [Linux provisionar nós Azure lote pools de computadores](batch-linux-nodes.md) para obter mais informações sobre as duas configurações diferentes.

## <a name="about-applications-and-application-packages"></a>Sobre aplicativos e pacotes de aplicativos

Dentro do Azure lote, um *aplicativo* se referir a um conjunto de binários versão que podem ser baixados automaticamente para os nós de computação em seu pool. Um *pacote de aplicativo* se refere a um *conjunto específico* desses binários e representa uma determinada *versão* do aplicativo.

![Diagrama de alto nível de aplicativos e pacotes de aplicativos][1]

### <a name="applications"></a>Aplicativos

Um aplicativo em lote contém uma ou mais aplicativo pacotes e especifica as opções de configuração para o aplicativo. Por exemplo, um aplicativo pode especificar a versão padrão do pacote de aplicativo instalar em nós de computação e se os seus pacotes podem ser atualizados ou excluídos.

### <a name="application-packages"></a>Pacotes de aplicativos

Um pacote de aplicativo é um arquivo. zip que contém os binários do aplicativo e arquivos de suporte que são necessários para execução por suas tarefas. Cada pacote de aplicativo representa uma versão específica do aplicativo.

Você pode especificar pacotes de aplicativos no nível do pool e tarefa. Você pode especificar um ou mais desses pacotes e (opcionalmente) uma versão quando você cria uma tarefa ou um pool.

* **Pacotes de pool de aplicativos** são implantados para *cada* nó no pool. Aplicativos são implantados quando um nó ingressa um pool e quando ele é reiniciado ou recriar a imagem.

    Pacotes de aplicativos do pool são apropriados quando todos os nós em um pool de executar tarefas de uma tarefa. Você pode especificar um ou mais pacotes de aplicativo quando você cria um pool, e você pode adicionar ou atualizar pacotes do pool uma existente. Se você atualizar os pacotes de aplicativos de um pool existente, você deve reiniciar seus nós para instalar o novo pacote.

* **Pacotes de aplicativos de tarefa** são implantados somente um nó de computação agendado para executar uma tarefa, logo antes da execução de linha de comando da tarefa. Se o pacote de aplicativo especificado e a versão já estiver no nó, ele não é redistribuído e o pacote existente é usado.

    Pacotes de aplicativos de tarefa são úteis em ambientes de pool compartilhado, onde diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho for concluído. Se seu trabalho tem menos tarefas de nós no pool, pacotes de aplicativos de tarefa podem minimizá transferência de dados desde que o aplicativo é implantado apenas a nós que executar tarefas.

    Outros cenários que podem se beneficiar com os pacotes de aplicativos de tarefa são trabalhos que usam um aplicativo especialmente grande, mas para apenas um pequeno número de tarefas. Por exemplo, um estágio de pré-processamento ou uma tarefa de mesclagem, onde o aplicativo de pré-processamento ou mesclagem é pesado.

> [AZURE.IMPORTANT] Existem restrições quanto ao número de aplicativos e pacotes de aplicativos dentro de uma conta de lote, bem como o tamanho máximo do aplicativo pacote. Consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md) para obter detalhes sobre esses limites.

### <a name="benefits-of-application-packages"></a>Benefícios dos pacotes de aplicativo

Pacotes de aplicativos podem simplificar o código em sua solução de lote e reduzir a sobrecarga necessária para gerenciar os aplicativos que executar suas tarefas.

Tarefa de início do seu pool não precisa especificar uma lista longa de arquivos de recursos individuais para instalar em nós. Você não precisa gerenciar várias versões de seus arquivos de aplicativo no armazenamento do Azure ou em seus nós manualmente. E, você não precisa se preocupar em gerar [SAS URLs](../storage/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos arquivos na sua conta de armazenamento. Lote funciona no plano de fundo com o armazenamento do Azure para armazenar pacotes de aplicativos e implantá-los para nós de computadores.

## <a name="upload-and-manage-applications"></a>Carregar e gerenciar aplicativos

Você pode usar o [portal do Azure] [ portal] ou a biblioteca de [Lote gerenciamento .NET](batch-management-dotnet.md) para gerenciar os pacotes de aplicativo na sua conta do lote. Nas próximas seções, nós primeiro vincular uma conta de armazenamento e discutir adicionar aplicativos e pacotes e gerenciá-los com o portal.

### <a name="link-a-storage-account"></a>Vincular uma conta de armazenamento

Para usar os pacotes de aplicativos, você deve primeiro vincular uma conta de armazenamento do Azure à sua conta do lote. Se você ainda não configurou uma conta de armazenamento para sua conta de lote, o portal do Azure exibirá um aviso na primeira vez que você clique no bloco de **aplicativos** na **conta de lote** lâmina.

> [AZURE.IMPORTANT] Lote atualmente suporta *apenas* o tipo de conta de armazenamento de **finalidade geral** conforme descrito na etapa 5, [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Quando você vincula uma conta de armazenamento do Azure à sua conta do lote, link *apenas* uma conta de armazenamento de **finalidade geral** .

![Nenhum aviso de conta configurada de armazenamento no portal do Azure][9]

O serviço de lote usa a conta de armazenamento associada para o armazenamento e a recuperação de pacotes de aplicativos. Depois que tiver vinculado as duas contas, lote pode implantar automaticamente os pacotes armazenados na conta vinculada de armazenamento para seus nós de computação. Clique em **configurações de conta de armazenamento** na lâmina **Aviso** e clique em **Conta de armazenamento** na lâmina **Conta de armazenamento** para vincular uma conta de armazenamento à sua conta do lote.

![Escolher blade de conta de armazenamento no portal do Azure][10]

Recomendamos que você cria um armazenamento conta *especificamente* para uso com sua conta de lote e selecioná-lo aqui. Para obter detalhes sobre como criar uma conta de armazenamento, consulte "Criar uma conta de armazenamento" no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Depois de criar uma conta de armazenamento, você pode, em seguida, vinculá-lo à sua conta do lote usando a lâmina de **Conta de armazenamento** .

> [AZURE.WARNING] Como lote usa o armazenamento do Azure para armazenar seus pacotes de aplicativos, é [cobrados como normal] [ storage_pricing] para os dados de blob de bloco. Certifique-se de considerar o tamanho e o número de pacotes seu aplicativo e remover periodicamente pacotes preteridos para minimizar o custo.

### <a name="view-current-applications"></a>Aplicativos atuais do modo de exibição

Para exibir os aplicativos na sua conta de lote, clique no item de menu de **aplicativos** no menu à esquerda enquanto exibe a lâmina de **conta de lote** .

![Bloco de aplicativos][2]

Isso abre a lâmina de **aplicativos** :

![Lista de aplicativos][3]

A lâmina de **aplicativos** exibe a identificação de cada aplicativo em sua conta e as seguintes propriedades:

* **Pacotes**– o número de versões associadas a esse aplicativo.
* **Versão padrão**– a versão que será instalada se você não especificar uma versão quando você definir o aplicativo de um pool. Esta configuração é opcional.
* **Permitir atualizações**--o valor que especifica se o pacote de atualizações, exclusões e adições são permitidos. Se isso for definido como **não**, exclusões e atualizações de pacote estão desabilitadas para o aplicativo. Somente novas versões pacote de aplicativo podem ser adicionadas. O padrão é **Sim**.

### <a name="view-application-details"></a>Exibir detalhes do aplicativo

Clique em um aplicativo na lâmina **aplicativos** para abrir a lâmina que inclui os detalhes desse aplicativo.

![Detalhes do aplicativo][4]

Na lâmina de detalhes do aplicativo, você pode configurar as seguintes configurações para seu aplicativo.

* **Permitir atualizações**-- especificar se os seus pacotes de aplicativo podem ser atualizados ou excluídos. Consulte "Atualizar ou excluir um pacote de aplicativos" posteriormente neste artigo.
* **Versão padrão**, especificar um pacote de aplicativo padrão para implantar para nós de computadores.
* **Nome de exibição**-- especificar um "amigável" nome que o lote solução pode usar quando ele exibe informações sobre o aplicativo, como na interface de usuário de um serviço que você forneça seus clientes por meio de lote.

### <a name="add-a-new-application"></a>Adicionar um novo aplicativo

Para criar um novo aplicativo, adicione um pacote de aplicativos e especifique uma ID de aplicativo de novos e exclusivos. O primeiro pacote de aplicativo que você adicionar com a nova ID de aplicativo também criará o novo aplicativo.

Clique em **Adicionar** na lâmina **aplicativos** para abrir o **novo aplicativo** blade.

![Novo blade de aplicativo no portal do Azure][5]

O **novo aplicativo** blade fornece os seguintes campos para especificar as configurações do seu novo aplicativo e pacote de aplicativos.

**Id do aplicativo**

Este campo especifica a ID do seu novo aplicativo, que está sujeitas as regras de validação de ID do Azure lote padrão:

* Pode conter qualquer combinação de caracteres alfanuméricos, incluindo hifens e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser exclusivo dentro da conta de lote.
* É diferenciação de maiusculas preservação e maiusculas e minúsculas.

**Versão**

Especifica a versão do pacote de aplicativo que você está carregando. Cadeias de caracteres de versão estão sujeitos a regras de validação a seguir:

* Pode conter qualquer combinação de caracteres alfanuméricos, incluindo hifens, sublinhados e períodos.
* Não pode conter mais de 64 caracteres.
* Deve ser exclusivo dentro do aplicativo.
* Caso preservando e maiusculas de minúsculas.

**Pacote de aplicativos**

Este campo especifica o arquivo. zip que contém os binários do aplicativo e arquivos de suporte que são necessários para executar o aplicativo. Clique na caixa de **Selecionar um arquivo** ou o ícone de pasta para procure e selecione um arquivo. zip que contém os arquivos do seu aplicativo.

Depois de selecionar um arquivo, clique em **Okey** para iniciar o carregamento ao armazenamento do Azure. Quando a operação de upload for concluída, você será notificado e a lâmina será fechada. Dependendo do tamanho do arquivo que você está carregando e a velocidade de sua conexão de rede, essa operação pode levar alguns instantes.

> [AZURE.WARNING] Não feche o **novo aplicativo** blade antes da operação de carregamento é concluída. Isso interromperá o processo de carregamento.

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicativo

Para adicionar uma nova versão do pacote de aplicativo para um aplicativo existente, selecione um aplicativo na lâmina **aplicativos** , clique em **pacotes**, clique em **Adicionar** para abrir a lâmina de **pacote de adicionar** .

![Adicionar blade de pacote de aplicativo no portal do Azure][8]

Como você pode ver, os campos correspondam da lâmina **novo aplicativo** , mas a caixa **id do aplicativo** está desabilitada. Como você fez para o novo aplicativo, especifique a **versão** para seu novo pacote, navegue até o arquivo. zip do **pacote de aplicativos** , clique em **Okey** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou excluir um pacote de aplicativos

Para atualizar ou excluir um pacote de aplicativo existente, abra a lâmina de detalhes para o aplicativo, clique em **pacotes** para abrir a lâmina de **pacotes** , clique nas **reticências** na linha do pacote de aplicativo que você deseja modificar e selecione a ação que deseja executar.

![Atualizar ou excluir pacote no portal do Azure][7]

**Atualização**

Quando você clicar em **Atualizar**, a lâmina de *pacote de atualização* é exibida. Este blade é semelhante a lâmina o *novo pacote de aplicativo* , porém somente o campo de seleção pacote está habilitado, permitindo que você especifique um novo arquivo ZIP para carregar.

![Blade de pacote de atualização no portal do Azure][11]

**Excluir**

Quando você clicar em **Excluir**, você será solicitado a confirmar a exclusão da versão de pacote e lote exclui o pacote do armazenamento do Azure. Se você excluir a versão padrão de um aplicativo, a configuração de **versão padrão** é removida para o aplicativo.

![Excluir aplicativo][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicativos em nós de computação

Agora que você viu como gerenciar pacotes de aplicativo com o portal do Azure, podemos discutir como implantá-las para nós de computadores e executá-los com tarefas de lote.

### <a name="install-pool-application-packages"></a>Instalar pacotes de pool de aplicativos

Para instalar um pacote de aplicativos em todos os nós de computação em um pool, especifique uma ou mais aplicativo pacote *referências* para o pool. Os pacotes de aplicativo que você especificar para um pool são instalados em cada nó de computação quando nó une o pool e quando o nó for reiniciado ou recriar a imagem.

No lote .NET, especifique uma ou mais [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] quando você cria um novo pool, ou para um pool existente. O [ApplicationPackageReference] [ net_pkgref] classe especifica uma ID de aplicativo e versão instalar em um pool de nós de computadores.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Se uma implantação de pacote de aplicativo falhar por algum motivo, o serviço de lote marca o nó [inutilizável][net_nodestate], e não há tarefas serão agendadas para execução nesse nó. Nesse caso, você deve **Reiniciar** o nó para reiniciar a implantação de pacote. Reiniciar o nó também permitirá a agendamento da tarefa no nó novamente.

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicativos de tarefa

Semelhante a um pool, que você especificar de pacote de aplicativo *referências* para uma tarefa. Quando uma tarefa está agendada para ser executado em um nó, o pacote é baixado e extraído antes de linha de comando da tarefa é executada. Se um pacote especificado e a versão já estiver instalado no nó, o pacote não foi baixado e o pacote existente é usado.

Para instalar um pacote de aplicativo de tarefa, configurar da tarefa [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] propriedade:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Execute os aplicativos instalados

Os pacotes que você especificou para uma tarefa ou um pool são baixados e extraídos em um diretório chamado dentro do `AZ_BATCH_ROOT_DIR` do nó. Em lotes também cria uma variável de ambiente que contém o caminho para o diretório nomeado. As linhas de comando tarefa use esta variável de ambiente ao referenciar o aplicativo no nó. A variável é no seguinte formato:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`e `version` são valores que correspondem a versão de aplicativo e pacote que você especificou para implantação. Por exemplo, se especificada essa versão 2.7 do aplicativo *Misturador* deve ser instalado, as linhas de comando tarefa usaria esta variável de ambiente para acessar seus arquivos:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se você especificar uma versão padrão para um aplicativo, você pode omitir o sufixo da versão. Por exemplo, se você definir "2.7" como a versão padrão do aplicativo *Misturador*, suas tarefas podem fazer referência a seguinte variável de ambiente e elas serão executadas versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O trecho de código a seguir mostra uma linha de comando de tarefa de exemplo que inicia a versão padrão do aplicativo *Misturador* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Consulte a [Visão geral do recurso de lote](batch-api-basics.md) para mais informações sobre configurações de ambiente de nó de computação de [configurações de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) .

## <a name="update-a-pools-application-packages"></a>Pacotes de aplicativo de um pool de atualização

Se um pool existente já foi configurado com um pacote de aplicativos, você pode especificar um novo pacote para o pool. Se você especificar uma nova referência de pacote para um pool, a seguir se aplicarem:

* Todos os novos nós que ingressar o pool e qualquer nó existente que seja reiniciado ou criada serão instalados o pacote recentemente especificado.
* Calcule nós que já estão no pool quando você atualiza as referências de pacote não instale automaticamente o novo pacote de aplicativo. Esses calcular nós devem ser reinicializados ou criadas para receber o novo pacote.
* Quando um novo pacote é implantado, as variáveis de ambiente criado refletem as novas referências de pacote de aplicativo.

Neste exemplo, o pool existente tem versão 2.7 do aplicativo *Misturador* configurado como um dos seus [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Para atualizar nós do pool com versão 2.76b, especifique uma nova [ApplicationPackageReference] [ net_pkgref] com a nova versão e confirmar a alteração.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Agora que a nova versão tiver sido configurada, qualquer *novo* nó que associa o pool terá versão 2.76b implantado nele. Para instalar o 2.76b em nós *já* no pool, reinicializar ou imagem-los. Observe que nós reinicializado manterá os arquivos de implantações de pacote anterior.

## <a name="list-the-applications-in-a-batch-account"></a>Listar os aplicativos em uma conta de lote

Você pode listar os aplicativos e seus pacotes em uma conta de lote usando o [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] método.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Conclusão

Com os pacotes de aplicativos, você pode ajudar seus clientes selecione os aplicativos para seus trabalhos e especifique a versão exata para usar ao processar trabalhos com o seu serviço habilitado para lotes. Você também pode fornecer a capacidade de seus clientes carregar e controlar seus próprios aplicativos no seu serviço.

## <a name="next-steps"></a>Próximas etapas

* A [API REST de lote] [ api_rest] também oferece suporte para trabalhar com pacotes de aplicativos. Por exemplo, consulte o [applicationPackageReferences] [ rest_add_pool_with_packages] elemento em [Adicionar um pool a uma conta] [ rest_add_pool] para obter informações sobre como especificar pacotes para instalar usando a API REST. Consulte [aplicativos] [ rest_applications] para obter detalhes sobre como obter informações de aplicativo usando a API REST de lote.

* Saiba como programaticamente [Gerenciar contas de lote do Azure e cotas com .NET de gerenciamento de lote](batch-management-dotnet.md). O [Lote de gerenciamento .NET] [ api_net_mgmt] biblioteca pode habilitar recursos de criação e a exclusão de contas para seu aplicativo de lote ou serviço.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicativo"
[2]: ./media/batch-application-packages/app_pkg_02.png "Bloco de aplicativos no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Blade de aplicativos no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blade de detalhes do aplicativo no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Novo blade de aplicativo no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Atualizar ou excluir pacotes suspenso no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Novo blade de pacote de aplicativo no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nenhum alerta de conta de armazenamento vinculada"
[10]: ./media/batch-application-packages/app_pkg_10.png "Escolher blade de conta de armazenamento no portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Blade de pacote de atualização no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Excluir a caixa de diálogo de confirmação de pacote no portal do Azure"
