<properties
    pageTitle="Configurações e dados roaming FAQ | Microsoft Azure"
    description="Fornece respostas para algumas perguntas aos administradores de TI podem ter sobre configurações e sincronização de dados de aplicativo."
    services="active-directory"
    keywords="Enterprise estado configurações móveis, nuvem do windows, perguntas frequentes no enterprise estado roaming"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="settings-and-data-roaming-faq"></a>Configurações e dados roaming perguntas Frequentes

Este tópico responde algumas perguntas que os administradores de TI podem ter sobre configurações e sincronização de dados de aplicativo.

## <a name="what-data-roams"></a>Quais dados roaming?
**Configurações do Windows**: as configurações do PC que são criadas no sistema operacional Windows. Em geral, estas são as configurações que personalize seu PC, e eles incluem as seguintes categorias genéricas:

- *Tema*, que inclui recursos, como configurações de tema e barra de tarefas da área de trabalho.
- *Configurações do Internet Explorer*, incluindo recentemente abertos guias e Favoritos.
- *Configurações do navegador de borda*, como Favoritos e lista de leitura.
- *Senhas*, incluindo senhas de Internet, perfis de Wi-Fi e outras pessoas.
- *Preferências de idioma*, que incluem configurações para layouts de teclado, idioma do sistema, data e hora e muito mais.
- *Facilidade de recursos do access*, como o tema de alto contraste, Narrator e Lupa.
- *Configurações de outras janelas*, como configurações de prompt de comando e lista de aplicativos.


**Dados de aplicativos**: aplicativos do Windows Universal podem gravar dados de configurações para uma pasta móvel e todos os dados gravados nesta pasta serão automaticamente sincronizados. Ele é o desenvolvedor do aplicativo individuais para criar um aplicativo para tirar proveito desse recurso. Para obter mais detalhes sobre como desenvolver um Universal do Windows, que usa móveis, consulte o [armazenamento de appdata API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e o [blog do Windows 8 appdata móvel desenvolvedor](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Qual conta é usada para sincronização de configurações?
No Windows 8 e Windows 8.1, a sincronização de configurações sempre usado contas da Microsoft consumidor. Os usuários do Enterprise tinham a capacidade de se conectar a uma conta da Microsoft para sua conta de domínio do Active Directory para obter acesso a sincronização de configurações. No Windows 10, isso conectado conta da Microsoft funcionalidade está sendo substituída com uma estrutura de conta primário e secundário.

A conta principal é definida como a conta usada para entrar no Windows. Isso pode ser uma conta da Microsoft, uma conta do Azure Active Directory (AD Azure), uma conta do Active Directory local ou uma conta local. Além da conta primária, Windows 10 usuários podem adicionar uma ou mais contas de nuvem secundário para seus dispositivos. Uma conta secundária é geralmente uma conta da Microsoft, uma conta do Azure AD ou alguns outra conta como Gmail ou Facebook. Estas contas secundárias fornecem acesso a serviços adicionais, como o logon único e Windows Store, mas elas não são capazes de alimentar sincronizar configurações.

No Windows 10, apenas a conta principal para o dispositivo pode ser usada para sincronização de configurações (consulte [como atualizar de sincronização de configurações de conta da Microsoft no Windows 8 Azure AD sincronização de configurações no Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#How-do-I-upgrade-from-Microsoft-account-settings-sync-in-Windows-8-to-Azure-AD-settings-sync-in Windows-10?)).

Dados nunca é misto entre as contas de usuário diferentes no dispositivo. Há duas regras para sincronização de configurações:

- Configurações do Windows sempre serão móvel com a conta primária.
- Dados de aplicativo serão marcados com a conta usada para adquirir o aplicativo. Somente aplicativos marcados com a conta primária serão sincronizados. A propriedade de aplicativo marcação é determinado quando um aplicativo é carregado lado através da Windows Store ou o gerenciamento de dispositivo móvel (MDM).

Se o proprietário de um aplicativo não pode ser identificado, ele será móvel com a conta primária. Se um dispositivo é atualizado do Windows 8 ou Windows 8.1 para o Windows 10, todos os aplicativos serão marcados como adquirida pela conta da Microsoft. Isso ocorre porque a maioria dos usuários adquirir aplicativos por meio da loja do Windows e houve sem suporte da Windows Store para contas do Azure AD anteriores ao Windows 10. Se um aplicativo é instalado por meio de uma licença offline, o aplicativo será marcado usando a conta primária no dispositivo.

>[AZURE.NOTE]  
> Dispositivos Windows 10 que são pertence à empresa e estão conectados ao Azure AD já não podem se conectar suas contas do Microsoft para uma conta de domínio. A capacidade de se conectar a uma conta da Microsoft para uma conta de domínio e ter a sincronização de dados da todos os usuários conta da Microsoft (ou seja, a conta da Microsoft roaming através da conta Microsoft conectada e a funcionalidade do Active Directory) é removida do Windows 10 dispositivos que fazem parte de um ambiente do Active Directory ou Azure AD conectado.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Como faço para atualizar de sincronização de configurações de conta da Microsoft no Windows 8 ao Azure AD sincronização de configurações no Windows 10?
Se você fazem parte de domínio do Active Directory executando o Windows 8 ou Windows 8.1 com uma conta conectada da Microsoft, você sincronizará configurações por meio de sua conta da Microsoft. Após a atualização para o Windows 10, você continuará a sincronizar configurações de usuários por meio de conta da Microsoft, desde que você é um usuário de domínio e domínio do Active Directory não se conectar com o Azure AD.

Se o domínio do Active Directory local se conectar com o Azure AD, seu dispositivo tentará sincronizar configurações usando o conectada conta Azure AD. Se o administrador do Azure AD não habilitar Enterprise estado móveis, seu conectada conta Azure AD serão mais sincronizados configurações. Se você for um usuário do Windows 10 e você entra com uma identidade do Azure AD, você começará a sincronizar configurações do windows, assim que a sincronização de configurações de via Azure AD permite que o administrador.

Se você armazenou dados pessoais em seu dispositivo corporativo, você deve estar ciente de que os dados de aplicativo e sistema operacional Windows começará a sincronizar ao Azure AD. Isso tem as seguintes implicações:

- Suas configurações de conta pessoais do Microsoft serão incorretos exceto as configurações no seu trabalho ou da escola contas do Azure AD. Isso ocorre porque a conta da Microsoft e as configurações do Azure AD sincronizar agora estiver usando contas separadas.
- Dados pessoais como senhas Wi-Fi, as credenciais da web e Favoritos do Internet Explorer que anteriormente foram sincronizados por meio de uma conta da Microsoft conectada serão sincronizados por meio do Azure AD.


## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Como fazer a conta da Microsoft e o trabalho de interoperabilidade do Azure AD Enterprise estado móvel?
Nas edições de novembro de 2015 ou posterior do Windows 10, Roaming de estado de empresa tem suporte apenas para uma única conta de cada vez. Se você entrar no Windows, usando um trabalho ou escola conta do Azure AD, todos os dados serão sincronizados por meio do Azure AD. Se você entrar no Windows usando uma conta pessoal da Microsoft, todos os dados serão sincronizados por meio da conta da Microsoft. Appdata universal entrará em roaming usando somente a entrada conta principal no dispositivo e, em seguida, ela entrará em roaming somente se a licença do aplicativo pertence a conta principal. Universal appdata para os aplicativos pertencentes a qualquer contas secundárias não será sincronizado.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Sincronizar as configurações para contas do Azure AD de vários locatários?
Ao Azure AD várias contas de diferentes Azure AD locatários no mesmo dispositivo, você deve atualizar o registro do dispositivo para se comunicar com o Azure Rights Management (Azure RMS) para cada locatário Azure AD.  

1. Localize o GUID para cada locatário Azure AD. Abra o portal do clássico Azure e selecione um locatário do Azure AD. GUID do locatário é na URL na barra de endereços do navegador. Por exemplo: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Depois de ter o GUID, você precisará adicionar a chave do registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<Identificação GUID de locatários >**.
Da chave **locatário GUID de identificação** , crie uma nova cadeia de caracteres múltipla value (registro-MULTI-SZ) chamada **AllowedRMSServerUrls**. Para seus dados, especifique o licenciamento URLs de ponto de distribuição dos outros locatários Azure que acessa o dispositivo.
3. Você pode encontrar as URLs de ponto de distribuição licenciamento executando o cmdlet **Get-AadrmConfiguration** . Se os valores para o **LicensingIntranetDistributionPointUrl** e **LicensingExtranetDistributionPointUrl** forem diferentes, especifique os dois valores. Se os valores são iguais, especifique o valor de apenas uma vez.


## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quais são as opções de configurações móvel para aplicativos de área de trabalho do Windows existentes?
Roaming funciona somente para os aplicativos do Windows Universal. Há duas opções disponíveis para a habilitação móveis em um aplicativo de área de trabalho existente do Windows:

- A [Área de trabalho ponte](http://aka.ms/desktopbridge) ajuda você a trazer seus aplicativos de área de trabalho existentes do Windows para a plataforma Windows Universal. A partir daqui, alterações mínimas de códigos será necessárias para tirar proveito da mobilidade de dados de aplicativo do Azure AD. A ponte de área de trabalho fornece seus aplicativos com uma identidade de aplicativo, que é necessário habilitar dados de aplicativo móvel para aplicativos de área de trabalho existentes.
- [Virtualização de experiência do usuário (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) ajuda você a criar um modelo de configurações personalizadas para aplicativos de desktop do Windows existentes e habilitar roaming para aplicativos Win32. Esta opção não exige o desenvolvedor do aplicativo alterar o código do aplicativo. UE-V está limitada aos locais do Active Directory roaming para os clientes que compraram o pacote de otimização de área de trabalho do Microsoft.

Os administradores podem configurar UE-V para mover dados de aplicativo da área de trabalho do Windows, alterando o roaming de configurações do sistema operacional Windows e dados de aplicativo Universal por meio de [políticas de grupo de UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), incluindo:

- Transitar política de grupo de configurações do Windows
- Não sincronizar a política de grupo de aplicativos do Windows
- Internet Explorer roaming na seção aplicativos

No futuro, a Microsoft pode investigar maneiras de fazer UE-V profundamente integrada ao Windows e estender UE-V para transitar configurações através da nuvem do Azure AD.


## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Pode armazenar dados e configurações sincronizadas no local?
Enterprise estado Roaming armazena todos os dados sincronizados na nuvem Azure. UE-V oferece um local roaming solução.

## <a name="who-owns-the-data-thats-being-roamed"></a>Quem possui os dados que estão sendo transferidos?
As empresas próprias dados mobilidade via Roaming de estado de empresa. Dados são armazenados em um data center Azure. Todos os dados de usuário são criptografados em trânsito e inativos na nuvem usando o Azure RMS. Esse é um aperfeiçoamento comparado a sincronização de configurações baseadas em conta da Microsoft, que criptografa somente determinados dados confidenciais, como credenciais de usuário antes de deixar o dispositivo.

Microsoft está comprometida em proteger os dados do cliente. Dados de configurações de um usuário empresarial são criptografados automaticamente pelo Azure RMS antes de deixar um dispositivo com Windows 10, para que nenhum outro usuário possa ler esses dados. Se sua organização tiver uma assinatura paga do Azure RMS, você pode usar outros recursos do Azure RMS, como controlar e revogar documentos, automaticamente proteger emails que contêm informações confidenciais e gerenciar suas próprias chaves (a "trazer a sua própria chave" solução, também conhecido como BYOK). Para obter mais informações sobre esses recursos e como Azure RMS funciona, consulte [o que é Azure Rights Management](https://technet.microsoft.com/jj585026.aspx).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Para gerenciar a sincronização de um aplicativo específico ou configuração?
No Windows 10, não há nenhuma configuração MDM ou política de grupo para desabilitar o roaming para um aplicativo individual. Administradores de inquilinos podem desabilitar a sincronização de appdata para todos os aplicativos em um dispositivo gerenciado, mas não há nenhum exercer um melhor controle em um nível por aplicativo ou aplicativo de dentro.

## <a name="how-can-i-enable-or-disable-roaming"></a>Como habilitar ou desabilitar roaming?
No aplicativo **configurações** , vá para **contas** > **sincronizar suas configurações**. Nesta página, você pode ver qual conta está sendo usada para transitar configurações, e você pode habilitar ou desabilitar grupos individuais de configurações para mobilidade.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>O que é recomendação da Microsoft para habilitar roaming no Windows 10?
A Microsoft tem algumas configurações diferentes roaming soluções disponíveis, incluindo perfis de usuário móvel, UE-V e Enterprise estado móvel.  A Microsoft está comprometida em oferecer um investimento no Enterprise estado Roaming em futuras versões do Windows. Se sua organização não está pronto ou à vontade para movimentação de dados para a nuvem, em seguida, recomendamos que você use UE-V como sua principal tecnologia móvel. Se sua organização exige roaming suporte para aplicativos de área de trabalho do Windows existentes, mas está ansioso para mover para a nuvem, recomendamos que você use UE-V e Enterprise estado móveis. Embora UE-V e Enterprise estado Roaming são muito semelhantes tecnologias, elas não são mutuamente exclusivas. Eles complementam uns aos outros para ajudar a garantir que sua organização fornece os serviços móveis que precisam de seus usuários.  

Ao usar Enterprise estado Roaming e UE-V, aplicam as seguintes regras:

- Enterprise estado Roaming é o principal agente de móvel no dispositivo. UE-V está sendo usado para suplementar o "espaço Win32".
- UE-V roaming de configurações do Windows e os dados de aplicativo UWP modernos deve ser desabilitado quando usando o grupo de UE-V diretivas. Estes já estão cobertos por móvel de estado de empresa.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Como Enterprise estado Roaming suporta (VDI) virtual desktop infrastructure?
Enterprise estado Roaming é suportado nos SKUs de clientes do Windows 10, mas não no servidor SKUs. Se um cliente máquina virtual está hospedado em um computador de hipervisor e remotamente entrar na máquina virtual, seus dados entrará em roaming. Se vários usuários compartilham o mesmo sistema operacional e os usuários remotamente entrar em um servidor para uma experiência de área de trabalho completa, o roaming pode não funcionar. O último cenário baseado em sessão não é suportado oficialmente.


## <a name="what-happens-when-my-organization-purchases-azure-rms-after-using-roaming"></a>O que acontece quando minha organização adquire Azure RMS após usar roaming?
Se sua organização já estiver usando roaming no Windows 10 com a assinatura gratuita do uso limitado de Azure RMS, adquirir uma assinatura paga do Azure RMS não terá qualquer impacto sobre a funcionalidade do recurso móvel e nenhuma alteração de configuração será necessário pelo seu administrador de TI.

## <a name="known-issues"></a>Problemas conhecidos

- Se você tentar entrar no seu dispositivo Windows usando um cartão inteligente ou virtual cartão inteligente, a sincronização de configurações interromperá trabalho. Atualizações futuras do Windows 10 podem resolver esse problema.
- Você precisará a atualização cumulativa de julho para Windows 10 (build 10586.494 ou superior) para sincronizar para trabalhar de favoritos do Internet Explorer.
- Dados protegidos com proteção de informações do Windows não serão mais sincronizados por meio de Roaming do Enterprise estado. Além disso, máquinas que possuem proteção de informações do Windows habilitada não terão sincronizar de tema. 
- Sob certas condições, Enterprise estado Roaming pode falhar ao sincronizar dados se a autenticação multifator Azure está configurada.
    - Se seu dispositivo estiver configurado para exigir [Autenticação multifator](multi-factor-authentication.md) no portal do Azure Active Directory, você pode falhar ao sincronizar configurações ao entrar em um dispositivo Windows 10 usando uma senha. Esse tipo de configuração de autenticação multifator destina-se a proteger uma conta de administrador Azure. Os usuários Admin ainda poderá sincronizar entrando em seus dispositivos Windows 10 com o [Microsoft Passport para trabalho](active-directory-azureadjoin-passport.md) PIN ou realizando autenticação multifator ao acessar outros serviços Azure como o Office 365.
    - A sincronização pode falhar se o administrador configura a política de acesso condicional de autenticação multifator Active Directory Federation Services e o token de acesso do dispositivo expira.  Certifique-se de que você entrar e sair usando o PIN do [Microsoft Passport para trabalho](active-directory-azureadjoin-passport.md) ou concluir autenticação multifator ao acessar outros serviços Azure como o Office 365.

- Se uma máquina for domínio com o registro automático para dispositivos do Active Directory do Azure, ele pode enfrentar falhas de sincronização máquina destina externo longos períodos de tempo, e não consegue concluir a autenticação de domínio. Para resolver esse problema, conecte o computador a uma rede corporativa, para que possa retomar a sincronização.


## <a name="related-topics"></a>Tópicos relacionados
- [Visão geral do Enterprise estado móvel](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitar o estado da empresa roaming no Active Directory do Azure](active-directory-windows-enterprise-state-roaming-enable.md)
- [Política de grupo e configurações de MDM para sincronização de configurações](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Referência de configurações móvel do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
