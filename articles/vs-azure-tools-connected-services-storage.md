<properties 
   pageTitle="Adicionar armazenamento Azure usando serviços conectados no Visual Studio | Microsoft Azure"
   description="Adicionar armazenamento do Azure para o seu aplicativo usando a caixa de diálogo do Visual Studio adicionar conectado serviços"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Adicionando o armazenamento do Azure usando o Visual Studio conectado Services

## <a name="overview"></a>Visão geral

Com o Visual Studio de 2015, você pode conectar qualquer serviço de nuvem do c#, serviço móvel do .NET back-end, ASP.NET site ou serviço, ASP.NET 5 serviço ou serviço do Azure WebJob ao armazenamento do Azure usando a caixa de diálogo **Adicionar serviços conectados** . A funcionalidade de serviço conectada adiciona todas as referências necessárias e código de conexão e modifica seus arquivos de configuração adequadamente. A caixa de diálogo também leva você à documentação que indica quais são as próximas etapas para iniciar o armazenamento de blob, filas e tabelas.

## <a name="supported-project-types"></a>Tipos de projeto com suporte

Você pode usar a caixa de diálogo Serviços conectados para se conectar ao armazenamento do Azure nos seguintes tipos de projeto.

- Projetos Web do ASP.NET

- ASP.NET 5 projetos

- Função de Web de serviço de nuvem do .NET e projetos de função de trabalho

- Projetos de serviços móveis do .NET

- Projetos WebJob Azure


## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Conectar ao armazenamento do Azure usando a caixa de diálogo Serviços conectados

1. Verifique se que você tiver uma conta do Azure. Se você não tiver uma conta do Azure, você pode inscrever-se para uma [avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Quando você tiver uma conta do Azure, você pode criar contas de armazenamento, criar serviços móveis e configurar o Azure Active Directory.

1. Abra o projeto no Visual Studio, abrir o menu de contexto para o nó de **referências** em Solution Explorer e escolha **Adicionar serviço conectado**.

    ![Adicionando um serviço conectado](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Na caixa de diálogo **Adicionar serviço conectado** , escolha **O armazenamento do Azure**e, em seguida, escolha o botão **Configurar** . Você pode ser solicitado a fazer logon no Azure se você ainda não fez isso.

    ![Adicionar caixa de diálogo de serviço conectado - armazenamento](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Na caixa de diálogo **Armazenamento do Azure** , selecione uma conta de armazenamento existente e selecione **Adicionar**.

    Se você precisa criar uma nova conta de armazenamento, vá para a próxima etapa. Caso contrário, pule para a etapa 6.

    ![Caixa de diálogo de armazenamento Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Para criar uma nova conta de armazenamento: 

    1. Escolha o botão **criar uma nova conta de armazenamento** na parte inferior da caixa de diálogo armazenamento do Azure.

    1. Preencha a caixa de diálogo **Criar conta de armazenamento** e, em seguida, escolha o botão **criar** .
    
        ![Caixa de diálogo de armazenamento Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Quando você estiver na caixa de diálogo **Armazenamento do Azure** , o novo armazenamento aparece na lista.

    1. Selecione o novo armazenamento na lista e selecione **Adicionar**.

1. O serviço de armazenamento conectado aparece sob o nó de referências de serviço do seu projeto WebJob.

    ![Armazenamento do Azure no projeto de trabalhos de web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Analise a página de Introdução que aparece e descubra como seu projeto foi modificado. Uma página de Introdução aparece no navegador sempre que você adicionar um serviço conectado. Você pode examinar as próximas etapas sugeridas e exemplos de código ou alterne para a página o que aconteceu para ver quais referências foram adicionadas ao seu projeto e como seus arquivos de código e configuração foram modificados.

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Quando terminar a caixa de diálogo, o Visual Studio adiciona referências e modifica determinados arquivos de configuração. As alterações específicas dependem do tipo de projeto. 

 - Para projetos ASP.NET, veja [o que aconteceu – projetos ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Para projetos ASP.NET 5, veja [o que aconteceu – projetos do ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Para nuvem projetos de serviço (funções da web e funções de trabalho), consulte [o que aconteceu – projetos de serviço de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Para projetos de WebJob, veja [o que aconteceu - WebJob projetos](./storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Próximas etapas

1. Usando os exemplos de código Introdução como um guia, criar o tipo de armazenamento que você deseja e, em seguida, comece a escrever código para acessar sua conta de armazenamento!

1. Fazer perguntas e obter ajuda
     - [Fórum do MSDN: Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Armazenamento em azure.microsoft.com](https://azure.microsoft.com/services/storage/)

     - [Documentação de armazenamento em azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)

