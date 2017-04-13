<properties
   pageTitle="Como migrar e publicar um aplicativo da Web em um serviço de nuvem Azure do Visual Studio | Microsoft Azure"
   description="Saiba como migrar e publicar seu aplicativo web para um serviço de nuvem Azure usando o Visual Studio."
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

# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Como: migrar e publicar um aplicativo Web em um serviço de nuvem Azure do Visual Studio

Para tirar proveito dos serviços de hospedagem e escalabilidade do Azure, talvez você queira migrar e publicar seu aplicativo web para um serviço de nuvem Azure. Você pode executar um aplicativo da web no Azure com mínimas alterações a seu aplicativo existente.

>[AZURE.NOTE] Este tópico é sobre como distribuir para serviços de nuvem, não a sites da web. Para obter informações sobre como distribuir para sites da web, consulte [implantar um aplicativo web no serviço de aplicativo do Azure](./app-service-web/web-sites-deploy.md).

Para uma lista de modelos específicos que são suportados para Visual c# e Visual Basic, consulte a seção **Suporte para modelos de projeto** mais adiante neste tópico.

Primeiro você deve habilitar o aplicativo da web para o Azure do Visual Studio. A ilustração a seguir mostra as principais etapas para publicar seu aplicativo web existente, adicionando um projeto do Azure para usar para implantação. Esse processo adiciona um projeto Azure com a função web necessários à sua solução. Com base no tipo de projeto da web que você tem, as propriedades do projeto para conjuntos também são atualizadas quando o pacote de serviço requer conjuntos adicionais para implantação.

![Publicar um aplicativo Web para o Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] O **Converter**, comando **Converter para o projeto de serviço de nuvem do Azure** é exibido somente para o projeto da web em sua solução. Por exemplo, o comando não está disponível para um projeto do Silverlight em sua solução.
Quando você cria um pacote de serviço ou publica seu aplicativo no Azure, avisos ou erros podem ocorrer. Esses erros e avisos podem ajudá-lo a corrigir problemas antes de implantar no Azure. Por exemplo, você poderá receber um aviso sobre um assembly ausente. Para obter mais informações sobre como tratar avisos como erros, consulte [Configurar um projeto de serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Se você cria seu aplicativo, executá-lo localmente usando o emulador de computação ou publicá-lo no Azure, talvez você veja o seguinte erro na janela de **Lista de erro** : **O caminho especificado, o nome do arquivo ou ambos são muito longos**. Este erro ocorre porque o comprimento do nome do projeto Azure totalmente qualificado é muito longo. O comprimento do nome do projeto, incluindo o caminho completo, não pode ter mais de 146 caracteres. Por exemplo, este é o nome de projeto completo incluindo o caminho do arquivo para um projeto do Azure que é criado para um aplicativo do Silverlight: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Talvez você precise mover sua solução para uma pasta diferente que tenha um caminho mais curto para reduzir o tamanho do nome do projeto totalmente qualificado.

Para migrar e publicar um aplicativo web no Azure do Visual Studio, siga estas etapas.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Habilitar um aplicativo Web para implantação do Azure

### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Para habilitar um aplicativo web para implantação do Azure

1. Para habilitar o aplicativo da web para implantação do Azure, abra o menu de atalho para um projeto da web na sua solução e escolha Adicionar projeto de implantação do Azure.

    Ocorrem as seguintes ações:

    - Um projeto do Azure chamado `<name of the web project>.Azure` é adicionado à solução para o seu aplicativo.

    - Uma função da web para o projeto da web é adicionada a este projeto Azure.

    - A propriedade **Cópia Local** está definida como true para quaisquer conjuntos necessários para MVC MVC 2, MVC 3, 4 e aplicativos de negócios do Silverlight. Isso adiciona esses conjuntos para o pacote de serviço que é usado para implantação.

  >[AZURE.IMPORTANT] Se você tiver outros conjuntos ou arquivos que são necessários para este aplicativo web, você deve definir manualmente as propriedades para esses arquivos. Para obter informações sobre como definir essas propriedades, consulte a seção **Incluir arquivos no pacote de serviço** mais adiante neste artigo.

  >[AZURE.NOTE] Se já existe uma função da web para um projeto da web específico em um projeto do Azure na solução, **Converter**, **Converter para o projeto de serviço de nuvem do Azure** não é exibido no menu de atalho para este projeto da web.

  Se você tiver vários projetos da web no seu aplicativo web e você deseja criar funções da web para cada projeto da web, você deve executar as etapas neste procedimento para cada projeto da web. Isso cria projetos Azure separados para cada função da web. Cada projeto da web pode ser publicado separadamente. Como alternativa, você pode adicionar manualmente outra função de web a um projeto Azure existente no seu aplicativo web. Para fazer isso, abra o menu de atalho para a pasta de **funções** em seu projeto Azure, escolha **Adicionar**, em seguida, **Projeto de função Web na solução**, escolha o projeto para adicionar como uma função da web e, em seguida, escolha o botão **Okey** .

## <a name="use-an-azure-sql-database-for-your-application"></a>Usar um banco de dados do SQL Azure para seu aplicativo

Se você tiver uma cadeia de conexão para o aplicativo web que usa um banco de dados do SQL Server que é o local, você deve alterar essa cadeia de conexão para usar uma instância de banco de dados SQL que hospeda Azure em vez disso.

>[AZURE.IMPORTANT] Sua assinatura deve habilitar a usar o banco de dados SQL. Se você acessar sua assinatura do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885), você pode determinar quais serviços fornece sua assinatura. As instruções a seguir se aplicam lançadas [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885). Se você estiver usando o [portal do Azure](http://portal.microsoft.com), vá para o próximo procedimento.

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Para usar uma instância de banco de dados SQL na sua função web para sua cadeia de conexão

1. Para criar uma instância do banco de dados SQL no [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885), siga as etapas no seguinte artigo: [criar um banco de dados do SQL Server](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Quando você configura as regras de firewall para sua instância do banco de dados SQL, você deve selecionar a caixa de seleção **Permitir que outros serviços do Azure acessar este servidor** .

1. Para criar uma instância do banco de dados SQL para usar para sua cadeia de conexão, siga as etapas na próxima seção no seguinte artigo: [criar um banco de dados do SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Para copiar a cadeia de conexão de ADO.NET para usar para sua cadeia de conexão, execute as seguintes etapas no [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).  

  1. Escolha o botão de **banco de dados** e, em seguida, abra o nó para a assinatura que você usou para criar a instância do banco de dados SQL.

  1. Para exibir as instâncias disponíveis do banco de dados SQL, escolha o nó de **Bancos de dados SQL** .

  1. Para exibir as propriedades do banco de dados, escolha o banco de dados. O modo de exibição de **Propriedades** será exibida.

      >[AZURE.NOTE] Se o modo de exibição de **Propriedades** não aparecer, você talvez seja necessário abri-lo usando o divisor.

  1. Para exibir as cadeias de caracteres de conexão, escolha o botão de reticências (...) ao lado de modo de exibição.

    A caixa de diálogo de **Cadeias de caracteres de Conexão** é exibida.

  1. Para copiar a cadeia de conexão de ADO.NET, realce o texto e escolha as teclas Ctrl + C.

  1. Para fechar a caixa de diálogo, escolha o botão **Fechar** .

1. Para substituir a cadeia de conexão no arquivo Web. config para usar essa instância do banco de dados SQL, abra o arquivo Web. config, realce a entrada de cadeia de conexão existente e escolha as teclas Ctrl + V. A cadeia de conexão de ADO.NET para a instância do banco de dados SQL substitui a cadeia de conexão existente.

1. Você também deve adicionar o parâmetro `MultipleActiveResultSets=True` para a cadeia de conexão. A cadeia de conexão deve ter o seguinte formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Opcional) Um método alternativo a alteração a cadeia de conexão diretamente no arquivo Web. config é adicionar uma seção em um dos arquivos de transformação de Web. config, dependendo da configuração de compilação que você usa para criar o pacote de serviço. Abra o arquivo de Web.Debug.Config ou o arquivo Web.Release.Config. Adicione a seção a seguir para este arquivo:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Salve o arquivo que você modificou e republique o aplicativo.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Usar uma instância do banco de dados SQL usando o portal de clássico Azure

1. No [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885), escolha o nó de bancos de dados SQL.

  - Se a instância do banco de dados SQL que você deseja usar for exibida, escolha abri-lo.

  - Se você ainda não tiver criado quaisquer instâncias, escolha o link apropriado e, em seguida, criar uma instância.

1. Depois de abrir ou criar uma instância de banco de dados, escolha o link de **Cadeias de caracteres de Conexão** .

1. Na parte inferior da página, escolha o link para definir configurações de firewall e aceite os valores padrão ou configurar os valores que você precisa.

1. Copie a cadeia de conexão de ADO.NET, colá-lo em seu arquivo Web. config sobre a cadeia de conexão antiga do banco de dados local e certifique-se de adicionar `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicar um aplicativo Web para o Azure

### <a name="to-publish-a-web-application-to-azure"></a>Publicar um aplicativo Web para Azure

1. Para testar o aplicativo no desenvolvimento local ambiente usando o Azure calcular emulador, abre o menu de atalho para o projeto Azure para a função da web e escolha **Definir como projeto de inicialização**. Escolha **Depurar**, **Iniciar depuração** (teclado: **F5**).

    Abre a caixa de diálogo **Iniciar o ambiente de depuração do Azure** e o aplicativo for iniciado no navegador. Para obter detalhes específicos sobre como iniciar cada tipo de aplicativo da web no emulador computação, consulte a tabela nesta seção.

1. Para configurar os serviços para seu aplicativo para publicar no Azure, você deve ter uma conta da Microsoft e uma assinatura do Azure. Use as etapas no tópico a seguir para configurar seus serviços: [preparar para publicar ou implantar um aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para publicar o aplicativo da web no Azure, abra o menu de atalho para o projeto da web e escolha **Publicar Azure**.

    Abre a caixa de diálogo **Publicar aplicativo do Azure** e Visual Studio inicia o processo de implantação. Para obter mais informações sobre como publicar o aplicativo, consulte a seção **publicar um aplicativo do Azure do Visual Studio** na [publicação a um serviço de nuvem usando as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] Você também pode publicar o aplicativo da web do projeto Azure. Para fazer isso, abra o menu de atalho para o projeto Azure e escolha **Publicar**.

1. Para ver o andamento da implantação, você pode exibir a janela de **Log de atividade do Azure** . Esse log é exibido automaticamente quando inicia o processo de implantação. Você pode expandir o item de linha no registro de atividade para mostrar informações detalhadas, conforme mostrado na ilustração a seguir:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcional) Para cancelar o processo de implantação, abra o menu de atalho para o item de linha no registro de atividade e escolha **Cancelar e remover**. Isso interrompe o processo de implantação e exclui o ambiente de implantação do Azure.

    >[AZURE.NOTE] Para remover esse ambiente de implantação após ele ter sido implantado, você deve usar o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Depois que sua instâncias de função tenham iniciado, o Visual Studio automaticamente mostra o ambiente de implantação no nó de **Computação Azure** no **Explorador de nuvem** ou **Server Explorer**. A partir daqui, você pode exibir o status das instâncias de função individuais.

    A ilustração a seguir mostra as instâncias de função no **Explorador de servidor** enquanto elas ainda estão no estado de inicialização:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Para acessar seu aplicativo após a implantação, escolha na seta ao lado de sua implantação quando um status de **concluído** for exibida no **log de atividade do Azure**. Isso exibe a URL do seu aplicativo da web no Azure. Consulte a tabela a seguir para obter os detalhes sobre como iniciar um tipo específico de aplicativo web do Azure.

    A tabela a seguir lista os detalhes sobre como iniciar aplicativos web específicos do Azure ou executar ou depurar um aplicativo web localmente usando o emulador de computação do Azure:

  	|Tipo de aplicativo Web|Executar/depurar localmente usando o emulador de computação|Em execução no Azure|
  	|---|---|---|
  	|Aplicativo Web do ASP.NET|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Escolha o hiperlink de URL exibido na guia **implantação** para o **registro de atividade do Azure** carregar a página inicial no navegador.|
  	|Aplicativo Web do ASP.NET MVC 2|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Escolha o hiperlink de URL exibido na guia **implantação** para o **registro de atividade do Azure** carregar a página inicial no navegador.|
  	|Aplicativo Web do ASP.NET MVC 3|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Escolha o hiperlink de URL exibido na guia **implantação** para o **registro de atividade do Azure** carregar a página inicial no navegador.|
  	|Aplicativo Web do ASP.NET MVC 4|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Escolha o hiperlink de URL exibido na guia **implantação** para o **registro de atividade do Azure** carregar a página inicial no navegador.|
  	|Aplicativo Web do ASP.NET vazio|Você deve adicionar uma página. aspx no seu aplicativo que você definir como página inicial para seu projeto da web. Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Se você tiver uma página. aspx padrão em seu aplicativo, escolha o hiperlink de URL exibido na guia **implantação** para o **registro de atividade do Azure** e esta página é carregada no navegador. Se você tiver uma página. aspx diferente, você precisa navegar para essa página específica usando o seguinte formato URL:`<url for deployment>/<name of page>.aspx`|
  	|Aplicativo do Silverlight|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você precisa navegar até a página específica para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of page>.aspx`|
  	|Aplicativo de negócios do Silverlight|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você precisa navegar até a página específica para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of page>.aspx`|
  	|Aplicativo de navegação do Silverlight|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você precisa navegar até a página específica para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of page>.aspx`|
  	|Aplicativo de serviço do WCF|Você deve definir o arquivo. svc como a página inicial do seu projeto de serviço WCF. Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você precisa navegar até o arquivo svc para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of service file>.svc`|
  	|Aplicativo de serviço de fluxo de trabalho do WCF|Você deve definir o arquivo. svc como a página inicial do seu projeto de serviço WCF. Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você precisa navegar até o arquivo svc para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of service file>.svc`|
  	|Entidades dinâmicos do ASP.NET|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você deve atualizar a cadeia de conexão (consulte a próxima seção). Você também precisa navegar até a página específica para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of page>.aspx`|
  	|Linq de dados dinâmicos do ASP.NET para SQL|Na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolha a tecla **F5** .).|Você deve seguir as etapas neste procedimento: usar um banco de dados do SQL Azure para seu aplicativo (consulte a seção anterior deste tópico). Você também precisa navegar até a página específica para seu aplicativo usando o seguinte formato URL:`<url for deployment>/<name of page>.aspx`|

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Atualizar uma cadeia de Conexão para entidades dinâmicos do ASP.NET

### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Atualizar uma cadeia de Conexão para entidades dinâmicos do ASP.NET

1. Para criar um banco de dados do SQL Azure que pode ser usado para um aplicativo de web ASP.NET dinâmico entidades, siga as etapas do procedimento **Use um banco de dados do SQL Azure para seu aplicativo** anteriormente contidas neste tópico.

1. Adicione as tabelas e campos que você precisa para este banco de dados do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

1. A cadeia de conexão para este tipo de aplicativo tem o seguinte formato no arquivo Web. config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Atualize o valor *connectionString* com a cadeia de conexão de ADO.NET para seu banco de dados do SQL Azure, da seguinte maneira:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

1. Para salvar o arquivo Web. config com as alterações que você fez a cadeia de conexão, na barra de menus escolha **arquivo**, **Salvar Web. config**.

## <a name="supported-project-templates"></a>Modelos de projeto com suporte

Para publicar um aplicativo web para Azure, o aplicativo deve usar um dos modelos de projeto para c# ou Visual Basic que está listado na tabela abaixo.

|Grupo de modelos de projeto|Modelo do Project|
|---|---|
|Web|Aplicativo Web do ASP.NET|
|Web|Aplicativo Web do ASP.NET MVC 2|
|Web|Aplicativo Web do ASP.NET MVC 3|
|Web|Aplicativo Web do ASP.NET MVC4|
|Web|Aplicativo Web do ASP.NET vazio|
|Web|Aplicativo de Web vazia do ASP.NET MVC 2|
|Web|Aplicativo da Web de entidades de dados dinâmicos do ASP.NET|
|Web|Linq de dados dinâmicos do ASP.NET ao aplicativo da Web SQL|
|Silverlight|Aplicativo do Silverlight|
|Silverlight|Aplicativo de negócios do Silverlight|
|Silverlight|Aplicativo de navegação do Silverlight|
|WCF|Aplicativo de serviço do WCF|
|WCF|Aplicativo de serviço de fluxo de trabalho do WCF|
|Fluxo de trabalho|Aplicativo de serviço de fluxo de trabalho do WCF|

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a publicação, consulte [preparar para publicar ou implantar um aplicativo do Azure do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Verifique também [Configuração backup chamado as credenciais de autenticação](vs-azure-tools-setting-up-named-authentication-credentials.md).
