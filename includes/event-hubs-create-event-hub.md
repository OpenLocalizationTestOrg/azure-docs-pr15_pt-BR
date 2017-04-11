## <a name="create-an-event-hub"></a>Criar um Hub de evento

1. Faça logon no [portal do Azure][]e clique em **novo** na parte superior esquerda da tela.

2. Clique em **dados + análise**, clique em **Hubs de evento**.

    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)

3. Na lâmina **criar namespace** , insira um nome de namespace. O sistema imediatamente verifica se o nome está disponível.

    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)

4. Depois de garantir que o nome do namespace estiver disponível, escolha o nível de preços (Basic ou padrão). Além disso, escolha uma assinatura do Azure, grupo de recursos e local criar o recurso. 

2. Clique em **criar** para criar o espaço para nome.

6. Na lista de namespace Hubs de evento, clique em namespace recém-criado.      

    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)

7. Na lâmina namespace, clique em **Hubs de evento**.

    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)

8. Na parte superior da lâmina, clique em **Adicionar Hub de evento**.

    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)

3. Digite um nome para seu Hub de evento e, em seguida, clique em **criar**.

    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)

4. Na lista de Hubs de evento, clique no nome de Hub de evento recém-criado. 

    ![](./media/event-hubs-create-event-hub/create-event-hub6.png)

5. Novamente na lâmina namespace (não a lâmina Hub de evento específico), clique em **políticas de acesso compartilhado**e clique em **RootManageSharedAccessKey**.

    ![](./media/event-hubs-create-event-hub/create-event-hub7.png)

5. Clique no botão Copiar para copiar a cadeia de conexão **RootManageSharedAccessKey** para a área de transferência. Salve essa cadeia de caracteres de conexão para usar posteriormente no tutorial.

    ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Seu Hub de evento agora é criado, e você tiver as cadeias de caracteres de conexão que você precisa enviar e receber eventos.

[Portal do Azure]: https://portal.azure.com/