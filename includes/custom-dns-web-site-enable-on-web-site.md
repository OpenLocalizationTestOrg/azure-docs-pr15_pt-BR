Depois que os registros do seu nome de domínio tiveram propagado, você deve associá-los com seu aplicativo Web. Use as etapas a seguir para habilitar os nomes de domínio usando o navegador da web.

> [AZURE.NOTE] Pode levar algum tempo para registros TXT criada nas etapas anteriores para propagar pelo sistema DNS. Você não pode adicionar o nome de domínio para seu aplicativo web até que o registro TXT tiver propagado. Se você estiver usando um registro, você não pode adicionar o nome de domínio registro A para seu aplicativo web até que o registro TXT criado na etapa anterior tiver propagado.
>
> Você pode usar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o registro TXT está disponível.

1. No navegador, abra o [Portal do Azure](https://portal.azure.com).

2. Na guia **Web Apps** , clique no nome do seu aplicativo web e selecione **domínios personalizados**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Na lâmina **domínios personalizados** , clique em **Adicionar hostname**.
    
4. Use as caixas de texto **nome do host** para inserir os nomes de domínio para associar a este aplicativo web.

    ![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Clique em **Validar**.

7.  Após clicar em **Validar** Azure para Iniciar fluxo de trabalho de verificação de domínio. Isto irá verificar a propriedade do domínio, bem como sucesso de disponibilidade e relatório do nome do host ou erro detalhadas com dirigidas diretriz sobre como corrigir o erro.    

Neste ponto, você deve ser capaz de insira o nome de domínio personalizado no seu navegador e ver que ele com êxito leva você ao seu aplicativo web.
