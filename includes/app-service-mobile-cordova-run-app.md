
1. Visite o [Portal do Azure]. Clique em **Procurar tudo** > **Aplicativos Mobile** > back-end que você acabou de criar. Em configurações do aplicativo móvel, clique em **início rápido** > **Cordova**. Em **configurar seu aplicativo cliente**, selecione **criar um novo aplicativo**, clique em **Baixar**. Isso faz o download de um projeto de Cordova completo de um aplicativo pré-configurado para se conectar ao seu back-end.

2. Descompactar o arquivo ZIP baixado para uma pasta no disco rígido, navegue até o arquivo de solução (. sln) e abri-lo usando o Visual Studio.

5. No Visual Studio, escolha a plataforma de solução (Android, iOS ou Windows) na lista suspensa ao lado de na seta de início e selecione um dispositivo de implantação específicas ou emulador clicando na lista suspensa na seta verde. Observe que você pode usar a plataforma Android padrão e ondulação emulador. Tutoriais mais avançados exigirão que você selecione um dispositivo com suporte ou emulador. 

6. Pressione F5 ou clique na seta verde para construir e e executar o aplicativo Cordova. Se você vir uma caixa de diálogo de segurança no acesso à rede solicitando emulador, aceitá-la.   

7. Após o aplicativo é iniciado no dispositivo ou emulador, digite texto significativo na **Insira o novo texto**, como _o tutorial concluída_ e, em seguida, clique no botão **Adicionar** .  
Isso envia uma solicitação de POSTAGEM no Azure back-end que você implantado anteriormente. Os dados de inserções de back-end da solicitação é na tabela TodoItem do banco de dados do SQL e retorna informações sobre os itens recentemente armazenados volta para o aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Repita as três etapas anteriores para cada plataforma do dispositivo que você planeja oferecer suporte.

[Portal do Azure]: https://portal.azure.com/
