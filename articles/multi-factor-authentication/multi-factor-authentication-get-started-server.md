<properties 
    pageTitle="Guia de Introdução com o servidor de autenticação multifator do Azure"
    description="Esta é a página de autenticação multifator do Azure que descreve como começar com o servidor do Azure MFA."
    services="multi-factor-authentication"
    keywords="autenticação servidor, azure multi fator autenticação aplicativo página ativação, download de servidor de autenticação"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Guia de Introdução com o servidor de autenticação multifator do Azure




<center>![Nuvem](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Agora que podemos determinar se usar local autenticação multifator, vamos contínuo. Esta página aborda uma nova instalação do servidor e obtenção de instalação com o Active Directory local. Se você já tiver o servidor de PhoneFactor instalado e procura para atualizar, consulte [atualizar para o servidor do Azure multifator](multi-factor-authentication-get-started-server-upgrade.md) ou se você estiver procurando informações sobre como instalar apenas o serviço da web consulte [Implantando o serviço Azure multifator autenticação de servidor Mobile aplicativo Web](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Baixar o servidor do Azure autenticação multifator



Há duas maneiras diferentes que você pode baixar o servidor de autenticação multifator Azure. Ambos são feitos por meio do portal do Azure. A primeira é Gerenciando o provedor de autenticação multifator diretamente. O segundo é via as configurações de serviço. A segunda opção requer um provedor de autenticação multifator ou uma licença do Azure MFA, o Azure AD Premium ou o pacote de mobilidade do Enterprise.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Para baixar o servidor do Azure multi-Factor Authentication de portal do Azure
--------------------------------------------------------------------------------

1. Entre portal do Azure como administrador.
2. À esquerda, selecione Active Directory.
3. Na página do Active Directory, na parte superior, clique em **Provedores de autenticação multifator**
4. Na parte inferior, clique em **Gerenciar**
5. Isso abrirá uma nova página.  Clique em **Downloads.** 
 ![Baixar](./media/multi-factor-authentication-sdk/download.png)
6. Acima **Gerar credenciais de ativação**, clique em **Baixe.** 
 ![Baixar](./media/multi-factor-authentication-get-started-server/download4.png)
7. Salve o download.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Para baixar o servidor de autenticação multifator Azure via as configurações de serviço


1. Entre portal do Azure como administrador.
2. À esquerda, selecione Active Directory.
3. Clique duas vezes em sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
![baixar](./media/multi-factor-authentication-sdk/download2.png)
5. Em autenticação multifator selecione **Gerenciar configurações de serviço**
6. Na página de configurações de serviços, na parte inferior da tela, clique em **Ir para o portal**.
![Baixar](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Isso abrirá uma nova página. Clique em **Downloads.**
8. Acima **Gerar credenciais de ativação**, clique em **Baixe.**
9. Salve o download.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar e configurar o servidor do Azure autenticação multifator
Agora que você baixou o servidor, você pode instalar e configurá-lo.  Certifique-se de que o servidor em que está instalando-o atende aos seguintes requisitos:



Requisitos de servidor do Azure autenticação multifator|Descrição|
:------------- | :------------- |
Hardware|<li>200 MB de espaço de disco rígido</li><li>processador capaz de x32 ou x64</li><li>1 GB de RAM ou mais</li>
Software|<li>Windows Server 2008 ou maior se o host for um servidor de sistema operacional</li><li>Windows 7 ou superior se o host for um sistema operacional cliente</li><li>4.0 do Microsoft .NET Framework</li><li>SDK do IIS 7.0 ou superior se instalando o serviço de web ou portal do usuário</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisitos de firewall de servidor de autenticação multifator Azure
--------------------------------------------------------------------------------
Cada servidor MFA deve ser capaz de se comunicar na porta 443 saída para o seguinte:

- https://PFD.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Se firewalls de saída estão restrito na porta 443, os seguintes intervalos de endereços IP precisará ser aberto:

Sub-rede IP|Máscara de rede|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Se você não estiver usando os recursos de confirmação de evento do Azure multifator autenticação e se os usuários não estão autenticar com os aplicativos móveis autenticação multifator de dispositivos na rede corporativa IP intervalos podem ser reduzidos ao seguinte:


Sub-rede IP|Máscara de rede|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar e configurar o servidor do Azure multi-Factor Authentication
--------------------------------------------------------------------------------


1. Clique duas vezes no executável. Isso iniciará a instalação.
2. Na tela Selecionar pasta de instalação, certifique-se de que a pasta está correta e clique em Avançar.
3. Depois de concluir a instalação, clique em Concluir.  Isso iniciará o Assistente de configuração.
4. No Assistente de configuração de tela Bem-vindo, coloque uma seleção **Ignorar** usando o Assistente de configuração de autenticação e clique em **Avançar**.  Isso fechará o assistente e iniciar o servidor.
    ![Nuvem](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Volte à página que estamos baixados o servidor de, clique no botão **Gerar credenciais de ativação** .  Copie essas informações para o servidor de MFA Azure nas caixas fornecidas e clique em **Ativar**.


As etapas acima mostram uma instalação express com o Assistente de configuração.  Você pode executar novamente o Assistente de autenticação, selecionando-o no menu Ferramentas no servidor.



##<a name="import-users-from-active-directory"></a>Importar usuários do Active Directory

Agora que o servidor está instalado e configurado você pode importar rapidamente os usuários para o servidor de MFA do Azure.

### <a name="to-import-users-from-active-directory"></a>Para importar usuários do Active Directory
--------------------------------------------------------------------------------


1. No servidor do Azure MFA, à esquerda, selecione **usuários**.
2. Na parte inferior, selecione **Importar do Active Directory**.
3. Agora você pode pesquisar para usuários individuais ou pesquisar o diretório AD unidades organizacionais com usuários neles.  Nesse caso, estamos será especificar os usuários OU.
4. Realce todos os usuários à direita e clique em **Importar**.  Você deve receber um pop-up informando que você obteve êxito.  Feche a janela de importação.

![Nuvem](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Enviar um email de usuários
Agora que você importou seus usuários para o servidor de autenticação multifator de Azure, é recomendável que você enviar aos usuários um email informando que elas tiverem sido inscritos no autenticação multifator.

Com o servidor de autenticação multifator Azure, há várias maneiras de configurar seus usuários usando a autenticação multifator.  Por exemplo, se você souber os números de telefone dos usuários ou foram capaz de importar os números de telefone para o servidor de autenticação multifator Azure do diretório da sua empresa, o email avisará os usuários que eles foram configurados para usar autenticação multifator do Azure, fornecem algumas instruções sobre como usar a autenticação multifator do Azure e informe o usuário do número de telefone receberão seus autenticação em.  

O conteúdo do email variará dependendo do método de autenticação que foi definida para o usuário (por exemplo, telefonema, SMS, o aplicativo móvel).  Por exemplo, se o usuário é necessária para usar um PIN quando autenticam, o email informará-los o que seu PIN inicial foi definida como.  Os usuários geralmente são necessários para alterar seu PIN durante sua primeira autenticação.

Se os números de telefone dos usuários não foram configurados ou importados para o servidor de autenticação multifator Azure ou os usuários são pré-configurada para usar o aplicativo móvel para autenticação, você pode enviar um email que lhes permite saber que eles foram configurados para usar a autenticação multifator Azure e ele será direcioná-los para concluir seu registro de conta por meio do Portal do Azure multifator autenticação do usuário.  Um hiperlink será incluído que o usuário clica em para acessar o Portal de usuário. Quando o usuário clica no hiperlink, o seu navegador da web será aberto e levá-los portal da empresa Azure multifator autenticação do usuário.   


### <a name="configuring-email-and-email-templates"></a>Configurando email e modelos de email

Clicando no ícone de email à esquerda, você pode configurar as configurações para enviar esses emails.  Isso é onde você pode inserir as informações de SMTP do seu servidor de email e permite que você envie um email de ampla ampla adicionando uma seleção ao enviar emails a caixa de seleção de usuários.

![Configurações de email](./media/multi-factor-authentication-get-started-server/email1.png)

Na guia conteúdo do Email, você verá todos os vários modelos de email que estão disponíveis para escolher.  Portanto, dependendo de como você configurou seus usuários para usar a autenticação multifator, você pode escolher o modelo que melhor adequado para você.

![Modelos de email](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Como o servidor de autenticação multifator Azure lida com dados do usuário

Quando você usa o servidor de autenticação multifator (MFA) local, dados de um usuário são armazenados nos servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário realiza uma autenticação de dois fatores, o servidor de MFA envia dados para o serviço de nuvem do Azure MFA para realizar a autenticação. Quando essas solicitações de autenticação são enviadas ao serviço de nuvem, os campos a seguir são enviados na solicitação e logs para que fiquem disponíveis nos relatórios de uso/de autenticação do cliente. Alguns dos campos são opcionais para que pode ser habilitados ou desabilitados dentro do servidor de autenticação multifator. A comunicação do servidor MFA para o serviço de nuvem MFA usa SSL/TLS pela porta 443 saída. Esses campos são:

- ID exclusiva - nome de usuário ou identificação de servidor MFA interna
- Primeiro e último nome - opcional
- Endereço de email - opcional
- Número de telefone - ao fazer uma chamada de voz ou autenticação de SMS
- Símbolo de dispositivo - ao fazer a autenticação do aplicativo móvel
- Modo de autenticação
- Resultado de autenticação
- Nome do servidor MFA
- Servidor MFA IP
- Cliente IP – se disponível



Além dos campos acima, o resultado de autenticação (sucesso/negação) e o motivo para qualquer negação também é armazenado com os dados de autenticação e disponível por meio dos relatórios de uso de autenticação /.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Configurações de servidor do Azure autenticação multifator avançadas
Para obter informações adicionais sobre configuração avançada e informações de configuração, use a tabela abaixo.

Método|Descrição
:------------- | :------------- |
[Portal de usuário](multi-factor-authentication-get-started-portal.md)|  Informações sobre instalação e configurando o portal de usuário, incluindo a implantação e autoatendimento de usuário.
[Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md)|Informações sobre como configurar a autenticação multifator de Azure com o AD FS.
[Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informações sobre instalação e configuração do servidor de MFA Azure com RADIUS.
[Autenticação do IIS](multi-factor-authentication-get-started-server-iis.md)|Informações sobre instalação e configuração do servidor de MFA Azure com IIS.
[Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md)|  Informações sobre instalação e configuração do servidor de MFA Azure com autenticação do Windows.
[Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informações sobre instalação e configuração do servidor de MFA Azure com autenticação LDAP.
[O Gateway de área de trabalho e servidor de autenticação multifator Azure usando RADIUS remoto](multi-factor-authentication-get-started-server-rdg.md)|  Informações sobre instalação e configuração do servidor de MFA do Azure com o Gateway de área de trabalho remota usando o RADIUS.
[Sincronizar com o Active Directory do Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informações sobre instalação e configurando a sincronização entre o Active Directory e o servidor de MFA do Azure.
[Implantando o serviço Web de aplicativo móvel do Azure autenticação multifator servidor](multi-factor-authentication-get-started-server-webservice.md)|Informações sobre instalação e configurar o serviço do Azure MFA servidor da web.
