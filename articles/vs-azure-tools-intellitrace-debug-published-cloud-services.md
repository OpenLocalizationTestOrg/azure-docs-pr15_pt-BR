<properties 
   pageTitle="Depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio | Microsoft Azure"
   description="Depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>Depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio

##<a name="overview"></a>Visão geral

Com IntelliTrace, você pode registrar informações de depuração considerável para uma instância da função quando ele é executado no Azure. Se você precisar localizar a causa de um problema, você pode usar os logs do IntelliTrace para percorrer seu código do Visual Studio como se fosse executado no Azure. Na verdade, IntelliTrace registros principais a execução de código e dados de ambiente quando seu aplicativo do Azure está sendo executado como um serviço de nuvem no Azure e permite que você reproduzir os dados registrados do Visual Studio. Como alternativa, você pode usar a depuração remota para anexar diretamente a um serviço de nuvem que está em execução no Azure. Consulte [depuração de serviços de nuvem](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace destina-se somente a cenários de depuração e não deve ser usado para uma implantação de produção.

>[AZURE.NOTE] Você pode usar o IntelliTrace se você tiver o Visual Studio Enterprise instalado e seus destinos de aplicativo do Azure .NET Framework 4 ou uma versão posterior. IntelliTrace coleta informações para suas funções Azure. As máquinas virtuais para essas funções sempre executar sistemas operacionais de 64 bits.

## <a name="to-configure-an-azure-application-for-intellitrace"></a>Para configurar um aplicativo do Azure para IntelliTrace

Para habilitar IntelliTrace para um aplicativo do Azure, você deve criar e publicar o aplicativo de um projeto do Visual Studio Azure. Você deve configurar IntelliTrace para seu aplicativo do Azure antes de publicá-lo no Azure. Se você publica seu aplicativo sem configurar IntelliTrace mas decida que você deseja fazer isso, você precisará publicar o aplicativo novamente do Visual Studio. Para obter mais informações, consulte [Publicando um serviço de nuvem usando as ferramentas do Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando você estiver pronto para implantar o seu aplicativo do Azure, verifique sua destinos de compilação de projeto são definidos para **Depurar**.

1. Abrir o menu de atalho para o projeto Azure em Solution Explorer e escolha **Publicar**.
 
    O Assistente de aplicativo do Azure publicar aparece.

1. Para coletar logs do IntelliTrace para seu aplicativo quando ele é publicado na nuvem, marque a caixa de seleção **Habilitar IntelliTrace** .

    >[AZURE.NOTE] Você pode habilitar IntelliTrace ou perfil quando você publica seu aplicativo do Azure. Você não pode ativar ambos.

1. Para personalizar a configuração de IntelliTrace básica, escolha o hiperlink **configurações** .

    Caixa de diálogo Configurações de IntelliTrace é exibida, conforme mostrado na figura a seguir. Você pode especificar quais eventos log, se deseja coletar informações de chamada, quais módulos e processos para coletar logs para e quanto espaço a ser alocado para a gravação. Para obter mais informações sobre o IntelliTrace, consulte [depuração com IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

O log de IntelliTrace é um arquivo de log circular do tamanho máximo especificado nas configurações do IntelliTrace (o tamanho padrão é 250 MB). Logs do IntelliTrace são coletadas para um arquivo no sistema de arquivos da máquina virtual. Quando você solicita os logs, um instantâneo é feito nesse momento no tempo e baixado para o seu computador local.

Após a publicação de aplicativo do Azure para o Azure, você pode determinar se IntelliTrace foi habilitada do nó computação Azure no Server Explorer, conforme mostrado na imagem a seguir:

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>Baixando Logs do IntelliTrace para uma instância de função

Você pode baixar logs do IntelliTrace para uma instância de função a partir do nó de **Serviços de nuvem** no **Server Explorer**. Expanda o nó **Serviços de nuvem** até localizar a instância que você está interessado, abra o menu de atalho para esta instância e escolha **Exibir Logs de IntelliTrace**. Os logs do IntelliTrace são baixados para um arquivo em uma pasta em seu computador local. Logs de cada vez que você solicite o IntelliTrace, um novo instantâneo é criado.

Quando os logs são baixados, o Visual Studio exibe o progresso da operação na janela do Log de atividade do Azure. Como mostrado na figura a seguir, você pode expandir o item de linha para a operação ver mais detalhes.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Você pode continuar a trabalhar no Visual Studio enquanto os logs do IntelliTrace são baixadas. Quando o log de download for concluído, ele será aberto automaticamente no Visual Studio.

>[AZURE.NOTE] Os logs do IntelliTrace podem conter exceções que a estrutura gera e subsequentemente manipula. Código de estrutura interna gera essas exceções como uma parte normal do início de uma função, para que você pode ignorá-los.

## <a name="see-also"></a>Consulte também

[Depuração de serviços de nuvem](https://msdn.microsoft.com/library/ee405479.aspx)

