<properties
    pageTitle="Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory | Microsoft Azure"
    description="Fornece uma visão detalhada de como dispositivos Windows 10 podem utilizar Azure AD ingresse obter registrado no Active Directory do Azure."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory

## <a name="what-is-azure-active-directory-join"></a>O que é a participar do Azure Active Directory?
Azure Active Directory ingressar (Azure AD ingressar) é a funcionalidade que registra um dispositivo de empresa no Active Directory do Azure para habilitar o gerenciamento centralizado do dispositivo. Ele possibilita para usuários como funcionários e alunos para se conectar à nuvem enterprise através do Azure Active Directory. Isso permite implantações simplificadas do Windows e o acesso aos recursos e aplicativos organizacionais de qualquer dispositivo Windows, ambos corporativos e pessoalmente pertencentes (BYOD).

Junção do Azure AD destina-se a empresas que são primeiro/nuvem-somente na nuvem – normalmente pequenas e médias empresas que não têm uma infraestrutura do Windows Server Active Directory local. Que disse, Azure junção AD pode e também serão usadas pela grandes organizações em dispositivos que tenham incapazes de fazer uma associação de domínio tradicionais (dispositivos móveis, por exemplo), ou para os usuários que precisam principalmente para acessar o Office 365 ou outros aplicativos do Azure AD SaaS.

Embora a junção de domínio tradicionais ainda oferece que o melhor local experiência em dispositivos que são capazes de participar do domínio, Azure AD junção é adequada para dispositivos que não podem participar de domínio. Junção do Azure AD também é adequada para o gerenciamento de usuários na nuvem. Ele faz isso usando recursos de gerenciamento de dispositivo móvel, em vez de ferramentas de gerenciamento de domínio tradicionais como diretiva de grupo e System Center Configuration Manager (SCCM).

![Visão geral dos dispositivos corporativos e pessoais com o Active Directory local e o Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Por que devem empresas adotar Azure AD ingressar?

* **Empresas que são amplamente na nuvem**: se você tiver movido ou está mudando para um modelo no qual você está reduzindo o seu espaço de local e deseja operar mais na nuvem, participar do Azure AD poderia se beneficiar você. Talvez você criou contas do Azure AD manualmente ou por meio de sincronização do Active Directory local. De qualquer forma, você tem uma conta no Azure AD, e você pode usá-lo para entrar no Windows 10. Seus usuários podem ingressar em seus computadores ao Azure AD por meio de um da experiência de prontos (OOBE) ou no menu Configurações. Após ingressar, os usuários gostará único acesso sign-on (SSO) para os recursos de nuvem como o Office 365, em seus navegadores ou em aplicativos do Office.

* **Instituições de ensino**: um dos cenários que ouvimos sobre com frequência é que instituições de ensino tem dois tipos de usuário: professores e alunos. Membros do Corpo Docente são considerados membros de longo prazo da organização, para que a criação de contas locais para eles é desejável. Mas os alunos são shorter-term membros da organização e, portanto, podem ser gerenciados no Azure AD. Isso significa que a escala de diretório pode ser enviada para a nuvem em vez de armazenados no local. Isso também significa que os alunos podem entrar no Windows com suas contas do Azure AD e obter acesso aos recursos do Office 365, em seus navegadores ou em aplicativos do Office.

* **Empresas de varejo**: outro cenário que ouvimos sobre de clientes é desejo de gerenciar trabalhadores sazonais com mais facilidade.  Novamente, contas para funcionários de longo prazo, tempo integral geralmente são criadas como contas nas máquinas domínio local. Mas trabalhadores sazonais são membros shorter-term do organograma, portanto, é desejável para gerenciá-los onde licenças de usuário podem ser movidas com mais facilidade ao redor. Criar essas contas de usuário na nuvem com licenças do Office 365 permite que os usuários de obter as vantagens de entrar no Windows e aplicativos do Office com uma conta do Azure AD. Enquanto isso, você manter mais flexibilidade com suas licenças depois que eles saírem.
* **Outras empresas**: Embora você manter usuários no Active Directory local, você poderia ainda se beneficiar ter usuários Azure AD-ingressaram em. Isso ocorre porque o Azure AD oferece uma experiência simplificada de ingresso, gerenciamento de dispositivo eficiente, inscrição de gerenciamento de dispositivo móvel automáticas e capacidade de logon única para Azure AD e recursos locais.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Quais recursos participar do Azure AD oferta?
Com o Azure AD ingressar, você obtém o seguinte:

* **Auto provisionamento de dispositivos de propriedade corporativa**: com o Windows 10, os usuários podem configurar um novo dispositivo têm embalagem a experiência de prontos, sem envolvimento de TI.


* **Suporte para fatores de forma moderna**: Azure AD junção funciona em dispositivos que não têm o domínio tradicional participar de recursos.  


* **Suporte para contas organizacionais existentes**: os usuários não for mais necessário criar e manter uma uma conta da Microsoft para obter a melhor experiência em dispositivos emitidos da empresa, como faziam com o Windows 8. Eles podem usar suas contas de trabalho existente no Azure AD em vez disso. Para muitas organizações, isso basicamente significa que os usuários podem configurar e entrar no Windows com as mesmas credenciais que usar para acessar o Office 365.


* **Registro de gerenciamento de dispositivo móvel automáticas**: dispositivos podem inscritos automaticamente no gerenciamento de dispositivo móvel quando conectado ao Azure AD. Esse processo funciona com as soluções de gerenciamento de dispositivo móvel Microsoft Intune e parceiros. Quando gerenciamento de dispositivo é feito com o Intune, os administradores de TI podem monitor/gerenciar dispositivos Azure AD ingressaram junto com o domínio dispositivos no console de gerenciamento do SCCM.


* **Logon único para os recursos da empresa**: usuários aproveitam logon único da área de trabalho do Windows para aplicativos e recursos na nuvem, como o Office 365 e milhares de aplicativos de negócios que dependem Azure AD para autenticação por meio do [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Propriedade corporativa dispositivos que fazem parte de Azure AD também aproveite SSO aos recursos locais quando o dispositivo está em uma rede corporativa e de qualquer lugar quando esses recursos estão expostos pelo [Proxy de aplicativo do Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Roaming de estado de sistema operacional**: configurações de acessibilidade, sites, Wi-Fi senhas e outras configurações são sincronizadas em dispositivos corporativos sem a necessidade de uma conta da Microsoft.


* **Pronto para Enterprise da Windows Store**: A Windows Store dá suporte a aquisição de aplicativo e licenciamento com contas do Azure AD. As organizações podem aplicativos de licença de volume e disponibilizá-los para os usuários em sua organização.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Como diferentes dispositivos funcionam com o Azure AD ingressar?

| Dispositivo corporativo (associado ao domínio local)                                                                                                                                                                                         | Dispositivo corporativo (ingressado na nuvem)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Dispositivo pessoal                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Os usuários podem entrar no Windows com credenciais de trabalho (como fazem atualmente).                                                                                                                                                                        | Os usuários possam entrar Windows com credenciais de trabalho que são gerenciadas no Azure AD. Isso é relevante para dispositivos corporativos em três casos: 1) a organização não tiver o Active Directory no local (por exemplo, uma pequena empresa). 2) a organização não cria todas as contas de usuário no Active Directory (por exemplo, contas para os alunos, consultores ou trabalhadores sazonais não são criadas no Active Directory). 3) a organização tiver corporativos dispositivos que não podem ser associados a um domínio (local), como telefones ou tablets executando uma SKU Mobile (por exemplo, um dispositivo secundário direcionado a um andar de fábrica/varejo). Junção do Azure AD oferece suporte para participar de dispositivos corporativos para organizações gerenciados e federados. | Usuários entrar Windows com suas credenciais de conta da Microsoft pessoais (nenhuma alteração).                                                |
| Os usuários têm acesso às configurações móveis e a empresa da Windows Store. Esses serviços trabalhar com contas de trabalho e não exigem uma conta da Microsoft. Isso requer que as organizações para se conectar a seus do Active Directory local ao Azure AD.                                        | Os usuários podem fazer a configuração de autoatendimento. Eles podem percorrer a experiência de primeira execução (FRX) por meio de sua conta de trabalho como uma alternativa para tendo provisionar IT os dispositivos, embora ambos os métodos são suportados.                                                                                                                                                                                                                                                                                                                                                                             | Os usuários podem adicionar facilmente uma conta de trabalho que é gerenciada no Active Directory ou Azure AD.                                                      |
| Os usuários têm capacidade SSO da área de trabalho para trabalhar aplicativos, sites e recursos--incluindo recursos locais e aplicativos de nuvem que usam o Azure AD para autenticação.                                                                                                            | Dispositivos são automaticamente registrados no diretório corporativo (Azure AD) e automaticamente registrados no gerenciamento de dispositivo móvel. (Recurso do azure AD Premium).                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Os usuários têm a capacidade SSO entre aplicativos e sites/recursos com essa conta de trabalho.                                              |
| Os usuários podem adicionar suas contas pessoais do Microsoft para acessar seus arquivos e imagens pessoais sem afetar os dados da empresa. (Configurações roaming continuam a trabalhar com suas contas de trabalho.) A conta da Microsoft permite SSO e não está mais unidades móvel das configurações.  | Os usuários podem fazer uma redefinição de senha de autoatendimento (SSPR) em winlogon, significando que eles podem redefinir uma senha esquecida. (Recurso do azure AD Premium).                                                                                                                                                                                                                                                                                                                                                                                                                                   | Os usuários têm acesso à empresa da Windows Store para que eles possam adquirir e usar os aplicativos de linha de negócios em seus dispositivos pessoais. |                                                               |


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação de identidades sem senhas por meio do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
