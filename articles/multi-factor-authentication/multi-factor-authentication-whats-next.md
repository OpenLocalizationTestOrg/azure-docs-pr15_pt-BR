<properties
    pageTitle="Azure autenticação multifator - qual é a próxima"
    description="Esta é a página de autenticação multifator do Azure que descreva o que fazer em seguida com MFA.  Isso inclui relatórios, alerta de fraude, bypass única, mensagens de voz personalizada, cache, senhas de ips e aplicativo confiáveis."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Configurando a autenticação multifator Azure

Este artigo ajuda você a gerenciar a autenticação multifator Azure agora que você está em execução.  Ele cobre uma variedade de tópicos que ajudam você a aproveitar ao máximo o Azure multi-Factor Authentication.  Nem todos esses recursos estão disponíveis em cada versão do Azure multi-Factor Authentication.

A configuração para alguns dos recursos abaixo encontra-se no Portal de gerenciamento de autenticação multifator do Azure. Há duas maneiras diferentes que você pode acessar o portal de gerenciamento de MFA, que são feitas por meio do portal do Azure. A primeira é ao gerenciar um provedor de autenticação multifator se usando baseada em consumo MFA. O segundo é via as configurações de serviço MFA. A segunda opção requer um provedor de autenticação multifator ou uma licença do Azure MFA, o Azure AD Premium ou o pacote de mobilidade do Enterprise.

Para acessar o Portal de gerenciamento de MFA por meio de um provedor de autenticação multifator Azure, entrar no portal do Azure como administrador e selecione a opção Active Directory. Clique na guia de **Provedores de autenticação multifator** , selecione seu diretório e clique no botão **Gerenciar** na parte inferior.

Para acessar o Portal de gerenciamento de MFA por meio da página de configurações de serviço MFA, entrar no portal do Azure como administrador e selecione a opção Active Directory. Clique no seu diretório e clique na guia **Configurar** . Na seção autenticação multifator, selecione **Gerenciar configurações de serviço**. Na parte inferior da página Configurações de serviço de MFA, clique no link **acesse o portal** .


Recurso| Descrição| O que é coberto
:------------- | :------------- | :------------- |
[Alerta de fraude](#fraud-alert)|Alerta de fraude pode ser configurada e configurar para que os usuários podem reportar fraudulenta tenta acessar seus recursos.|Como configurar, configurar e relatar fraude
[Ignorar avulsa](#one-time-bypass) |Ignorar uma única permite que um usuário autenticar uma única vez "ignorando" autenticação multifator.|Como instalar e configurar uma única bypass
[Mensagens de voz personalizada](#custom-voice-messages) |Mensagens de voz personalizada permite que você use seu próprio gravações ou saudações com autenticação multifator. |Como instalar e configurar mensagens e saudações personalizadas
[Armazenamento em cache](#caching-in-azure-multi-factor-authentication)|Armazenamento em cache permite que você defina uma hora específica período para que as tentativas de autenticação subsequentes êxito automaticamente. |Como instalar e configurar o cache de autenticação.
[IPs confiáveis](#trusted-ips)|Confiáveis que IPS é um recurso de autenticação multifator que permite que os administradores de um locatário gerenciado ou federado a capacidade de ignorar a autenticação multifator para usuários que estão entrando da intranet local da empresa.|Configurar e os endereços IP que estão isentos para autenticação multifator
[Senhas de aplicativos](#app-passwords)|Uma senha de aplicativo permite que um aplicativo que não esteja ciente de MFA ignorar autenticação multifator e continuar a trabalhar.|Informações sobre senhas de aplicativos.
[Lembre-se de autenticação multifator para lembradas dispositivos e navegadores](#remember-multi-factor-authentication-for-devices-users-trust)|Permite que você se lembrar dispositivos para um número definido de dias depois de um usuário tiver conectado com êxito usando MFA.|Informações sobre como habilitar esse recurso e configurar o número de dias.
[Métodos de verificação selecionável](#selectable-verification-methods)|Permite que você escolha os métodos de autenticação que estão disponíveis para usuários utilizem.|Informações sobre como habilitar ou desabilitar métodos de autenticação específicos, como mensagens de texto ou de chamada.



## <a name="fraud-alert"></a>Alerta de fraude
Alerta de fraude pode ser configurada e configurar para que os usuários podem reportar fraudulenta tenta acessar seus recursos.  Os usuários podem relatar fraude com o aplicativo móvel ou por meio de seu telefone.

### <a name="to-set-up-and-configure-fraud-alert"></a>Para instalar e configurar o alerta de fraude

1.  Faça logon no http://azure.microsoft.com
2.  Navegue até o Portal de gerenciamento de MFA seguindo as instruções na parte superior desta página.
3.  O Portal de gerenciamento do Azure multifator autenticação, clique em configurações na seção Configurar.
4.  Na seção fraude alerta da página Configurações, verifique a permitir que usuários para enviar alertas de fraude de caixa de seleção.
5.  Se quiser que os usuários a ser bloqueados quando fraude é informada, coloque uma verificação em impedir que o usuário quando fraude é informada.
6.  Na caixa de texto de **Código para relatório fraude durante inicial de saudação** , insira um código numérico que pode ser usado durante a verificação de chamada. Se um usuário insere este código plus # em vez de apenas o sinal de #, em seguida, um alerta de fraude será informado.
7.  Na parte inferior, clique em Salvar.

>[AZURE.NOTE]
>Saudações de caixa padrão da Microsoft instruem os usuários a pressione 0# para enviar um alerta de fraude. Se você quiser usar um código diferente de 0, você deve gravar e carregar seus próprios saudações de caixa personalizada com instruções apropriadas.


![Nuvem](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Para o alerta de fraude de relatório
Alerta de fraude pode ser informada duas maneiras.  Tanto o aplicativo móvel ou através do telefone.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Para o alerta de fraude de relatório com o aplicativo móvel



1. Quando uma verificação é enviada para o seu telefone, selecione-o para iniciar o aplicativo Microsoft Authenticator.
2. Para relatar fraude, clique Cancelar e fraudes de relatório. Isto traz uma caixa que diz que a equipe de suporte de TI da sua organização será notificada.
3. Clique em fraudes de relatório.
4. No aplicativo, clique em Fechar.

![Nuvem](./media/multi-factor-authentication-whats-next/report1.png)


![Nuvem](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Para o alerta de fraude de relatório com o telefone

1. Quando uma chamada de verificação entra no telefone, responda a ele.  
2. Para relatar fraude, insira o código que foi configurado para corresponder à relatar fraude via telefone e, em seguida, o sinal #. Você será notificado de que um alerta de fraude foi enviado.
3. Encerre a chamada.

### <a name="to-view-the-fraud-report"></a>Para exibir o relatório de fraude

1. Faça logon no [http://azure.microsoft.com](https://azure.microsoft.com/)
2. À esquerda, selecione Active Directory.
3. Na parte superior, selecione provedores de autenticação multifator. Isto traz uma lista dos seus provedores de autenticação multifator.
4. Se você tiver mais de um provedor de autenticação multifator, selecione aquele que você deseja exibir o relatório de alerta de fraude e clique em gerenciar na parte inferior da página. Se você tiver apenas uma, clique em gerenciar. Isso abre o Portal de gerenciamento de autenticação multifator do Azure.
5. No Portal de gerenciamento do Azure multifator autenticação, à esquerda, em um relatório de modo de exibição, clique em alerta de fraude.
6. Especifique o intervalo de datas que você deseja exibir no relatório. Além disso, você pode especificar quaisquer nomes de usuários específicos, números de telefone e o status do usuário.
7. Clique em executar. Isto traz um relatório semelhante ao abaixo. Você também pode clicar em Exportar para CSV se desejar exportar o relatório.

## <a name="one-time-bypass"></a>Ignorar avulsa

Ignorar uma única permite que um usuário autenticar uma única vez "ignorando" autenticação multifator. O bypass é temporário e expira após o número especificado de segundos.  Para que situações em que o aplicativo móvel ou telefone não está recebendo uma notificação ou telefonema, você pode habilitar uma única bypass para que o usuário pode acessar o recurso desejado.

### <a name="to-create-a-one-time-bypass"></a>Para criar uma única bypass

1.  Faça logon no http://azure.microsoft.com
2.  Navegue até o Portal de gerenciamento de MFA seguindo as instruções na parte superior desta página.
3.  No Portal do Azure multifator autenticação gerenciamento, se você vir o nome do seu provedor de MFA do Azure ou seu locatário à esquerda com um + ao lado dela, clique na + ver diferentes grupos de replicação de servidor de MFA e o grupo padrão do Azure. Clique no grupo adequado.
4.  Em administração do usuário, clique em **Ignorar de uso único**.
![Nuvem](./media/multi-factor-authentication-whats-next/create1.png)
5.  Na página Bypass de uso único, clique em **Novo Bypass de uso único**.
6.  Insira o nome do usuário, o número de segundos que o bypass continuará a existir, o motivo para o bypass e clique em **Ignorar**.
![Nuvem](./media/multi-factor-authentication-whats-next/create2.png)
7.  Neste ponto, o usuário deve entrar antes que expire o bypass de uso único.



### <a name="to-view-the-one-time-bypass-report"></a>Para exibir o relatório de uso único bypass

1. Faça logon no [http://azure.microsoft.com](https://azure.microsoft.com/)
2. À esquerda, selecione Active Directory.
3. Na parte superior, selecione provedores de autenticação multifator. Isto traz uma lista dos seus provedores de autenticação multifator.
4. Se você tiver mais de um provedor de autenticação multifator, selecione aquele que você deseja exibir o relatório de alerta de fraude e clique em gerenciar na parte inferior da página. Se você tiver apenas uma, clique em gerenciar. Isso abre o Portal de gerenciamento de autenticação multifator do Azure.
5. No Portal de gerenciamento do Azure multifator autenticação, à esquerda, em um relatório de modo de exibição, clique em Ignorar de uso único.
6. Especifique o intervalo de datas que você deseja exibir no relatório. Além disso, você pode especificar quaisquer nomes de usuários específicos, números de telefone e o status do usuário.
7. Clique em executar. Isto traz um relatório semelhante ao abaixo. Você também pode clicar em Exportar para CSV se desejar exportar o relatório.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Mensagens de voz personalizada

Mensagens de voz personalizada permite que você use seu próprio gravações ou saudações com autenticação multifator.  Elas podem ser usadas além ou para substituir o Microsoft registros.

Antes de começar estar ciente do seguinte:

- Os formatos de arquivo suportados atual são. wav e MP3.
- O limite de tamanho de arquivo é de 5 MB.
- É recomendável que para mensagens de autenticação ele não ter mais de 20 segundos. Nada maior do que isso pode causar a verificação falha porque o usuário não pode responder antes de concluir a mensagem e a verificação atinge o tempo limite.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Configurar mensagens de voz personalizada em autenticação multifator do Azure
1.  Crie uma mensagem de voz personalizada usando um dos formatos de arquivo com suporte.
2.  Faça logon no http://azure.microsoft.com
3.  Navegue até o Portal de gerenciamento de MFA seguindo as instruções na parte superior desta página.
4.  No Portal de gerenciamento do Azure multifator autenticação, clique em mensagens de voz em seção Configurar.
5.  Sob a seção de mensagens de voz, clique em **Nova mensagem de voz**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom1.png)
6.  Em configurar: novas mensagens de voz página, clique em **Gerenciar arquivos de som**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom2.png)
7.  Em configurar: página arquivos de som, clique em **Carregar arquivo de som**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom3.png)
8.  Em configurar: carregar o arquivo de som, clique em **Procurar** e navegue até sua mensagem de voz, clique em **Abrir**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Adicionar uma descrição e clique em carregar.
10. Quando isso estiver concluído, uma mensagem confirma que você carregou o arquivo com êxito.
11. À esquerda, clique em mensagens de voz.
12. Sob a seção de mensagens de voz, clique em nova mensagem de voz.
13. Na lista suspensa idioma, selecione um idioma.
14. Se esta mensagem for para um aplicativo específico, especifique-o na caixa aplicativo.
15. Selecione o tipo de mensagem, o tipo de mensagem a ser substituído por nossa nova mensagem personalizada.
16. Na lista suspensa arquivo de som, selecione o arquivo de som.
17. Clique em **criar**. Uma mensagem confirma que você criou uma mensagem de voz com êxito.
![Nuvem](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>Cache no Azure autenticação multifator

Armazenamento em cache permite que você defina uma hora específica período para que as tentativas de autenticação subsequentes êxito automaticamente. Isso é usado principalmente quando sistemas locais como VPN enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Isso permite que as solicitações subsequentes tenha êxito automaticamente depois que o usuário é bem sucedida a verificação em andamento. Observe que o cache não se destina a ser usado para inscrições ao Azure AD.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Configurar o cache em autenticação multifator do Azure

1.  Faça logon no http://azure.microsoft.com
2.  Navegue até o Portal de gerenciamento de MFA seguindo as instruções na parte superior desta página.
3.  No Portal de gerenciamento do Azure multifator autenticação, clique em cache sob a seção de configurar.
4.  Sobre a configuração do cache de página, clique em novo Cache
5.  Selecione o tipo de Cache e os segundos de cache. Clique em criar.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IPs confiáveis

Confiáveis que IPS é um recurso de autenticação multifator que permite que os administradores de um locatário gerenciado ou federado a capacidade de ignorar a autenticação multifator para usuários que estão entrando da intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que tenham licenças Azure AD Premium, Enterprise mobilidade Suite ou autenticação multifator do Azure.


Tipo de locatário do Azure AD| Opções de IP confiáveis disponíveis
:------------- | :------------- |
Gerenciado|Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que podem ignorar a autenticação multifator para usuários que estão entrar a partir da intranet da empresa.
Federado|<li>Todos os usuários federados - todos os usuários federados que estão entrando-in de dentro da organização ignorará autenticação multifator usando uma declaração emitida pelo AD FS.</li><li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que podem ignorar a autenticação multifator para usuários que estão entrar a partir da intranet da empresa.

Isso ignorar funciona somente de dentro da intranet da empresa. Por exemplo, se você selecionou apenas todos os usuários federados e um usuário entra de fora da intranet da empresa, que o usuário tem que autenticar usando autenticação multifator, mesmo que o usuário apresenta uma declaração do AD FS. A tabela a seguir descreve quando senhas de autenticação e aplicativo multifatores são necessárias dentro de sua rede corporativa e fora da sua rede corporativa quando IPs confiáveis está habilitado.


|IPs habilitado confiáveis| IPs desabilitado confiáveis
:------------- | :------------- | :------------- |
Rede corporativa interna|Para fluxos de navegador, autenticação multifator não se necessário.|Para fluxos de navegador, autenticação multifator obrigatório
|Para aplicativos cliente avançado, senhas regulares funcionarão se o usuário não criou qualquer senhas de aplicativos. Depois que uma senha de aplicativo tiver sido criada, senhas de aplicativos são necessárias.|Para aplicativos cliente avançado, senhas de aplicativos obrigatórios
Rede corporativa externa|Para fluxos de navegador, autenticação multifator necessários.|Para fluxos de navegador, autenticação multifator necessários.
|Para aplicativos cliente avançado, senhas de aplicativos necessários.|Para aplicativos cliente avançado, senhas de aplicativos necessários.

### <a name="to-enable-trusted-ips"></a>Para habilitar o IPs confiáveis

1. Entrar no portal de clássico do Azure.
2. À esquerda, clique em Active Directory.
3. Em, diretório clique no diretório que você deseja configurar o IPsing confiáveis em.
4. No diretório que você selecionou, clique em configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações de serviço, em IPs confiáveis, selecione:

    - Para solicitações de federado usuários provenientes minha intranet – todos federado usuários que entrar da rede corporativa ignorará autenticação multifator usando uma declaração emitida pelo AD FS.
    - Para solicitações de um intervalo específico de IPs público – insira os endereços IP nas caixas fornecidas usando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para endereços IP no xxx.xxx.xxx.1 intervalo – xxx.xxx.xxx.254 ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Você pode inserir até 50 intervalos de endereços IP.

7. Clique em Salvar.
8. Depois que as atualizações foram aplicadas, clique em Fechar.



![IPs confiáveis](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Senhas de aplicativos

Em alguns aplicativos, como o Office 2010 ou mais antigo e o Apple Mail, você não pode usar autenticação multifator.  Para usar esses aplicativos, você precisará usar "senhas de aplicativo" no lugar de sua senha tradicional.  A senha do aplicativo permite que o aplicativo ignorar a autenticação multifator e continuar a trabalhar.

>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Agora, os clientes do Office 2013 (incluindo Outlook) novos protocolos de autenticação de suporte e podem ser habilitados para dar suporte a autenticação multifator.  Isso significa que, quando ativada, senhas de aplicativos não são necessárias para uso com clientes do Office 2013.  Para obter mais informações, consulte [demonstração de pública de autenticação moderno do Office 2013 anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### <a name="important-things-to-know-about-app-passwords"></a>Coisas importantes a saber sobre as senhas de aplicativo

A seguir é uma lista importante das coisas que você deve saber sobre senhas de aplicativos.

- Os usuários podem ter várias senhas de aplicativo, o que aumenta a área de superfície para roubo. Como senhas de aplicativos são difícil se lembrar, ele pode incentivar pessoas anotar isso. Isso não é recomendado e não é recomendável porque somente um fator é necessário para fazer logon com senha de aplicativo.
- Aplicativos que as senhas de cache e usá-lo em cenários de local poderão começar falhando desde que a senha do aplicativo não é conhecida fora a id organizacional. Um exemplo é emails do Exchange que estão no local, mas o email arquivado é na nuvem. A mesma senha não funciona.
- A senha real é gerada automaticamente e não for fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente, é mais difícil para um invasor a descoberta e é mais seguro.
- Atualmente não há um limite de 40 senhas por usuário. Você será solicitado a excluir uma de suas senhas de aplicativo existente para criar um novo.
- Quando a autenticação multifator é ativada em uma conta de usuário, senhas de aplicativo podem ser usadas com a maioria dos clientes não-localizador como o Outlook e o Lync, mas ações administrativas não podem ser executadas usando senhas de aplicativos por meio de aplicativos não-localizador como o Windows PowerShell, mesmo se esse usuário tem uma conta administrativa.  Certifique-se de que você cria uma conta de serviço com uma senha forte para executar scripts do PowerShell e não habilite essa conta para autenticação multifator.

>[AZURE.WARNING]  Senhas de aplicativos não funcionam em ambientes híbridos onde os clientes se comunicar com ambos os locais e na nuvem pontos de extremidade de descoberta automática. Isso ocorre porque as senhas de domínio são necessárias para autenticar local e senhas de aplicativos são necessários para autenticar com a nuvem.


### <a name="naming-guidance-for-app-passwords"></a>Diretrizes de nomenclatura para senhas de aplicativos
É recomendável que os nomes de senha de aplicativo devem refletir o dispositivo em que elas são usadas. Por exemplo, se você tiver um laptop com aplicativos de não-localizador como Outlook, Word e no Excel, você só precisa criar uma senha de aplicativo denominada Laptop e usar essa senha de aplicativo em todos esses aplicativos. Embora você possa criar senhas separadas para todos esses aplicativos, não é recomendável. A recomendar forma é usar uma senha de aplicativo por dispositivo.


<center>![Nuvem](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Senhas de aplicativos federados (SSO)
Azure AD dá suporte à Federação com local Windows Server Active Directory Domain Services (AD DS). Se sua organização está federated(SSO) com o Azure AD e você vai estar usando a autenticação multifator Azure, em seguida, o seguinte é informações importantes que você deve estar ciente ao usar senhas de aplicativo. Isso se aplica somente aos clientes de federated(SSO).

- A senha do aplicativo é verificada por Azure AD e, portanto, ignora federação. Federação somente ativamente é usada quando a configuração de senha de aplicativo.
- Para usuários de federated(SSO), nós nunca vá para o provedor de identidade (IdP) diferentemente o fluxo passivo. As senhas são armazenadas na id organizacional. Se o usuário sair da empresa, essas informações tem flua para id organizacional usando o DirSync em tempo real. Exclusão da desativar conta pode levar até três horas para sincronizar, atrasar desabilitar/exclusão de senha de aplicativo no Azure AD.
- Configurações de controle de acesso do cliente local não são observadas por senha de aplicativo
- Nenhuma autenticação local log / capacidade de auditoria está disponível para senha de aplicativo
- Mais treinamento do usuário final é necessário para o cliente do Microsoft Lync 2013. Para ver as etapas necessárias, veja como alterar a senha em seu email para a senha do aplicativo.
- Determinadas designs de arquiteturais avançados podem exigir usando uma combinação de organizacional nome de usuário e senhas e senhas de aplicativo usando a autenticação multifator com clientes, dependendo de onde elas se autenticar. Para clientes que autenticam uma infraestrutura no local, você usaria uma organizacional nome de usuário e senha. Para clientes que autenticam Azure AD, você usaria a senha do aplicativo.

Por exemplo, suponha que você tenha uma arquitetura que consiste no seguinte:

- Você está federando sua instância no local do Active Directory com o Azure AD
- Você está usando o Exchange online
- Você está usando o Lync que é especificamente no local
- Você está usando a autenticação multifator do Azure


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 Nesses casos, você deve fazer o seguinte:

- Quando entrar no Lync, use as suas organizações nome de usuário e senha.
- Ao tentar acessar o catálogo de endereços por meio de um cliente do Outlook que se conecte ao Exchange online, use uma senha de aplicativo.

### <a name="allowing-app-password-creation"></a>Permitir a criação de senha de aplicativo
Por padrão, os usuários não podem criar senhas de aplicativos.  Esse recurso deve ser ativado.  Para permitir que os usuários a capacidade de criar senhas de aplicativos, use o procedimento a seguir.

#### <a name="to-enable-users-to-create-app-passwords"></a>Para permitir que os usuários criem senhas de aplicativo



1. Entre portal do Azure clássico.
2. À esquerda, clique em Active Directory.
3. Em, diretório clique no diretório para o usuário que você deseja ativar.
4. Na parte superior, clique em usuários.
5. Na parte inferior da página, clique em Gerenciar multifator autenticação  
6. Na parte superior da página autenticação multifator, clique em configurações de serviço.
7. Verifique se o botão de opção ao lado de permitir que os usuários criem senhas de aplicativos para entrar em aplicativos de navegador não está selecionado.


![Criar senhas de aplicativo](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Criar senhas de aplicativo
Os usuários podem criar senhas de aplicativos durante o seu registro inicial.  Eles recebem uma opção no final do processo de registro que permite que eles criá-los.

Além disso usuários também podem criar senhas de aplicativos posteriormente alterando suas configurações no portal do Azure, o portal do Office 365 ou

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Criar senhas de aplicativos no portal do Office 365
--------------------------------------------------------------------------------


1. Entrar no portal do Office 365
2. No canto superior direito, selecione o widget de configurações
3. À esquerda, selecione verificação de segurança adicional
4. À direita, selecione **Atualizar meus números de telefone usados para segurança da conta**
5. Na página proofup, na parte superior, selecione senhas de aplicativos
6. Clique em **criar**
7. Insira um nome para a senha do aplicativo e clique em **Avançar**
8. Copiar a senha de aplicativo na área de transferência e colá-lo em seu aplicativo.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Criar senhas de aplicativo no portal do Azure
--------------------------------------------------------------------------------
1. Entre portal do Azure clássico.
3. Na parte superior, clique com botão direito no seu nome de usuário e selecione verificação de segurança adicional.
5. Na página proofup, na parte superior, selecione senhas de aplicativos
6. Clique em **criar**
7. Insira um nome para a senha do aplicativo e clique em **Avançar**
8. Copiar a senha de aplicativo na área de transferência e colá-lo em seu aplicativo.


![Senhas de aplicativos](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Criar senhas de aplicativo se você não tiver uma assinatura do Office 365 ou do Azure
--------------------------------------------------------------------------------
1. Entrar no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione o perfil.
3. Clique no seu nome de usuário e selecione verificação de segurança adicional.
5. Na página proofup, na parte superior, selecione senhas de aplicativos
6. Clique em **criar**
7. Insira um nome para a senha do aplicativo e clique em **Avançar**
8. Copiar a senha de aplicativo na área de transferência e colá-lo em seu aplicativo.

![Senhas de aplicativos](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Lembre-se de autenticação multifator para confiança de usuários de dispositivos

Lembre-se de autenticação multifator para dispositivos e navegadores que usuários confiabilidade é um recurso gratuito para todos os usuários MFA.  Ele permite conceder aos usuários a opção de desviar MFA para um número definido de dias depois de executar um bem-sucedido entrar usando MFA. Isso pode aprimorar a usabilidade para seus usuários.

No entanto, desde que os usuários tenham permissão para lembrar MFA dispositivos confiáveis, esse recurso pode reduzir a segurança da conta. Para garantir a segurança da conta, você deve restaurar a autenticação multifator para seus dispositivos para qualquer uma das seguintes situações:

- Se sua conta corporativa forem comprometida
- Se um dispositivo lembrado é perdido ou roubado

> [AZURE.NOTE] Esse recurso é implementado como um cache de cookie do navegador. Ele não funciona se seu navegador cookies não estão ativados.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Como ativar/desativar a autenticação multifator lembrar

1. Entre portal do Azure clássico.
2. À esquerda, clique em Active Directory.
3. Em Active Directory, clique na pasta que você deseja configurar lembrar multi-Factor Authentication para dispositivos.
4. No diretório que você selecionou, clique em configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações de serviço, em Gerenciar configurações de dispositivo do usuário, Selecionar/cancelar a seleção de **Permitir que os usuários a lembrar autenticação multifator em dispositivos que eles confiar**.
![Lembre-se de dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
8. Defina o número de dias que você deseja permitir a suspensão. O padrão é 14 dias.
9. Clique em Salvar.
10. Clique em Fechar.


## <a name="selectable-verification-methods"></a>Métodos de verificação selecionável
Em versões de nuvem tanto o local, você pode escolher quais métodos de verificação estão disponíveis para seus usuários. A tabela a seguir fornece uma breve visão geral de cada método.

Quando seus usuários registrar suas contas para MFA, eles escolhem seu verde fora as opções que você ativou. A orientação para o processo de inscrição é coberta de [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

Método|Descrição
:------------- | :------------- |
Ligar para telefone |  Coloca uma chamada de voz automatizada para o telefone de autenticação. O usuário responde a chamada e pressionar # no teclado telefônico para autenticar. Este número de telefone não está sincronizado com o Active Directory local.
Mensagem de texto para telefone | Envia uma mensagem de texto que contém um código de verificação para o usuário. O usuário será solicitado a qualquer resposta para a mensagem de texto com o código de verificação ou insira o código de verificação para a interface de entrada.
Notificação por meio do aplicativo móvel | Nesse modo, o aplicativo Microsoft Authenticator impede acesso não autorizado a contas e parar transações fraudulentas. Isso é feito usando uma notificação por push para o seu telefone ou dispositivo registrado. Exibir simplesmente a notificação e se ele for legítimo tocar verificar. Caso contrário, você pode escolha negar ou negar e relatar a notificação fraudulenta. Para obter informações sobre relatórios notificações fraudulentas veja como usar o recurso de fraude de relatório e negar para autenticação multifator.</br></br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Código de verificação do aplicativo móvel | Nesse modo, o aplicativo Microsoft Authenticator pode ser usado como um token de software para gerar um código de verificação de JURAMENTO. Este código de verificação, em seguida, pode ser inserido junto com o nome de usuário e senha para fornecer a segunda forma de autenticação.</li><br><p> O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Como ativar/desativar métodos de autenticação

1. Entre portal do Azure clássico.
2. À esquerda, clique em Active Directory.
3. Em Active Directory, clique na pasta que você deseja habilitar ou desabilitar métodos de autenticação.
4. No diretório que você selecionou, clique em configurar.
5. Na seção autenticação multifator, clique em Gerenciar configurações de serviço.
6. Na página Configurações de serviço, em Opções de verificação, selecione/desmarca as opções que deseja usar.</br></br>
![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Clique em Salvar.
10. Clique em Fechar.
