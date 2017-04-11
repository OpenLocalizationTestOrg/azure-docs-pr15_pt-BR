

1. Faça logon no [console de Firebase](https://firebase.google.com/console/). Crie um novo projeto de Firebase se ainda não tiver uma.
2. Depois que seu projeto for criado clique **Adicionar Firebase para o seu aplicativo Android** e siga as instruções fornecidas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. No Console de Firebase, clique na cog para seu projeto e clique em **Configurações do Project**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Clique na guia **Mensagens de nuvem** em suas configurações de projeto e copie o valor da **chave de servidor** e **ID do remetente**.  Esses valores serão usados mais tarde para configurar o política de acesso do hub de notificação e seu manipulador de notificação no aplicativo.
  