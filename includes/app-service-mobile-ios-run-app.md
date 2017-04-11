
1. No seu Mac, visite o [Portal do Azure]. Clique em **Procurar tudo** > **Aplicativos Mobile** > back-end que você acabou de criar. Em configurações do aplicativo móvel, clique em **início rápido** > **iOS (objetivo-C)**. Se você preferir rápido, clique em **início rápido** > **iOS (Swift)** em vez disso. Em **baixar e executa o seu projeto do iOS**, clique em **Download**. Isso faz o download de um projeto completo do Xcode para um aplicativo pré-configurado para se conectar ao seu back-end. Abra o projeto usando o Xcode.

2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo em simulador iOS.

3. No aplicativo, digite texto significativo, como _concluída o tutorial_ e clique no sinal de adição (**+**) ícone. Isso envia uma solicitação de POSTAGEM no Azure back-end que você implantado anteriormente. Os dados de inserções de back-end da solicitação é para a tabela TodoItem SQL e retorna informações sobre os itens recentemente armazenados volta para o aplicativo móvel. O aplicativo móvel exibe esses dados na lista. 

    ![](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Portal do Azure]: https://portal.azure.com/
