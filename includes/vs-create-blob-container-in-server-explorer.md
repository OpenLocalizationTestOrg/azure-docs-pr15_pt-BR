Você pode criar filas de armazenamento do Azure usando o Visual Studio **Server Explorer**.

![Servidor Explorer Blobs][Image1]

1. No menu **Exibir** , escolha **Server Explorer**.
2. No Server Explorer, expanda o nó do **Azure** para sua assinatura, expanda o nó de **armazenamento** e o nó para a conta de armazenamento que você especificou no serviço de armazenamento do Azure conectado.
3. Selecione o nó **filas** e escolha **Criar fila** no menu de contexto.
4. Insira um nome para o contêiner e escolha **Okey**.   

Por padrão, o novo contêiner é particular e especifique a chave de acesso de armazenamento para baixar blobs desse recipiente. Se você quiser fazer os arquivos no contêiner de público, selecione o recipiente no **Server Explorer** e pressione `F4` para exibir a janela de **Propriedades** . Defina o **acesso de leitura pública** como **Blob**. Qualquer pessoa na Internet pode ver blobs em um contêiner de público, mas você pode modificar ou excluí-las somente se você tiver a chave de acesso apropriado.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png