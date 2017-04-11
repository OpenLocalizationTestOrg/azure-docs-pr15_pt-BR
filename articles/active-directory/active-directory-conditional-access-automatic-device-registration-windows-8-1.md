<properties
    pageTitle="Configurar o registro de dispositivos automáticas para dispositivos de domínio associado do Windows 8.1 | Microsoft Azure"
    description=" Etapas para configurar a política de grupo para dispositivos de domínio do Windows 8.1 registrar automaticamente com o Azure AD. "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Configurar o registro de dispositivos automáticas para dispositivos de domínio associado do Windows 8.1

Você pode usar uma política de grupo do Active Directory para configurar os dispositivos de domínio associado do Windows 8.1 para registrar automaticamente com o Azure AD. Para configurar a política de grupo, você deve ter pelo menos um domínio unidas Windows Server 2012 R2 ou Windows 8.1 máquina com o recurso de gerenciamento de política de grupo instalado. Quando esta política de grupo estiver ativada para seu domínio, qualquer usuário do domínio que fizer na máquina será automaticamente e silenciosamente registrado com um objeto de dispositivo no Azure AD. Haverá um objeto de dispositivo no Azure AD para cada usuário registrado do dispositivo físico. Certifique-se de ler e concluir os pré-requisitos do registro de dispositivo automático com dispositivos de Azure Active Directory for Windows Domain-Joined.

>[AZURE.NOTE]
 Para mais recentes instruções sobre como configurar o registro de dispositivo automática, veja [como configurar o registro automático de domínio do Windows unidas dispositivos com o Active Directory do Azure](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Configurar a política de grupo para seus dispositivos de domínio associado do Windows 8.1

1. Abrir o Gerenciador de servidor e navegue até **Ferramentas** > **Gerenciamento de política de grupo**.
2. Do gerenciamento de política de grupo, navegue até o nó do domínio que corresponde ao domínio no qual deseja habilitar **Ingressar automática de local de trabalho**.
3. **Objetos de política de grupo** de atalho e selecione **novo**. Nomeie seu objeto de política de grupo, por exemplo, **Ingressar automática de local de trabalho**. Clique em **Okey**.
4. Clique com botão direito em seu novo objeto de política de grupo e selecione **Editar**.
5. Navegue para **configuração do computador** > **políticas** > **modelos administrativos** > **componentes do Windows** > **participar do local de trabalho**.
6. Clique com botão direito automaticamente computadores de cliente de junção do local de trabalho e selecione **Editar**.
7. Selecione o botão de rádio ativado e, em seguida, clique em Aplicar. Clique em **Okey**.
8. Agora você pode vincular o objeto de política de grupo para um local de sua escolha. Para habilitar essa política para todos os dispositivos do Windows 8.1 de domínio associado a sua organização, vincule a política de grupo para o domínio.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Cancelando o registro de seu domínio de Windows 8.1 unidas dispositivos

Você pode optar por cancelar o registro de seus dispositivos Windows 8.1 de domínio associado, fazendo o seguinte: modificar as configurações de política de grupo de participar do local de trabalho criadas na seção anterior. Definir o automaticamente local de trabalho ingressar computadores política de cliente como desativado. Isso impedirá novos dispositivos ingressem automaticamente o local de trabalho.

Cancelar o registro de domínio associado Windows 8.1 máquinas existentes por seguir uma das duas opções abaixo:

* Opção 1: **Cancelar registro um Windows 8.1 domínio ingressou dispositivo usando as configurações do PC**
  1. No dispositivo Windows 8.1, navegue até **Configurações do PC** > **rede** > **local de trabalho**
  2. Selecione **Sair**.
Esse processo deve ser repetido para cada usuário de domínio que entrou na máquina e foi automaticamente unida do local de trabalho.

* Opção 2: Cancelar o registro de um dispositivo de unidas de domínio do Windows 8.1 usando um script
    1. Abra um prompt de comando no computador Windows 8.1 e execute o seguinte comando:` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Este comando deve ser executado no contexto de cada usuário do domínio que assinou na máquina.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Dispositivos de domínio associado de Visualizador de eventos e erros para Windows 8.1

O Log de eventos do Windows em um computador Windows 8.1 exibe mensagens relacionadas ao registro do dispositivo. Você encontrará mensagens para eventos de com e sem êxito. 

O Log de eventos podem ser encontrados no Visualizador de eventos em aplicativos e serviços **Logs** > **Microsoft** > **Windows > local de trabalho ingressar**.

##<a name="additional-details"></a>Detalhes adicionais

A política de grupo permite que uma tarefa agendada no sistema que é executado no contexto do usuário e é disparado em acesso do usuário. A tarefa silenciosamente registrará o usuário e o dispositivo com o Azure AD após o logon do conclusão. A tarefa agendada pode ser encontrada em dispositivos Windows 8.1 na biblioteca do Agendador de tarefas em **Microsoft** > **Windows** > **Participar do local de trabalho**. A tarefa será executada e registrar todos e usuários do Active Directory essa entrada ao. 

## <a name="additional-topics"></a>Tópicos adicionais
- [Visão geral do Azure Active Directory para o registro de dispositivos](active-directory-conditional-access-device-registration-overview.md)
- [Registro de dispositivo automática com dispositivos de domínio do Azure Active Directory para Windows 10](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar o registro de dispositivos automáticas para dispositivos de domínio associado do Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

