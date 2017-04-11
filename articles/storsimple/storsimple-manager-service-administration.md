<properties 
   pageTitle="Administração de serviço do Gerenciador de StorSimple | Microsoft Azure"
   description="Saiba como gerenciar seu dispositivo StorSimple usando o serviço do Gerenciador de StorSimple no portal de clássico do Azure."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Usar o serviço do Gerenciador de StorSimple para administrar seu dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve a interface de serviço do Gerenciador de StorSimple, incluindo como se conectar a ele, as diversas opções disponíveis e links check-out para os fluxos de trabalho específicos que podem ser realizados via esta UI. Este guia é aplicável a ambos; físico StorSimple e o dispositivo virtual.

Após ler este artigo, você aprenderá a:

- Conectar-se ao serviço Gerenciador de StorSimple
- Navegar na interface do usuário do StorSimple Manager
- Administrar seu dispositivo StorSimple por meio do serviço do Gerenciador de StorSimple


## <a name="connect-to-storsimple-manager-service"></a>Conectar-se ao serviço Gerenciador de StorSimple

O serviço do Gerenciador de StorSimple é executado no Microsoft Azure e se conecta a vários dispositivos de StorSimple. Use um portal clássico central do Microsoft Azure em execução em um navegador para gerenciar esses dispositivos. Para se conectar ao serviço Gerenciador de StorSimple, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para se conectar ao serviço

1. Navegue até [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. Usando suas credenciais de conta da Microsoft, faça logon no portal do Microsoft Azure clássico (localizada no canto superior direito do painel).

1. Role para baixo no painel de navegação à esquerda para acessar o serviço do Gerenciador de StorSimple.


## <a name="navigate-storsimple-manager-service-ui"></a>Navegar a interface do usuário de serviço do Gerenciador de StorSimple

A hierarquia de navegação para o serviço do Gerenciador de StorSimple UI é mostrado na tabela a seguir.

- Página inicial do **Gerenciador de StorSimple** leva você para as páginas de nível de serviço de interface do usuário aplicáveis a todos os dispositivos dentro de um serviço.

- Página de **dispositivos** leva você para as páginas de interface do usuário do dispositivo – nível aplicáveis a um dispositivo específico.

- Página de **Contêineres de volume** leva você para a página de volume que mostra todos os volumes associados a um dispositivo.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hierarquia de navegação de serviço do Gerenciador de StorSimple

|Página inicial|Páginas de nível de serviço|Páginas de nível de dispositivo|Páginas de nível de dispositivo|
|---|---|---|---|
|Serviço Gerenciador de StorSimple|Painel de serviço|Painel de dispositivo||
||Dispositivos →|Monitor|
||Catálogo de backup|Containers→ de volume|Volumes|
||Configurar (serviço)|Políticas de backup||
||Trabalhos|Configurar (dispositivo)|
||Alertas|Manutenção|

![Vídeo disponível](./media/storsimple-manager-service-administration/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que orienta você através da interface de usuário de serviço do Gerenciador de StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrar o dispositivo StorSimple usando o serviço do Gerenciador de StorSimple

A tabela a seguir mostra um resumo de todas as tarefas comuns de gerenciamento e fluxos de trabalho complexos que podem ser realizados dentro do serviço do Gerenciador de StorSimple da interface do usuário. Essas tarefas são organizadas com base nas páginas de interface do usuário em que eles são iniciados.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-manager-workflows"></a>Fluxos de trabalho do Gerenciador de StorSimple

|Se você quiser fazer isso...|Vá para a página de interface do usuário...|Use este procedimento.|
|---|---|---|
|Criar um serviço</br>Excluir um serviço</br>Obter a chave do registro de serviço</br>Gerar novamente a chave do registro de serviço|Serviço Gerenciador de StorSimple|[Implantar um serviço Gerenciador de StorSimple](storsimple-manage-service.md)
|Alterar a chave de criptografia de dados de serviço</br>Exibir os logs de operação|Painel de StorSimple → de serviço do gerente|[Use o painel de serviço do Gerenciador de StorSimple](storsimple-service-dashboard.md)|
|Desativar um dispositivo</br>Excluir um dispositivo|Gerenciador de StorSimple serviço → dispositivos|[Desativar ou excluir um dispositivo](storsimple-deactivate-and-delete-device.md)|
|Saiba mais sobre failover de dispositivo e a recuperação de desastres</br>Failover para um dispositivo físico</br>Failover para um dispositivo virtual</br>Business continuidade recuperação de dados (BCDR)|Gerenciador de StorSimple serviço → dispositivos|[Failover e recuperação de desastres para seu dispositivo de StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Lista de backups para um volume</br>Selecione um conjunto de backup</br>Excluir um conjunto de backup|Catálogo de Backup StorSimple → de serviço do gerente|[Gerenciar backups](storsimple-manage-backup-catalog.md)|
|Clonar um volume|Catálogo de Backup StorSimple → de serviço do gerente|[Clonar um volume](storsimple-clone-volume.md)|
|Restaurar um conjunto de backup|Catálogo de Backup StorSimple → de serviço do gerente|[Restaurar um conjunto de backup](storsimple-restore-from-backup-set.md)|
|Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Excluir uma conta de armazenamento</br>Rotação de chaves de contas de armazenamento|Configurar o Gerenciador de StorSimple serviço →|[Gerenciar contas de armazenamento](storsimple-manage-storage-accounts.md)|
|Sobre modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Excluir um modelo de largura de banda</br>Usar um modelo de largura de banda padrão</br>Criar um modelo de largura de banda de dia inteiro que começa em uma hora específica|Configurar o Gerenciador de StorSimple serviço →|[Gerenciar modelos de largura de banda](storsimple-manage-bandwidth-templates.md)|
|Sobre os registros de controle de acesso</br>Criar um registro de controle de acesso</br>Editar um registro de controle de acesso</br>Excluir um registro de controle de acesso|Configurar o Gerenciador de StorSimple serviço →|[Gerenciar registros de controle de acesso](storsimple-manage-acrs.md)|
|Exibir detalhes do trabalho</br>Cancelar um trabalho|Gerenciador de StorSimple serviço → trabalhos|[Gerenciar trabalhos](storsimple-manage-jobs.md)
|Receber notificações de alerta</br>Gerenciar alertas</br>Revisar alertas|Gerenciador de StorSimple serviço → alertas|[Exibir e gerenciar alertas de StorSimple](storsimple-manage-alerts.md)
|Exibir iniciadores conectados</br>Localizar o número de série do dispositivo</br>Encontrar o destino IQN|Gerenciador de StorSimple serviço → dispositivos → Dashboard|[Use o painel de dispositivo StorSimple](storsimple-device-dashboard.md)|
|Criar gráficos de monitoramento|Gerenciador de StorSimple serviço → dispositivos → monitorar|[Monitorar seu dispositivo StorSimple](storsimple-monitor-device.md)|
|Adicionar um contêiner de volume</br>Modificar um contêiner de volume</br>Excluir um contêiner de volume|StorSimple Manager serviço → dispositivos → Volume contêineres|[Gerenciar contêineres de volume](storsimple-manage-volume-containers.md)|
|Adicionar um volume</br>Modificar um volume</br>Colocar um volume offline</br>Excluir um volume</br>Monitorar um volume|StorSimple Manager serviço → dispositivos → contêineres de Volume → Volumes|[Gerenciar volumes](storsimple-manage-volumes.md)|
|Modificar as configurações de dispositivo</br>Modificar as configurações de tempo</br>Modificar as configurações de DNS.md</br>Configurar interfaces de rede|Gerenciador de StorSimple serviço → dispositivos → configurar|[Modificar a configuração de dispositivo para seu dispositivo de StorSimple](storsimple-modify-device-config.md)|
|Configurações de proxy do modo de exibição da web|Gerenciador de StorSimple serviço → dispositivos → configurar|[Configurar o proxy da web para o seu dispositivo](storsimple-configure-web-proxy.md)|
|Modificar a senha de administrador do dispositivo</br>Modificar senha StorSimple Gerenciador de instantâneo|Gerenciador de StorSimple serviço → dispositivos → configurar|[Alterar senhas de StorSimple](storsimple-change-passwords.md)|
|Configurar o gerenciamento remoto|Gerenciador de StorSimple serviço → dispositivos → configurar|[Conectar-se remotamente ao seu dispositivo de StorSimple](storsimple-remote-connect.md)|
|Configurar definições de alerta|Gerenciador de StorSimple serviço → dispositivos → configurar|[Exibir e gerenciar alertas de StorSimple](storsimple-manage-alerts.md)|
|Configurar o CHAP para seu dispositivo de StorSimple|Gerenciador de StorSimple serviço → dispositivos → configurar|[Configurar o CHAP para seu dispositivo de StorSimple](storsimple-configure-chap.md)|
|Adicionar uma política de backup</br>Adicionar ou modificar uma agenda</br>Excluir uma política de backup</br>Fazer um backup manual</br>Criar uma política de backup personalizada com vários volumes e agendas|Políticas de Backup do Gerenciador de StorSimple serviço → dispositivos →|[Gerenciar políticas de backup](storsimple-manage-backup-policies.md)|
|Parar controladores de dispositivo</br>Reinicie controladores de dispositivo</br>Desligar controladores de dispositivo</br>Redefina o dispositivo para padrões de fábrica</br>(Acima são para dispositivo local somente)|Gerenciador de StorSimple serviço → dispositivos → manutenção|[Gerenciar o controlador de dispositivo de StorSimple](storsimple-manage-device-controller.md)|
|Saiba mais sobre os componentes de hardware StorSimple</br>Monitorar o status de hardware</br>(Acima são para dispositivo local somente)|Gerenciador de StorSimple serviço → dispositivos → manutenção|[Componentes de hardware do monitor](storsimple-monitor-hardware-status.md)|
|Criar um pacote de suporte|Gerenciador de StorSimple serviço → dispositivos → manutenção|[Criar e gerenciar um pacote de suporte](storsimple-create-manage-support-package.md)|
|Instalar atualizações de software|Gerenciador de StorSimple serviço → dispositivos → manutenção|[Atualizar seu dispositivo](storsimple-update-device.md)|


##<a name="next-steps"></a>Próximas etapas
Se você tiver problemas com a operação diária do seu dispositivo de StorSimple ou com qualquer um dos seus componentes de hardware, consulte:

- [Solucionar problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md)
- [Use StorSimple monitoramento LED indicador](storsimple-monitoring-indicators.md)

Se você não consegue resolver os problemas e você precisa criar uma solicitação de serviço, consulte [Suporte da Microsoft de contato](storsimple-contact-microsoft-support.md).
