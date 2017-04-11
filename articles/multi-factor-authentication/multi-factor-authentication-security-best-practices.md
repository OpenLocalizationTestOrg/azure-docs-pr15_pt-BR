<properties 
    pageTitle="Práticas recomendadas de segurança para usar MFA do Azure"
    description="Este documento fornece práticas recomendadas sobre usando o Azure MFA com contas do Azure"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Práticas recomendadas de segurança para usar a autenticação multifator Azure com contas do Azure AD

Quando se trata de aprimorar o processo de autenticação, autenticação multifator é a opção preferida para a maioria das organizações. Autenticação de vários fatores Azure (MFA) permite que empresas atender aos requisitos de segurança e conformidade enquanto fornece uma experiência de entrada simple para seus usuários. Este artigo abordará algumas práticas recomendadas que você deve considerar ao planejar a adoção do Azure MFA.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Práticas recomendadas para a autenticação multifator de Azure na nuvem
Para fornecer todos os seus usuários com autenticação multifator e tirar vantagens dos recursos estendidas que oferece autenticação multifator do Azure, você precisa habilitar a autenticação de vários fatores do Azure em todos os seus usuários.  Isso é feito usando um destes procedimentos:

- Azure AD Premium ou o pacote de mobilidade do Enterprise
- Provedor de autenticação multifator

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Pacote de mobilidade do Azure AD Premium/Enterprise

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

A primeira etapa recomendada para adoção MFA Azure na nuvem usando o Azure AD Premium ou o pacote de mobilidade do Enterprise é atribuir licenças aos usuários.  Azure autenticação multifator faz parte desses pacotes e como tal sua organização não precise de nada adicionais para estender a capacidade de autenticação multifator para todos os usuários.

Quando a configuração de autenticação multifator considere seguintes:

- Você não precisa criar um provedor de autenticação multifator.  Azure AD Premium e o pacote de mobilidade do Enterprise vem com autenticação multifator do Azure.  Se você criar um provedor de Auth poderia chegar duplas cobrado.
- Azure AD Connect é apenas um requisito se você estiver sincronizando seu ambiente do Active Directory local com um diretório Azure AD. Se você usar apenas um diretório Azure AD que não está sincronizado com uma instância de local do Active Directory, você não precisam Azure AD Connect.


### <a name="multi-factor-auth-provider"></a>Provedor de autenticação multifator

![Provedor de autenticação multifator](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se você não tiver Azure AD Premium ou o pacote de mobilidade do Enterprise é a primeira etapa recomendada para adoção MFA Azure na nuvem criar um provedor de Auth MFA. Embora MFA está disponível por padrão para os administradores globais que têm o Active Directory do Azure, quando você estiver implantando MFA para sua organização, você precisa estender a capacidade de autenticação multifator para todos os usuários e fazer a que você precisa de um provedor de autenticação multifator.
Ao selecionar o provedor de autenticação, você precisa selecionar um diretório e considere o seguinte:

- Não é necessário um diretório Azure AD para criar um provedor de autenticação multifator.
- Você precisará associar o provedor de autenticação multifator um diretório do Azure AD se quiser estender a autenticação multifator para todos os seus usuários e/ou queira que os administradores globais sejam capazes de tirar proveito de recursos, como o portal de gerenciamento, saudações personalizadas e relatórios.
- DirSync ou AAD Sync são apenas um requisito se você estiver sincronizando seu ambiente do Active Directory local com um diretório Azure AD. Se você usar somente um diretório Azure AD que não está sincronizado com uma instância de local do Active Directory, você não precisa DirSync ou AAD Sync.
- Se você tiver Azure AD Premium ou o pacote de mobilidade de empresa, você não precisa criar um provedor de autenticação multifator. Você só precisa atribuir uma licença a um usuário e, em seguida, você pode começar a como ativar MFA para os usuários.
- Certifique-se de escolher o modelo de uso correto para sua empresa (por auth ou por usuário habilitado), depois que você selecionar o modelo de uso não é possível alterá-lo.

### <a name="user-account"></a>Conta de usuário
Ao ativar o MFA para seus usuários, contas de usuário podem estar em um de três estados de núcleo: desativada, habilitado ou impostas.
Use as diretrizes abaixo para garantir que você está usando a opção mais adequada para sua implantação:

- Quando o estado do usuário for definido como desabilitado, esse usuário não está usando a autenticação multifator. Este é o estado padrão.
- Quando o estado do usuário é definido como habilitado, isso significa que o usuário está habilitado, mas não concluiu o processo de registro. Ele será solicitado para concluir o processo ao entrar próximo. Essa configuração não afeta aplicativos de navegador não. Todos os aplicativos vai continuar a funcionar até que o processo de registro seja concluído.
- Quando o estado do usuário é definido como imposto, significa que o usuário pode ou não tenha concluído registro. Se ele tem concluído o processo de registro que eles estão usando autenticação multifator. Caso contrário, o usuário será solicitado a concluir o processo de registro ao entrar próximo. Esta configuração afeta o navegador não aplicativos. Esses aplicativos não funcionará até que as senhas de aplicativos são criadas e usadas.
- Use o modelo de notificação do usuário disponíveis no artigo [Introdução ao Azure multi-Factor Authentication na nuvem](multi-factor-authentication-get-started-cloud.md) para enviar um email aos seus usuários sobre adoção de MFA.

### <a name="supportability"></a>Suporte

Como a maioria dos usuários está acostumado a usar somente senhas para autenticar, é importante que sua empresa trazer percepção para todos os usuários sobre esse processo. Esse reconhecimento pode reduzir a probabilidade de usuários contatar o suporte técnico para pequenos problemas relacionados a MFA.
No entanto, há alguns cenários onde desativar temporariamente MFA é necessário. Use as diretrizes abaixo para entender como lidar com esses cenários:

- Certificar-se de que sua equipe de suporte técnico é treinamento a alça de cenários em que o aplicativo móvel ou telefone não está recebendo uma notificação ou telefonema e por esse motivo que o usuário é possível entrar. Eles podem habilitar uma opção de ignorar avulsa para permitir que um usuário autenticar uma única vez "ignorando" autenticação multifator. O bypass é temporário e expira após o número especificado de segundos.
- Se necessário, você pode aproveitar o recurso de IPs confiáveis no Azure MFA. Esse recurso permite que os administradores de um locatário gerenciado ou federado ignorar a autenticação multifator para usuários que estão entrando da intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que tenham licenças Azure AD Premium, Enterprise mobilidade Suite ou autenticação multifator do Azure.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Práticas recomendadas para o Azure autenticação multifator local
Se sua empresa decidiu aproveitar sua própria infraestrutura para habilitar MFA, será necessário implantar um servidor de autenticação multifator Azure local. Os componentes do servidor de MFA são mostrados no diagrama abaixo:

![Provedor de autenticação multifator](./media/multi-factor-authentication-security-best-practices/server.png)
*não instalado por padrão ** instalado mas não é ativado por padrão


Servidor de autenticação multifator Azure pode ser usado para proteger recursos de nuvem e no local que são acessados por contas do Azure AD.  No entanto isso só pode feito usando federação.  Isto é, você deve ter o AD FS e tê-la federado com seu locatário do Azure AD.
Quando a configuração de servidor de autenticação multifator considere seguintes:

- Se você estiver protegendo recursos de Azure AD usando serviços de Federação do Active Directory, e em seguida, o fator de 1º de autenticação é executado local usam AD FS e fator de 2a é local realizado por aceitar a declaração.
- Não é um requisito que o servidor de autenticação multifator Azure ser instalada no seu servidor de Federação do AD FS, mas o adaptador de autenticação multifator do AD FS deve ser instalado em um Windows Server 2012 R2 em execução do AD FS. Você pode instalar o servidor em um computador diferente, desde que é uma versão compatível e instale o adaptador do AD FS separadamente no seu servidor de Federação do AD FS. Consulte o procedimento abaixo para obter instruções sobre como instalar o adaptador separadamente.
- O Assistente de instalação de autenticação multifator AD FS adaptador cria um grupo de segurança chamado PhoneFactor Admins no Active Directory e adiciona a conta de serviço do seu serviço de Federação do AD FS a esse grupo. É recomendável que você verifique em seu controlador de domínio que o grupo PhoneFactor Admins realmente é criado e que a conta de serviço do AD FS é um membro desse grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no controlador de domínio.

### <a name="user-portal"></a>Portal de usuário
Este portal é executado em um site de servidor de informações da Internet (IIS), que permite capacidades de autoatendimento e fornece um conjunto completo de recursos de administração do usuário. Use as diretrizes abaixo para configurar este componente:

- IIS 6 ou superior é necessário
- ASP.NET v2.0.507207 precisa ser instalado e registrado
- Este servidor pode ser implantado em uma rede de perímetro.



### <a name="app-passwords"></a>Senhas de aplicativos
Se sua organização federada usando o SSO com o Azure AD e você vai usar Azure MFA, em seguida, lembre-se dos seguintes ao usar senhas de aplicativo (Lembre-se de que isso se aplica somente federado (SSO) é usada):

- A senha do aplicativo é verificada por Azure AD e, portanto, ignora federação. Federação é usada somente quando a configuração de senha de aplicativo.
- Para federados (SSO) senhas de usuários serão armazenadas na id organizacional. Se o usuário sair da empresa, essas informações tem flua para id organizacional usando o DirSync em tempo real. Exclusão da desativar conta pode levar até 3 horas para sincronizar, atrasar desabilitar/exclusão de senha de aplicativo no Azure AD.
- Configurações de controle de acesso do cliente local não são observadas por senha de aplicativo
- Nenhuma autenticação local log / capacidade de auditoria está disponível para senha de aplicativo
- Mais treinamento do usuário final é necessário para o cliente do Microsoft Lync 2013.
- Determinadas designs de arquiteturais avançados podem exigir usando uma combinação de organizacional nome de usuário e senhas e senhas de aplicativo usando a autenticação multifator com clientes, dependendo de onde elas se autenticar. Para clientes que autenticam uma infraestrutura de locais, você usaria uma organizacional nome de usuário e senha. Para clientes que autenticam Azure AD, você usaria a senha do aplicativo.
- Por padrão, os usuários não podem criar senhas de aplicativos, se sua empresa requer que ou se é preciso permitir que os usuários criem senha de aplicativo em alguns cenários, certifique-se de que a opção Permitir que os usuários criar senhas de aplicativo entrar em aplicativos de navegador não está selecionada.

## <a name="additional-considerations"></a>Considerações adicionais
Use a lista abaixo para entender algumas considerações adicionais e práticas recomendadas para cada componente que será implantada no local:

Método|Descrição
:------------- | :------------- |
[Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md)|Informações sobre como configurar a autenticação multifator de Azure com o AD FS.
[Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informações sobre instalação e configuração do servidor de MFA Azure com RADIUS.
[Autenticação do IIS](multi-factor-authentication-get-started-server-iis.md)|Informações sobre instalação e configuração do servidor de MFA Azure com IIS.
[Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md)|  Informações sobre instalação e configuração do servidor de MFA Azure com autenticação do Windows.
[Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informações sobre instalação e configuração do servidor de MFA Azure com autenticação LDAP.
[O Gateway de área de trabalho e servidor de autenticação multifator Azure usando RADIUS remoto](multi-factor-authentication-get-started-server-rdg.md)|  Informações sobre instalação e configuração do servidor de MFA do Azure com o Gateway de área de trabalho remota usando o RADIUS.
[Sincronizar com o Active Directory do Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informações sobre instalação e configurando a sincronização entre o Active Directory e o servidor de MFA do Azure.
[Implantando o serviço Web de aplicativo móvel do Azure autenticação multifator servidor](multi-factor-authentication-get-started-server-webservice.md)|Informações sobre instalação e configurar o serviço do Azure MFA servidor da web.
[Configuração de VPN avançadas com a autenticação multifator Azure](multi-factor-authentication-advanced-vpn-configurations.md)|Informações sobre como configurar dispositivos Cisco ASA, Citrix Netscaler e Juniper/Pulse seguro VPN usando LDAP ou RADIUS.


## <a name="additional-resources"></a>Recursos adicionais
Embora este artigo destaca algumas práticas recomendadas para MFA do Azure, há outros recursos que você também pode usar ao planejar a sua implantação MFA. A lista a seguir tem alguns artigos principais que podem ajudá-lo durante este processo:

- [Relatórios no Azure autenticação multifator](multi-factor-authentication-manage-reports.md)
- [Experiência de configuração para autenticação multifator do Azure](multi-factor-authentication-end-user-first-time.md)
- [Perguntas Frequentes do Azure autenticação multifator](multi-factor-authentication-faq.md)
