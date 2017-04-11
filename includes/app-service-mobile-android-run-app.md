
1. Visite o [Portal do Azure]. Clique em **Procurar tudo** > **Aplicativos Mobile** > back-end que você acabou de criar. Em configurações do aplicativo móvel, clique em **início rápido** > **Android)**. Em **configurar seu aplicativo cliente**, clique em **Baixar**. Isso faz o download de um projeto completo Android para um aplicativo pré-configurado para se conectar ao seu back-end. 

2. Abra o projeto usando **Studio Android**, usando **Importar projeto (Eclipse ADT, Gradle, etc.)**. Certifique-se de que você faça essa seleção de importação para evitar erros JDK.

3. Pressione o botão **Executar 'aplicativo'** para criar o projeto e iniciar o aplicativo em simulador Android.

4. No aplicativo, digite o texto significativo, como _concluída o tutorial_ e clique no botão 'Adicionar'. Isso envia uma solicitação de POSTAGEM no Azure back-end que você implantado anteriormente. Os dados de inserções de back-end da solicitação é para a tabela TodoItem SQL e retorna informações sobre os itens recentemente armazenados volta para o aplicativo móvel. O aplicativo móvel exibe esses dados na lista. 

    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Portal do Azure]: https://portal.azure.com/
