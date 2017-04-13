<properties 
   pageTitle="Acessando nuvens privadas Azure com o Visual Studio | Microsoft Azure"
   description="Saiba como acessar recursos de nuvem privada usando o Visual Studio."
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

# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Acessando nuvens privadas Azure com o Visual Studio

##<a name="overview"></a>Visão geral

Por padrão, o Visual Studio oferece suporte a pontos de extremidade do Azure de nuvem pública restante. Isso pode ser um problema, no entanto, se você estiver usando o Visual Studio com uma nuvem Azure privada. Você pode usar certificados para configurar o Visual Studio para acessar os pontos de extremidade do Azure de nuvem privada restante. Você pode obter esses certificados pelo seu Azure Publicar arquivo de configurações.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para acessar um Azure particular nuvem no Visual Studio

1. No [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885) para a nuvem privada, baixe o arquivo de configurações de publicação ou contate o administrador para um arquivo de configurações de publicação. Na versão pública do Azure, o link para baixar essa é [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (O arquivo baixado deve ter uma extensão de publishsettings.)

1. No **Server Explorer** no Visual Studio, escolha o nó do **Azure** e, no menu de atalho, escolha o comando **Gerenciar assinaturas** .

    ![Gerenciar o comando assinaturas](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Na caixa de diálogo **Gerenciar assinaturas do Microsoft Azure** , escolha a guia **certificados** e, em seguida, escolha o botão **Importar** .

    ![Importando certificados Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Na caixa de diálogo **Importar Microsoft Azure assinaturas** , navegue até a pasta onde você salvou o arquivo de configurações de publicação e escolha o arquivo e escolha o botão **Importar** . Isso importa os certificados no arquivo de configurações de publicação para Visual Studio. Você agora deve conseguir interagir com seus recursos de nuvem privada.

    ![Importar configurações de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Próximas etapas

[Publicação em um serviço de nuvem Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Como: publicam de Download e importar configurações e informações de assinatura](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

