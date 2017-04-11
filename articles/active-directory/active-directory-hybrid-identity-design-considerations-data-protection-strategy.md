<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - Definir estratégia de proteção de dados | Microsoft Azure"
    description="Você deverá definir a estratégia de proteção de dados para sua solução de identidade híbrido atender a requisitos de negócios que você definiu."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir estratégia de proteção de dados para sua solução de identidade híbrido

Nesta tarefa, você deverá definir a estratégia de proteção de dados para sua solução de identidade híbrido atender a requisitos de negócios que você definiu na:

- [Determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Determinar os requisitos de gerenciamento de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Determinar os requisitos de controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Determinar os requisitos de resposta ao incidente](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Como ele foi explicado em [Determinar requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), o Microsoft Azure AD pode sincronizar aos seus serviços de domínio Active Directory (AD DS) localizado no local. Essa integração permite que as organizações utilizem Azure AD para verificar as credenciais do usuário quando eles estão tentando acessar os recursos corporativos. Isso pode ser feito para os dois cenários: dados no restante locais e na nuvem.  Acesso a dados no Azure AD requer autenticação de usuário por meio de um serviço de token de segurança (STS).

Depois de autenticado, o nome de usuário principal (UPN) é ler o token de autenticação e a partição replicada e contêiner correspondente ao domínio do usuário é determinado. Informações sobre existência, estado habilitado e função do usuário são usadas pelo sistema de autorização para determinar se o acesso solicitado para o locatário de destino está autorizado para este usuário nesta sessão. Determinadas ações autorizadas (especificamente, criar usuário, redefinição de senha) criar uma trilha de auditoria que pode ser usada por um administrador de locatário para gerenciar os esforços de conformidade ou investigações.

Movimentação de dados de seu centro de dados local para o armazenamento do Azure ao longo de uma conexão de Internet não pode ser viável devido a volume de dados, a disponibilidade de largura de banda ou outras considerações sempre. O [Serviço de importação/exportação de armazenamento do Azure](../storage/storage-import-export-service.md) fornece uma opção baseada em hardware para colocar/recuperar grandes volumes de dados no armazenamento de blob. Ele permite que você envie [criptografados pelo BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) unidades de disco rígido diretamente para um data center Azure onde operadores de nuvem carregará o conteúdo da sua conta de armazenamento ou eles podem baixar seus dados do Azure para suas unidades para retornar a você. Somente os volumes criptografados são aceitos para esse processo (usando uma chave de BitLocker gerada pelo serviço durante a configuração de trabalho). A chave do BitLocker é fornecida para Azure separadamente, proporcionando sair do compartilhamento de chave de banda.

Como dados em trânsito podem ocorrer em cenários diferentes, também é relevante para saber que o Microsoft Azure usa [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego dos locatários uns dos outros, emprego medidas como firewalls de nível host e convidado, filtragem de pacotes IP, bloqueio de portas e pontos de extremidade HTTPS. No entanto, na maioria das comunicações internas do Azure, incluindo infraestrutura de infraestrutura e infraestrutura para cliente (local), também é criptografada. Outro cenário importante é as comunicações dentro dos data centers do Azure; A Microsoft gerencia redes para garantir que nenhuma máquina virtual pode representar ou interceptar o endereço IP do outro. SSL/TLS é usada ao acessar o armazenamento do Azure ou bancos de dados do SQL ou ao se conectar aos serviços de nuvem. Nesse caso, o administrador do cliente é responsável por obter um certificado SSL/TLS e implantá-lo em sua infraestrutura de locatário. Movimentação de tráfego de dados entre máquinas virtuais na mesma implantação ou entre locatários em uma única implantação via rede Virtual do Microsoft Azure pode ser protegido por meio de protocolos de comunicação criptografada como HTTPS, SSL/TLS ou outras pessoas.

Dependendo de como você as perguntas respondidas em [Determinar requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), você deve ser capaz determinar como você deseja proteger seus dados e como a solução de identidade híbrido auxiliarão sobre isso. A tabela mostra as opções de suporte do Azure que estão disponíveis para cada cenário de proteção de dados.


| Opções de proteção de dados         | Inativos na nuvem | Em outros locais | Em trânsito |
|---------------------------------|----------------------|---------------------|------------|
| Criptografia de unidade BitLocker      | X                    | X                   |            |
| SQL Server para criptografar bancos de dados | X                    | X                   |            |
| Criptografia de máquina virtual-para-VM             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Leia [conformidade pelo recurso](https://azure.microsoft.com/support/trust-center/services/) em [Central de confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações que cada serviço Azure é compatível com.
Como as opções de proteção de dados usam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para essa tarefa. Certifique-se de que você está aproveitando todas as opções disponíveis para cada estado que os dados serão.

## <a name="define-content-management-options"></a>Definir opções de gerenciamento de conteúdo
Uma vantagem de usar o Azure AD para gerenciar uma infraestrutura de identidade híbrida é que o processo é totalmente transparente perspectiva do usuário final. O usuário tenta acessar um recurso compartilhado, o recurso requer autenticação, o usuário terá que enviar uma solicitação de autenticação para Azure AD para obter o token e acessar o recurso. Todo esse processo acontece no plano de fundo, sem interação do usuário. Também é possível conceder a permissão para um [grupo](active-directory-manage-groups.md#getting-started-with-access-management) de usuários para permitir que ele executar determinadas ações comuns.

As organizações que costumam ser preocupações sobre a privacidade dos dados requerem classificação de dados para sua solução. Se sua infraestrutura de local atual já está usando a classificação de dados, é possível utilizar o Azure AD como o repositório principal para a identidade do usuário. Uma ferramenta comum que é usado no local para classificação de dados é chamada [Kit de ferramentas de classificação de dados](https://msdn.microsoft.com/library/Hh204743.aspx) para o Windows Server 2012 R2. Esta ferramenta pode ajudar a identificar, classificar e proteger os dados em servidores de arquivos em sua nuvem privada. Também é possível utilizar a [Classificação automática de arquivo](https://technet.microsoft.com/library/hh831672.aspx) no Windows Server 2012 para fazer isso.

Se sua organização não tem a classificação de dados no lugar, mas precisa proteger arquivos confidenciais sem adicionar novos servidores locais, eles podem usar o Microsoft [Azure Rights Management Services](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS usa criptografia, identidade e diretivas de autorização para ajudar a proteger seus arquivos e email, e ele funciona em vários dispositivos — telefones, tablets e computadores. Como Azure RMS é um serviço de nuvem, não é necessário configurar explicitamente relações de confiança com outras organizações antes de você pode compartilhar conteúdo protegido com eles. Se elas já tiverem um Office 365 ou um diretório de Azure AD, automaticamente há suporte para a colaboração entre organizações. Você também pode sincronizar apenas os atributos de diretório que Azure RMS precisa dar suporte a uma identidade comum para suas contas do Active Directory local, usando serviços de sincronização de diretório ativo do Azure (AAD Sync) ou Azure AD Connect.

Uma parte vital do gerenciamento de conteúdo é compreender quem está acessando qual recurso, portanto, um recurso de log de rich é importante para a solução de gerenciamento de identidade. Azure AD fornece log por 30 dias, incluindo:

- Alterações no membro da função (ex: usuário adicionado à função de Administrador Global)
- Atualizações de credencial (ex: alterações de senha)
- Gerenciamento de domínio (ex: verificar um domínio personalizado, removendo um domínio)
- Adicionando ou removendo aplicativos
- Gerenciamento de usuário (ex: adicionando, removendo, atualizando um usuário)
- Adicionando ou removendo licenças

>[AZURE.NOTE]
Leia [Microsoft Azure segurança e gerenciamento de Log de auditoria](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre os recursos de log no Azure.
Dependendo de como você respondeu as perguntas em [requisitos de gerenciamento de conteúdo de determinar](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), você deve ser capaz de determinar como deseja que o conteúdo a ser gerenciado em sua solução de identidade híbrido. Enquanto todas as opções expostas na tabela 6 são capazes de integração com o Azure AD, é importante definir o que é mais adequado às suas necessidades de negócios.

| Opções de gerenciamento de conteúdo                                                               | Vantagens                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Desvantagens                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralizado local (servidor de gerenciamento de direitos do Active Directory)                      | Controle total sobre a infraestrutura de servidor responsável para classificar os dados <br> Recurso interno no Windows Server, sem a necessidade de assinatura ou licença extra <br> Pode ser integrado ao Azure AD em um cenário de híbrido <br> Suporta os recursos IRM (gerenciamento) de direitos de informação Serviços Online da Microsoft como o Exchange Online e do SharePoint Online, bem como Office 365 <br> Oferece suporte a produtos de servidor local Microsoft, como Exchange Server, o SharePoint Server e servidores de arquivos que executam o Windows Server e infraestrutura de classificação de arquivo (FCI). | Superior, manutenção (Lembre-se com atualizações, configuração e possíveis atualizações), desde IT é o proprietário do servidor <br> Exigir uma infraestrutura de servidor local<br> Doesn'tleverage recursos Azure nativamente                                     |
| Centralizado na nuvem (Azure RMS)                                                     | Mais fácil de gerenciar em comparação à solução local <br> Pode ser integrado com o AD DS em um cenário de híbrido <br>  Totalmente integrado com o Azure AD <br> Não requer um servidor local para implantar o serviço <br> Suporte para produtos de servidor Microsoft como o Exchange Server, do SharePoint, servidor e servidores de arquivos que executam o Windows Server e classificação de arquivos, infraestrutura (FCI) local <br> IT, pode ter controle completo sobre a chave do seu locatário com recurso de BYOK.                                                                                    | Sua organização deve ter uma assinatura de nuvem que oferece suporte a RMS <br> Sua organização deve ter um diretório Azure AD para oferecer suporte à autenticação de usuário para RMS                                                                                  |
| Híbrido (Azure RMS integrado, servidor de gerenciamento de direitos do Active Directory do local) | Este cenário acumula as vantagens de ambos, centralizados locais e na nuvem.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Sua organização deve ter uma assinatura de nuvem que oferece suporte a RMS <br> Sua organização deve ter um diretório Azure AD para oferecer suporte à autenticação de usuário para o RMS, <br> Requer uma conexão entre o serviço de nuvem do Azure e infraestrutura local |

## <a name="define-access-control-options"></a>Definir opções de controle de acesso
Utilizando a autenticação, autorização e acesso controlam recursos disponíveis no Azure AD, você poderá ativar sua empresa usem um repositório central identidade permitindo a usuários e parceiros usar logon único (SSO), conforme mostrado na figura abaixo:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gerenciamento centralizado e totalmente a integração com outros diretórios

Active Directory do Azure fornece logon único para milhares de aplicativos SaaS e aplicativos da web no local. Leia o [lista de compatibilidade de Federação do Active Directory do Azure: provedores de identidade de terceiros que podem ser usadas para implementar o logon único](https://msdn.microsoft.com/library/azure/jj679342.aspx) artigo para obter mais detalhes sobre o SSO terceiros que foram testadas pela Microsoft. Esse recurso permite que a organização implemente uma variedade de cenários de B2B mantendo o controle do gerenciamento de identidades e acesso. No entanto, durante a B2B projetar processo é importante entender o método de autenticação que será usado pelo parceiro e valida se este método é suportado pelo Azure. No momento, estes são suportados pelo Azure AD de métodos:

- Linguagem de marcação de declaração de segurança SAML)
- OAuth
- Kerberos
- Tokens
- Certificados


>[AZURE.NOTE] Leia [Protocolos de autenticação do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais detalhes sobre cada protocolo e seus recursos no Azure.

Usando o suporte do Azure AD, aplicativos de negócios móvel podem usar a mesma experiência de autenticação de serviços Mobile fácil para permitir que os funcionários entrar em seus aplicativos móveis com suas credenciais do Active Directory corporativas. Com esse recurso, o Azure AD é suportado como um provedor de identidade nos serviços do Mobile junto com os outros provedores de identidade já suportamos (que incluem Accounts da Microsoft, Facebook ID, Google ID e ID do Twitter). Se os aplicativos do local usa a credencial do usuário localizada no AD DS da empresa, o acesso de parceiros e usuários provenientes de nuvem deve ser transparente. Você pode gerenciar o controle de acesso condicional do usuário para aplicativos web (baseado em nuvem), web API, serviços de nuvem da Microsoft, 3º aplicativos SaaS de festa e aplicativos cliente (móveis) nativos e ter os benefícios de segurança, auditoria, relatórios em um só lugar. No entanto, é recomendável para validar isso em um ambiente de não produção ou uma quantidade limitada de usuários.


>[AZURE.TIP] é importante mencionar que Azure AD não tenha política de grupo AD DS tem. Para impor a política para dispositivos, você precisará uma solução de gerenciamento de dispositivo móvel como [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Quando o usuário é autenticado usando o Azure AD, é importante avaliar o nível de acesso que o usuário terá a ele. O nível de acesso que o usuário terá sobre um recurso pode variar, enquanto Azure AD pode adicionar uma camada de segurança adicional, controlando o acesso a alguns recursos, você também deve ter em mente que o recurso propriamente dito também pode ter sua própria lista de controle de acesso separadamente, como o controle de acesso para arquivos localizados em um servidor de arquivos. A figura a seguir resume os níveis de controle de acesso que você pode ter em um cenário de híbrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Cada interação no diagrama mostrada na Figura X representa um cenário de controle de acesso que pode ser coberto por Azure AD. Abaixo, você tem uma descrição de cada cenário:

1. condicional acesso aos aplicativos que está hospedado no local: você pode usar dispositivos registrados com políticas de acesso para aplicativos que estão configurados para usar o AD FS com o Windows Server 2012 R2. Para obter mais informações sobre como configurar o acesso condicional para no local, consulte [Configurar o acesso condicional local usando o registro de dispositivos do Azure Active Directory](active-directory-conditional-access-on-premises-setup.md).
2. controle de acesso para o Portal de gerenciamento do Azure: Azure também tem a capacidade de controlar o acesso ao Portal de gerenciamento usando RBAC (função com base em controle de acesso). Este método permite que a empresa restringir a quantidade de operações que um indivíduo pode fazer quando ele acessa o Portal de gerenciamento do Azure. Usando RBAC para controlar o acesso ao portal, administradores de TI ca acesso de representante, usando as seguintes abordagens de gerenciamento de acesso:

 - Atribuição de função baseado em grupo: você pode atribuir acesso aos grupos de Azure AD que podem ser sincronizados do seu Active Directory local. Isso permite que você aproveite os investimentos que sua organização tiver deixado em ferramentas e processos de gerenciamento de grupos. Você também pode usar o recurso de gerenciamento de grupo delegado do Azure AD Premium.
 - Aproveite integrado funções no Azure: você pode usar três funções — proprietário, Colaborador e leitor, para garantir que os usuários e grupos têm permissão para fazer somente as tarefas que precisam para realizar seus trabalhos.
 - Acesso granular aos recursos: você pode atribuir funções aos usuários e grupos para uma assinatura específica, grupo de recursos ou um recurso individual Azure como um site ou um banco de dados. Dessa forma, você pode garantir que os usuários tenham acesso a todos os recursos que precisam e nenhum acesso aos recursos que não precisam gerenciar.

>[AZURE.NOTE] Leia o [controle de acesso baseado em função no Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) para saber mais detalhes sobre esse recurso. Para os desenvolvedores que estão criando aplicativos e deseja personalizar o controle de acesso para eles, também é possível usar funções de aplicativo do Azure AD para autorização. Analise esse [exemplo de Web App-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) como criar seu aplicativo para usar esse recurso.

3. condicional acesso para aplicativos do Office 365 com o Microsoft Intune: IT administradores podem provisionar diretivas de dispositivo de acesso condicional para proteger recursos corporativos, enquanto ao mesmo tempo permitindo que os operadores de informações em dispositivos compatíveis para acessar os serviços. Para obter mais informações, consulte [Condicional diretivas de dispositivo de acesso para serviços do Office 365](active-directory-conditional-access-device-policies.md).

4. condicional acesso para aplicativos Saas: [Este recurso](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) permite que você configure as regras de acesso de autenticação multifator de por aplicativo e a capacidade de bloquear o acesso de usuários não em uma rede confiável. Você pode aplicar as regras de autenticação multifator para todos os usuários que estejam atribuídos ao aplicativo ou apenas para os usuários em grupos de segurança especificados. Os usuários podem ser excluídos da exigência de autenticação multifator se eles estiverem acessando o aplicativo de um endereço IP que em dentro da organização de rede.

Como as opções de controle de acesso usam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para essa tarefa. Certifique-se de que você está aproveitando todas as opções disponíveis para cada cenário que requer a controlar o acesso aos seus recursos.

## <a name="define-incident-response-options"></a>Definir opções de resposta a incidente
Azure AD pode ajudar a equipe de TI identidade possível riscos de segurança no ambiente monitorando atividade do usuário, que pode aproveitam Azure AD acesso e relatórios de uso do recurso para obter visibilidade a integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de TI pode determinar melhor onde os possíveis riscos de segurança podem estar para que eles possam planejar adequadamente a reduzir esses riscos.  [Assinatura do Azure AD Premium](active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que podem permitir IT para obter essas informações. [Relatórios do Azure AD](active-directory-view-access-usage-reports.md) são categorizados como mostrado abaixo:

- **Relatórios de anomalia**: contêm eventos que descobrimos ser anormais de entrada. Nosso objetivo é Explique você tal atividade e permitem que você seja capaz de tomar uma decisão sobre se um evento é suspeito.
- **Relatório de aplicativo integrado**: fornece ideias para como aplicativos em nuvem estão sendo usados em sua organização. Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.
- **Relatórios de erros**: indicar erros que podem ocorrer ao provisionar contas para aplicativos externos.
- **Relatórios específicos de usuário**: Exibir dispositivo/entrada em dados de atividade de um usuário específico.
- **Logs de atividades**: contêm um registro de todos os eventos auditados as últimas 24 horas, últimos 7 dias, ou últimos 30 dias, bem como alterações de atividade do grupo e atividade de registro e redefinição de senha.

>[AZURE.TIP]
Outro relatório que também pode ajudar a equipe de resposta de incidente trabalhando em uma ocorrência é o relatório de [usuário com credenciais perdidas](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Este relatório revela qualquer correspondência entre esses lista de credenciais perdida e seu locatário.

Outros importantes relatórios internos no Azure AD que pode ser usado durante uma investigação de resposta a incidente e são:

- **Atividade de redefinição de senha**: forneça o administrador ideias para como ativamente redefinição de senha está sendo usada na organização.
- **Atividade de registro de redefinição de senha**: fornece ideias no qual os usuários tem registrado seus métodos para redefinição de senha, e quais métodos escolheram.
- **Atividade de grupo**: fornece um histórico de alterações no grupo (ex: usuários adicionados ou removidos) que foram iniciados no painel de acesso.

Além do recurso de relatórios de núcleo disponível no Azure AD Premium que podem ser aproveitados durante um processo de investigação de resposta de incidente, que pode também Aproveite o relatório de auditoria para obter informações, como:

- Alterações no membro da função (ex: usuário adicionado à função de Administrador Global)
- Atualizações de credencial (ex: alterações de senha)
- Gerenciamento de domínio (ex: verificar um domínio personalizado, removendo um domínio)
- Adicionando ou removendo aplicativos
- Gerenciamento de usuário (ex: adicionando, removendo, atualizando um usuário)
- Adicionando ou removendo licenças

Como as opções de resposta a incidente usam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para essa tarefa. Certifique-se de que você está aproveitando todas as opções disponíveis para cada cenário que requer que você usar o recurso de relatórios do Azure AD como parte do processo de resposta a incidente da sua empresa.

## <a name="next-steps"></a>Próximas etapas
[Determinar as tarefas de gerenciamento de identidade híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
