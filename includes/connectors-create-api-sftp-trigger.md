Vamos adicionar um disparador.

1. Insira *sftp* na caixa de pesquisa no designer de aplicativos de lógica e selecione o disparador **SFTP - quando um arquivo é adicionado ou modificado**   
![Imagem de disparador SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
- Abre o controle **quando um arquivo é adicionado ou modificado**  
![Imagem de disparador SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
- Selecione o **…** localizada no lado direito do controle. Isso abre o controle de selecionador de pasta  
![Imagem de acionamento SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
- Selecione o **SFTP** para selecionar a pasta raiz da pasta para monitorar arquivos novos ou modificados. Observe que a pasta raiz agora é exibida no controle de **pasta** .  
![Imagem de acionamento SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

Neste ponto, seu aplicativo de lógica foi configurado com um disparador que iniciará uma execução de outros disparadores e as ações no fluxo de trabalho quando um arquivo é modificado ou criado na pasta SFTP específico. 

>[AZURE.NOTE]Para um aplicativo de lógica esteja funcional, ele deve conter pelo menos um disparador e uma ação. Siga as etapas na próxima seção para adicionar uma ação.  