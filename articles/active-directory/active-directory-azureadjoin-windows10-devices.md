<properties
    pageTitle="Usando dispositivos Windows 10 em seu local de trabalho | Microsoft Azure"
    description="Fornece um instantâneo de recursos para os usuários e IT, contrastantes as diferentes maneiras de um dispositivo pode ser provisionado e usado em uma empresa com o Windows 10."
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
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>Usando dispositivos Windows 10 em seu local de trabalho

Aplica-se a: PCs Windows 10

Windows 10 oferece três modelos para as organizações que permitem aos usuários acessar os recursos de trabalho de uma maneira conveniente e segura.

- **Participar do Active Directory do Azure** (Azure AD junção), para trabalhadores acessam recursos como o Office 365 principalmente na nuvem. Azure AD junção é trabalho autoatendimento provisionamento experiência que há de nova no Windows 10.
- **Associação de domínio**, para as organizações que possuem investimentos em aplicativos locais e recursos. Junção de domínio oferece uma experiência aprimorada no Windows 10 quando conectado ao Azure AD.
- **Uma nova experiência simplificada de BYOD**, para os usuários que deseja adicionar uma conta de trabalho ou na escola ao Windows e facilmente acessar recursos dispositivos pessoais.

A tabela a seguir apresenta um instantâneo dos recursos para os usuários e administradores de TI, contrastantes as diferentes maneiras de um dispositivo pode ser provisionado e usado em uma empresa com o Windows 10:

|                                                                                                                                                                 | Associação de domínio     | Junção do Azure AD | Dispositivo pessoal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows dispositivo entrar para contas de trabalho ou da escola.                                                                                                                      | Sim             | Sim           | Não              |
| Usuário do logon único (SSO) aos aplicativos do Office 365 e Azure AD. SSO é a capacidade de entrar no apenas uma vez para acessar recursos organizacionais. | Sim             | Sim           | Sim             |
| SSO do usuário aos aplicativos de Kerberos/NTLM.                                                                                                                                  | Sim             | Limitado       | Sim, por meio de VPN         |
| Autorização forte e conveniente entrar para contas de trabalho ou escola com o Microsoft Passport e Windows Hello.                                                                   | Sim             | Sim           | Sim             |
| Acesso a empresa da Windows Store com uma conta corporativa ou escolar (não uma conta da Microsoft).                                                                                    | Sim             | Sim           | Sim             |
| Compatível com Enterprise roaming das configurações do usuário em dispositivos com contas de trabalho ou da escola.                                                                                 | Sim             | Sim           | Sim             |
| A capacidade de restringir o acesso aos aplicativos organizacionais para dispositivos compatíveis com diretivas de dispositivo organizacional.                                                      | Sim             | Sim           | Sim             |
| Usuário autoatendimento provisionamento de dispositivos para "trabalho de praticamente qualquer lugar."                                                                                                | Não              | Sim           | Sim             |
| Capacidade de gerenciar dispositivos.                                                                                                                                       | Sim, por meio de GP/SCCM | Sim           | Sim             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Ingressar em uso dispositivos de propriedade do trabalho com Azure AD junção e domínio no Windows 10

Windows 10 oferece duas maneiras de dispositivos de propriedade do trabalho acessar os recursos de trabalho:

- Junção do Azure AD
- Associação de domínio

 Ambos podem ser opções válidas dependendo de necessidades e os requisitos de uma organização. Em alguns casos, as organizações podem beneficiar permitindo que ambos os métodos de implantação.

## <a name="when-to-use-azure-active-directory-join"></a>Quando usar a participar do Azure Active Directory

Azure AD junção é um novo trabalho de autoatendimento provisionamento experiência no Windows 10.  Ele é direcionado trabalhadores acessam os recursos de trabalho como o Office 365 principalmente na nuvem. É uma maneira simples para configurar computadores, tablets e telefones para a empresa. Dispositivos são gerenciados por meio de gerenciamento de dispositivo móvel, usando controles consistentes em plataformas Windows.

**Uso do Azure AD ingressar para qualquer um dos seguintes motivos**:

- Você deseja habilitar o autoatendimento provisionamento de dispositivos para trabalhadores em trânsito.
- Você fornece aos usuários pertencentes trabalho dispositivos móveis como telefones e tablets.
- Você deseja gerenciar um conjunto de usuários no Azure AD, em vez de no Active Directory, como trabalhadores sazonais, empreiteiros ou alunos.
- Você deseja fornecer recursos de ingresso para trabalhadores em escritórios da filial remota com infraestrutura local limitado.
- Você não tem um Active Directory local.

Algumas organizações usará Azure AD ingressar como a principal maneira para implantar dispositivos de propriedade do trabalho, especialmente à medida que eles migrar a maioria ou todos os seus recursos para a nuvem. Organizações híbrida com o Active Directory e o Azure AD também podem optar por implantar um método ou outro dependendo do usuário ou departamento.

Distritos escolares e universidades, por exemplo, poderão gerenciar a equipe do Active Directory e alunos no Azure AD. Algumas empresas talvez queira gerenciar escritórios remotos ou departamentos de vendas no Azure AD. Os métodos de junção de domínio e Azure AD junção podem ser usados em uma organização híbrido. Junção do Azure AD pode ser um ótimo complemento para participar de domínio para implantar dispositivos em um ambiente de trabalho.

**Se o acesso mais usual aos recursos da empresa é por meio da nuvem, sua organização pode aproveitar os benefícios adicionais se**:

- Você pode remover dependências de infraestrutura de identidade de local.
- Você pode simplificar o seu modelo de implantação de dispositivos, obtendo longe soluções de imagens, permitindo que a configuração de autoatendimento.
- Você pode usar o gerenciamento de dispositivo móvel para gerenciar todos os seus dispositivos entre plataformas diferentes.

Para obter mais informações sobre o Azure AD ingressar, consulte [recursos de extensão de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Quando usar associação de domínio (ou manter usá-lo)

Nos últimos 15 anos, muitas organizações usaram associação de domínio para conectar dispositivos de trabalho. Ele permite aos usuários entrar em seus dispositivos com seu trabalho Active Directory contas ou de estudante. Junção de domínio também permite TI centralmente e totalmente gerencie esses dispositivos. Organizações normalmente dependem imagens métodos para provisionar dispositivos e geralmente usam System Center Configuration Manager (SCCM) ou grupo de diretiva para gerenciá-los.

**Sua empresa deve usar associação de domínio (ou manter usá-lo) para qualquer um dos seguintes motivos**:

- Você tem aplicativos Win32 implantados para esses dispositivos que usam NTLM/Kerberos.
- Você exige GP ou SCCM/DCM gerenciar dispositivos.
- Você deseja continuar a usar soluções de imagens para configurar dispositivos para seus funcionários.

A **associação de domínio no Windows 10 também lhe fornece os seguintes benefícios quando conectado ao Azure AD**:

- Autenticação de dispositivo ligadas forte e conveniente entrar para contas de trabalho ou escola com o Microsoft Passport e Windows Hello.
- Acesso à empresa da Windows Store para dispositivos que usam o trabalho ou escola contas (nenhuma conta da Microsoft obrigatória).
- Compatível com Enterprise roaming das configurações do usuário em todos os dispositivos que usam contas corporativas ou de Estudante (nenhuma conta da Microsoft obrigatória).
- A capacidade de restringir o acesso aos aplicativos organizacionais para dispositivos que estão em conformidade com políticas de dispositivo organizacional.

Para obter mais informações sobre o Azure AD ingressar, consulte [recursos de extensão de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Habilitar o ingresso de dispositivos de propriedade pessoal para trabalho ou escola

Para dar suporte BYOD na empresa, o Windows 10 dá ao usuário a capacidade de adicionar uma conta de trabalho ou da escola ao seu computador, tablet ou telefone. Depois que o usuário adiciona uma conta corporativa ou escolar, o dispositivo é registrado no Azure AD e opcionalmente registrado no sistema de gerenciamento de dispositivo móvel que a organização tiver configurado. Diretório mostrará esses dispositivos como 'Registrado' versus 'Azure AD unidas'. IT administraors pode aplicar políticas diferentes com base nessas informações, fornecendo um toque mais claro em dispositivos de propriedade pessoal que em dispositivos de propriedade do trabalho se desejado.

Os usuários podem adicionar um trabalho ou da escola conta ao seu dispositivo pessoal muito convenientemente. Eles podem fazer isso ao acessar um aplicativo de trabalho pela primeira vez ou eles podem fazê-lo manualmente por meio do menu Configurações. Essa conta fornecerá SSO aos recursos organizacionais.

Para saber mais sobre Azure AD ingressar, consulte [conectar dispositivos de domínio para o Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Habilitar associação de domínio ou Azure AD

Todos os métodos descritos anteriormente (associação de domínio, Azure AD ingressar e adicionar trabalho ou escola conta) tem pontos de entrada na experiência do usuário do Windows 10. No entanto, exigem um administrador de TI habilitar a funcionalidade na infraestrutura antes da experiência funcionarão.

## <a name="requirements-for-deploying-azure-ad-join"></a>Requisitos para implantação do Azure AD ingressar

Para implantar o Azure AD ingressar para qualquer conjunto de usuários, você precisa do seguinte:

- Uma assinatura do Azure AD.
- Uma assinatura do Azure AD Premium, como o dispositivo móvel gerenciamento inscrição automática, se você precisar de mais recursos.
- Gerenciamento de dispositivo móvel – por exemplo, uma assinatura do Microsoft Intune, gerenciamento de dispositivo móvel para o Office 365 ou qualquer um dos fornecedores de gerenciamento de dispositivo móvel parceiro que se integram ao Azure AD. (Consulte a [seção Perguntas Frequentes](#frequently-asked-questions) próximo ao final deste artigo para obter mais informações).

Se suas instalações híbridos, recomendamos que você implantar Azure AD Connect para estender o diretório local ao Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Requisitos para usar o domínio ingressar com o Azure AD

Junção de domínio continuará a funcionar como tem sempre. No entanto, para obter os benefícios do Azure AD você precisará do seguinte:

- Uma assinatura do Azure AD.
- Uma implantação do Azure AD Connect para estender o diretório local ao Azure AD.
- Uma política que permite que os dispositivos domínio têm acesso condicional ao Azure AD.
- Uma política que permite o acesso a dispositivos "domínio" Se você quiser ser capaz de restringir o acesso para alguns dispositivos.
- System Center Configuration Manager versão 1509 para Technical Preview, ativar regras para exigir que dispositivos incompatíveis. (Consulte a documentação do TechNet e a postagem de blog).

Para obter mais informações sobre a associação de domínio no Windows 10, consulte [conectar dispositivos de domínio para o Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Requisitos para usar BYOD e "Adicionar uma conta corporativa ou escolar"

Para habilitar "trazer seu próprio dispositivo" (BYOD) com contas corporativas ou de estudante, você precisa fazer o seguinte:

- Uma assinatura do Azure AD.
- Uma assinatura do Azure AD Premium, como o dispositivo móvel gerenciamento inscrição automática, se você precisar de mais recursos.

## <a name="requirements-for-using-microsoft-passport"></a>Requisitos para usar o Microsoft Passport

Para habilitar o Microsoft Passport, você precisará do seguinte:

- Uma chave infraestrutura pública (PKI) para o suporte de autenticação baseada em certificado que usa o Microsoft Passport.
- Uma assinatura de Intune para suporte a autenticação baseada em certificado que usa o Microsoft Passport para participar do Azure AD e contas de trabalho ou da escola.
- Gerenciador de configuração do System Center versão 1509 para Technical Preview (consulte a documentação do TechNet e a postagem de blog) para o suporte de autenticação baseada em certificado que usa o Microsoft Passport de junção de domínio.
- Uma política para habilitar o Microsoft Passport na organização.

Como uma alternativa para usar uma PKI, você pode habilitar baseada em chave Microsoft Passport fazendo o seguinte:

- Implante o Windows Server 2016 "Visualização de produção 1" controladores de domínio (sem necessidade de níveis funcionais de domínio ou floresta; vários controladores de domínio para cada site do Active Directory será suficiente de serviço de redundância).
- Definir a política para habilitar o Microsoft Passport na organização.

Para obter mais informações sobre o Microsoft Passport e Windows Hello no Windows 10, consulte < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Quais produtos de gerenciamento de dispositivo móvel do parceiro integram com o Azure AD?

Os seguintes produtos de fornecedor integram com o Azure AD para o registro unificado e acesso condicional no Windows 10:

- AirWatch pela VMware
- Citrix Xenmobile
- Gerenciador de celular Lightspeed
- Gerenciamento de dispositivo móvel do local SOTI
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>E o local de trabalho ingressar no Windows 10?
Junção do local de trabalho foi usada no Windows 8.1 para habilitar BYOD. No Windows 10, BYOD está habilitado por meio de "Adicionar um trabalho ou escola conta" conforme explicado anteriormente neste documento. Para organizações que não integram o gerenciamento de dispositivo móvel com o Azure AD, os usuários podem inscrever o dispositivo em gerenciamento manualmente por meio de **configurações** > **contas** > **acesso de trabalho**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>Os usuários podem se conectar a conta da Microsoft para sua conta de domínio no Windows 10?
Não no Windows 10. No Windows 8.1, usuários de domínio dispositivos poderiam "conectar" a conta da Microsoft (por exemplo, Hotmail, Live, Outlook, Xbox, etc.) para sua conta de domínio para habilitar determinadas experiências como SSO para serviços ao vivo, o uso da Windows Store e roaming das configurações do usuário nos dispositivos. No Windows 10, a conta da Microsoft "conectar" funcionalidade foi retirada. O usuário pode adicionar uma ou mais contas da Microsoft como contas adicionais para habilitar SSO para serviços de consumidor como Windows Store. Isso é feito nas **configurações** > **contas** > **sua conta**.

Os usuários que estiver atualizando de dispositivos de domínio do Windows 8.1 e quem tem uma conta da Microsoft conectado, terão automaticamente a conta da Microsoft conectada adicionada à lista de contas adicionais usarem.


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio de participar do Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos de domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o Azure AD ingressar](active-directory-azureadjoin-setup.md)
