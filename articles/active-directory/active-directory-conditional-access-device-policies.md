<properties
    pageTitle="Políticas de dispositivo de acesso condicional para serviços do Office 365 | Microsoft Azure"
    description="Para obter detalhes sobre como baseados em dispositivos condições controlam o acesso aos serviços do Office 365. Enquanto os operadores de informações (IWs) deseja acessar o Office 365 serviços como o Exchange e o SharePoint Online no trabalho ou escola de seus dispositivos pessoais, o seu administrador de TI quer o acesso a ser secure.IT administradores podem provisionar diretivas de dispositivo de acesso condicional para proteger recursos corporativos, ao mesmo tempo permitindo IWs em dispositivos compatíveis para acessar os serviços."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>
# <a name="conditional-access-device-policies-for-office-365-services"></a>Políticas de dispositivo de acesso condicional para serviços do Office 365

O termo "condicional acesso" tem muitas condições associadas a ele como o usuário autenticado multifator, autenticado dispositivo, dispositivo compatível etc. Este tópico principalmente focusses em condições baseada em dispositivo para controlar o acesso aos serviços do Office 365. Enquanto os operadores de informações (IWs) deseja acessar o Office 365 serviços como o Exchange e o SharePoint Online no trabalho ou escola de seus dispositivos pessoais, o seu administrador de TI quer o acesso seguro. IT administradores podem provisionar diretivas de dispositivo de acesso condicional para proteger recursos corporativos, ao mesmo tempo permitindo IWs em dispositivos compatíveis para acessar os serviços. Políticas de acesso condicional para o Office 365 podem ser configuradas do portal do Microsoft Intune acesso condicional.

Azure Active Directory impõe políticas de acesso condicional para proteger o acesso aos serviços do Office 365. Um administrador locatário pode criar uma política de acesso condicional que bloqueia um usuário em um dispositivo não compatível acessem um serviço do Office 365. O usuário deve atender às políticas de dispositivo da empresa antes de acesso pode ser concedido ao serviço. Como alternativa, o administrador também pode criar uma política que exige que os usuários apenas registrar seus dispositivos para obter acesso a um serviço do Office 365. Políticas de podem ser aplicadas a todos os usuários de uma organização, ou limitadas a alguns grupos de destino e aprimoradas para incluir grupos de destino adicional.

É um pré-requisito para aplicar políticas de dispositivo para usuários registrar seus dispositivos com o serviço de registro de dispositivos do Azure Active Directory. Você pode optar por habilitar a autenticação multifator (MFA) para o registro de dispositivos com o serviço de registro de dispositivos do Azure Active Directory. MFA é recomendado para o serviço de registro de dispositivos do Azure Active Directory. Quando MFA está habilitado, os usuários registrar seus dispositivos com o serviço de registro de dispositivos do Azure Active Directory estão desafio para a segunda autenticação de fator.

##<a name="how-does-conditional-access-policy-work"></a>Como funciona a política de acesso condicional?

Quando um usuário solicita acesso ao serviço do Office 365 de uma plataforma de dispositivo compatível, Azure Active Directory autentica o usuário e o dispositivo do qual o usuário inicia a solicitação; e concede acesso ao serviço somente quando o usuário está em conformidade com a política definida para o serviço. Os usuários que não têm seus sobrenomes recebem corretivas instruções sobre como se inscrever e se tornarem compatíveis para acessar os serviços do Office 365 corporativos. Usuários em dispositivos iOS e Android deverão registrar seus dispositivos usando o aplicativo de Portal da empresa. Quando um usuário registra seu dispositivo, o dispositivo é registrado no Active Directory do Azure e registrado para conformidade e gerenciamento de dispositivo. Os clientes devem usar o serviço de registro de dispositivos do Azure Active Directory em conjunto com o Microsoft Intune para habilitar o gerenciamento de dispositivo móvel para o serviço do Office 365. Registro do dispositivo é um pré-requisito para usuários nos serviços do access Office 365 quando as políticas de dispositivo são aplicadas.

Quando um usuário registra seu dispositivo com êxito, o dispositivo se torna confiável. Active Directory do Azure fornece Single Sign-On para acessar aplicativos da empresa e impõe a política de acesso condicional para conceder acesso a um tempo de serviço não apenas o primeiro o usuário solicitar acesso, mas toda vez que o usuário solicitar para renovar o acesso. O usuário será negado acesso aos serviços quando credenciais de entrada são alteradas, dispositivo é perdido/roubado ou a política não for atendida no momento da solicitação para renovação.

## <a name="deployment-considerations"></a>Considerações de implantação:
Deve usar o serviço de registro de dispositivos do Azure Active Directory para o registro do dispositivo.

Quando os usuários devem ser autenticadas no local, Active Directory Federation Services (AD FS) (1,0 e acima) é necessários. Autenticação multifator para participar do local de trabalho falhará quando o provedor de identidade não é capaz de autenticação multifator. Por exemplo, o AD FS 2.0 não é multifator capacidade de autenticação. Administração de locatário deve garantir que o local do AD FS é capaz de MFA e um método MFA válido está habilitado, antes de habilitar MFA no serviço de registro de dispositivos do Azure Active Directory. Por exemplo, o AD FS no Windows Server 2012 R2 tem recursos MFA. Você também deve habilitar um método de autenticação adicionais válido (MFA) no servidor do AD FS antes de habilitar MFA no serviço de registro de dispositivos do Azure Active Directory. Para obter mais informações sobre métodos MFA com suporte no AD FS, consulte Configurar métodos de autenticação adicionais do AD FS.

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

P: qual é a política padrão de exclusão para plataformas de dispositivo sem suporte?

R: no momento, políticas de acesso condicional são impostas seletivamente usuários em dispositivos iOS e Android. Aplicativos em outras plataformas de dispositivo são, por padrão, afetado pela política de acesso condicional para dispositivos iOS e Android. Administração de locatário, no entanto, poderá substituir a política global para não permitir o acesso aos usuários em plataformas sem suporte.
É no roteiro para estender a política de acesso condicional a usuários em outras plataformas, incluindo o Windows.

P: quando a política de acesso condicional para serviços do Office 365 será estendida para aplicativos baseado em navegador (por exemplo, OWA, SharePoint baseadas em navegador).

R: no momento, condicional acesso aos serviços do Office 365 está limitado aos aplicativos sofisticados no dispositivo. É no roteiro para estender a política de acesso condicional a usuários acessando os serviços de navegadores.
