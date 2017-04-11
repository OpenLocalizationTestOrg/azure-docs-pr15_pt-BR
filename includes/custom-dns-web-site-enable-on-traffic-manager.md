Depois que os registros do seu nome de domínio tiveram propagado, você deve ser capaz de utilizar o navegador para verificar se o seu nome de domínio personalizado pode ser usada para acessar o aplicativo da web em um serviço de aplicativo do Azure.

> [AZURE.NOTE] Pode levar alguns minutos para que seu CNAME propagar pelo sistema DNS. Você pode usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

Se você ainda não tiver adicionado seu aplicativo web como um ponto de extremidade do Gerenciador de tráfego, você deve fazê-lo antes de resolução de nomes funcionará, como as rotas de nome de domínio personalizado ao Gerenciador de tráfego. Gerenciador de tráfego encaminha para o seu aplicativo web. Use as informações em [Adicionar ou excluir pontos de extremidade](../articles/traffic-manager/traffic-manager-endpoints.md) para adicionar seu aplicativo web como um ponto de extremidade no seu perfil do Gerenciador de tráfego.

> [AZURE.NOTE] Se seu aplicativo web não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado para o modo de plano de serviço de aplicativo **padrão** . Você deve usar o modo **padrão** para o aplicativo web para trabalhar com o Gerenciador de tráfego.

1. No navegador, abra o [Portal do Azure](https://portal.azure.com).

1. Na guia **Web Apps** , clique no nome do seu aplicativo web, selecione **configurações**e selecione **domínios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. Na lâmina **domínios personalizados** , clique em **Adicionar hostname**.
    
1. Use as caixas de texto **nome do host** para inserir o nome de domínio do Gerenciador de tráfego para associar a este aplicativo web.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Clique em **Validar** para salvar a configuração de nome de domínio.

7.  Após clicar em **Validar** Azure para Iniciar fluxo de trabalho de verificação de domínio. Isto irá verificar a propriedade do domínio, bem como sucesso de disponibilidade e relatório do nome do host ou erro detalhadas com dirigidas diretriz sobre como corrigir o erro.    

8.  Durante a validação bem-sucedida **hostname adicionar** botão se tornará ativo e você poderá ao nome do host atribuir. Agora, navegue até seu nome de domínio personalizado em um navegador. Agora você deve ver sua execução de aplicativo usando seu nome de domínio personalizado. 

    Após a conclusão da configuração, o nome de domínio personalizado será listado na seção **nomes de domínio** de seu aplicativo web.

Neste ponto, você deve ser capaz de inserir o nome de domínio do Gerenciador de tráfego em seu navegador e ver que ele com êxito leva você ao seu aplicativo web.
