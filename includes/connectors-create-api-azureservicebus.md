Agora que você adicionou um disparador, é hora de fazer algo interessante com os dados que são gerados pelo disparador. Siga estas etapas para adicionar uma a ação **Do SharePoint Online - criar arquivo** . Esta ação criará um arquivo no SharePoint Online sempre que aciona o novo disparador de item. 

Para configurar esta ação, você precisará fornecer as seguintes informações. Você notará que é fácil de usar dados gerados pelo disparador como entrada para algumas das propriedades para o novo arquivo:

|Criar propriedade de arquivo|Descrição|
|---|---|
|URL do site|Esta é a URL do site do SharePoint Online onde você deseja criar o novo arquivo. Selecione o site da lista apresentada.|
|Caminho da pasta|Esta é a pasta (na URL de Site) onde o novo arquivo será colocado. Procure e selecione a pasta.|
|Nome do arquivo|Este é o nome do arquivo está sendo criado.|
|Conteúdo do arquivo|O conteúdo que será gravado para o arquivo.|

1. Selecione **+ nova etapa** para adicionar a ação.  
![](./media/connectors-create-api-sharepointonline/action-1.png)  
- Selecione o link **Adicionar uma ação** . Isso abre a caixa de pesquisa onde você pode pesquisar qualquer ação você gostaria de fazer. Neste exemplo, as ações do SharePoint são de interesse.    
![](./media/connectors-create-api-sharepointonline/action-2.png)    
- Insira o *sharepoint* para pesquisar ações relacionadas ao SharePoint.
- Selecione **SharePoint Online - criar arquivo** como a ação a ser executada.   **Observação**: você será solicitado a autorizar seu aplicativo de lógica para acessar sua conta do SharePoint se você não tiver feito isso anteriormente.    
![](./media/connectors-create-api-sharepointonline/action-3.png)    
- O controle de **criar arquivo** abre.   
![](./media/connectors-create-api-sharepointonline/action-4.png)     
- Selecione a **URL do Site** e navegue para localizar o site onde você deseja criar o arquivo.     
![](./media/connectors-create-api-sharepointonline/action-5.png)  
- Selecione o **caminho da pasta** e procure a pasta onde o novo arquivo será colocado.  
![](./media/connectors-create-api-sharepointonline/action-6.png)  
- Selecione o controle de **nome de arquivo** e insira o nome do arquivo que você deseja criar. Nome do arquivo, observe que você pode usar qualquer uma das propriedades do disparador criado anteriormente, simplesmente selecionando-o na lista apresentada.     
![](./media/connectors-create-api-sharepointonline/action-7.png)  
- Selecione o controle de **conteúdo do arquivo** e insira o conteúdo que será gravado para o arquivo que será criado. Para ver o conteúdo do arquivo, observe que você pode usar qualquer uma das propriedades do disparador criado anteriormente. Basta selecione as propriedades da lista apresentada. Como alternativa, você pode inserir o **conteúdo do arquivo** de texto diretamente no controle. Neste exemplo, eu algumas propriedades selecionadas e adicionado espaços e um hífen entre cada propriedade.        
![](./media/connectors-create-api-sharepointonline/action-8.png)  
- Salvar as alterações ao seu fluxo de trabalho  
