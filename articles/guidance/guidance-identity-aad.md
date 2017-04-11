<properties
   pageTitle="Implementação do Active Directory do Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede híbrido seguro usando o Active Directory do Azure."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Implementação do Active Directory do Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para integrar o local do Active Directory (AD) domínios e florestas Azure Active Directory para fornecer autenticação de identidade baseado em nuvem.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Você pode usar os serviços de diretório e identidade, como aqueles fornecidos pelo diretório AD Services (AD DS) para autenticar identidades. Essas identidades podem pertencer a usuários, computadores, aplicativos ou outros recursos que fazem parte de um limite de segurança. Você pode hospedar o diretório e local de serviços de identidade, mas em um cenário de híbrido onde os elementos de um aplicativo estão localizados no Azure pode ser mais eficiente estender esse recurso para a nuvem. Essa abordagem pode ajudar a reduzir a latência causada enviando autenticação e solicitações de autorização local da nuvem de volta para os serviços de diretório e identidade em execução no local.

Azure fornece duas soluções para implementar serviços de diretório e identidade na nuvem:

1. Você pode usar o [Azure Active Directory (AAD)] [ azure-active-directory] para criar um domínio AD na nuvem e vinculá-lo a um local domínio do AD. AAD permite que você configurar o logon único (SSO) para usuários que executam aplicativos acessados por meio da nuvem. AAD utiliza [Azure AD Connect] [ azure-ad-connect] executar o local para replicar identidades e objetos contidos no repositório local para a nuvem.

    Você também pode implementar AAD sem usar um diretório local. Nesse caso, AAD atua como a principal fonte de todas as informações de identidade em vez que contêm dados duplicados de um diretório local.

    Observe que o diretório na nuvem é **não** uma extensão de um diretório local. Em vez disso, é uma cópia que contém os mesmos objetos e identidades. As alterações feitas para esses itens local são copiadas para a nuvem, mas as alterações feitas de nuvem **não são** ser replicados para o domínio local.

    >[AZURE.NOTE] As edições do Azure Active Directory Premium permitem regravação de senhas dos usuários, permitindo que os usuários locais realizar redefinições de senha de autoatendimento na nuvem. Esta é a única informação que AAD sincroniza novamente para o diretório local. Para obter mais informações sobre as diferentes edições AAD e seus recursos, consulte [Azure Active Directory edições][aad-editions].

2. Você pode implantar uma máquina virtual com o AD DS como um controlador de domínio no Azure, ampliando sua infraestrutura do AD existente (incluindo AD DS e do AD FS) do seu centro de dados local. Essa abordagem é mais comum para cenários onde a rede local e uma rede virtual Azure estão conectados por uma conexão VPN e/ou rota expressa. Essa solução também suporta bidirecional replicação permitindo que você faça alterações na nuvem e no local, onde ele é mais apropriado.

Essa arquitetura se concentra na solução 1. Para obter mais informações sobre a segunda solução, consulte [Estendendo Active Directory do Azure][guidance-adds].

Casos de uso típico para essa arquitetura incluem:

- Fornecendo SSO para os usuários finais executando SaaS e outros aplicativos na nuvem, usando as mesmas credenciais que eles especificarem para aplicativos de local.

- Publicação local aplicativos web por meio de nuvem para fornecer acesso aos usuários remotos que pertencem à sua organização.

- Implementando capacidades de autoatendimento para usuários finais, como redefinir suas senhas e delegando o gerenciamento de grupo. 

    >[AZURE.NOTE] Esses recursos podem ser controlados por um administrador por meio de política de grupo.

- Situações em que a rede local e o Azure rede virtual hospedando aplicativos de nuvem não são diretamente vinculados usando um túnel VPN ou rota expressa circuito.

Você deve observar que AAD não fornece todas as funcionalidades do AD. Por exemplo, AAD atualmente apenas lida com autenticação do usuário, em vez da autenticação do computador. Alguns aplicativos e serviços, como o SQL Server, podem exigir autenticação do computador caso em que esta solução não for apropriada. Além disso, AAD talvez não seja adequado para sistemas onde os componentes podem migrar através da fronteira no local/nuvem em vez de apenas sendo copiada.

> [AZURE.NOTE] Para obter uma explicação detalhada do funcionamento do Active Directory do Azure, assista a [Microsoft Active Directory explicadas][aad-explained].

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura. Para obter mais informações sobre os elementos de carga de trabalho no Azure, leia [VMs executando uma arquitetura de N camadas no Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Para simplificar, este diagrama só mostra as conexões diretamente relacionadas à AAD e não menciona redirecionamentos de solicitação do navegador da web ou outro tráfego relacionado que pode ocorrer como parte do processo de federação de autenticação e identidade de protocolo. Por exemplo, um usuário (local ou remoto) normalmente acessa um aplicativo web através de um navegador e o aplicativo web transparente pode redirecionar o navegador da web para autenticar a solicitação através de AAD. Uma vez autenticado, a solicitação pode ser passada volte ao aplicativo web junto com as informações de identidade apropriado.

[! [0]][0]

- **Locatário do Active Directory do azure**. Essa é uma instância de AAD criado pela sua organização. Ele atua como um serviço de diretório simples para aplicativos em nuvem (ele contém objetos copiados do local AD) e fornece serviços de identidade.

- **Sub-rede de nível da web**. Esta sub-rede detém VMs que implementam um aplicativo baseado em nuvem personalizado desenvolvido pela sua organização e para qual AAD pode atuar como um agente de identidade.

- **Local servidor AD DS**. Sua organização provavelmente já tem um serviço de diretório existente como o AD DS. Você pode sincronizar muitos dos itens no seu diretório de AD DS (como informações usuário e grupo) com AAD, habilitar AAD usar essas informações para autenticar identidades.

- **Servidor de sincronização do azure AD Connect**. Este é um computador local que executa o serviço de sincronização do Azure AD Connect. Você pode instalar esse serviço usando o software do Azure AD Connect. O serviço de sincronização do Azure AD Connect sincroniza informações (usuários, grupos, contatos, etc.) mantidas no local AD para AAD na nuvem. Por exemplo, você pode provisionar ou desprovisionamento grupos e usuários locais e essas alterações são propagadas para AAD. O serviço de sincronização do Azure AD Connect passa informações para o locatário AAD.

    >[AZURE.NOTE] Por razões de segurança, senhas do usuário não são armazenadas diretamente no AAD. Em vez disso, AAD contém um hash cada senha. Isso é suficiente para verificar a senha de um usuário. Se um usuário exigir uma redefinição de senha, deve ser realizado no local e o novo hash enviado para AAD. Edições Premium AAD incluem recursos que podem automatizar essa tarefa para permitir que usuários redefinir suas próprias senhas.

## <a name="recommendations"></a>Recomendações

Esta seção resume recomendações para implementar AAD da seguinte maneira.

- AD Connect
- Segurança

### <a name="azure-ad-connect-sync-service-recommendations"></a>Recomendações de serviço do Azure AD Connect sincronização

Sincronização trata garantindo que informações de identidade de usuários na nuvem sejam consistentes com que mantidos no local. A finalidade do serviço de sincronização do Azure AD Connect é manter essa consistência. Os pontos a seguir resumem recomendações para implementar o serviço de sincronização do Azure AD Connect:

- Antes de implementar a sincronização do Azure AD Connect, você deve determinar os requisitos de sincronização de sua organização (o que sincronizar, de quais domínios e frequência. O artigo [Determinar requisitos de sincronização de diretório] [ aad-sync-requirements] descreve os pontos que você deve considerar.

- Você pode executar o serviço de sincronização do Azure AD Connect usando uma máquina virtual ou um computador hospedado no local. Dependendo do volatilidade das informações no seu diretório AD, depois de realizar a sincronização inicial com AAD a carga o serviço de sincronização do Azure AD Connect é provavelmente não ser alto. Usar uma máquina virtual permite que você mais facilmente dimensionar o servidor (monitorar a atividade conforme descrito na seção [Considerações de monitoramento](#monitoring-considerations) para determinar se isso é necessário).

- Se você tiver vários domínios de locais em uma floresta, você pode armazenar e sincronizar as informações para toda a floresta para um único locatário AAD (essa é a abordagem recomendada). Filtre informações para identidades que ocorrem em mais de um domínio para que cada identidade aparece apenas uma vez no AAD em vez de sendo duplicado como isso pode levar a inconsistências quando os dados são sincronizados. Essa abordagem requer implementar vários servidores de sincronização do Azure AD Connect. Para obter mais informações, consulte o cenário de vários AAD na seção [Considerações de topologia](#topology-considerations) . 

- Use a filtragem para limitar os dados armazenados em AAD ao que é necessários. Por exemplo, sua organização talvez não queira armazenar informações sobre contas inativas ou não pessoais no AAD. Filtragem pode ser baseadas em grupo, baseada em domínio, baseada em unidade Organizacional ou baseado no atributo, e você pode combinar filtros para gerar regras mais complexas. Por exemplo, você pode selecionar para sincronizar somente objetos contidos em um domínio que têm um valor específico em um atributo selecionado. Para obter informações detalhadas, consulte [sincronização do Azure AD Connect: configurar a filtragem][aad-filtering].

- Para implementar alta disponibilidade para o serviço de sincronização do AD Connect, execute um servidor de teste secundário. Para obter mais informações, consulte a seção [Considerações de topologia](#topology-considerations) .

### <a name="security-recommendations"></a>Recomendações de segurança

Os itens a seguir resumem as recomendações de segurança principal para implementar AAD, dependendo dos requisitos da sua organização:

- Gerenciando senhas dos usuários.

    Se você estiver usando uma edição premium do AAD, você pode ativar senha regravação de AAD ao seu diretório local executando uma instalação personalizada do Azure AD Connect:

    [! [9]][9]

    Esse recurso permite que os usuários redefinir suas próprias senhas de dentro do portal Azure, mas só deve ser habilitado após examinar a política de segurança de senha da sua organização. Por exemplo, você pode restringir quais usuários podem alterar suas senhas, e você pode personalizar a experiência de gerenciamento de senha. Para obter mais informações, consulte [Personalizar o gerenciamento de senha para atender às necessidades da sua organização][aad-password-management].

- Manter a proteção para aplicativos de local que possa ser acessado externamente.

    Use o Proxy de aplicativo Azure AD para fornecer acesso controlado aos aplicativos do local da web de usuários externos através de AAD. Essa abordagem protege seus aplicativos não expô-los diretamente à Internet. Somente os usuários que têm credenciais válidas no seu diretório Azure são capazes de acessar seus aplicativos. Para obter mais informações, consulte o artigo [Habilitar o Proxy de aplicativo no portal do Azure][aad-application-proxy].

- Monitorando ativamente AAD sinais de atividade suspeita.

    Considere usar AAD Premium P2 edition, que inclui proteção de identidade AAD. Proteção de identidade usa algoritmos de aprendizado de máquina adaptativa e heurística para detectar problemas e riscos eventos que podem indicar que uma identidade foi comprometida. Por exemplo, ele pode detectar atividade potencialmente anormal como irregulares entrar atividades, suplementos de sinal de fontes desconhecidas ou de endereços IP com atividades suspeitas ou suplementos de entrada de dispositivos talvez seja preferível. Usando esses dados, proteção de identidade gera relatórios e alertas que permite investigar esses eventos de risco e execute a ação de atenuação ou correção apropriado. Para obter mais informações, consulte [Azure Active Directory proteção de identidade][aad-identity-protection].

    Você também pode usar o recurso de relatório do AAD no portal do Azure para monitorar suspeitas e outras atividades relacionadas à segurança ocorrendo dentro do seu sistema. AAD pode gerar uma série de relatórios de resumo:

    [! [17]][17]

    Para obter mais informações sobre como usar esses relatórios, consulte [Azure Active Directory Reporting Guide][aad-reporting-guide].

## <a name="topology-considerations"></a>Considerações de topologia

Se você estiver integrar um diretório local com AAD, configure Azure AD Connect para implementar uma topologia que mais se aproxime os requisitos da sua organização. Topologias que Azure AD Connect suporte incluem o seguinte:

- **Única floresta, único diretório AAD**. Nesta topologia, use Azure AD Connect para sincronizar objetos e informações de identidade em um ou mais domínios em uma única floresta com um único locatário AAD local. Esta é a topologia de padrão implementada pela instalação expressa do Azure AD Connect.

    [! [1]][1]

    Não criar vários servidores de sincronização do Azure AD Connect para conectar domínios diferentes na mesma floresta local ao mesmo locatário AAD, a menos que você está executando um servidor de sincronização do Azure AD Connect no modo de teste (consulte preparar Server abaixo).

- **Várias florestas, único diretório AAD**. Use essa topologia se você tiver mais de uma floresta local. Você pode consolidar informações de identidade para que cada usuário único é representado uma vez no diretório AAD, mesmo se o mesmo usuário existe em mais de uma floresta. Todas as florestas usam o mesmo servidor de sincronização do Azure AD Connect. O servidor de sincronização do Azure AD Connect não precisa fazer parte de qualquer domínio, mas ele deve ser acessível de todas as florestas:

    [! [2]][2]

    Nesta topologia, não use separada Azure AD Connect sincronizar servidores para se conectar a cada floresta local para um único locatário AAD. Isso pode resultar em informações de identidade duplicado na AAD se os usuários estão presentes em mais de uma floresta.

- **Várias florestas, topologias separadas**. Essa abordagem permite mesclar informações de identidade de florestas separadas em um único locatário AAD. Essa estratégia é útil se você combina florestas de organizações diferentes (após uma fusão ou aquisição, por exemplo), e as informações de identidade para cada usuário são mantidas em apenas uma floresta:

    Se o GALs em cada floresta estiverem sincronizadas, um usuário em uma floresta pode estar presente em outro como um contato. Isso pode ocorrer se, por exemplo, sua organização tiver implementado GALSync com o Gerenciador de identidades do Forefront 2010 ou Gerenciador de identidades do Microsoft 2016. Neste cenário, você pode especificar que os usuários devem ser identificados por seu atributo *Mail* . Você também pode fazer a correspondência de identidades usando os atributos *ObjectSID* e *msExchMasterAccountSID* . Isso é útil se você tiver uma ou mais florestas de recurso com contas desativadas.

- **Servidor de teste**. Nessa configuração, você pode executar uma segunda instância do servidor de sincronização do Azure AD Connect em paralelo com o primeiro. Essa estrutura oferece suporte a cenários, tais como:

    - Alta disponibilidade.

    - Testar e implantar uma nova configuração do servidor de sincronização do Azure AD Connect.

    - Apresentando um novo servidor e encerrando uma configuração antiga. 

    Nesses cenários, a segunda instância é executado em *modo de teste*. O servidor registra objetos importados e dados de sincronização em seu banco de dados, mas não passar os dados para AAD. Somente quando você desativar o modo de preparação servidor iniciar gravar dados AAD e também inicia senha desempenho gravação-faz nos diretórios local onde apropriado:

    [! [4]][4]

    Para obter mais informações, consulte [sincronização do Azure AD Connect: tarefas operacionais e considerações][aad-connect-sync-operational-tasks].

- **AAD vários diretórios**. É recomendável que você criar um único diretório AAD para uma organização, mas pode haver situações em que você precisa informações de partição em diretórios AAD separados. Nesse caso, você deve garantir que cada objeto da floresta local aparece apenas em uma pasta AAD, para evitar problemas de sincronização e senha de write-back.

    Para implementar este cenário, configurar separada Azure AD Connect sincronizar servidores para cada diretório AAD e usar a filtragem para cada servidor de sincronização do Azure AD Connect opera em um conjunto mutuamente exclusivo de objetos: 

    [! [5]][5]

Para obter mais informações sobre essas topologias, consulte [topologias para Azure AD Connect][aad-topologies].

## <a name="user-sign-in-considerations"></a>Considerações de entrada do usuário

Por padrão, o serviço AAD pressupõe que os usuários fazem logon, fornecendo a mesma senha que usam locais e o servidor de sincronização do Azure AD Connect configura a sincronização de senha entre o domínio local e AAD. Para muitas organizações, isso é apropriado, mas você deve considerar as diretivas existentes e a infraestrutura de sua organização. Por exemplo:

- A política de segurança de sua organização pode proibir sincronizando hash de senha para a nuvem.

- Você pode exigir que os usuários experiência perfeita SSO (sem prompts de senha adicional) quando acessar os recursos de nuvem do domínio ingressou máquinas na rede corporativa.

- Sua organização já pode ter ADFS ou em um provedor de federação de festa implantado. Você pode configurar AAD para usar este infraestrutura para implementar autenticação e SSO em vez de usar informações de senha mantidas na nuvem.

Para obter mais informações, consulte [Azure AD conectar usuário logon opções][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Considerações de proxy do Azure AD aplicativo

Use o Azure AD para fornecer acesso aos aplicativos de local.

- Expor seus aplicativos web do local usando o proxy de aplicativo conectores gerenciar o componente de proxy de aplicativo do Azure AD. O conector de proxy de aplicativo abre uma conexão de rede de saída para o proxy de aplicativo do Azure AD. Solicitações de usuários remotos são roteadas volta de AAD através esta conexão para aplicativos da web. Esse mecanismo remove a necessidade de abrir portas de entrada no firewall local, reduzindo a superfície de ataque exposta pela sua organização.

Para obter mais informações, consulte [publicar aplicativos usando o proxy de aplicativo do Azure AD][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Considerações de sincronização do objeto

A configuração padrão do Azure AD Connect sincroniza objetos do seu diretório local do AD com base no conjunto de regras especificadas no artigo [sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão][aad-connect-sync-default-rules]. Somente os objetos que satisfaçam essas regras são sincronizadas, outros são ignorados. Por exemplo, objetos de usuário devem ter um atributo exclusivo *sourceAnchor* e o atributo *accounEnabled* deve ser preenchido. Objetos de usuário que não têm um atributo *sAMAccountName* ou que começam com o texto *AAD_* ou *MSOL_* não são sincronizados. Azure AD Connect se aplica a várias regras aos objetos de usuário, contato, grupo, ForeignSecurityPrincipal e computador. Se você precisar modificar o conjunto de regras padrão, use o Editor de regras de sincronização instalado com o Azure AD Connect (também documentadas no [sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão][aad-connect-sync-default-rules]).

Você pode definir seus próprios filtros para limitar os objetos a serem sincronizados por domínio ou unidade Organizacional. Ou implementar a filtragem personalizada mais complexas, conforme descrito em [sincronização do Azure AD Connect: configurar a filtragem][aad-filtering].

## <a name="security-considerations"></a>Considerações de segurança

Use o controle de acesso condicional para negar solicitações de autenticação de fontes inesperadas:

- Disparar [Autenticação multifator da Azure (MFA)] [ azure-multifactor-authentication] se um usuário tenta se conectar de um local não confiável (como através da Internet) em vez de uma rede confiável.

- Use o tipo de plataforma do dispositivo do usuário (iOS, Android, Windows Mobile, Windows) para determinar a política de acesso aos aplicativos e recursos.

- Registre o estado ativado/desativado dos dispositivos dos usuários e incorporar essas informações as verificações de política de acesso. Por exemplo, se o telefone de um usuário for perdido ou roubado-deve ser registrada como desativado para impedir que ele está sendo usado para obter acesso.

- Controle o nível de acesso para um usuário com base nos membros do grupo. Usar [regras de associação dinâmico AAD] [ aad-dynamic-membership-rules] para simplificar a administração de grupo. Para uma visão geral de como isso funciona, consulte [Introdução a participação dinâmico em grupos de][aad-dynamic-memberships].

- Use regras de risco de acesso condicional com proteção de identidade AAD para fornecer proteção avançada com base em atividades de entrada incomuns ou outros eventos.

Para obter mais informações, consulte [acessar condicional do Active Directory do Azure][aad-conditional-access].

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Escalabilidade endereçada pelo serviço AAD e a configuração do servidor de sincronização do Azure AD Connect:

- Para o serviço AAD, você não precisa configurar opções para implementar o dimensionamento. O serviço AAD suporta escalabilidade com base em réplicas. AAD implementa uma única primária réplica, que controla as operações de gravação, e vários somente leitura secundário. AAD transparente redireciona tentativas gravações feitas contra réplicas secundárias a réplica principal. AAD fornece consistência eventual. Todas as alterações feitas a réplica principal são propagadas para as réplicas secundárias. Como a maioria das operações contra AAD são leituras em vez de gravações, essa arquitetura escalas bem.

    Para obter mais informações, consulte [Azure AD: nos bastidores de nosso diretório de nuvem geográfica redundantes, altamente disponíveis e distribuídos][aad-scalability].

- Para o servidor de sincronização do Azure AD Connect, você deve determinar quantos objetos que estão provavelmente a sincronização de diretório local. Se você tiver menos de 100.000 objetos, você pode usar o software do SQL Server Express LocalDB padrão fornecido com o Azure AD Connect. Se você tiver um número maior de objetos, você deve instalar uma versão de produção do SQL Server e executar uma instalação personalizada da Azure AD Connect especificando que ele deve usar uma instância existente do SQL Server.

## <a name="availability-considerations"></a>Considerações de disponibilidade

Como com preocupações de escalabilidade, disponibilidade estende o serviço AAD e a configuração do Azure AD Connect:

- O serviço AAD destina-se para fornecer alta disponibilidade. Não existem opções disponibilidade configurável pelo usuário. É distribuído de localização geográfica e é executado nos dados de vários centros de visualização em todo o mundo, com failover automatizado. Se um data center ficar indisponível, AAD garante que os dados de diretório estão disponíveis para acesso de instância em pelo menos duas mais disperso regional data centers.

    >[AZURE.NOTE] O SLA AAD básico e Premium serviços garantias pelo menos 99,9% de disponibilidade. Não há nenhum SLA para a camada gratuita de AAD. Para obter mais informações, consulte [SLA Azure Active Directory][sla-aad].

- Para aumentar a disponibilidade do servidor de sincronização do Azure AD Connect é possível executar uma segunda instância no modo de teste, conforme descrito na seção [Considerações de topologia](#topology-considerations) . 

    Além disso, se você não estiver usando a instância do SQL Server Express LocalDB que vem com o Azure AD Connect, você deve considerar alta disponibilidade para o SQL Server. Observe que a solução de alta somente disponibilidade suportada é SQL cluster. Soluções como espelhamento e sempre ligado não são suportadas pelo Azure AD Connect.

    Para obter considerações adicionais sobre a disponibilidade do servidor de sincronização do Azure AD Connect e como recuperar após uma falha de manutenção, consulte [sincronização do Azure AD Connect: tarefas operacionais e considerações - recuperação][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Considerações de gerenciamento

Há dois aspectos Gerenciando AAD:

1. Administrando AAD na nuvem.

2. Manter os servidores de sincronização do Azure AD Connect.

AAD fornece as seguintes opções de gerenciamento de domínios e diretórios na nuvem:

- [Módulo do PowerShell do Active Directory do Azure][aad-powershell]. Use este módulo se você precisar script Azure AD tarefas administrativas comuns como gerenciamento de usuário, gerenciamento de domínio e para configurar o logon único.

- Lâmina de gerenciamento de AD Azure no portal do Azure. Este blade fornece uma visão de gerenciamento interativo do diretório e permite que você controle e configure a maioria dos aspectos de AAD.

    [! [10]][10]

Azure AD Connect instala as seguintes ferramentas que você usa para manter os serviços de sincronização do Azure AD Connect de seus computadores locais:

- Console do Microsoft Azure Active Directory Connect. Esta ferramenta permite modificar a configuração do servidor do Azure AD Sync, personalizar como a sincronização ocorre, ativar ou desativar o modo de preparação e alternar o usuário entrar modo (você pode habilitar AD FS entrar usando sua infraestrutura de local).

- O Gerenciador de serviço de sincronização. Use a guia de *operações* nesta ferramenta para gerenciar o processo de sincronização e detectar se qualquer parte do processo falha. Você pode disparar sincronizações manualmente usando esta ferramenta. 

    [! [12]][12]

    Na guia *conectores* permite que você controle as conexões para os domínios (locais e na nuvem) à qual o mecanismo de sincronização está associado:

    [! [13]][13]

-  O Editor de regras de sincronização. Use esta ferramenta para personalizar a forma na qual os objetos são transformados quando eles são copiados entre um diretório local e AAD na nuvem. Esta ferramenta permite que você especifique atributos adicionais e objetos para sincronização e filtros para determinar quais instâncias devem ou não deverão ser sincronizados.

    Para obter mais informações, consulte a seção de Editor de regras de sincronização no documento [sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão][aad-connect-sync-default-rules].

A página [sincronização do Azure AD Connect: práticas recomendadas para alterar a configuração padrão] [ aad-sync-best-practices] contém informações adicionais e dicas para gerenciar Azure AD Connect.

## <a name="monitoring-considerations"></a>Considerações de monitoramento

Monitoramento de integridade é executado por meio de uma série de agentes instalados no local:

- Azure AD Connect instala um agente que captura informações sobre sincronizações. Use a lâmina do Azure Active Directory conectar integridade no portal do Azure para monitorar a integridade e o desempenho do Azure AD Connect. Para obter mais informações, consulte [Usando o Azure AD conectar integridade para sincronização][aad-health].

- Para monitorar a integridade dos domínios AD DS e diretórios do Azure, instale o Azure AD conectar integridade do agente do AD DS em uma máquina dentro do domínio local. Use a lâmina do Azure Active Directory conectar integridade no portal do Azure AD DS do monitor. Para obter mais informações, consulte [Usando o Azure AD conectar integridade com o AD DS][aad-health-adds]

- Instale o Azure AD conectar integridade do agente do AD FS monitorar a integridade do AD FS em execução no local e usar a lâmina do Azure Active Directory conectar integridade no portal do Azure para monitorar AD DS. Para obter mais informações, consulte [Usando o Azure AD conectar integridade com o AD FS][aad-health-adfs]

Para obter mais informações sobre como instalar os agentes de integridade de conectar AD e seus requisitos, consulte [Azure AD conectar integridade do agente de instalação][aad-agent-installation].

## <a name="sample-solution"></a>Solução de exemplo

Esta seção documentos as etapas para criar uma solução de exemplo que você pode usar para testar a configuração do AAD. A solução consiste nos seguintes elementos:

- Um aplicativo web fileiras, seguindo a estrutura descrita por [VMs executando uma arquitetura de N camadas no Azure][implementing-a-multi-tier-architecture-on-Azure].

- Um computador cliente de teste. Recomendamos usar outra VM para este computador. A configuração desta máquina virtual não é importante, desde que você tem acesso de administrador e ele pode se conectar ao aplicativo web fileiras.

- Um ambiente simulado no local que contém o seu próprio domínio criado usando o AD DS.

Os cenários que demonstram essas etapas são:

- Habilitar acesso ao aplicativo web fileiras na nuvem para usuários externos, AAD fornecendo autenticação de senha.

- Habilitar acesso ao aplicativo web fileiras executado na nuvem para usuários que executam dentro da organização, com AAD fornecendo autenticação de senha.

### <a name="prerequisites"></a>Pré-requisitos

As etapas a seguir presumem os seguintes pré-requisitos:

- Você tem uma assinatura existente do Azure na qual você pode criar grupos de recursos.

- Você instalou a [Interface de linha de comando do Azure][azure-cli].

- Baixar e instalar a versão mais recente. Veja [aqui] [ azure-powershell-download] para obter instruções.

- Você instalou uma cópia do [makecert] [ makecert] utilitário no computador cliente de teste.

- Você tem acesso a um computador de desenvolvimento que tenha Visual Studio instalado.

### <a name="create-the-n-tier-web-application-architecture"></a>Criar a arquitetura do aplicativo web de n camadas

1. Crie uma pasta chamada `Scripts` que contém uma subpasta chamada `Parameters`.

2. Baixar o [Implantar-ReferenceArchitecture.ps1] [ solution-script] script do PowerShell para a pasta Scripts.

3. Na pasta parâmetros, crie outra subpasta nomeada Windows.

4. Baixe os arquivos a seguir para pasta parâmetros/Windows:

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Editar o arquivo de **Implantar ReferenceArchitecture.ps**1 na pasta Scripts e altere a seguinte linha para especificar o grupo de recursos que deve ser criado ou usado para armazenar a máquina virtual e recursos criados pelo script:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Edite os seguintes arquivos na pasta de **s/janela de parâmetros**e defina a `resourceGroup` valor na `virtualNetworkSettings` seção em cada um desses arquivos para ser a mesma que você especificou no arquivo de script **ReferenceArchitecture.ps1 implantar** .

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Abra uma janela do PowerShell do Azure, mover para a pasta de Scripts e execute o seguinte comando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Substituir **<subscription id>** com sua ID do Azure assinatura.

    Para **<location>**, especifique uma região Azure, como *eastus* ou *westus*.

8. Quando o script for concluído, use o portal do Azure para obter o endereço IP público o camada da web do balanceador de carga (*ar aad-ntier-web-kg*):

    [! [18]][18]

9. Faça logon em seu teste cliente computador (ou máquina virtual) e verifique se você pode acessar a camada da web usando o Internet Explorer para navegar até o endereço IP público o camada da web do balanceador de carga. A página padrão do IIS deverá aparecer.

### <a name="simulate-configuration-of-a-public-web-site"></a>Simular a configuração de um site público

>[AZURE.NOTE] As etapas a seguir simular associando a camada da web a DNS nome www.contoso.com modificando o arquivo hosts no computador cliente de teste. Além disso, as VMs camada da web são configuradas com certificados autoassinados e FALSO autoridade de hospedagem. Isso é para teste apenas e **que você não deve usar essas técnicas em um ambiente de produção**.

1. No computador cliente de teste, edite o arquivo **C:\Windows\System32\drivers\etc\hosts** usando o bloco de notas e adicionar uma entrada que associa a www.contoso.com nome com o endereço IP público o camada da web do balanceador de carga:

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Verifique se que agora você pode navegar para www.contoso.com do computador cliente de teste. A mesma página IIS padrão deverá aparecer como antes.

3. No computador cliente de teste, abra um prompt de comando como administrador e usar o utilitário makecert c
4. criar um certificado de autoridade de certificação raiz falsa:

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Verificar se os seguintes arquivos são criados:

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Execute o seguinte comando para instalar a autoridade de certificação raiz de teste:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Use a autoridade de certificação de teste para gerar um certificado para www.contoso.com:

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Execute o comando **mmc** e adicione o snap-in de certificados para a conta de computador para o computador local.

7. No */Certificates (computador Local) / Personal/certificado/* armazenar, exporte o certificado de www.contoso.com com sua chave privada em um arquivo denominado www.contoso.com.pfx:

    [! [20]][20]

8. Retornar para o portal do Azure e conecte-se para a camada de gerenciamento máquina virtual (*ar-aad-ntier-gerenciamento-vm1*). O nome de usuário padrão é *testuser* com senha *AweS0me@PW*:

    [! [21]][21]
    
9. Da camada de gerenciamento máquina virtual, conectar-se a cada um da camada da web VMs alternadamente com username *testuser* e senha *AweS0me@PW*e executar as seguintes tarefas. Observe que as VMs tem particular endereços IP 10.0.1.4, 10.0.1.5 e 10.0.1.6:

    >[AZURE.NOTE] A camada da web VMs só tem endereços IP particulares. Você somente pode se conectar a eles usando a camada de gerenciamento máquina virtual.

    1. Copie os arquivos *www.contoso.com.pfx* e *MyFakeRootCertificateAuthority.cer* do computador cliente de teste.
    
    2. Abra um prompt de comando como administrador, mover para a pasta que contém os arquivos que você copiou e execute os seguintes comandos:
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Executar o `mmc` comando, adicione o snap-in de certificados para a conta de computador para o computador local e verificar se os certificados a seguir foram instalados:

        - \Certificates (computador local) \Personal\Certificates\ www.contoso.com

        - Authorities\Certificates\MyFakeRootCertificateAuthority de certificação de raiz de \Trusted \Certificates (computador local)

    4. Inicie o console do Gerenciador de serviços de informações da Internet (IIS) e navegue até o *Site da Web Web\Site* no computador.

    5. No painel de *ações* , clique em *ligações*e adicionar uma associação https usando o certificado SSL de www.contoso.com:

        [! [22]][22]

10. Retorne ao computador cliente de teste e verifique se que agora você pode navegar para https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Crie um locatário do Active Directory do Azure

1. Usando o portal do Azure, crie um locatário do Active Directory do Azure como *myaadname*. onmicrosoft.com, onde *myaadname* é um nome de diretório selecionado por você.

2. Adicione um usuário denominado *admin* com a função de GlobalAdmin no diretório. Tome nota da senha recentemente gerada.

3. Usando o Internet Explorer, navegue até https://account.activedirectory.windowsazure.com/ e faça logon como admin@ *myaadname*. onmicrosoft.com. Altere sua senha quando solicitado.

### <a name="create-and-deploy-a-test-web-application"></a>Criar e implantar um aplicativo da web de teste

1. Usando o Visual Studio no computador de desenvolvimento, criar um aplicativo Web ASP.NET chamado ContosoWebApp1 (use o .NET Framework 4.5.2):

    [! [23]][23]

2. Selecione o modelo *MVC* , altere a autenticação para *contas de trabalho e escola*e especifique o nome do seu locatário AAD. Não crie um serviço de aplicativo na nuvem.

    [! [24]][24]

3. Criar e executar o aplicativo para testar a autenticação. Na página de entrada, insira o nome da conta admin@ *myaadname*. onmicrosoft.com, forneça a senha e clique em *entrar*:

    [! [25]][25]

4. Verifique se AAD pede permissão para entrar e ler o seu perfil e, em seguida, começa a ser executado o aplicativo da web com a identidade do administrador.

5. Feche o Internet Explorer e retorne ao Visual Studio.

6. Abra o arquivo Web. config e no `<appSettings>` seção, altere o valor da chave de *ida: PostLogoutRedirectUri* para *https://www.contoso.com:443 /*. Salve o arquivo.

7. Na janela *Solution Explorer* , clique com botão direito no projeto ContosoWebApp1, clique em *Publicar*.

8. Na janela *Publicar Web* , clique em *personalizado*. Crie um perfil personalizado chamado *ContosoWebApp1*.

9. Na página *Conexão* , defina o *método de publicar* sistema de *Arquivos* e especifique uma pasta chamada *ContosoWebApp*, localizado em um local conveniente em seu computador de desenvolvimento.

10. Na página *configurações* , defina a *configuração* para *lançamento*.

11. Na página de *visualização* , clique em *Publicar*.

12. Conectar-se a cada servidor web alternadamente (via a camada de gerenciamento da máquina virtual) e execute as seguintes tarefas:

    1. Copie a pasta *ContosoWebApp* e seu conteúdo de computador de desenvolvimento para a pasta *C:\inetpub* .

    2. Usando o console do Gerenciador de serviços de informações da Internet (IIS), navegue até o *Site da Web Web\Site* no computador.

    3. No painel de *ações* , clique em *Configurações de base*e altere o caminho físico do site para *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Publicar o aplicativo da web de teste por meio de AAD

1. Faça logon no portal do Azure e navegue até o diretório AAD.

2. Na guia *aplicativos* , clique no aplicativo ContosoWebApp1.

3. Verifique se seu aplicativo com êxito é adicionado ao diretório e clique na guia *Configurar* .

4. Altere a *URL de SIGN-ON* para https://www.contoso.com:443 e definir a *URL de resposta* para https://www.contoso.com:443 (a mesma URL).

5. Salve a configuração.

6. No computador cliente de teste, navegue até https://www.contoso.com. Verifique se que AAD solicita suas credenciais e faça logon.

>[AZURE.NOTE] Este é o ponto de extremidade para o primeiro cenário: habilitar o acesso ao aplicativo web fileiras na nuvem para usuários externos, AAD fornecendo autenticação de senha.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Criar um ambiente local simulado com o Active Directory

O ambiente local contém dois controladores de domínio para o `contoso.com` domínio e dois servidores para hospedar o Azure AD Connect sincronizar o serviço. Os servidores de hospedagem Azure AD Connect não são domínio.

1. No Explorador de arquivos, retorne para a pasta de Scripts que contém o script usado para criar o aplicativo da web de fileiras.

2. Na pasta parâmetros, adicione outra pasta sub chamada `Onpremise`.

3. Baixe os seguintes arquivos para pasta de parâmetros/Onpremise:

    - [Adicionar-adiciona-domínio-controller.parameters.json][add-adds-domain-controller-parameters]

    - [Criar-adiciona-floresta-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines-adc.parameters.json][virtualMachines-adc-parameters]

    - [virtualMachines-adc-joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [virtualMachines-adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [dns.parameters.json adiciona virtualNetwork][virtualNetwork-adds-dns-parameters]

5. Editar o arquivo ReferenceArchitecture.ps1 implantar na pasta Scripts e altere a seguinte linha para especificar o grupo de recursos que deve ser criado ou usado para armazenar a máquina virtual e recursos criados pelo script:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Edite os seguintes arquivos na pasta parâmetros/Onpremise e defina o `resourceGroup` valor na `virtualNetworkSettings` seção em cada um desses arquivos para ser a mesma que você especificou no arquivo de script ReferenceArchitecture.ps1 implantar.

    - virtualMachines-adds.parameters.json

    - virtualMachines-adc.parameters.json

    - virtualNetwork.parameters.json

    - dns.parameters.json adiciona virtualNetwork

7. Abra uma janela do PowerShell do Azure, mover para a pasta de Scripts e execute o seguinte comando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Substituir `<subscription id>` com sua ID do Azure assinatura.

    Para `<location>`, especifique uma região Azure, tais como `eastus` ou `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Instalar e configurar o serviço de sincronização do Azure AD Connect

A configuração ilustrada nestas etapas consiste em duas instâncias do serviço de sincronização do Azure AD Connect. O primeiro está ativo, enquanto o segundo é configurado no modo de preparação para fornecer alta disponibilidade e failover rápido se o primeiro servidor falhar.

1. Usando o portal do Azure, navegue até o grupo de recursos mantendo as VMs para os serviços de sincronização do Azure AD Connect (*ar-aad-onpremise-rg* por padrão) e conectar ao *ar-aad-onpremise-adc-vm1* máquina virtual. Faça logon como *testuser* com senha *AweS0me@PW*.

2. Baixe o Azure AD Connect da [aqui][aad-connect-download].

3. Execute o instalador do Azure AD Connect e executar uma instalação usando a opção *Personalizar* .

    >[AZURE.NOTE] Você não pode usar a opção *Configurações expressas* porque a máquina virtual hospedando o serviço de sincronização do Azure AD Connect não está associado a um domínio.

4. Na página *instalar componentes necessários* , deixe as configurações de configuração opcional em branco para aceitar as opções padrão.

5. Na página de *entrada do usuário* , selecione *A sincronização de senha*.

6. Na página *conectar ao Azure AD* , insira admin@ *myaadname*. onmicrosoft.com, onde *myaadname* é o nome do seu locatário AAD. Insira a senha da conta de administrador.

7. Na página *conectar seus diretórios* , especifique contoso.com da floresta (digite o valor na porque ela não aparece na lista suspensa), digite CONTOSO\testuser para o nome de usuário, especifique AweS0me@PW a senha e clique em *Adicionar diretório*.

8. Na página *configuração de entrada do Azure AD* , aceite o valor padrão para o nome de usuário principal. Verificar *continuar sem qualquer domínio verificado*.

    >[AZURE.NOTE] O diretório de contoso.com são listadas como *Não verificados*. Para verificar um nome de domínio, você deve criar um registro TXT para o seu registrador de nome de domínio. Neste exemplo, o domínio local não está registrado externamente. Para obter mais informações, consulte [Adicionar um nome de domínio personalizado ao Azure Active Directory][aad-custom-directory].

9. Na página de *filtragem de domínio e unidade Organizacional* , selecione *sincronizar todos os domínios e unidades organizacionais* (padrão).

10. Na página *identifica exclusivamente seus usuários* , aceite os valores padrão.

11. Na página *usuários e dispositivos de filtro* , selecione *sincronizar todos os usuários e dispositivos* (padrão).

12. Na página *recursos opcionais* , selecione *senha write-back*.

13. Na página *pronto para configurar* , selecione *Iniciar o processo de sincronização quando a configuração for concluída*, mas deixe de *Habilitar o modo de preparação* desmarcada.

14. Verificar que o processo de configuração concluirá sem erros e, em seguida, sair do instalador.

15. Azure no portal do, conecte ao *ar-aad-onpremise-adc-da vm2* máquina virtual. Faça logon como *testuser* com senha *AweS0me@PW*.

16. Baixe o Azure AD Connect e, em seguida, execute o instalador.

17. Percorrer o assistente e responda conforme descrito nas etapas 3 a 12.

18. Na página *pronto para configurar* , selecione *Iniciar o processo de sincronização quando a configuração for concluída*e **também selecionar** *Habilitar o modo de teste*. Isso faz com que o serviço de sincronização do Azure AD Connect recuperar detalhes sobre contas e objetos de domínio contoso.com e armazená-los em seu banco de dados, mas ele não transmitir esses detalhes para seu locatário AAD.

14. Verificar que o processo de configuração concluirá sem erros e, em seguida, sair do instalador.

### <a name="test-the-aad-configuration"></a>Testar a configuração de AAD

1. Usando o portal do Azure, alterne para o seu diretório AAD, abra a lâmina do Azure Active Directory, clique em *usuários e grupos*e clique em *todos os usuários* para exibir a lista de usuários e grupos sincronizados com o diretório. Você deve ver os usuários para as contas a seguir:

    - administração (admin@ *myaadname*. onmicrosoft.com)

    - Conta de serviço de sincronização de diretório local (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Conta de serviço de sincronização de diretório local (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. No portal do Azure, navegue até o grupo de recursos mantendo as VMs para os controladores de domínio do AD DS (*ar-aad-onpremise-rg* por padrão) e conectar ao *ar-aad-onpremise-ad-vm1* máquina virtual. Faça logon como *testuser* com senha *AweS0me@PW*.

3. Usando o console *Active Directory Users e computadores* , adicione um novo usuário de domínio chamado Diane Tibbot, com o nome de logon dianet@contoso.com. Especifica uma senha de sua escolha. Tornar o usuário um membro do grupo de administradores local (Isso é apenas para que você pode fazer logon localmente no como este usuário posteriormente - as apenas máquinas no domínio são controladores de domínio).

4. Alternar para o *ar-aad-onpremise-adc-vm1* máquina virtual, abra uma janela do PowerShell e execute os seguintes comandos:

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Confirme se o comando retorna *sucesso*.

    >[AZURE.NOTE] Essa etapa é necessária porque por padrão, o processo de sincronização está agendado para ser executado em intervalos de 30 minutos. Esses comandos forçam uma sincronização para ocorrer imediatamente.

5. Retornar para o portal do Azure, alterne para o seu diretório AAD, abrir a lâmina do Azure Active Directory, clique em *usuários e grupos*e clique em *todos os usuários*. Agora você deve ver Diane Tibbot (dianet@ *myaadname*. onmicrosoft.com) aparecem na lista de usuários.

6. Na lâmina Azure Active Directory, clique em *Aplicativos corporativos*e clique em *todos os aplicativos*. Clique no aplicativo de *ContosoWebApp1* e clique em *usuários e grupos*. Na barra de ferramentas, clique em *Adicionar*. Clique em *usuários e grupos*e selecione *Alice Tibbot*. Clique em *atribuir*.

7. Usando o Internet Explorer, navegue até o site https://account.activedirectory.windowsazure.com. Faça logon como dianet@ *myaadname*. onmicrosoft.com com a senha especificados anteriormente.

    >[AZURE.NOTE] Não clique no ícone de ContosoWebApp na lista de aplicativos. AAD tenta localizar o aplicativo da web no endereço DNS publicamente listado para www.contoso.com, que é diferente do endereço do seu balanceador de carga de camada da web.

8. Clique na guia *perfil* . Os detalhes do usuário (se você especificou qualquer quando ela foi criada) devem ser exibidos.

    >[AZURE.NOTE] Se você clicar em *Alterar senha*, você não tem permissão para executar esta tarefa, como essa operação deve ser habilitada por um administrador primeiro. Para obter mais informações, consulte [Introdução ao gerenciamento de senha][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Permitir que os usuários locais executar o aplicativo usando a autenticação por meio de AAD

1. Retornar para o controlador de domínio de *ar-aad-onpremise-ad-vm1* máquina virtual.

2. Abra o console do *Gerenciador de DNS* e adicione um novo registro de host para www.contoso.com. Especifique o endereço IP público o camada da web do balanceador de carga.

3. Copie o arquivo *MyFakeRootCertificateAuthority.cer* do computador cliente de teste (você criou este arquivos no procedimento [simular a configuração de um site público](#simulate-configuration-of-a-public-web-site)
    
4. Abra um prompt de comando como administrador, mover para a pasta que contém o arquivo que você acabou de copiar e execute o seguinte comando:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Usando o Internet Explorer, navegue até https://www.contoso.com. Verifique se AAD-na página de entrada do aplicativo da web ContosoWebApp1 aparece.

6. Faça logon como dianet@ *myaadname*. onmicrosoft.com. O aplicativo deve executar e entrar no corretamente.

## <a name="next-steps"></a>Próximas etapas

- Aprenda as práticas recomendadas para [estender seu domínio local ADICIONA Azure][adds-extend-domain]

- Aprenda as práticas recomendadas para a [criação de uma floresta de recursos ADICIONA] [ adds-resource-forest] no Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Arquitetura de identidade de nuvem usando o Active Directory do Azure"
[1]: ./media/guidance-identity-aad/figure2.png "Única floresta, um único topologia de diretório AAD"
[2]: ./media/guidance-identity-aad/figure3.png "Várias florestas, topologia de diretório AAD única"
[4]: ./media/guidance-identity-aad/figure5.png "Topologia de servidor de teste"
[5]: ./media/guidance-identity-aad/figure6.png "Topologia de diretórios AAD vários"
[6]: ./media/guidance-identity-aad/figure7.png "Selecionando uma instalação personalizada do Azure AD conectar sincronizar com uma instância específica do SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Especificar o método de SSO para o acesso do usuário"
[8]: ./media/guidance-identity-aad/figure9.png "Especifica o domínio e OU opções de filtragem"
[9]: ./media/guidance-identity-aad/figure10.png "Habilitando regravação de senha"
[10]: ./media/guidance-identity-aad/figure11.png "A lâmina de gerenciamento do Azure AD no portal"
[11]: ./media/guidance-identity-aad/figure12.png "Console do Azure AD Connect"
[12]: ./media/guidance-identity-aad/figure13.png "Guia de operações no Gerenciador de serviço de sincronização"
[13]: ./media/guidance-identity-aad/figure14.png "Guia de conectores no Gerenciador de serviço de sincronização"
[14]: ./media/guidance-identity-aad/figure15.png "O Editor de regras de sincronização"
[15]: ./media/guidance-identity-aad/figure16.png "A lâmina Azure Active Directory conectar integridade no portal do Azure mostrando a integridade de sincronização"
[16]: ./media/guidance-identity-aad/figure17.png "A lâmina Azure Active Directory conectar integridade no portal do Azure mostrando a integridade do AD DS"
[17]: ./media/guidance-identity-aad/figure18.png "Relatórios de segurança disponíveis no portal do Azure"
[18]: ./media/guidance-identity-aad/figure19.png "Portal do Azure realçando o endereço IP público o camada da web do balanceador de carga"
[19]: ./media/guidance-identity-aad/figure20.png "Usando o Internet Explorer para navegar até o endereço IP público o camada da web do balanceador de carga"
[20]: ./media/guidance-identity-aad/figure21.png "Snap-in de certificados mostrando o certificado www.contoso.com"
[21]: ./media/guidance-identity-aad/figure22.png "Conectar-se para a camada de gerenciamento máquina virtual"
[22]: ./media/guidance-identity-aad/figure23.png "Criando a ligação HTTPS para o site padrão"
[23]: ./media/guidance-identity-aad/figure24.png "Criar o aplicativo da web de ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Definindo as propriedades de autenticação do aplicativo da web ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "Entrando no Azure AAD do aplicativo da web ContosoWebApp1"
[26]: ./media/guidance-identity-aad/figure27.png "Alterar a pasta para o site padrão"