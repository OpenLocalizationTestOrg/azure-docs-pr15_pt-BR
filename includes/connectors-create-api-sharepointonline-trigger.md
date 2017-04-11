Neste exemplo, para mostrar como usar o **SharePoint Online, quando um novo item é criado** disparador para iniciar um fluxo de trabalho do aplicativo de lógica quando um novo item é criado em uma lista do SharePoint Online.

>[AZURE.NOTE]Você será solicitado que entre na sua conta do SharePoint se você já não tiver criado uma *conexão* com o SharePoint Online.  

1. Insira o *sharepoint* na caixa de pesquisa no designer de aplicativos de lógica e selecione o disparador **Do SharePoint Online, quando um novo item é criado** .  
![Imagem de acionamento online do SharePoint](./media/connectors-create-api-sharepointonline/trigger-1.png)  
- O controle **quando um novo item é criado** é exibido.  
![Imagem do SharePoint online disparador 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
- Selecione uma **URL de Site**. Este é o site do SharePoint online que você deseja monitorar para novos itens disparar seu fluxo de trabalho.  
![Imagem de acionamento online do SharePoint 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
- Selecione um **nome da lista**. Esta é a lista no site do SharePoint Online que você deseja monitorar para novos itens que acionarão seu fluxo de trabalho.  
![Imagem de acionamento online do SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Neste ponto, seu aplicativo de lógica foi configurado com um disparador que iniciará uma execução de outros disparadores e as ações no fluxo de trabalho. Isso ocorrerá sempre que um novo item é criado na lista do SharePoint Online que você selecionou.  