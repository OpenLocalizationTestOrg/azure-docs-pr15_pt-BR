
1. Clique com botão direito do projeto da Windows Store, clique em **Definir como projeto de inicialização**e pressione a tecla F5 para executar o aplicativo da Windows Store.
    
    Depois que o aplicativo for iniciado, o dispositivo é registrado para as notificações por push.

2. Pare o aplicativo da Windows Store e repita a etapa anterior para o aplicativo de armazenamento do Windows Phone.

    Neste ponto, os dois dispositivos são registrados para receber notificações por push.

3. Executar o aplicativo da Windows Store novamente, digite o texto na **Inserir um TodoItem**e clique em **Salvar**.

    Observe que após a inserção for concluído, Windows Store e os aplicativos do Windows Phone recebem uma notificação de envio de WNS. A notificação é exibida no Windows Phone, mesmo quando o aplicativo não está em execução.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

