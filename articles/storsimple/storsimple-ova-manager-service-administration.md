<properties 
   pageTitle="Administração de matriz de Virtual do Gerenciador de StorSimple | Microsoft Azure"
   description="Saiba como gerenciar sua matriz de Virtual local StorSimple usando o serviço do Gerenciador de StorSimple no portal de clássico do Azure."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Usar o serviço do Gerenciador de StorSimple para administrar sua matriz Virtual StorSimple

![fluxo de processo de configuração](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Visão geral

Este artigo descreve a interface de serviço do Gerenciador de StorSimple, incluindo como se conectar a ela e as diversas opções disponíveis e fornece links para os fluxos de trabalho específicos que podem ser realizados via esta UI. 

Após ler este artigo, você saberá como:

- Conectar-se ao serviço Gerenciador de StorSimple
- Navegar na interface do usuário do StorSimple Manager
- Administrar sua matriz Virtual StorSimple por meio do serviço do Gerenciador de StorSimple

> [AZURE.NOTE] Para exibir as opções de gerenciamento disponíveis para o dispositivo de série 8000 StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

## <a name="connect-to-the-storsimple-manager-service"></a>Conectar-se ao serviço Gerenciador de StorSimple

O serviço do Gerenciador de StorSimple executado no Microsoft Azure e se conecta a vários conjuntos de Virtual StorSimple. Use um portal clássico central do Microsoft Azure em execução em um navegador para gerenciar esses dispositivos. Para se conectar ao serviço Gerenciador de StorSimple, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para se conectar ao serviço

1. Vá para [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Usando suas credenciais de conta da Microsoft, faça logon no portal do Microsoft Azure clássico (localizada no canto superior direito do painel).

3. Role para baixo no painel de navegação à esquerda para acessar o serviço do Gerenciador de StorSimple.

    ![Role até o serviço](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Navegar o interface do usuário de serviço do Gerenciador de StorSimple

A hierarquia de navegação para o serviço do Gerenciador de StorSimple UI é mostrado na tabela a seguir.

- A página inicial do **Gerenciador de StorSimple** leva você para as páginas de nível de serviço de interface do usuário aplicáveis a todos os conjuntos de Virtual dentro de um serviço.

- A página de **dispositivos** leva você para as páginas de interface do usuário do dispositivo – nível aplicáveis a um conjunto específico de Virtual.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hierarquia de navegação de serviço do Gerenciador de StorSimple

|Página inicial|Páginas de nível de serviço|Páginas de nível de dispositivo|
|---|---|---|
|Serviço Gerenciador de StorSimple|Painel de controle (serviço)|Painel de controle (dispositivo)|
||Dispositivos →|Monitor|
||Catálogo de backup|Compartilhamentos (servidor de arquivos) ou </br>Volumes (iSCSI server)|
||Configurar (serviço)|Configurar (dispositivo)|
||Trabalhos|Manutenção|
||Alertas|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Usar o serviço do Gerenciador de StorSimple para executar tarefas de gerenciamento

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser realizados dentro do serviço do Gerenciador de StorSimple da interface do usuário. Essas tarefas são organizadas com base nas páginas de interface do usuário em que eles são iniciados.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-manager-workflows"></a>Fluxos de trabalho do Gerenciador de StorSimple

|Se você quiser fazer isso...|Vá para a página de interface do usuário...|Use este procedimento|
|---|---|---|
|Criar um serviço</br>Excluir um serviço</br>Obter a chave do registro de serviço</br>Gerar novamente a chave do registro de serviço|Serviço Gerenciador de StorSimple|[Implantar o serviço do Gerenciador de StorSimple](storsimple-ova-manage-service.md)|
|Alterar a chave de criptografia de dados de serviço</br>Exibir os logs de operações|Painel de StorSimple → de serviço do gerente|[Use o painel de StorSimple de serviço](storsimple-ova-service-dashboard.md)|
|Desativar uma matriz Virtual</br>Excluir uma matriz Virtual|Gerenciador de StorSimple serviço → dispositivos|[Desativar ou excluir uma matriz Virtual](storsimple-ova-deactivate-and-delete-device.md)|
|Failover de dispositivo e a recuperação de desastres</br>Pré-requisitos de failover</br>Failover para um dispositivo virtual</br>Business continuidade recuperação de dados (BCDR)</br>Erros durante a recuperação de dados|Gerenciador de StorSimple serviço → dispositivos|[Failover de dispositivo e recuperação de desastres para sua matriz Virtual StorSimple](storsimple-ova-failover-dr.md)|
|Fazer backup de compartilhamentos e volumes</br>Fazer um backup manual</br>Alterar o agendamento de backup</br>Exibir backups existentes|Catálogo de Backup do Gerenciador de StorSimple serviço →|[Fazer backup de seu Array Virtual StorSimple](storsimple-ova-backup.md)|
|Restaurar compartilhamentos de um conjunto de backup</br>Restaurar volumes de um conjunto de backup</br>Recuperação de nível do item (modo de servidor de arquivos)|Catálogo de Backup StorSimple → de serviço do gerente|[Restaurar a partir de um backup do seu Array Virtual StorSimple](storsimple-ova-restore.md)|
|Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Excluir uma conta de armazenamento|Configurar o Gerenciador de StorSimple serviço →|[Gerenciar contas de armazenamento para a matriz Virtual StorSimple](storsimple-ova-manage-storage-accounts.md)|
|Sobre os registros de controle de acesso</br>Adicionar ou modificar um registro de controle de acesso </br>Excluir um registro de controle de acesso|Configurar o Gerenciador de StorSimple serviço →|[Gerenciar os registros de controle de acesso para a matriz Virtual StorSimple](storsimple-ova-manage-acrs.md)|
|Exibir detalhes do trabalho|Gerenciador de StorSimple serviço → trabalhos| [Gerenciar trabalhos de Array Virtual StorSimple](storsimple-ova-manage-jobs.md)|
|Configurar definições de alerta</br>Receber notificações de alerta</br>Gerenciar alertas</br>Revisar alertas|Gerenciador de StorSimple serviço → alertas|[Exibir e gerenciar alertas para a matriz Virtual StorSimple](storsimple-ova-manage-alerts.md)|
|Modificar a senha de administrador do dispositivo|Gerenciador de StorSimple serviço → dispositivos → configurar|[Alterar a senha de administrador do dispositivo de Array Virtual StorSimple](storsimple-ova-change-device-admin-password.md)|
|Instalar atualizações de software|Gerenciador de StorSimple serviço → dispositivos → manutenção|[Atualizar sua matriz Virtual](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] Você deve usar a [web local da interface do usuário](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
>
>- [Recuperar a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Parar e reiniciar uma matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Próximas etapas
Para obter informações sobre a interface do usuário da web e como usá-lo, vá para [usar a web StorSimple interface de usuário para administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
