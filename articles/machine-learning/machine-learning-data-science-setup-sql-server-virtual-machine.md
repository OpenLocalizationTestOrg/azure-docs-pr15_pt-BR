<properties
    pageTitle="Configurar uma máquina virtual de SQL Server como um servidor de bloco de anotações de IPython | Microsoft Azure"
    description="Defina para cima uma dados ciência Máquina Virtual com SQL Server e IPython Server."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar uma máquina virtual de Azure SQL Server como um servidor de bloco de anotações de IPython para análise avançada

Este tópico mostra como provisionar e configurar uma máquina virtual de SQL Server a ser usado como parte de um ambiente de ciência de dados baseada em nuvem. O computador virtual Windows está configurado com ferramentas como o bloco de anotações de IPython, Gerenciador de armazenamento do Azure e AzCopy, bem como outros utilitários que são úteis para projetos de ciência de dados de suporte. Azure Storage Explorer e AzCopy, por exemplo, fornecem maneiras convenientes para carregar dados para o armazenamento de blob do Microsoft Azure do computador local ou baixá-la em sua máquina local do armazenamento de blob.

A Galeria do Azure máquina virtual inclui várias imagens que contêm o Microsoft SQL Server. Selecione uma imagem de máquina virtual do SQL Server que seja adequada às suas necessidades de dados. Imagens recomendadas são:

- SQL Server 2012 SP2 Enterprise para pequenas e tamanhos de dados médios
- SQL Server 2012 SP2 Enterprise otimizado para cargas de trabalho de DataWarehousing para tamanhos grandes de dados

 > [AZURE.NOTE] SQL Server 2012 SP2 Enterprise imagem **não inclui um disco de dados**. Você precisará adicionar e/ou anexar um ou mais discos virtuais para armazenar seus dados. Quando você cria uma máquina virtual Azure, ele tem um para o sistema operacional mapeado para a unidade C e um disco temporário mapeados para a unidade D. Não use unidade D para armazenar dados. Como o nome indica, ele oferece somente armazenamento temporário. Ele oferece sem redundância ou backup porque ela não reside no armazenamento do Azure.


##<a name="Provision"></a>Conectar ao Portal de clássico do Azure e provisionar uma máquina virtual de SQL Server

1.  Faça logon no [Portal de clássico do Azure](http://manage.windowsazure.com/) usando sua conta.
    Se você não tiver uma conta do Azure, visite a [versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

2.  No Portal clássico do Azure, no canto inferior esquerdo da página da web, clique em **+ novo**, clique em **CALCULAR**, clique **máquina VIRTUAL**e, em seguida, clique em **GALERIA de**.

3.  Na página **criar uma máquina Virtual** , selecione uma imagem de máquina virtual contendo o SQL Server com base nas suas necessidades de dados e, em seguida, clique na seta próxima na parte inferior direita da página. Para as informações mais atualizadas sobre as imagens do SQL Server com suporte no Azure, consulte [Introdução ao SQL Server em máquinas virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) tópico na documentação do [SQL Server em máquinas virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) definir.

    ![Selecione máquina virtual do SQL Server][1]

4.  Na primeira página de **Configuração de máquina Virtual** , forneça as seguintes informações:

    -   Forneça um **nome de máquina VIRTUAL**.
    -   Na caixa **Novo nome de usuário** , digite o nome de usuário exclusiva para a conta de administrador local da máquina virtual.
    -   Na caixa **Nova senha** , digite uma senha forte. Para obter mais informações, consulte [Senhas fortes](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Na caixa **Confirmar senha** , redigite a senha.
    -   Selecione o **tamanho** de apropriado na lista suspensa.

     > [AZURE.NOTE] O tamanho da máquina virtual é especificado durante a configuração: A2 é menor tamanho de recomendados para cargas de trabalho de produção. O tamanho mínimo recomendado para uma máquina virtual é A3 ao usar o SQL Server Enterprise Edition. Selecione A3 ou superior ao usar o SQL Server Enterprise Edition. Selecione A4 ao usar o SQL Server 2012 ou 2014 Enterprise otimizada para imagens de transações cargas de trabalho.
Selecione A7 ao usar o SQL Server 2012 ou 2014 Enterprise otimizada para imagens de Data warehouse cargas de trabalho. O tamanho selecionado limita o número de discos de dados que você pode configurar. Para obter informações mais atualizadas em tamanhos de máquina virtual disponível e o número de discos de dados que você pode anexar a uma máquina virtual, consulte [Tamanhos de máquina Virtual do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para informações sobre preços, consulte [VIrtual Macines preços](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Clique na seta próxima na parte inferior direita para continuar.

    ![Configuração de máquina virtual][2]

5.  Na página de **configuração de máquina Virtual** do segunda, configure os recursos de rede, armazenamento e disponibilidade:

    -   Na caixa de **Serviço de nuvem** , escolha **criar um novo serviço de nuvem**.
    -   Na caixa **Nome de DNS de serviço de nuvem** , fornecer a primeira parte de um nome DNS de sua escolha, para que ele seja concluído um nome no formato **TESTNAME.cloudapp.net**
    -   Na caixa de **Rede de grupo/VIRTUAL de região/AFINIDADE** , selecione uma região onde esta imagem virtual será hospedada.
    -   Na **Conta de armazenamento**, selecione uma conta de armazenamento existente ou selecione um gerado automaticamente.
    -   Na caixa **Definir disponibilidade** , selecione **(nenhum)**.
    -   Leia e aceite as informações sobre preços.

6.  Na seção **pontos de EXTREMIDADE** , clique na lista suspensa vazia em **nome**, selecione **MSSQL** e digite o número da porta da instância do mecanismo de banco de dados (**1433** para a instância padrão).

7.  Sua máquina de virtual do SQL Server também pode servir como um servidor de bloco de anotações de IPython, que serão configurados em uma etapa posterior.
    Adicione um novo ponto de extremidade para especificar a porta para usar para o servidor de bloco de anotações de IPython. Insira um nome na coluna **nome** , selecione um número de porta de sua escolha para a porta pública e 9999 para a porta particular.

    Clique na seta próxima na parte inferior direita para continuar.

    ![Selecione portas MSSQL e IPython][3]

8.  Aceitar a opção de **máquina virtual instalar agente** padrão marcada e clique no a marca de seleção no canto inferior direito do Assistente para concluir a máquina virtual o processo de provisionamento.

    `![Opções de Final de máquina virtual][4]

9.  Aguarde enquanto o Azure prepara sua máquina virtual. Espera o status de máquina virtual para continuar:

    -   Iniciando (provisionamento)
    -   Interrompido
    -   Iniciando (provisionamento)
    -   Executando (provisionamento)
    -   Em execução


##<a name="RemoteDesktop"></a>Abra a máquina virtual usando a área de trabalho remota e concluir a instalação

1.  Quando provisionamento for concluído, clique no nome da sua máquina virtual para ir para a página de painel. Na parte inferior da página, clique em **Conectar**.

2.  Optar por abrir o arquivo de rpd usando o programa de área de trabalho remota do Windows (`%windir%\system32\mstsc.exe`).

3.  Na caixa de diálogo **Segurança do Windows** , forneça a senha da conta administrador local que você especificou em uma etapa anterior.
    (Você pode ser solicitado para verificar as credenciais da máquina virtual.)

4.  Na primeira vez que você efetuar logon nesta máquina virtual, vários processos talvez precise ser concluído, incluindo a configuração de sua área de trabalho, atualizações do Windows e concluir as tarefas de configuração inicial do Windows (sysprep). Após a conclusão de sysprep do Windows, o programa de instalação do SQL Server conclui tarefas de configuração. Essas tarefas podem causar um atraso de alguns minutos enquanto eles concluem. `SELECT @@SERVERNAME`pode não retornar o nome correto até a conclusão da instalação do SQL Server e SQL Server Management Studio pode não estar visable na página inicial.

Quando você estiver conectado à máquina virtual com área de trabalho remota do Windows, a máquina virtual funciona muito parecido com qualquer outro computador. Conecte-se a instância padrão do SQL Server com o SQL Server Management Studio (executando na máquina virtual) da maneira normal.


##<a name="InstallIPython"></a>Instalar o bloco de anotações de IPython e outras ferramentas de suporte

Para configurar sua nova máquina virtual Server de SQL para servir como um servidor de bloco de anotações de IPython e instalar as ferramentas de suporte adicionais tal AzCopy, Gerenciador de armazenamento do Azure, útil pacotes de Python de ciência de dados e outras pessoas, um script de personalização especial é fornecido a você. Para instalar:

- Clique no ícone **Iniciar do Windows** e clique em **Prompt de comando (Admin)**
- Os seguintes comandos de copiar e colar no prompt de comando.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Quando solicitado, digite uma senha de sua escolha para o servidor de bloco de anotações de IPython.
- O script de personalização automatiza vários procedimentos de pós-instalação, que incluem:
    + Instalação e configuração do servidor de bloco de anotações de IPython
    + Abrindo portas TCP no firewall do Windows para os pontos de extremidade criado anteriormente:
    + Para conectividade remota do SQL Server
    + Para conectividade remota do bloco de anotações de IPython server
    + Buscar blocos de anotações de IPython de exemplo e scripts SQL
    + Baixar e instalar pacotes de dados ciência Python útil
    + Baixar e instalar o Azure ferramentas tais como AzCopy e Gerenciador de armazenamento do Azure  
<br>
- Você pode acessar e executar o bloco de anotações de IPython de qualquer navegador local ou remoto usando uma URL do formulário `https://<virtual_machine_DNS_name>:<port>`, onde a porta é a IPython público selecionado ao provisionar a máquina virtual.
- Servidor de bloco de anotações de IPython está sendo executado como um serviço de plano de fundo e será reiniciada automaticamente quando você reiniciar a máquina virtual.

##<a name="Optional"></a>Anexar dados disco conforme necessário

Se sua imagem de máquina virtual não incluir dados discos, ou seja, além unidade C (disco do sistema operacional) e unidade D (disco temporário), é necessário adicionar um ou mais discos de dados para armazenar seus dados. A imagem de máquina virtual para SQL Server 2012 SP2 Enterprise otimizado para cargas de trabalho de DataWarehousing vem pré-configurado com discos adicionais para arquivos de dados e log do SQL Server.

 > [AZURE.NOTE] Não use unidade D para armazenar dados. Como o nome indica, ele oferece somente armazenamento temporário. Ele oferece sem redundância ou backup porque ela não reside no armazenamento do Azure.

Para anexar discos de dados adicionais, siga as etapas descritas em [como anexar um disco de dados para uma máquina Virtual do Windows](virtual-machines-windows-classic-attach-disk.md), que o orientará:

1. Anexar s vazia na máquina virtual provisionado em etapas anteriores
2. Inicialização do novo s na máquina virtual


##<a name="SSMS"></a>Conectar ao SQL Server Management Studio e habilitar a autenticação de modo misto

O mecanismo de banco de dados do SQL Server não pode usar a autenticação do Windows sem ambiente de domínio. Para conectar o mecanismo de banco de dados de outro computador, configure o SQL Server para autenticação de modo misto. Autenticação de modo misto permite a autenticação do SQL Server e autenticação do Windows. Modo de autenticação do SQL é necessário incluir dados diretamente de seus bancos de dados de máquina virtual do SQL Server no [Studio de aprendizado de máquina do Azure](https://studio.azureml.net) usando o módulo de importar dados.

1.  Enquanto estiver conectado à máquina virtual usando a área de trabalho remota, use o painel de **pesquisa** do Windows e digite o **SQL Server Management Studio** (SMSS). Clique para iniciar o SQL Server Management Studio (SSMS). Talvez você queira adicionar um atalho para SSMS na área de trabalho para uso futuro.

    ![Iniciar SSMS][5]

    Na primeira vez que você abre Management Studio ele deve criar o ambiente de Management Studio de usuários. Isso pode levar alguns momentos.

2.  Ao abrir, Management Studio apresenta a caixa de diálogo **conectar ao servidor** . Na caixa **nome do servidor** , digite o nome da máquina virtual para se conectar ao mecanismo de banco de dados com o Explorador de objeto.
    (Em vez do nome da máquina virtual você também pode usar **(local)** ou um único período como o **nome do servidor**. Selecione a **Autenticação do Windows**e deixe ** *sua\_máquina virtual\_nome*\\sua\_local\_administrador** na caixa **nome de usuário** . Clique em **Conectar**.

    ![Conectar ao servidor][6]

    <br>

     > [AZURE.TIP] Você pode alterar o modo de autenticação do SQL Server usando uma alteração de chave de registro do Windows ou o SQL Server Management Studio. Para alterar o modo de autenticação usando a alteração de chave do registro, inicie uma **Nova consulta** e execute o seguinte script:

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Para alterar o modo de autenticação usando o SQL Server management Studio:

3.  No **Pesquisador de objetos do SQL Server Management Studio**, clique com botão direito no nome da instância do SQL Server (o nome de máquina virtual) e clique em **Propriedades**.

    ![Propriedades do servidor][7]

4.  Na página **segurança** , em **autenticação de servidor**, selecione o **modo de autenticação do Windows e do SQL Server**e clique em **Okey**.

    ![Selecione o modo de autenticação][8]

5.  Na caixa de diálogo **SQL Server Management Studio** , clique em **Okey** para confirmar a necessidade de reiniciar o SQL Server.

6.  No **Pesquisador de objetos**, seu servidor de atalho e, em seguida, clique em **Reiniciar**. (Se estiver executando o agente do SQL Server, ele deve também ser reiniciado.)

    ![Reiniciar][9]

7.  Na caixa de diálogo **SQL Server Management Studio** , clique em **Sim** para concordar que você quiser reiniciar o SQL Server.

##<a name="Logins"></a>Criar logon de autenticação do SQL Server

Para conectar o mecanismo de banco de dados de outro computador, você deve criar pelo menos um logon de autenticação do SQL Server.  

Você pode criar novo logon do SQL Server programaticamente ou usando o SQL Server Management Studio. Para criar um novo usuário de administrador do sistema com autenticação SQL programaticamente, inicie uma **Nova consulta** e execute o seguinte script. Substitua < novo nome de usuário\> e < nova senha\> com sua opção de *nome de usuário* e *senha*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Ajuste a política de senha conforme necessário (a amostra código desativa expiração de verificação e a senha de política). Para obter mais informações sobre o logon do SQL Server, consulte [criar um logon](http://msdn.microsoft.com/library/aa337562.aspx).  

Para criar o novo logon do SQL Server usando o SQL Server Management Studio:

1.  No **Pesquisador de objetos do SQL Server Management Studio**, expanda a pasta da instância do servidor no qual você deseja criar o novo logon.

2.  Clique com botão direito na pasta de **segurança** , aponte para **novo**e selecione **logon...**.

    ![Novo logon][10]

3.  Na caixa de diálogo **Login - novo** , na página **Geral** , digite o nome do novo usuário na caixa **nome de Login** .

4.  Selecione a **autenticação do SQL Server**.

5.  Na caixa **senha** , digite uma senha para o novo usuário. Digite a senha novamente na caixa **Confirmar senha** .

6.  Para impor opções de política de senha para a imposição e complexidade, selecione **Aplicar diretiva de senha** (recomendado). Essa é uma opção de padrão quando a autenticação do SQL Server está selecionada.

7.  Para impor opções de política de senha para expiração, selecione **Aplicar a expiração de senha** (recomendado). Impor diretiva de senha deve estar marcada para esta caixa de seleção Habilitar. Essa é uma opção de padrão quando a autenticação do SQL Server está selecionada.

8.  Para forçar o usuário para criar uma nova senha depois da primeira vez que o logon é usado, selecione o **usuário deve alterar senha no próximo logon** (recomendado se este logon é para outra pessoa usar. Se o logon for para seu próprio uso, não selecione essa opção.) Impor expiração de senha deve estar marcada para esta caixa de seleção Habilitar. Essa é uma opção de padrão quando a autenticação do SQL Server está selecionada.

9.  Na lista de **banco de dados padrão** , selecione um banco de dados padrão para o logon. **mestre** é o padrão para essa opção. Se você ainda não tiver criado um banco de dados do usuário, deixe esse conjunto **mestre**.

10. Na lista **idioma padrão** , deixe **padrão** como o valor.

    ![Propriedades de login][11]

11. Se esse for o primeiro login que você está criando, você talvez queira designar este login como um administrador do SQL Server. Em caso afirmativo, na página de **Funções de servidor** , marque **administrador do sistema**.

    > [AZURE.IMPORTANT] Membros da função de servidor fixa sysadmin tem total controle sobre o mecanismo de banco de dados. Por razões de segurança, você deve restringir cuidadosamente associação a essa função.

    ![administrador do sistema][12]

12. Clique em Okey.

##<a name="DNS"></a>Determinar o nome DNS da máquina virtual

Para conectar o mecanismo de banco de dados do SQL Server de outro computador, você deve saber o nome do sistema de nome de domínio (DNS) da máquina virtual.

(Este é o nome que na internet usa para identificar a máquina virtual. Você pode usar o endereço IP, mas o endereço IP pode ser alterada quando Azure move recursos para redundância ou manutenção. O nome DNS será estável porque ele pode ser redirecionado para um novo endereço IP.)

1.  No Portal de clássico do Azure (ou da etapa anterior), selecione **máquinas virtuais**.

2.  Na página **INSTÂNCIAS de máquina VIRTUAL** , na coluna **Nome DNS** , localize e copie o nome DNS para a máquina virtual que aparece precedida por **http://**. (A interface do usuário não pode exibir o nome completo, mas você pode com o botão direito nele e selecione Copiar.)

##<a name="cde"></a>Conectar-se ao mecanismo de banco de dados do outro computador

1.  Em um computador conectado à internet, abra o SQL Server Management Studio.

2.  Na caixa de diálogo **conectar-se ao servidor** ou **conectar-se ao mecanismo de banco de dados** , na caixa **nome do servidor** , insira o nome DNS da máquina virtual (determinado na tarefa anterior) e um número de porta do ponto de extremidade pública no formato de *DNSName, número da porta* como **tutorialtestVM.cloudapp.net,57500**.

3.  Na caixa de **autenticação** , selecione **Autenticação do SQL Server**.

4.  Na caixa **Login** , digite o nome de um logon que você criou em uma tarefa anterior.

5.  Na caixa **senha** , digite a senha de login que você cria em uma tarefa anterior.

6.  Clique em **Conectar**.

##<a name="amlconnect"></a>Conectar-se ao mecanismo de banco de dados de aprendizado de máquina Azure

Em estágios posteriores do processo de ciência de dados de equipe, você usará o [Studio de aprendizado de máquina do Azure](https://studio.azureml.net) para construir e implantar modelos de aprendizado de máquina. Para incluir dados de seus bancos de dados de máquina virtual do SQL Server diretamente para o aprendizado de máquina do Azure para treinamento ou pontuação, usar o módulo de **Importar dados** em um novato experimento [Studio de aprendizado de máquina do Azure](https://studio.azureml.net) . Este tópico é coberto de mais detalhes nos links guia processo de ciência de dados de equipe. Para uma introdução, consulte [o que é Azure Studio de aprendizado de máquina?](machine-learning-what-is-ml-studio.md).

2.  No painel **Propriedades** do [módulo de importar dados](https://msdn.microsoft.com/library/azure/dn905997.aspx), selecione **o banco de dados do Azure SQL** na lista suspensa de **Fonte de dados** .

3.  Na caixa de texto **nome do servidor de banco de dados** , insira`tcp:<DNS name of your virtual machine>,1433`

4.  Insira o nome de usuário do SQL na caixa de texto **nome de conta de usuário do servidor** .

5.  Insira a senha do usuário sql na caixa de texto **senha de conta de usuário do servidor** .

    ![Azure ML importar dados][13]

##<a name="shutdown"></a>Desligamento e desalocar máquina virtual quando não estiver em uso

Azure máquinas virtuais têm preços como **pagar somente para o qual você usa**. Para garantir que você não seja sendo cobrado quando não usando sua máquina virtual, ele deve estar no estado **parado (Deallocated)** .

> [AZURE.NOTE] Desligar a máquina virtual do dentro (usando opções de energia do Windows), a máquina virtual for interrompida, mas permanece alocado. Para garantir que você não está sendo cobrado, sempre pare máquinas virtuais a partir do [Portal clássico do Azure](http://manage.windowsazure.com/). Você também pode interromper a máquina virtual através do Powershell chamando ShutdownRoleOperation com "PostShutdownAction" igual a "StoppedDeallocated".

Para desligar e desalocar a máquina virtual:

1. Faça logon no [Portal de clássico do Azure](http://manage.windowsazure.com/) usando sua conta.  

2. Selecione **máquinas virtuais** da barra de navegação à esquerda.

3. Na lista de máquinas virtuais, clique no nome da sua máquina virtual e vá para a página de **painel** .

4. Na parte inferior da página, clique em **Desligar**.

![Desligamento de máquina virtual][15]

A máquina virtual será desalocada mas não excluída. Você pode reiniciar sua máquina virtual a qualquer momento a partir do Portal de clássico do Azure.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Sua máquina virtual Server de SQL Azure está pronto para uso: o que fazer a seguir?

Sua máquina virtual está pronta para usar em seus exercícios de ciência de dados. Na máquina virtual também está pronta para ser usado como um servidor de bloco de anotações de IPython a exploração e o processamento de dados e outras tarefas em conjunto com o aprendizado de máquina do Azure e o processo de ciência de dados de equipe (TDSP).

As próximas etapas no processo de ciência de dados são mapeadas no [Processo de ciência de dados de equipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir etapas que mover dados para HDInsight, processam e amostra-lo em preparação para aprender a partir dos dados com aprendizado de máquina do Azure.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
