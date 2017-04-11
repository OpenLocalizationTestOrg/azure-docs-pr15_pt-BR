Veja aqui como usar o disparador **Barramento de serviço - quando uma mensagem for recebida em uma fila** para iniciar um fluxo de trabalho do aplicativo de lógica quando um novo item é enviado para uma fila de barramento de serviço.  

>[AZURE.NOTE]Você será solicitado a entrar com sua cadeia de caracteres de conexão do barramento de serviço se você ainda não tiver criado uma conexão para barramento de serviço.  

1. Na caixa de pesquisa no designer lógica aplicativos, insira **barramento de serviço**. Selecione o disparador **Barramento de serviço - quando uma mensagem for recebida em uma fila** .  
![Imagem de gatilho de barramento de serviço 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- A caixa de diálogo **quando uma mensagem for recebida em uma fila** é exibida.  
![Imagem de gatilho de barramento de serviço 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Insira o nome da fila barramento de serviço que você gostaria que o disparador para monitorar.   
![Imagem de acionamento barramento de serviço 3](./media/connectors-create-api-servicebus/trigger-3.png)   

Neste ponto, seu aplicativo de lógica foi configurado com um disparador. Quando um novo item é recebido na fila selecionada, o disparador começará uma execução de outros disparadores e as ações no fluxo de trabalho.    
