
   * Entrar na conta do Microsoft Azure por meio de suas credenciais.

     Este método é mais rápido e fácil, mas se você usar esse método não será capaz de ver o banco de dados do SQL Azure ou serviços de celular na janela **Gerenciador de servidor** .

     No **Server Explorer**, clique no botão **conectar ao Azure** . Uma alternativa é o nó do **Azure** de atalho e, em seguida, clique em **conectar à Azure** no menu de contexto.

   * Instale um certificado de gerenciamento que permite o acesso à sua conta.

     No **Server Explorer**, clique com botão direito no nó do **Azure** e, em seguida, clique em **Gerenciar inscrições** no menu de contexto. Na caixa de diálogo **Gerenciar assinaturas do Azure** , clique na guia **certificados** e, em seguida, clique em **Importar**. Siga as instruções para baixar e, em seguida, importar um arquivo de assinatura (também chamado de um arquivo *. publishsettings* ) para sua conta do Azure.

     > [AZURE.NOTE] Baixar o arquivo de inscrição para uma pasta fora de seus diretórios de código de origem (por exemplo, na pasta Downloads) e, em seguida, excluí-la após a importação ter sido concluído. Um usuário mal-intencionado que obtém acesso para o arquivo de assinatura pode editar, criar e excluir seus serviços Azure.

    Para obter mais informações, consulte [como conectar-se ao Azure do Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).
