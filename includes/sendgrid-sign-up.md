Clientes Azure podem desbloquear 25.000 emails gratuitos cada mês. Esses 25.000 e-mails mensais gratuitos lhe dará acesso ao avançado e geração de relatórios e a análise de [todas as APIs][] (Web, SMTP, eventos, análise e muito mais). Para obter informações sobre outros serviços fornecidos pelo SendGrid, consulte a página de [Recursos de SendGrid][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>Para se inscrever para uma conta de SendGrid

1. Faça logon [Portal de gerenciamento do Azure][].

2. No painel inferior do portal de gerenciamento, clique em **novo**.

    ![nova barra de comando][command-bar-new]

3. Clique em **Marketplace**.

    ![repositório de sendgrid][sendgrid-store]

4. Na caixa de diálogo **Escolher um aplicativo e serviço** , selecione **SendGrid** e clique na seta à direita.

5. Na caixa de diálogo **Personalizar o aplicativo e serviço** selecione o plano de **SendGrid** que você deseja inscrever.

6. Insira um nome para identificar seu serviço **SendGrid** nas configurações do Azure ou use o valor padrão de **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Nomes devem estar entre 1 e 100 caracteres de comprimento e conter somente caracteres alfanuméricos, pontos, traços e sublinhados. O nome deve ser exclusivo em sua lista de itens de armazenamento do Azure inscritos.

    ![repositório-tela-2][store-screen-2]

7. Escolha um valor para a região; Por exemplo, oeste EUA.

8. Clique na seta à direita.

9. Na guia **Revisar compra** , examine o plano e informações sobre preços e examine os termos legais. Se você concordar com os termos, clique na marca de seleção. Depois de clicar na marca de seleção, sua conta SendGrid iniciará o [processo de provisionamento de SendGrid].

    ![repositório-tela-3][store-screen-3]

10. Depois de confirmar sua compra, você será redirecionado para o painel de complementos e você verá a mensagem **SendGrid de compra**.

    ![mensagem comprando sendgrid][sendgrid-purchasing-message]

    Sua conta de SendGrid está provisionada imediatamente e você verá a mensagem **com êxito adquirido complemento SendGrid**. Sua conta e as credenciais agora são criadas. Você está pronto para enviar emails neste momento. 

    Para modificar o seu plano de assinatura ou consulte o SendGrid configurações do contato, clique no nome do seu serviço de SendGrid para abrir o painel de SendGrid Marketplace. 

    ![sendgrid-adicionar-em-Painel de controle][sendgrid-add-on-dashboard]

    Para enviar um email usando SendGrid, você deve fornecer suas credenciais de conta (nome de usuário e senha).

### <a name="to-find-your-sendgrid-credentials"></a>Para encontrar suas credenciais de SendGrid ###

1. Clique em **informações de Conexão**.

    ![sendgrid-conexão-botão de informações][sendgrid-connection-info-button]

2. Na caixa de diálogo *informações de Conexão* , copie a **senha** e o nome de usuário para usar mais tarde neste tutorial.

    ![informações de conexão sendgrid][sendgrid-connection-info]

    Para definir configurações de deliverability de seu email, clique no botão **Gerenciar** . Ele será redirecionado para o painel de controle de SendGrid. 

    ![Painel de controle de sendgrid][sendgrid-control-panel]

    Para obter mais informações sobre como começar com SendGrid, consulte [SendGrid Introdução][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Recursos de SendGrid]: http://sendgrid.com/features
[Portal de gerenciamento do Azure]: https://manage.windowsazure.com
[SendGrid Introdução]: http://sendgrid.com/docs
[Processo de provisionamento de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[todas as APIs]: https://sendgrid.com/docs/API_Reference/index.html

