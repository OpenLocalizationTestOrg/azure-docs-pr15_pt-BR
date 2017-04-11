<properties
    pageTitle="# Configurar o registro de dispositivos automáticas para dispositivos de domínio associado do Windows 7 | Microsoft Azure"
    description="Etapas para configurar seu domínio do Windows 7 ingressou dispositivos para registrar automaticamente com o Azure AD. e etapas para implantar o pacote de software de registro do dispositivo em seu domínio do Windows 7 unidas dispositivos usando um sistema de distribuição de software como o System Center Configuration Manager."
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
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Configurar o registro de dispositivos automáticas para dispositivos de domínio associado do Windows 7

Como um administrador de TI, você pode configurar seus dispositivos de domínio associado do Windows 7 para registrar automaticamente com o Azure AD. Para fazer isso, você deve implantar o pacote de software de registro do dispositivo para seu domínio do Windows 7 ingressou dispositivos usando um sistema de distribuição de software como o System Center Configuration Manager. Certifique-se de ler e concluir os pré-requisitos listados no registro de dispositivo automático com dispositivos Azure Active Directory para Windows domínio.

>[AZURE.NOTE]
 Para mais recentes instruções sobre como configurar o registro de dispositivo automática, veja [como configurar o registro automático de domínio do Windows unidas dispositivos com o Active Directory do Azure](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Instalando o pacote de software de registro de dispositivo no Windows 7 integrado ao domínio dispositivos

Registro de dispositivo para o Windows 7 está disponível como um [pacote MSI para download](https://connect.microsoft.com/site1164). O pacote deve ser instalado no Windows 7 máquinas que fazem parte de um domínio do Active Directory. Você deve implantar o pacote usando um sistema de distribuição de software como o System Center Configuration Manager. O pacote MSI suporta as opções de instalação silencioso padrão usando o /quiet parâmetro.
O pacote de software está disponível para download no [site do Microsoft Connect](https://connect.microsoft.com/site1164). Aqui você pode selecionar e baixar participar do local de trabalho para o Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Junção de local de trabalho com o Active Directory do Azure
Registro de dispositivos para dispositivos de domínio associado do Windows 7 não exigem ou incluir uma interface do usuário. Depois de instalado na máquina, qualquer usuário do domínio que fizer na máquina será automaticamente e silenciosamente registrado com um objeto de dispositivo no Azure AD. Haverá um objeto de dispositivo no Azure AD para cada usuário registrado do dispositivo físico.

O instalador cria uma tarefa agendada no sistema que é executado no contexto do usuário e é disparado em acesso do usuário. A tarefa silenciosamente registra o usuário e o dispositivo com o Azure AD após sinais de usuário for concluído.
A tarefa agendada pode ser encontrada na biblioteca do Agendador de tarefas em **Microsoft** > **Participar do local de trabalho**.
A tarefa será executada e registrar todos e usuários do Active Directory que entrar na máquina.
A ilustração a seguir lista o processo passo a passo para o registro de automática do dispositivo.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. (Operador de informações) logon do usuário para um computador de cliente do Windows 7 usando credenciais de domínio do Active Directory.
1. A tarefa agendada participar do local de trabalho é executada.
1. O usuário é autenticado silenciosamente com o AD FS usando a autenticação integrada do Windows.
1. O PC com Windows 7 está registrado para o usuário no Azure AD.
1. Um objeto de dispositivo e um certificado é criado no Azure AD. O objeto representa o user@device.
1. O certificado de participar do local de trabalho está armazenado na máquina.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Cancelando o registro de seu domínio do Windows 7 unidas dispositivos

Você pode optar por cancelar o registro de seus dispositivos Windows 7 de domínio associado, fazendo o seguinte: desinstalar o local de trabalho ingressar pacote de software do seu domínio do Windows 7 unidas dispositivos usando um sistema de distribuição de software como o System Center Configuration Manager.

Em seguida, abra um prompt de comando no computador Windows 7 e execute o seguinte comando para cancelar o registro do dispositivo:

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Este comando deve ser executado no contexto de cada usuário do domínio que assinou na máquina.
Dispositivos de domínio associado do Visualizador de eventos e erros do Windows 7.

O Log de eventos do Windows no Windows 7 computador exibirá mensagens relacionadas para participar do local de trabalho. Você pode localizar mensagens para eventos de com e sem êxito participar do local de trabalho. O Log de eventos podem ser encontrados no Visualizador de eventos em Logs de aplicativos e serviços > Microsoft local de trabalho ingressar.

## <a name="additional-topics"></a>Tópicos adicionais

- [Visão geral do Azure Active Directory para o registro de dispositivos](active-directory-conditional-access-device-registration-overview.md)
- [Registro de dispositivo automática com dispositivos de Azure Active Directory for Windows Domain-Joined](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar o registro de dispositivos automáticas para dispositivos de domínio associado do Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registro de dispositivo automática com dispositivos de domínio do Azure Active Directory para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
