
1. No computador local, faça logon no [Portal de gerenciamento do Azure](http://manager.windowsazure.com) (Este é o portal antigo).

2. Na parte inferior do painel de navegação, selecione **+ novo** > **Os serviços de aplicativo** > **Serviço BizTalk** > **Criar personalizada**.

3. Forneça um **Nome de serviço BizTalk** e selecione uma **edição**. 

    Este tutorial usa **mobile1**. Você precisará fornecer um nome exclusivo para sua nova BizTalk Service.

4. Depois que o BizTalk Service tiver sido criado, selecione a guia **Híbrido conexões** , clique em **Adicionar**.

    ![Adicionar Conexão híbrido](./media/hybrid-connections-create-new/3.png)

    Isso cria uma nova conexão de híbrido.

5. Forneça um **nome** e o **Nome do Host** para sua conexão de híbrido e configurar **porta** para `1433`. 
  
    ![Configurar Conexão híbrido](./media/hybrid-connections-create-new/4.png)

    O nome do host é o nome do servidor local. Isso configura a conexão híbrido para acessar o SQL Server em execução na porta 1433. Se você estiver usando uma instância nomeada do SQL Server, use a porta estática definido anteriormente.

6. Depois que a nova conexão for criada, o status do da nova conexão mostra o **local de instalação incompleto**.

7. Navegue de volta para o serviço móvel, clique em **Configurar**, role para baixo até **conexões híbrido** e clique em **Adicionar conexão de híbrido**, depois selecione a conexão de híbrido que você acabou de criar e clique **Okey**.

    Isso permite que o serviço móvel usar sua nova conexão híbrido.

Em seguida, você precisará instalar o Gerenciador de Conexão híbrido no computador local.