
<properties 
    pageTitle="Protegendo access RemoteApp do Azure e além | Microsoft Azure"
    description="Saiba como seguro acesso ao Azure RemoteApp usando acesso condicional no Active Directory do Azure"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Protegendo access RemoteApp do Azure e além

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Neste artigo, vamos lhe dará uma visão geral de como um administrador pode configurar um canal de acesso seguro começando do usuário final, através de RemoteApp do Azure e terminando por um recurso seguro, como um banco de dados do SQL ou outro aplicativo back-end. O objetivo é garantir que apenas usuários autorizados que atendem às condições desejadas podem acessar aplicativos remotos e que o back-end seguro só pode ser acessado do ambiente do Azure RemoteApp controlado e não a partir de outros locais.

Existem 3 áreas principais, que o administrador precisa examinar:

![Considerações de acesso condicional RemoteApp Azure](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Leia sobre para obter informações e respostas para essas perguntas.

## <a name="who-can-access-the-collection"></a>Quem pode acessar a coleção?
O administrador escolhe os usuários que podem acessar aplicativos remotos na coleção. Você pode usar o Azure Active Directory (AD Azure) ou contas corporativas Estudante (anteriormente chamadas, "contas organizacionais") ou contas da Microsoft (por exemplo, @outlook.com). A maioria dos cenários de empresa usam contas do Azure AD; Eles permitem que você usar o access condicional recursos discutido posteriormente e também são a única opção para conjuntos de domínio. O restante do artigo pressupõe que você está usando contas do Azure AD com o Azure RemoteApp.

**O que podemos ter feito:**

Usando contas do Azure AD para controlar o acesso ao Azure RemoteApp oferece duas coisas:

1.  Estamos sempre saber quem pode acessar os aplicativos podemos publicou e acessar qualquer back-ends esses aplicativos conectam a.
2.  Podemos controlar o Azure AD subjacente para que podemos criar e excluir contas de usuário, definir políticas de senha, usam autenticação multifator, etc. 

## <a name="how-is-the-collection-accessed-from-where"></a>Como a coleção é acessada? De onde?
Comumente administradores desejam definir políticas para acessar um ambiente voltado para a Internet público, como RemoteApp do Azure. Por exemplo, se desejarem garantir que os usuários acessam o ambiente de fora da rede corporativa tenham usar autenticação multifator (MFA) para obter acesso; ou, talvez eles devem ser bloqueados completamente.

Administradores de RemoteApp Azure podem usar a funcionalidade disponível por meio do Azure AD Premium para definir regras de acesso condicional para seu ambiente do Azure RemoteApp. Eles também podem usar rich relatórios e alertas recursos para monitorar como o ambiente está sendo acessado.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Como configurar o acesso condicional para RemoteApp do Azure
Vamos percorrer um cenário de exemplo – o RemoteApp Azure administrador quiser bloquear o acesso para o ambiente quando os usuários estiverem fora da rede corporativa.

>[AZURE.NOTE] Vamos supor que você atualizou Azure AD para o nível Premium e que você tenha criado pelo menos um conjunto de RemoteApp do Azure.

1.  No portal Azure clique na guia do **Active Directory** . Em seguida, clique na pasta que você deseja configurar.

    Lembre-se: Acesso condicional é uma propriedade de seu diretório e não do Azure RemoteApp, portanto, toda a configuração é feita no nível do diretório. Isso também significa que você precisa ser o administrador de diretório para fazer essas alterações.

2.  Clique em **aplicativos**e clique em **Microsoft Azure RemoteApp** para configurar o acesso condicional. Observe que você pode configurar o acesso condicional para cada aplicativo "software como um serviço" no seu diretório separadamente.
![Configurar o acesso condicional para RemoteApp do Azure](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  Na guia **Configurar** , defina **Habilitar regras de acesso** para ativar.
![Habilitar as regras de acesso para RemoteApp do Azure](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  Agora você pode configurar várias regras e escolha quem aplicá-los para:

    1. Escolha **bloquear o acesso quando não estiver no trabalho** completamente impedir que usuários acessem Azure RemoteApp fora do ambiente de rede que você especificar.
    2. Clique na opção abaixo para definir os intervalos de endereços IP que constituem sua "rede confiável". Tudo fora aqueles será rejeitado.

5.  Teste sua configuração iniciando o cliente do Azure RemoteApp de um endereço IP fora do intervalo especificado. Depois que você entrar com suas credenciais do Azure AD você verá uma mensagem como esta:

![Acesso negado a RemoteApp do Azure](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Recursos de acesso condicional futuro 
A equipe do Active Directory do Azure está trabalhando em novos recursos no Access condicional. Os administradores poderão criar novos tipos de regras além de rede regras de localização com base. Uma visualização pública das novas funcionalidades deve estar disponível em breve.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Como monitorar acesso ao Azure RemoteApp
Um ótimo recurso para usar junto com acesso condicional é a funcionalidade de geração de relatórios do Azure Active Directory Premium. Você pode usar relatórios para monitorar quem está acessando o seu ambiente e detectar qualquer atividade suspeita.

Por exemplo, você pode ver os nomes dos usuários que acessado Azure RemoteApp, quantas vezes o fizeram e quando.

1.  No portal do Azure, clique em **Active Directory**e, em seguida, clique em seu diretório.

2.  Vá para a guia **relatórios** .

3.  Na lista de relatórios, selecione **uso do aplicativo** em **aplicativos integrados**.

    Você verá algumas estatísticas agregadas RemoteApp do Azure. 
![Estatísticas de acesso do agregado RemoteApp do Azure](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Clique no aplicativo para revelar informações sobre usuários acessando RemoteApp do Azure.
![Estatísticas de acesso do usuário para RemoteApp do Azure](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Resumo
Com o Azure Active Directory Premium, você pode configurar regras de acesso do Azure RemoteApp (e outros softwares como um aplicativos de serviço disponíveis por meio do Azure AD). Regras limitam-se atualmente às políticas de local com base de rede, mas serão estendidas no futuro para outros aspectos de gerenciamento corporativo.

Azure AD Premium também oferece relatórios e recursos que ampliar o controle ainda mais o administrador de monitoramento tem sobre seu ambiente do Azure RemoteApp.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>Como tornar-se de que meu recurso seguro é acessível somente a partir de meu ambiente Azure RemoteApp?
Nas seções anteriores deste artigo podemos voltada para proteger o acesso para o ambiente do Azure RemoteApp. Nós tiver realizado que escolhendo os usuários que têm permissão de acesso e configurando regras de acesso para controlar mais como eles podem usar o serviço.

Um cenário comum para implantações do Azure RemoteApp é que os aplicativos remotos precisam se comunicar com um recurso de back-end, por exemplo um banco de dados do SQL. Este recurso está hospedado locais (por exemplo, em uma rede corporativa) ou na nuvem (por exemplo, no Azure IaaS). Os administradores geralmente querem certificar-se de que o recurso de back-end só pode ser acessado por aplicativos implantados via RemoteApp do Azure e não por exemplo por um aplicativo de execução diretamente no computador de um usuário e acessando Internet pública. Azure RemoteApp geralmente é visto como o ambiente gerenciados centralmente e seguro e, portanto, somente o caminho pelo qual os usuários devem interagir com o recurso de back-end.

A solução é colocar o ambiente do Azure RemoteApp e o recurso seguro na mesma Azure Virtual rede (VNET). Se o recurso estiver em um site diferente, você pode estabelecer uma conexão de VPN-to-site, por exemplo para criar um VNet abrangendo o Centro de dados do Azure e o ambiente local do cliente.

RemoteApp Azure suporta dois tipos de implantações de conjunto onde você pode fornecer seu próprio VNET:

-   Não-domínio: os aplicativos terão "linha de visão" dos outros recursos na VNET. Por exemplo, isso pode ser usado para conectar aplicativos para um banco de dados do SQL que usa a autenticação do SQL (aplicativos autenticam o usuário diretamente no banco de dados)

-   Domínio: as máquinas virtuais usadas pelo RemoteApp Azure fazem parte de um controlador de domínio a VNET. Isso é útil quando os aplicativos precisam autenticar em um controlador de domínio do Windows para obter acesso a um recurso de back-end.
![Um conjunto de domínio no RemoteApp do Azure](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Como criar uma conexão segura entre Azure e meu ambiente local
Há várias opções de configuração para os ambientes do Azure e local de conexão. Uma boa visão geral das opções está disponível aqui.

Com o Azure RemoteApp, você precisa configurar sua VNet primeiro e usá-lo durante o processo de criação do seu conjunto. 

## <a name="the-complete-solution"></a>A solução completa
O diagrama a seguir mostra a solução completa onde criamos um canal de acesso seguro do usuário final, através do Azure RemoteApp (ARA), para o recurso de back-end.
![Proteger Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) no estágio 1 nós os usuários selecionados e criado regras de acesso que determinam como ARA pode ser acessado. No exemplo abaixo estamos só permitir acesso de usuários trabalhando da rede corporativa. Usuários sem conformidade não poderão acessar o ambiente de ARA todo.
Em "Estágio 2" podemos ter expostas o recurso de back-end somente por meio da configuração de VNet/VPN que estamos controle. Azure RemoteApp foi colocado no mesmo VNet. O resultado final é que o recurso só pode ser acessado através do ambiente ARA.


