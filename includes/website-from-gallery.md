Do Azure Marketplace disponibiliza uma ampla variedade de aplicativos web populares desenvolvida pela Microsoft, outras empresas e iniciativas de software de fonte aberta. Aplicativos Web criados a partir do Azure Marketplace não exige instalação de qualquer software diferente do navegador usado para conexão com o [Portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). 

Neste tutorial, você aprenderá:

- Como criar um novo aplicativo web por meio do Azure Marketplace.

- Como implantar o aplicativo web por meio do Portal de visualização do Azure.
 
Você vai criar um blog de WordPress que usa um modelo padrão. A ilustração a seguir mostra o aplicativo concluído:


![Blog de WordPress][13]

>[AZURE.NOTE] Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="create-a-web-app-in-the-portal"></a>Criar um aplicativo web no portal

1. Faça logon no Portal do Azure Preview.

2. Abra o Azure Marketplace clicando no ícone de **Marketplace** :

    ![Ícone de Marketplace][marketplace]

    Ou clicando no ícone de **novo** no canto superior direito do painel de controle e selecionando o **Marketplace** na bottow da lista.
    
    ![Criar novo][5]
    
3. Selecione **Web + Mobile**. Pesquise **WordPress** e clique no ícone de **WordPress** .

    ![WordPress de lista][7]
    
5. Após ler a descrição do aplicativo WordPress, selecione **criar**.

6. Clique no **aplicativo da Web**e fornecem os valores necessários para configurar seu aplicativo web.
    
    ![configurar seu aplicativo][8]

7. Clique no **banco de dados**e fornecem os valores necessários para configurar seu banco de dados MySQL. 

    ![Configurar banco de dados][database]

8. Forneça um nome para um novo grupo de recursos.

    ![Grupo de recursos do conjunto][groupname]

8. Se necessário, clique em **assinatura**e especifique a inscrição para usar. 

7. Quando você terminar de definir o web app, clique em **criar**e aguarde enquanto o novo aplicativo web é criado.

   Quando o aplicativo tiver sido criado, você verá o grupo de recursos que contém o banco de dados e web app.

   ![grupo Mostrar][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Iniciar e gerenciar seu aplicativo web do WordPress
    
1. Clique em seu novo aplicativo web para ver detalhes sobre seu aplicativo.

    ![Iniciar o painel de controle][10]

2. Na página **Essentials** , clique em **Procurar** ou no link em **Url** para abrir a página de boas-vindas do aplicativo da web.

    ![URL do site][browse]

3. Se você não instalou WordPress, insira as informações de configuração apropriada exigidas pelo WordPress e clique em **Instalar WordPress** para finalizar a configuração e abrir a página de login do aplicativo da web.

4. Clique em **Login** e insira suas credenciais.  

5. Você terá um novo aplicativo da web de WordPress parecido com o aplicativo web abaixo.    

    ![seu site WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
