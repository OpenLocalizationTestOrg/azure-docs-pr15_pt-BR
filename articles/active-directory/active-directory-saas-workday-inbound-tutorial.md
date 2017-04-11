<properties 
    pageTitle="Tutorial: Configurando DIATRABALHO para sincronização de entrada | Microsoft Azure" 
    description="Saiba como usar DIATRABALHO como fonte de dados de identidade do Active Directory do Azure." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Tutorial: Configurando DIATRABALHO para sincronização de entrada


O objetivo deste tutorial é mostrar as etapas que necessárias para executar no DIATRABALHO e Azure AD para importar pessoas do dia de trabalho para o Azure AD. 

O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure AD Premium
-   Um locatário em DIATRABALHO
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1. Habilitar a integração de aplicativo para o dia de trabalho 


2. Criação de um usuário do sistema de integração 


3. Criar um grupo de segurança 


4. Atribuindo o usuário do sistema de integração ao grupo de segurança 


5. Configurar opções de grupo de segurança 


6. Ativando alterações de política de segurança 


7. Configurando importação de usuário no Azure AD 



##<a name="enabling-the-application-integration-for-workday"></a>Habilitar a integração de aplicativo para o dia de trabalho
  
O objetivo desta seção é como habilitar a integração de aplicativo para DIATRABALHO da estrutura de tópicos.

### <a name="steps"></a>Etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Adicionar aplicativo")

  
5. Na caixa de pesquisa, digite **DIATRABALHO**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Adicionar um aplicativo de gallerry")

6. No painel de resultados, selecione o dia de trabalho e, em seguida, clique em Concluir para adicionar o aplicativo.

    ![Galeria de aplicativo] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galeria de aplicativo")





## <a name="creating-an-integration-system-user"></a>Criação de um usuário do sistema de integração

### <a name="steps"></a>Etapas:


1. No **Ambiente de trabalho do dia de trabalho**, digite criar usuário na caixa de pesquisa e clique em **Criar usuário de integração de sistema**. 

    ![Criar usuário] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Criar usuário")



2. Conclua a tarefa de **Criar usuário de integração de sistema** , fornecendo um nome de usuário e senha para um novo usuário do sistema de integração.  Deixe a senha nova exigir no entrar próxima opção desmarcada, porque esse usuário fizerem logon em programaticamente. Deixe os minutos de tempo limite de sessão com seu valor padrão de 0, o que impedirá sessões do usuário tempo limite prematuramente. 

    ![Criar usuário de integração de sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Criar usuário de integração de sistema")
 




## <a name="creating-a-security-group"></a>Criar um grupo de segurança

Para o cenário descrito neste tutorial, você precisa criar um grupo de segurança do sistema de integração irrestrito e atribuir o usuário a ela.

### <a name="steps"></a>Etapas:

1. Insira criar grupo de segurança na caixa de pesquisa e, em seguida, clique em **Criar grupo de segurança**. 

    ![Grupo de CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Grupo de CreateSecurity")
 

2. Conclua a tarefa de criar o grupo de segurança.  Selecionar grupo de segurança de sistema de integração — ilimitado no menu suspenso de tipo de grupo de segurança com inquilinos, para criar um grupo de segurança aos quais os membros serão explicitamente adicionados. 

    ![Grupo de CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Grupo de CreateSecurity")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Atribuindo o usuário do sistema de integração ao grupo de segurança

### <a name="steps"></a>Etapas:


1. Insira o grupo de segurança de editar na caixa de pesquisa e clique em **Editar grupo de segurança**. 

    ![Editar grupo de segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Editar grupo de segurança")
 
 

2. Procure e selecione o novo grupo de segurança de integração por nome. 

    ![Editar grupo de segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Editar grupo de segurança")

 

3. Adicione o novo usuário de sistema de integração para o novo grupo de segurança. 

    ![Grupo de segurança do sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Grupo de segurança do sistema")  




## <a name="configuring-security-group-options"></a>Configurar opções de grupo de segurança

Nesta etapa, você conceder para as novas permissões de grupo de segurança para operações de **obter** e **colocar** nos objetos protegidos pelas seguintes políticas de segurança de domínio:

- Provisionamento de conta externa

- Dados de trabalho: Relatórios de trabalho público

- Dados de trabalho: Posições de todos os

- Dados de trabalho: Atual informações de pessoal

- Dados de trabalho: Título de negócios no perfil de trabalho

 
### <a name="steps"></a>Etapas:

1. Insira políticas de segurança de domínio na caixa de pesquisa e, em seguida, clique no link, políticas de segurança de domínio para a área funcional.  

    ![Políticas de segurança de domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Políticas de segurança de domínio")  
 

2. Pesquise sistema e selecionar a área funcional do **sistema** .  Clique em **Okey**.  

    ![Políticas de segurança de domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Políticas de segurança de domínio")  


3. Na lista de políticas de segurança para a área funcional do sistema, expanda administração de segurança e selecione a política de segurança de domínio, provisionamento de contas externas.  

    ![Políticas de segurança de domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Políticas de segurança de domínio")  


4. Clique em **Editar permissões**e, em seguida, na página de diálogo **Editar permissões**, adicione o novo grupo de segurança para a lista de grupos de segurança com permissões de integração de **obter** e **colocar** . 

    ![Permissão de edição] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Permissão de edição")  

 

5. Repita a etapa 1, acima, para retornar para a tela para selecionar áreas funcionais, e desta vez, procure pessoal, selecione a área funcional pessoal e clique em **Okey**.

    ![Políticas de segurança de domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Políticas de segurança de domínio")  
 

6. Na lista de políticas de segurança para a área funcional pessoal, expanda trabalhador dados: pessoal e repita a etapa 4 acima para cada um desses restante políticas de segurança:

     - Dados de trabalho: Relatórios de trabalho público

     - Dados de trabalho: Posições de todos os

     - Dados de trabalho: Atual informações de pessoal

     - Dados de trabalho: Título de negócios no perfil de trabalho


    ![Políticas de segurança de domínio] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Políticas de segurança de domínio")  







## <a name="activating-security-policy-changes"></a>Ativando alterações de política de segurança

### <a name="steps"></a>Etapas:

1. Insira ativar na caixa de pesquisa e, em seguida, clique no link, ativar alterações de política de segurança pendentes. 

    ![Ativar] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Ativar") 
 

2. Iniciar a tarefa de ativar alterações de política de segurança pendentes por meio de um comentário para fins de auditoria e, em seguida, clique em **Okey**. 

    ![Ativar pendentes segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Ativar pendentes segurança")   
 

3. Concluir a tarefa na próxima tela marcando a caixa de seleção rotulada confirmar e clique em **Okey**. 

    ![Ativar pendentes segurança] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Ativar pendentes segurança")  





## <a name="configuring-user-import-in-azure-ad"></a>Configurando importação de usuário no Azure AD

O objetivo desta seção é como configurar Azure AD para importar pessoas do DIATRABALHO da estrutura de tópicos.


### <a name="steps"></a>Etapas:


1. Na página de integração de aplicativo **DIATRABALHO** , clique em **Configurar usuário importar** para abrir a caixa de diálogo **Configurar provisionamento** .


2. Na página **configurações e credenciais de administrador** , execute as seguintes etapas e clique em **Avançar**: 

    ![Configurações e credenciais de administrador] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Configurações e credenciais de administrador")  
 
    a. Na caixa DIATRABALHO administração usuário nome de texto, digite o nome do usuário que você criou na criando uma seção de usuário do sistema de integração.

    b. Na caixa senha de administrador DIATRABALHO texto, digite a senha do usuário que você criou na criando uma seção de usuário do sistema de integração.

    c. Na caixa de texto DIATRABALHO locatário URL, digite a URL ou seu locatário DIATRABALHO.


3. Na página **Testar conexão** , clique em **Iniciar teste** para confirmar a conectividade e, em seguida, clique em **Avançar**. 

    ![Conexão de teste] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Conexão de teste")  
 

4. Na página de opções de **provisionamento** , clique em **Avançar**. 

    ![Opções de provisionamento] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opções de provisionamento")


5. Na caixa de diálogo **Iniciar provisionamento** , clique em **Concluir**. 

    ![Iniciar provisionamento] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Iniciar provisionamento")
 

Agora você pode ir para a seção de **usuários** e verificar se o usuário do seu dia de trabalho foi importado.



## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
