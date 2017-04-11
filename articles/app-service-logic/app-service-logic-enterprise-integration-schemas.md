<properties
    pageTitle="Visão geral de esquemas e o pacote de integração do Enterprise | Microsoft Azure"
    description="Saiba como usar esquemas com aplicativos do pacote de integração de empresa e lógica"
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Saiba mais sobre os esquemas e o pacote de integração do Enterprise  

## <a name="why-use-a-schema"></a>Por que usar um esquema?
Use esquemas para confirmar que os documentos XML que receber são válidos, com os dados esperados em um formato predefinido. Os esquemas são usados para validar a mensagens trocadas em um cenário B2B.

## <a name="add-a-schema"></a>Adicionar um esquema
A partir do portal Azure:  

1. Selecione **mais serviços**.  
![Portal de captura de tela do Azure, com "Mais serviços" realçado](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. Na caixa de pesquisa de filtro, insira **integração**e selecione **Contas de integração** da lista de resultados.     
![Captura de tela da caixa de pesquisa de filtro](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Selecione a **conta de integração** ao qual você adiciona o esquema.    
![Captura de tela da lista de contas de integração](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Selecione o bloco de **esquemas** .  
![Captura de tela da IEP integração conta, com "Esquemas" realçados](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Adicionar um arquivo de esquema menor que 2 MB  

1. Na lâmina **esquemas** que abre (a partir das etapas anteriores), selecione **Adicionar**.  
![Lâmina de captura de tela de esquemas, com o botão "Adicionar" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Insira um nome para o esquema. Em seguida, para carregar o arquivo de esquema, selecione o ícone de pasta ao lado da caixa de texto de **esquema** . Após o processo de carregamento, selecione **Okey**.    
![Captura de tela de "Adicionar esquema", com "Arquivo pequeno" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Adicionar um arquivo de esquema maior que 2 MB (até no máximo 8 MB)  

O processo para isso depende do nível de acesso de contêiner de blob: **público** ou **não o acesso anônimo**. Para determinar a esse nível de acesso, no **Gerenciador de armazenamento do Azure**, em **Contêineres de Blob**, selecione contêiner de blob desejado. Selecione **segurança**e selecione a guia de **Nível de acesso** .

1. Se o nível de acesso de segurança do blob for **público**, siga estas etapas.  
  ![Captura de tela do Azure Storage Explorer, com "Contêineres de Blob", "Segurança" e "Público" realçado](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    a. Carregar o esquema de armazenamento e, em seguida, copie o URI.  
    ![Captura de tela da conta de armazenamento, com URI realçado](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. **Adicionar esquema**, selecione **arquivo grande**e fornecer o URI na caixa de texto **URI do conteúdo** .  
    ![Captura de tela de esquemas, com o botão "Adicionar" e "Arquivos grandes" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Se o nível de acesso de segurança do blob for **sem acesso anônimo**, siga estas etapas.  
  ![Captura de tela do Azure Storage Explorer, com "Contêineres de Blob", "Segurança" e "Sem acesso anônimo" realçado](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    a. Carregue o esquema de armazenamento.  
    ![Captura de tela da conta de armazenamento](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Gere uma assinatura de acesso compartilhado para o esquema.  
    ![Captura de tela da conta de armazenamento, com o guia de assinaturas de acesso compartilhado realçado](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. **Adicionar esquema**, selecione **arquivo grande**e forneça a assinatura de acesso compartilhado URI na caixa de texto **URI do conteúdo** .  
    ![Captura de tela de esquemas, com o botão "Adicionar" e "Arquivos grandes" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. Na lâmina **esquemas** da conta de integração de EIP, agora você deve ver o esquema recém-adicionado.  
![Captura de tela de EIP integração conta, com "Esquemas" e o novo esquema realçado](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Editar esquemas
1. Selecione o bloco de **esquemas** .  
2. Selecione o esquema que você deseja editar da lâmina **esquemas** que se abre.
3. Na lâmina **esquemas** , selecione **Editar**.  
![Blade de captura de tela de esquemas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Selecione o arquivo de esquema que você deseja editar, usando a caixa de diálogo de seletor de arquivo aberto.
5. Selecione **Abrir** no seletor de arquivo.  
![Captura de tela do seletor de arquivo](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Você recebe uma notificação que indica que o carregamento foi bem-sucedido.  

## <a name="delete-schemas"></a>Excluir esquemas
1. Selecione o bloco de **esquemas** .  
2. Selecione o esquema que você deseja excluir da lâmina **esquemas** que se abre.  
3. Na lâmina **esquemas** , selecione **Excluir**.
![Blade de captura de tela de esquemas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Para confirmar sua escolha, selecione **Sim**.  
![Captura de tela da mensagem de confirmação de "Excluir esquema"](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Finalmente, observe que a lista de esquemas na lâmina **esquemas** atualiza e o esquema excluídos não está mais listado.  
![Captura de tela de EIP integração conta, com "Esquemas" realçados](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o pacote de integração do Enterprise] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa").  
