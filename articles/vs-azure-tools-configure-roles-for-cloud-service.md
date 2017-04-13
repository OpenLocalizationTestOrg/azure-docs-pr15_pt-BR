<properties
   pageTitle="Configurar as funções para um serviço de nuvem Azure com o Visual Studio | Microsoft Azure"
   description="Saiba como instalar e configurar funções para serviços de nuvem Azure usando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Configurar as funções para um serviço de nuvem Azure com o Visual Studio

Um serviço de nuvem Azure pode ter uma ou mais trabalhador ou funções da web. Para cada função, você precisa definir como essa função estiver configurada e também configurar como essa função é executado. Para saber mais sobre as funções nos serviços de nuvem, veja o vídeo de [Introdução aos serviços de nuvem do Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). As informações de seu serviço de nuvem são armazenadas nos seguintes arquivos:

- **ServiceDefinition.csdef**

    O arquivo de definição de serviço define as configurações de tempo de execução para seu serviço de nuvem, incluindo quais funções são necessárias, pontos de extremidade e tamanho da máquina virtual. Nenhum dos dados armazenados nesse arquivo podem ser alteradas quando sua função está em execução.

- **ServiceConfiguration**

    O arquivo de configuração do serviço configura quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função. Os dados armazenados neste arquivo podem ser alterados enquanto sua função está em execução.

Para poder armazenar valores diferentes para essas configurações como sua função é executado, você pode ter várias configurações de serviço. Você pode usar uma configuração de serviço diferente para cada ambiente de implantação. Por exemplo, você pode definir a cadeia de caracteres de conexão de conta de armazenamento para usar o emulador de armazenamento do Azure local em uma configuração de serviço local e criar outra configuração de serviço para usar o armazenamento do Azure na nuvem.

Quando você cria um novo serviço de nuvem Azure no Visual Studio, duas configurações de serviço são criadas por padrão. Essas configurações são adicionadas ao seu projeto Azure. As configurações são nomeadas:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Configurar um serviço de nuvem do Azure

Você pode configurar um serviço de nuvem Azure do Solution Explorer no Visual Studio, conforme mostrado na ilustração a seguir.

![Configurar o serviço de nuvem](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Para configurar um serviço de nuvem do Azure

1. Para configurar cada função em seu projeto Azure do **Solution Explorer**, abra o menu de atalho para a função do Azure projeto e escolha **Propriedades**.

    Uma página com o nome da função é exibida no editor do Visual Studio. A página exibe os campos para a guia **configuração** .

1. Na lista de **Configuração do serviço** , escolha o nome da configuração do serviço que você deseja editar.

    Se você quiser fazer alterações em todas as configurações de serviço para essa função, você pode escolher **Todas as configurações**.

    >[AZURE.IMPORTANT] Se você escolher uma configuração de serviço específico, algumas propriedades estão desativadas porque eles só podem ser definidos para todas as configurações. Para editar estas propriedades, você deve escolher todas as configurações.

    Agora você pode escolher uma guia para atualizar as propriedades enabled essa exibição.

## <a name="change-the-number-of-role-instances"></a>Alterar o número de instâncias de função

Para melhorar o desempenho do seu serviço de nuvem, você pode alterar o número de instâncias de uma função que estejam em execução, com base no número de usuários ou a carga esperado para uma função em particular. Uma outra máquina virtual é criada para cada instância de uma função quando o serviço de nuvem é executado no Azure. Isso afetará a cobrança para a implantação desse serviço de nuvem. Para obter mais informações sobre faturamento, consulte [compreender a sua fatura do Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-number-of-instances-for-a-role"></a>Para alterar o número de instâncias para uma função

1. Escolha a guia de **configuração** .

1. Na lista de **Configuração do serviço** , escolha a configuração do serviço que você deseja atualizar.

    >[AZURE.NOTE] Você pode definir a contagem de instância para uma configuração de serviço específico ou para todas as configurações de serviço.

1. Na caixa de texto **contagem de instância** , insira o número de instâncias que você deseja iniciar para esta função.

    >[AZURE.NOTE] Cada instância é executada em uma outra máquina virtual quando você publica seu serviço de nuvem do Azure.

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações no arquivo de configuração do serviço.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gerenciar cadeias de caracteres de conexão para contas de armazenamento

Você pode adicionar, remover ou modificar cadeias de caracteres de conexão para suas configurações de serviço. Talvez você queira cadeias de caracteres de conexão diferentes para as configurações de serviço diferente. Por exemplo, você pode querer uma cadeia de conexão local para uma configuração de serviço local que tenha um valor de `UseDevelopmentStorage=true`. Convém também definir uma configuração de serviço de nuvem que usa uma conta de armazenamento no Azure.

>[AZURE.WARNING] Quando você insere as informações de chave de conta de armazenamento do Azure para uma cadeia de conexão de conta de armazenamento, essas informações são armazenadas localmente no arquivo de configuração do serviço. No entanto, essas informações não atualmente são armazenadas como texto criptografado.

Usando um valor diferente para cada configuração de serviço, você não precisa usar cadeias de caracteres de conexão diferente no seu serviço de nuvem ou modificar seu código, quando você publica seu serviço de nuvem do Azure. Você pode usar o mesmo nome para a cadeia de conexão no seu código e o valor será diferente, com base na configuração do serviço que você selecionar quando você cria o seu serviço de nuvem ou quando publicá-la.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Gerenciar cadeias de caracteres de conexão para contas de armazenamento

1. Escolha a guia **configurações** .

1. Na lista de **Configuração do serviço** , escolha a configuração do serviço que você deseja atualizar.

    >[AZURE.NOTE] Você pode atualizar cadeias de caracteres de conexão para uma configuração de serviço específico, mas se você precisar adicionar ou excluir uma cadeia de conexão que você deve selecionar todas as configurações.

1. Para adicionar uma cadeia de conexão, escolha o botão **Adicionar configuração** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , digite o nome que você deseja usar para a cadeia de conexão.

1. Na lista suspensa **tipo** , escolha **Cadeia de Conexão**.

1. Para alterar o valor para a cadeia de conexão, escolha o botão de reticências (...). A caixa de diálogo **Criar cadeia de Conexão de armazenamento** aparece.

1. Para usar o emulador de conta de armazenamento local, escolha o botão de opção **emulador de armazenamento do Microsoft Azure** e, em seguida, escolha o botão de **Okey** .

1. Para usar uma conta de armazenamento no Azure, escolha o botão de opção de **sua assinatura** e selecione a conta de armazenamento desejada.

1. Para usar credenciais personalizadas, escolha o botão de opções **inseridos manualmente credenciais** . Insira o nome da conta de armazenamento e a chave primária ou segunda. Para obter informações sobre como criar uma conta de armazenamento e como inserir os detalhes da conta de armazenamento na caixa de diálogo **Criar cadeia de Conexão de armazenamento** , consulte [preparar para publicar ou implantar um aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para excluir uma cadeia de conexão, selecione a cadeia de conexão e, em seguida, escolha o botão de **Configuração de remover** .

1. Escolha o ícone **Salvar** na barra de ferramentas para salvar essas alterações no arquivo de configuração do serviço.

1. Para acessar a cadeia de conexão no arquivo de configuração do serviço, você deve obter o valor da configuração. O código a seguir mostra um exemplo onde o armazenamento de blob é criado e carregados usando uma cadeia de conexão de dados `MyConnectionString` do arquivo de configuração de serviço quando um usuário escolhe **Button1** na página Default. aspx na função da web para um serviço de nuvem Azure. Adicione o seguinte usando instruções para Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Abra Default.aspx.cs no modo design e adicione um botão da caixa de ferramentas. Adicione o seguinte código para o `Button1_Click` método. Este código usa `GetConfigurationSettingValue` para obter o valor do arquivo de configuração de serviço para a cadeia de conexão. Depois de um blob é criado na conta de armazenamento que é referenciada na cadeia de conexão `MyConnectionString` e finalmente o programa adiciona o texto para o blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Adicionar configurações personalizadas para usar em seu serviço de nuvem do Azure

Configurações personalizadas no arquivo de configuração do serviço permitem que você adicione um nome e o valor de uma cadeia de caracteres para uma configuração de serviço específico. Você pode optar por usar essa configuração para configurar um recurso no seu serviço de nuvem lendo o valor da configuração e usando esse valor para controlar a lógica em seu código. Você pode alterar esses valores de configuração de serviço sem precisar recriar seu pacote de serviço ou quando o seu serviço de nuvem está sendo executado. Seu código pode verificar notificações de quando uma configuração é alterada. Consulte [RoleEnvironment.Changing evento](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Você pode adicionar, remover ou modificar as configurações personalizadas para suas configurações de serviço. Talvez você queira valores diferentes para essas cadeias de caracteres para configurações de serviço diferente.

Usando um valor diferente para cada configuração de serviço, você não tenha usar cadeias de caracteres diferentes em seu serviço de nuvem ou modificar o seu código quando você publica seu serviço de nuvem do Azure. Você pode usar o mesmo nome para a cadeia de caracteres em seu código e o valor será diferente, com base na configuração do serviço que você selecionar quando você cria o seu serviço de nuvem ou quando publicá-la.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Para adicionar configurações personalizadas para usar em seu serviço de nuvem do Azure

1. Escolha a guia **configurações** .

1. Na lista de **Configuração do serviço** , escolha a configuração do serviço que você deseja atualizar.

    >[AZURE.NOTE] Você pode atualizar cadeias de caracteres para uma configuração de serviço específico, mas se você precisar adicionar ou excluir uma cadeia de caracteres, você deve selecionar **Todas as configurações**.

1. Para adicionar uma cadeia de caracteres, escolha o botão **Adicionar configuração** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , digite o nome que você deseja usar para a cadeia de caracteres.

1. Na lista suspensa **tipo** , escolha **cadeia de caracteres**.

1. Para adicionar ou alterar o valor de cadeia de caracteres, na caixa de texto **valor** , digite o novo valor.

1. Para excluir uma cadeia de caracteres, selecione a cadeia de caracteres e, em seguida, escolha o botão de **Configuração de remover** .

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações no arquivo de configuração do serviço.

1. Para acessar a cadeia de caracteres no arquivo de configuração do serviço, você deve obter o valor da configuração.

    Você precisa certificar-se de que a seguir usando instruções já são adicionadas ao Default.aspx.cs exatamente como você fez no procedimento anterior.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Adicione o seguinte código para o `Button1_Click` método para acessar essa cadeia de caracteres da mesma maneira que você acessa uma cadeia de conexão. Seu código pode executar alguns códigos específicos com base no valor da cadeia de caracteres configurações para o arquivo de configuração do serviço que é usado.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gerenciar armazenamento local para cada instância de função

Você pode adicionar armazenamento de sistema de arquivo local para cada instância de uma função. Você pode armazenar aqui de dados locais que não precisam ser acessado por outras funções. Quaisquer dados que você não precisa salvar em tabela, blob ou armazenamento de banco de dados SQL podem ser armazenados aqui. Por exemplo, você pode usar este armazenamento local de cache de dados que talvez precise ser usada novamente. Estes dados armazenados não podem ser acessados por outras instâncias de uma função. 

Configurações de armazenamento local se aplicam a todas as configurações de serviço. Você só pode adicionar, remover ou modificar o armazenamento local para todas as configurações de serviço.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Para gerenciar o armazenamento local para cada instância de função

1. Escolha a guia de **Armazenamento Local** .

1. Na lista de **Configuração do serviço** , escolha **Todas as configurações**.

1. Para adicionar uma entrada de armazenamento local, escolha o botão **Adicionar armazenamento Local** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , digite o nome que você deseja usar para este armazenamento local.

1. Na caixa de texto **tamanho** , digite o tamanho em MB que você precisa para esse armazenamento local.

1. Para remover os dados neste armazenamento local quando a máquina virtual para esta função é reciclada, marque a caixa de seleção **Limpar na função Lixeira** .

1. Para editar uma entrada de armazenamento local existente, selecione a linha que você precisa atualizar. Em seguida, você pode editar os campos, conforme descrito nas etapas anteriores.

1. Para excluir uma entrada de armazenamento local, escolha a entrada de armazenamento na lista e escolha o botão **Remover armazenamento Local** .

1. Para salvar essas alterações em arquivos de configuração do serviço, escolha o ícone **Salvar** na barra de ferramentas.

1. Para acessar o armazenamento local que você adicionou no arquivo de configuração do serviço, você deve obter o valor da configuração do recurso local. Use as seguintes linhas de código para acessar esse valor, crie um arquivo chamado **MyStorageTest.txt** e escrever uma linha de dados de teste para o arquivo. Você pode adicionar este código para o `Button_Click` método que você usou nos procedimentos anteriores:

1. Você precisa certificar-se de que a seguir usando instruções são adicionadas à Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Adicione o seguinte código para o `Button1_Click` método. Isso cria o arquivo no armazenamento local e grava dados de teste para o arquivo.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Opcional) Para exibir este arquivo criado quando você executa seu serviço de nuvem localmente, use as seguintes etapas:

  1. Execute a função da web e selecione **Button1** para certificar-se de que o código dentro `Button1_Click` é chamado.

  1. Na área de notificação, abra o menu de atalho para o ícone do Azure e escolha **Mostrar calcular emulador interface do usuário**. A caixa de diálogo **Emulador de computação Azure** aparece.

  1. Selecione a função da web.

  1. Na barra de menus, escolha **Ferramentas** **armazenamento local aberto**. Aparece uma janela do Windows Explorer.

  1. Na barra de menus, digite **MyStorageTest.txt** na caixa de texto de **pesquisa** e escolha **Enter** para iniciar a pesquisa.

    O arquivo é exibido nos resultados da pesquisa.

  1. Para exibir o conteúdo do arquivo, abra o menu de atalho para o arquivo e escolha **Abrir**.

## <a name="collect-cloud-service-diagnostics"></a>Coletar diagnóstico de serviço de nuvem

Você pode coletar dados de diagnóstico para o serviço de nuvem Azure. Esses dados são adicionados a uma conta de armazenamento. Talvez você queira cadeias de caracteres de conexão diferentes para as configurações de serviço diferente. Por exemplo, você pode querer uma conta de armazenamento local para uma configuração de serviço local que tenha um valor de seja UseDevelopmentStorage = true. Convém também definir uma configuração de serviço de nuvem que usa uma conta de armazenamento no Azure. Para obter mais informações sobre o diagnóstico do Azure, consulte coletar dados de registro em log pelo usando o diagnóstico do Azure.

>[AZURE.NOTE] A configuração do serviço local já está configurada para usar recursos locais. Se você usar a configuração do serviço de nuvem para publicar seu serviço de nuvem Azure, a cadeia de conexão que você especificar quando você publica também é usada para a cadeia de conexão de diagnóstico, a menos que você especificou uma cadeia de conexão. Se você compactar seu serviço de nuvem usando o Visual Studio, a cadeia de conexão na configuração de serviço não é alterada.

### <a name="to-collect-cloud-service-diagnostics"></a>Coletar diagnóstico de serviço de nuvem

1. Escolha a guia de **configuração** .

1. Na lista de **Configuração do serviço** , escolha a configuração do serviço que você deseja atualizar ou escolha **Todas as configurações**.

    >[AZURE.NOTE] Você pode atualizar a conta de armazenamento para uma configuração de serviço específico, mas se você deseja habilitar ou desabilitar o diagnóstico escolha todas as configurações.

1. Para habilitar o diagnóstico, marque a caixa de seleção **Habilitar o diagnóstico** .

1. Para alterar o valor para a conta de armazenamento, escolha o botão de reticências (...).

    A caixa de diálogo **Criar cadeia de Conexão de armazenamento** aparece.

1. Para usar uma cadeia de conexão local, escolha a opção de emulador de armazenamento do Azure e escolha o botão de **Okey** .

1. Para usar uma conta de armazenamento associada a sua assinatura do Azure, escolha a opção de **sua assinatura** .

1. Para usar uma conta de armazenamento para a cadeia de conexão local, escolha a opção **inseridos manualmente credenciais** .

    Para obter mais informações sobre como criar uma conta de armazenamento e como inserir os detalhes da conta de armazenamento na caixa de diálogo **Criar cadeia de Conexão de armazenamento** , consulte [preparar para publicar ou implantar um aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Escolha a conta de armazenamento que você deseja usar no **nome da conta**.

    Se você estiver manualmente digitar suas credenciais de conta de armazenamento, copie ou digite sua chave primária na **chave da conta**. Esta tecla pode ser copiada do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885). Para copiar esta chave, seguindo estas etapas do modo de exibição **Contas de armazenamento** no [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Selecione a conta de armazenamento que você deseja usar para o seu serviço de nuvem.

  1. Escolha o botão de **Gerenciamento de chaves de acesso** localizado na parte inferior da tela. Aparece a caixa de diálogo **Gerenciar teclas de acesso** .

  1. Para copiar a tecla de acesso, escolha o botão **Copiar para área de transferência** . Agora você pode colar essa chave para o campo de **chave da conta** .

1. Para usar a conta de armazenamento que você fornecer, como a cadeia de conexão para diagnóstico (e cache) quando você publica seu serviço de nuvem Azure, selecione a caixa de seleção **Atualizar armazenamento conexão cadeias de desenvolvimento para diagnóstico e cache com o armazenamento do Azure durante a publicação no Azure de credenciais de conta** .

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações no arquivo de configuração do serviço.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Alterar o tamanho da máquina virtual usada para cada função

Você pode definir o tamanho da máquina virtual para cada função. Você só pode definir esse tamanho para todas as configurações de serviço. Se você selecionar um tamanho menor de máquina, em seguida, menos cores de CPU, memória e disco local armazenamento é alocado. A largura de banda alocada também é menor. Para obter mais informações sobre esses tamanhos e os recursos alocados, consulte [tamanhos para serviços de nuvem](cloud-services/cloud-services-sizes-specs.md).

Os recursos necessários para cada máquina virtual no Azure afeta o custo da execução seu serviço de nuvem no Azure. Para obter mais informações sobre cobrança do Azure, consulte [compreender a sua fatura do Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-size-of-the-virtual-machine"></a>Para alterar o tamanho da máquina virtual

1. Escolha a guia de **configuração** .

1. Na lista de **Configuração do serviço** , escolha **Todas as configurações**.

1. Para selecionar o tamanho da máquina virtual para esta função, escolha o tamanho apropriado na lista **tamanho da máquina virtual** .

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações no arquivo de configuração do serviço.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Gerenciar pontos de extremidade e certificados para as funções

Você configurar pontos de extremidade de rede, especificando o protocolo, o número da porta e, para HTTPS, as informações do certificado SSL. Versões antes de junho de 2012 suportam HTTP, HTTPS e TCP. A versão de junho de 2012 compatível com os protocolos e UDP. Você não pode usar UDP para pontos de extremidade de entrada no emulador computação. Você pode usar esse protocolo somente para pontos de extremidade internos.

Para melhorar a segurança do seu serviço de nuvem Azure, você pode criar pontos de extremidade que usam o protocolo HTTPS. Por exemplo, se você tiver um serviço de nuvem que é usado por clientes para ordens de compra, você quer certificar-se de que suas informações estão seguras usando SSL.

Você também pode adicionar pontos de extremidade que podem ser usados internamente ou externamente. Pontos de extremidade externos são chamados de pontos de extremidade de entrada. Um ponto de extremidade de entrada permite que outro ponto de acesso aos usuários para seu serviço de nuvem. Se você tiver um serviço WCF, talvez você queira expor um ponto de extremidade interno para uma função da web Use para acessar esse serviço.

>[AZURE.IMPORTANT] Você só pode atualizar pontos de extremidade para todas as configurações de serviço.

Se você adicionar pontos de extremidade HTTPS, você precisa usar um certificado SSL. Para fazer isso, você pode associar certificados a sua função para todas as configurações de serviço e usá-los para seus pontos de extremidade.

>[AZURE.IMPORTANT] Esses certificados não são compactados com o seu serviço. Você deve carregar seus certificados separadamente no Azure por meio do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

Quaisquer certificados de gerenciamento que você associar suas configurações de serviço se aplicam somente quando o seu serviço de nuvem é executado no Azure. Quando seu serviço de nuvem é executado no ambiente de desenvolvimento local, um certificado padrão que é gerenciado pelo emulador computação Azure é usado.

### <a name="to-add-a-certificate-to-a-role"></a>Para adicionar um certificado a uma função

1. Escolha a guia **certificados** .

1. Na lista de **Configuração do serviço** , escolha **Todas as configurações**.

    >[AZURE.NOTE] Para adicionar ou remover certificados, você deve selecionar todas as configurações. Se for necessário, você pode atualizar o nome e a impressão digital de uma configuração de serviço específico.

1. Para adicionar um certificado para essa função, escolha o botão **Adicionar certificado** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , digite o nome para o certificado.

1. Na lista **Local de armazenamento** , escolha o local para o certificado que você deseja adicionar.

1. Na lista **Armazenar nome** , escolha o armazenamento que você deseja usar para selecionar o certificado.

1. Para adicionar o certificado, escolha o botão de reticências (...). A caixa de diálogo de **Segurança do Windows** é exibida.

1. Escolha o certificado que você deseja usar na lista e, em seguida, escolha o botão de **Okey** .

    >[AZURE.NOTE] Quando você adiciona um certificado do repositório do certificado, quaisquer certificados intermediários são adicionados automaticamente às definições de configuração para você. Esses certificados intermediários também devem ser carregados Azure para configurar corretamente o serviço para SSL.

1. Para excluir um certificado, escolha o certificado e, em seguida, escolha o botão de **Certificado remover** .

1. Escolha o ícone **Salvar** na barra de ferramentas para salvar essas alterações em arquivos de configuração do serviço.

### <a name="to-manage-endpoints-for-a-role"></a>Para gerenciar pontos de extremidade para uma função

1. Escolha a guia de **pontos de extremidade** .

1. Na lista de **Configuração do serviço** , escolha **Todas as configurações**.

1. Para adicionar um ponto de extremidade, escolha o botão **Adicionar ponto de extremidade** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , digite o nome que você deseja usar para este ponto de extremidade.

1. Escolha o tipo de ponto de extremidade que você precisa na lista **tipo** .

1. Escolha o protocolo para o ponto de extremidade que você precisa na lista de **protocolo** .

1. Se for um ponto de extremidade de entrada, na caixa de texto **Porta pública** , insira a porta pública para usar.

1. Na caixa de texto **Porta particular** digite a porta particular para usar.

1. Se o ponto de extremidade requer o protocolo https, escolha um certificado para usar na lista **Nome do certificado SSL** .

    >[AZURE.NOTE] Esta lista mostra os certificados que você adicionou para esta função na guia **certificados** .

1. Escolha o botão **Salvar** na barra de ferramentas para salvar essas alterações em arquivos de configuração do serviço.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre projetos Azure no Visual Studio lendo [Configurando um projeto do Azure](vs-azure-tools-configuring-an-azure-project.md). Saiba mais sobre o esquema de serviço de nuvem lendo [Referência de esquema](https://msdn.microsoft.com/library/azure/dd179398).
