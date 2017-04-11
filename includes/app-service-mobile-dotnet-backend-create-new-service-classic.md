1. Logon no [Portal do Azure].

2. Clique em **+ novo** > **Web + Mobile** > **Aplicativo móvel**, em seguida, forneça um nome para seu back-end do aplicativo Mobile.

3. Para o **Grupo de recursos**, selecione um grupo de recursos existente ou crie um novo (usando o mesmo nome como seu aplicativo). 
 
    Você pode selecionar outro plano de serviço do aplicativo ou criar um novo. Para saber mais sobre os serviços de aplicativo planos e como criar um novo plano em uma guia de preços diferentes nível e no seu local desejado, consulte [Visão geral detalhada de planos de serviço de aplicativo do Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para o **plano de serviço de aplicativo**, o plano de padrão (na [camada padrão](https://azure.microsoft.com/pricing/details/app-service/)) está selecionado. Você também pode selecionar um plano diferente ou [Crie um novo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Configurações do plano de serviço de aplicativo determinam o [local, recursos, custos e recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associado ao seu aplicativo. 

    Depois que decidir sobre o plano, clique em **criar**. Isso cria o back-end do aplicativo Mobile. 
    
6. Na lâmina **configurações** para o novo back-end do aplicativo móvel, clique em **início rápido** > sua plataforma de aplicativo cliente > **conectar um banco de dados**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. Na lâmina **Adicionar conexão de dados** , clique em **Banco de dados SQL** > **criar um novo banco de dados**, digite o **nome**do banco de dados, escolha um nível de preços e clique em **servidor**.  Você pode reutilizar este novo banco de dados. Se você já tiver um banco de dados no mesmo local, em vez disso, você pode escolher **usar um banco de dados existente**. O uso de um banco de dados em um local diferente não é recomendado devido a custos de largura de banda e latência maior.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. Na lâmina **novo servidor** , digite um nome de servidor exclusivo no campo **nome do servidor** , forneça um login e a senha, marque **Permitir azure services para acessar o servidor**e clique em **Okey**. Isso cria o novo banco de dados.

9. Novamente na lâmina **Adicionar conexão de dados** , clique em **cadeia de caracteres de Conexão**, digite os valores de login e a senha do banco de dados e clique em **Okey**. Aguarde alguns minutos para o banco de dados ser implantado com êxito antes de continuar.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
