<properties 
    pageTitle="Tutorial: Configurando DIATRABALHO para sincronização de entrada | Microsoft Azure" 
    description="Aprenda a usar a sincronização de entrada com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Tutorial: Configurando DIATRABALHO para sincronização de entrada
>[AZURE.NOTE]Azure Active Directory (AD) Premium está disponível para clientes na China usando a instância em todo o mundo do Azure AD.    
Azure AD Premium não é suportado atualmente no serviço Microsoft Azure operado pela 21Vianet na China.    

O objetivo deste tutorial é mostrar as etapas que necessárias para executar no DIATRABALHO e Microsoft Azure AD para importar pessoas do dia de trabalho para o Microsoft Azure AD.    
 O cenário descrito neste tutorial supõe que você já tem os seguintes itens:  

-   Uma assinatura válida do Azure  
-   Um locatário em DIATRABALHO  

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:  

1.  Habilitar a integração de aplicativo para o dia de trabalho  
2.  Criação de um usuário do sistema de integração  
3.  Criar um grupo de segurança  
4.  Atribuindo o usuário do sistema de integração ao grupo de segurança  
5.  Configurar opções de grupo de segurança  
6.  Ativando alterações de política de segurança  
7.  Configurando importação de usuário no Microsoft Azure AD  

##<a name="enabling-the-application-integration-for-workday"></a>Habilitar a integração de aplicativo para o dia de trabalho

O objetivo desta seção é como habilitar a integração de aplicativo para a equipe de vendas da estrutura de tópicos.    

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para o dia de trabalho, execute as seguintes etapas:

1.  No Portal de gerenciamento do Azure, no painel de navegação esquerdo, clique em **Active Directory**.    

    ![Do Active Directory] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Do Active Directory")  

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.    

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.    

    ![Aplicativos] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Aplicativos")  

4.  Para abrir a **Galeria de aplicativo**, clique em **Adicionar um aplicativo**e clique em **Adicionar um aplicativo para minha organização usar**.    

    ![o que você deseja fazer?] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "o que você deseja fazer?")  

5.  Na **caixa de pesquisa**, digite **DIATRABALHO**.    

    ![DIATRABALHO] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "DIATRABALHO")  

6.  No painel de resultados, selecione **o dia de trabalho**e, em seguida, clique em **concluído** para adicionar o aplicativo.    

    ![DIATRABALHO] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "DIATRABALHO")  

##<a name="creating-an-integration-system-user"></a>Criação de um usuário do sistema de integração

1.  No **Ambiente de trabalho do dia de trabalho**, insira **Criar usuário** na caixa de pesquisa e, em seguida, clique no link, **Criar usuário de integração de sistema**.     

    ![Criar usuário] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "Criar usuário")  

2.  Conclua a tarefa de criar usuário do sistema de integração, fornecendo um nome de usuário e senha para um novo usuário do sistema de integração.  Deixe a senha nova exigir no entrar próxima opção desmarcada, porque esse usuário fizerem logon em programaticamente.    
    Deixe os minutos de tempo limite de sessão com seu valor padrão de 0, o que impedirá sessões do usuário tempo limite prematuramente.    

    ![Criar usuário de integração de sistema] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Criar usuário de integração de sistema")  

##<a name="creating-a-security-group"></a>Criar um grupo de segurança

Para o cenário descrito neste tutorial, você precisa criar um grupo de segurança do sistema de integração irrestrito e atribuir o usuário a ela.    

1.  Insira criar grupo de segurança na caixa de pesquisa e clique no link, criar grupo de segurança.     

    ![Grupo de CreateSecurity] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "Grupo de CreateSecurity")  

2.  Conclua a tarefa de criar o grupo de segurança.  Selecionar grupo de segurança de sistema de integração — ilimitado no menu suspenso de tipo de grupo de segurança com inquilinos, para criar um grupo de segurança aos quais os membros serão explicitamente adicionados.     

    ![Grupo de CreateSecurity] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "Grupo de CreateSecurity")  

##<a name="assigning-the-integration-system-user-to-the-security-group"></a>Atribuindo o usuário do sistema de integração ao grupo de segurança

1.  Insira Editar grupo de segurança na caixa de pesquisa e, em seguida, clique no link, **Edite o grupo de segurança**.     

    ![Editar grupo de segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Editar grupo de segurança")  

2.  Procurar e selecione o novo grupo de segurança de integração por nome    

    ![Editar grupo de segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Editar grupo de segurança")  

3.  Adicione o novo usuário de sistema de integração para o novo grupo de segurança.       

    ![Grupo de segurança do sistema] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "Grupo de segurança do sistema")  

##<a name="configuring-security-group-options"></a>Configurar opções de grupo de segurança

Nesta etapa, você conceder para as novas permissões de grupo de segurança para operações de obter e colocar nos objetos protegidos pelas seguintes políticas de segurança de domínio:  

-   Provisionamento de conta externa  
-   Dados de trabalho: Relatórios de trabalho público  
-   Dados de trabalho: Posições de todos os  
-   Dados de trabalho: Atual informações de pessoal  
-   Dados de trabalho: Título de negócios no perfil de trabalho  

&nbsp;  

1.  Insira políticas de segurança de domínio na caixa de pesquisa e, em seguida, clique no link, políticas de segurança de domínio para a área funcional.     

    ![Políticas de segurança de domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Políticas de segurança de domínio")  

2.  Pesquise sistema e selecionar a área funcional do sistema.  Clique no botão rotulado, Okey.     

    ![Políticas de segurança de domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Políticas de segurança de domínio")  

3.  Na lista de políticas de segurança para a área funcional do sistema, expanda administração de segurança e selecione a política de segurança de domínio, provisionamento de contas externas.     

    ![Políticas de segurança de domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Políticas de segurança de domínio")  

4.  Clique no botão Editar permissões e, em seguida, na tela de editar permissões, adicione o novo grupo de segurança para a lista de grupos de segurança com permissões de integração de obter e colocar.     

    ![Permissão de edição] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Permissão de edição")  

5.  Repita a etapa 1, acima, para retornar para a tela para selecionar áreas funcionais, e desta vez, procure pessoal, selecione área funcional pessoal e clique no botão rotulado, Okey.    

    ![Políticas de segurança de domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Políticas de segurança de domínio")  

6.  Na lista de políticas de segurança para a área funcional pessoal, expanda trabalhador dados: pessoal e repita a etapa 4 acima para cada um desses restante políticas de segurança:    

    -   Dados de trabalho: Relatórios de trabalho público  
    -   Dados de trabalho: Posições de todos os  
    -   Dados de trabalho: Atual informações de pessoal  
    -   Dados de trabalho: Título de negócios no perfil de trabalho    

    ![Políticas de segurança de domínio] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Políticas de segurança de domínio")  

##<a name="activating-security-policy-changes"></a>Ativando alterações de política de segurança

1.  Insira ativar na caixa de pesquisa e, em seguida, clique no link, ativar alterações de política de segurança pendentes.    

    ![Ativar] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Ativar")  

2.   Iniciar a tarefa de ativar alterações de política de segurança pendentes inserindo um comentário para fins de auditoria e, em seguida, clicando no botão rotulado, Okey.      

    ![Ativar pendentes segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Ativar pendentes segurança")  

3.  Conclua a tarefa na próxima tela marcando a caixa de seleção rotulada Confirm e clicando no botão rotulado, Okey.     

    ![Ativar pendentes segurança] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Ativar pendentes segurança")  

##<a name="configuring-user-import-in-microsoft-azure-ad"></a>Configurando importação de usuário no Microsoft Azure AD

O objetivo desta seção é descrevem como configurar o Microsoft Azure AD importar pessoas do dia de trabalho.    

###<a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>Para configurar a importação de usuário no Microsoft Azure AD, execute as seguintes etapas:

1.  Na página de integração de aplicativo **DIATRABALHO** , clique em **Configurar usuário importar** para abrir a caixa de diálogo **Configurar provisionamento** .    

2.  Na página **configurações e credenciais de administrador** , execute as seguintes etapas e clique em Avançar:    

    ![Configurações e credenciais de administrador] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Configurações e credenciais de administrador")    

    1.  Na caixa de texto **nome de usuário de administrador do dia de trabalho** , digite o nome do usuário que você criou na seção [Criando um usuário de integração de sistema](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    2.  Na caixa de texto **senha de administrador do dia de trabalho** , digite a senha do usuário que você criou na seção [Criando um usuário de integração de sistema](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    3.  Na caixa de texto **DIATRABALHO locatário URL** , digite a URL ou seu locatário DIATRABALHO.    

3.  Na página **Testar conexão** , clique em **Iniciar teste** para confirmar a conectividade e, em seguida, clique em **Avançar**.    

    ![Conexão de teste] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Conexão de teste")  

4.  Na página **Opções de provisionamento** , clique em **Avançar**.    

    ![Opções de provisionamento] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Opções de provisionamento")  

5.  Na caixa de diálogo **Iniciar provisionamento** , clique em **Concluir**.    

    ![Iniciar provisionamento] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Iniciar provisionamento")  

Agora você pode ir para a seção de **usuários** e verificar se o usuário do seu dia de trabalho foi importado.    
