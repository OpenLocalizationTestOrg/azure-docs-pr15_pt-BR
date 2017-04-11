<properties
    pageTitle="Azure domínio serviços do Active Directory: Cenários de implantação | Microsoft Azure"
    description="Cenários de implantação para serviços de domínio do Azure AD"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>Cenários de implantação e casos de uso
Nesta seção, examinaremos alguns cenários e casos de uso que usufruir dos benefícios dos serviços de domínio do Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Administração de segura e fácil de Azure máquinas virtuais
Você pode usar o Azure Active Directory Domain Services para gerenciar suas máquinas virtuais Azure de maneira simplificada. Azure máquinas virtuais podem ser unidas ao domínio gerenciado, permitindo que você use credenciais corporativas AD para efetuar login. Essa abordagem ajuda a evitar aborrecimentos de gerenciamento de credencial como manter contas de administrador local em cada uma das suas máquinas virtuais Azure.

Máquinas virtuais do servidor que fazem parte de domínio gerenciado também pode ser gerenciadas e protegidas usando a política de grupo. Você pode aplicar linhas de base de segurança necessárias para suas máquinas virtuais Azure e bloqueá-las para baixo de acordo com as diretrizes de segurança corporativa. Por exemplo, você pode usar recursos de gerenciamento de política de grupo para restringir os tipos de aplicativos que podem ser iniciados nessas máquinas virtuais.

![Administração simplificada do Azure máquinas virtuais](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Como servidores e outra infraestrutura atinge o fim da vida, Contoso está movendo muitos aplicativos atualmente hospedados no local para a nuvem. Seu padrão IT atual exige que os servidores que hospedam aplicativos corporativos devem ser gerenciado usando a política de grupo e de domínio. Do Contoso administrador de TI prefere máquinas de virtuais de junção de domínio implantadas no Azure, para facilitar a administração. Como resultado, administradores e usuários podem efetuar logon usando suas credenciais corporativas. Ao mesmo tempo, máquinas podem ser configuradas de acordo com as linhas de base de segurança necessárias usando a política de grupo. Contoso preferir não ter para implantar, monitorar e gerenciar controladores de domínio no Azure proteger o Azure máquinas virtuais. Portanto, os serviços de domínio do Azure AD é uma excelente opção para esse caso de uso.

**Notas de implantação**

Considere os seguintes pontos importantes para esse cenário de implantação:

- Domínios gerenciados fornecidos pelos serviços de domínio do Azure AD fornecem uma única estrutura simples de OU (unidade organizacional) por padrão. Todas as máquinas domínio residem em uma única OU simples. No entanto, você pode optar por criar unidades organizacionais personalizados.

- Os serviços de domínio do Azure AD dá suporte a política de grupo simples na forma de um interno GPO cada para os usuários e computadores contêineres. Você não consegue GP por unidade Organizacional/departamento de destino, realizar a filtragem WMI ou criar GPOs personalizados.

- Os serviços de domínio do Azure AD é compatível com o esquema de objeto de computador base do AD. Você não pode estender o esquema do objeto computador.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Lift shift e um aplicativo de local que usa autenticação de ligação LDAP para serviços de infraestrutura do Azure

![Ligação LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

A Contoso tem um aplicativo de locais que foi comprado de um ISV há muitos anos. O aplicativo está atualmente no modo de manutenção pelo ISV e solicitar as alterações ao aplicativo é muito mais alto da Contoso. Este aplicativo tem um front-end baseado na web que coleta credenciais de usuário usando um formulário da web e, em seguida, autentica usuários executando uma ligação LDAP para o Active Directory corporativa. Contoso gostaria migrar este aplicativo para serviços de infraestrutura do Azure. É recomendável que o aplicativo funciona como está, sem exigir alterações. Além disso, os usuários devem poder se autenticar usando suas credenciais corporativas existentes e sem precisar treinar novamente os usuários para fazer coisas de forma diferente. Em outras palavras, os usuários finais deve ser isso de onde o aplicativo está sendo executado e a migração deve ser transparente a elas.

**Notas de implantação**

Considere os seguintes pontos importantes para esse cenário de implantação:

- Certifique-se de que o aplicativo não é necessário modificar/gravação ao diretório. Não há suporte para o acesso de gravação LDAP domínios gerenciados fornecidos pelos serviços de domínio do Azure AD.

- Você não pode alterar senhas diretamente em relação ao domínio gerenciado. Os usuários finais possam alterar sua senha ou usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou em relação ao diretório local. Essas alterações são automaticamente sincronizadas e disponível no domínio gerenciado.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Lift-shift e um aplicativo de local que usa LDAP leitura para acessar o diretório para serviços de infraestrutura do Azure
A Contoso tem um aplicativo de linha de negócios (LOB) no local que foi desenvolvido quase uma década. Este aplicativo é ciente de diretório e foi projetado para funcionar com o Windows Server AD. O aplicativo usa LDAP (Lightweight Directory Access Protocol) para ler/atributos de informação sobre usuários do Active Directory. O aplicativo não modificar atributos ou caso contrário gravar no diretório. Contoso gostaria migrar este aplicativo para serviços de infraestrutura do Azure e desativar o hardware de locais de vencimento hospedando no momento este aplicativo. O aplicativo não pode ser reescrito para usar diretório moderno APIs como a API do Azure AD Graph baseados em REST. Portanto, uma opção de shift e levantar é desejada por meio da qual o aplicativo pode ser migrado para executar na nuvem, sem modificar o código ou regravação do aplicativo.

**Notas de implantação**

Considere os seguintes pontos importantes para esse cenário de implantação:

- Certifique-se de que o aplicativo não é necessário modificar/gravação ao diretório. Não há suporte para o acesso de gravação LDAP domínios gerenciados fornecidos pelos serviços de domínio do Azure AD.

- Certifique-se de que o aplicativo não precisa de um esquema de Active Directory/estendido personalizado. Extensões de esquema não são suportadas nos serviços de domínio do Azure AD.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrar um aplicativo de serviço ou daemon de locais para serviços de infraestrutura do Azure
Alguns aplicativos consistem em vários níveis, onde uma das camadas precisa executar chamadas autenticadas para um nível de back-end como uma camada de banco de dados. Contas de serviço do Active Directory são usadas para esses casos de uso. Você pode levantar-shift e esses aplicativos para serviços de infraestrutura do Azure e o uso dos serviços de domínio Azure AD para as necessidades de identidade desses aplicativos. Você pode optar por usar a mesma conta de serviço que é sincronizada entre seu diretório local ao Azure AD. Como alternativa, você pode primeiro criar uma OU personalizada e, em seguida, criar uma conta de serviço separada dessa ou, para implantar esses aplicativos.

![Conta de serviço usando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

A Contoso tem um aplicativo de Cofre de software personalizados que inclui um web front-end, um SQL server e um servidor back-end FTP. Autenticação integrada do Windows de contas de serviço é usada para autenticar web front-end para o servidor FTP. Web front-end está configurado para executar como uma conta de serviço. O servidor back-end está configurado para autorizar o acesso da conta de serviço para web front-end. Contoso prefere não precisa implantar uma máquina do virtual do controlador de domínio na nuvem mover este aplicativo para serviços de infraestrutura do Azure. Do Contoso administrador de TI pode implantar os servidores web front-end, SQL server e o servidor FTP ao Azure máquinas virtuais de hospedagem. Essas máquinas depois fazem parte de um domínio de serviços de domínio do Azure AD gerenciado. Em seguida, eles podem usar a mesma conta de serviço no seu diretório local para fins de autenticação do aplicativo. Essa conta de serviço é sincronizada com o domínio gerenciado de serviços de domínio do Azure AD e está disponível para uso.

**Notas de implantação**

Considere os seguintes pontos importantes para esse cenário de implantação:

- Certifique-se de que o aplicativo usa o nome de usuário/senha para autenticação. Autenticação de certificado/cartão inteligente com base não é suportada pelos serviços de domínio do Azure AD.

- Você não pode alterar senhas diretamente em relação ao domínio gerenciado. Os usuários finais possam alterar sua senha ou usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou em relação ao diretório local. Essas alterações são automaticamente sincronizadas e disponível no domínio gerenciado.


## <a name="azure-remoteapp"></a>RemoteApp Azure
Azure RemoteApp permite que o administrador da Contoso criar um conjunto de domínio. Este recurso permite que aplicativos remotos servidos por Azure RemoteApp para executar em computadores de domínio e acessar outros recursos usando a autenticação integrada do Windows. Contoso pode usar os serviços de domínio do Azure AD para fornecer um domínio gerenciado usado pelo RemoteApp Azure conjuntos de domínio.

![RemoteApp Azure](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Para obter mais informações sobre esse cenário de implantação, consulte o artigo de Blog de serviços de área de trabalho remota intitulado [shift e levantar suas cargas de trabalho com RemoteApp do Azure e os serviços de domínio do Azure AD](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
