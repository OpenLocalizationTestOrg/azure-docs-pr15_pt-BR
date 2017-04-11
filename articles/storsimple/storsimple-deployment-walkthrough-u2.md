<properties 
   pageTitle="Implantar o seu dispositivo de StorSimple (2 de atualização) | Microsoft Azure"
   description="Descreve as etapas e práticas recomendadas para implantar o dispositivo StorSimple atualização 2 e o serviço."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device-update-2"></a>Implantar o seu dispositivo de StorSimple locais (atualização 2)

> [AZURE.SELECTOR]
- [Atualização 2 e posterior](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Atualização 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [Lançamento de GA](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Visão geral

Bem-vindo ao Microsoft Azure StorSimple implantação no dispositivo. Estes tutoriais de implantação se aplicam a StorSimple 8000 série atualização 2. Esta série de tutoriais inclui uma lista de verificação de configuração, pré-requisitos de configuração e etapas de configuração detalhadas para seu dispositivo de StorSimple.

As informações nestes tutoriais pressupõe que você ter revisado as precauções de segurança e descompactados, em rack e conectados seu dispositivo StorSimple. Se você ainda precisar realizar essas tarefas, comece com revisando as [precauções de segurança](storsimple-safety.md). Siga as instruções específicas do dispositivo descompactar, montagem em rack e cabo seu dispositivo.

- [Descompactar, montagem em rack e cabo seu 8100](storsimple-8100-hardware-installation.md)
- [Descompactar, montagem em rack e cabo seu 8600](storsimple-8600-hardware-installation.md)

Você precisará privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que você examine a lista de verificação de configuração antes de começar. O processo de implantação e configuração pode levar algum tempo para ser concluída.

> [AZURE.NOTE] As informações de implantação de StorSimple publicadas no site do Microsoft Azure se aplica a StorSimple 8000 série dispositivos apenas. Para obter informações completas sobre os dispositivos de 7000 série, vá para: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para informações de implantação de série 7000, consulte o [StorSimple sistema Quick Start Guide](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Etapas de implantação

Execute estas etapas necessárias para configurar seu dispositivo StorSimple e conecte-o ao seu serviço de Gerenciador de StorSimple. Além das etapas necessárias, há etapas opcionais e procedimentos, que talvez seja necessário durante a implantação. As instruções passo a passo de implantação indicam quando você deve executar cada uma destas etapas opcionais.


| Etapa                                                                                   | Descrição                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PRÉ-REQUISITOS**                                                                      | Eles precisam ser concluídas em preparação para a implantação futura.                                                                                        |
| [Lista de verificação de configuração de implantação](#deployment-configuration-checklist)                                                     | Use esta lista de verificação para reunir e registrar informações antes e durante a implantação.                                                                       |
| [Pré-requisitos de implantação](#deployment-prerequisites)                                                               | Esses validar o ambiente está pronto para implantação.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **IMPLANTAÇÃO PASSO A PASSO**                                                                   | Essas etapas são necessárias para implantar o seu dispositivo StorSimple em produção.                                                                                      |
| [Etapa 1: Criar um novo serviço](#step-1-create-a-new-service)                                                         | Configure o gerenciamento de nuvem e armazenamento para o seu dispositivo StorSimple. *Ignorar esta etapa se você tiver um serviço existente para outros dispositivos de StorSimple*.                |
| [Etapa 2: Obter a chave do registro de serviço](#step-2-get-the-service-registration-key)                                               | Use esta tecla para registrar & conectar seu dispositivo StorSimple com o serviço de gerenciamento.                                                                         |
| [Etapa 3: Configurar e registrar o dispositivo através do Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)    | Conectar o dispositivo à sua rede e registre-o com o Azure para concluir a configuração usando o serviço de gerenciamento.                                            |
| [Etapa 4: Concluir a configuração de dispositivo mínimos](#step-4-complete-minimum-device-setupd)</br>[Opcional: Atualizar o dispositivo StorSimple](#scan-for-and-apply-updates)      | Use o serviço de gerenciamento para concluir a configuração do dispositivo e ativá-la fornecer armazenamento.                                                                      |
| [Etapa 5: Criar um contêiner de volume](#step-5-create-a-volume-container)                                                      | Crie um contêiner para volumes de provisionamento. Um contêiner de volume tem conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidas nele.    |
| [Etapa 6: Criar um volume](#step-6-create-a-volume)                                                                | Provisionar volumes de armazenamento no dispositivo StorSimple para os servidores.                                                                                        |
| [Etapa 7: Montar, inicializar e formatar um volume](#step-7-mount-initialize-and-format-a-volume)</br>[Opcional: Configurar MPIO](storsimple-configure-mpio-windows-server.md)            | Conecte seus servidores para o armazenamento de iSCSI fornecido pelo dispositivo. Opcionalmente, configure MPIO para garantir que seus servidores podem suportar falha de link, interface e de rede.                                                                                                                                                              |
| [Etapa 8: Fazer um backup](#step-8-take-a-backup)                                                                  | Configurar sua política de backup para proteger seus dados                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **OUTROS PROCEDIMENTOS**                                                                   | Você talvez precise fazer referência a esses procedimentos como implantar sua solução.                                                                                        |
| [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [Usar acabamento para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Obtenha o IQN de um host do Windows Server](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Criar um backup manual](#create-a-manual-backup)                                                                 | 


## <a name="deployment-configuration-checklist"></a>Lista de verificação de configuração de implantação

Antes de implantar seu dispositivo, você precisará coletar informações para configurar o software em seu dispositivo de StorSimple. Preparar algumas dessas informações antecedência ajudarão a simplificar o processo de implantação de dispositivo StorSimple no seu ambiente. Baixar e usar esta lista de verificação para anote os detalhes de configuração, como implantar o seu dispositivo.

- [Baixar a lista de verificação de configuração de implantação StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)


## <a name="deployment-prerequisites"></a>Pré-requisitos de implantação

As seções a seguir explicam os pré-requisitos de configuração para o serviço do Gerenciador de StorSimple e seu dispositivo StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gerenciador de StorSimple

Antes de começar, verifique se:

- Você tem uma conta da Microsoft credenciais de acesso.

- Você tem a sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

- Sua assinatura do Microsoft Azure está habilitada para o serviço do Gerenciador de StorSimple. Sua assinatura deve ser comprada por meio do [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Você tem acesso ao software de emulação de terminal como acabamento.

### <a name="for-the-device-in-the-datacenter"></a>Para o dispositivo no data center

Antes de configurar o dispositivo, verifique se seu dispositivo está totalmente descompactado, montado em um rack e totalmente conectado para power, rede e acesso serial conforme descrito em:

-  [Descompactar, montagem em rack e cabo seu dispositivo 8100](storsimple-8100-hardware-installation.md)
-  [Descompactar, montagem em rack e cabo seu dispositivo 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Rede no data center

Antes de começar, verifique se:

- As portas no firewall data center são abertas para permitir para iSCSI e nuvem tráfego conforme descrito na [rede requisitos para o seu dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).


## <a name="step-by-step-deployment"></a>Implantação passo a passo

Use as seguintes instruções passo a passo para implantar o seu dispositivo StorSimple no data center.

## <a name="step-1-create-a-new-service"></a>Etapa 1: Criar um novo serviço

Um serviço Gerenciador de StorSimple pode gerenciar vários dispositivos de StorSimple. Execute as seguintes etapas para criar uma nova instância do serviço do Gerenciador de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Se você não ativou a criação automática de uma conta de armazenamento com o seu serviço, você precisará criar pelo menos uma conta de armazenamento após ter criado com êxito um serviço. Esta conta de armazenamento será usada quando você cria um contêiner de volume. 
>
> * Se você não criou uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas. 
> * Se você habilitou a criação automática de uma conta de armazenamento, acesse [etapa 2: obter a chave do registro de serviço](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Etapa 2: Obter a chave do registro de serviço

Depois que o serviço Gerenciador de StorSimple está em execução, você precisará obter a chave do registro de serviço. Esta chave é usada para registrar e conectar seu dispositivo StorSimple com o serviço.

Execute as seguintes etapas no Portal de gerenciamento.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Etapa 3: Configurar e registrar o dispositivo através do Windows PowerShell para StorSimple

Use o Windows PowerShell para StorSimple para concluir a configuração inicial do seu dispositivo StorSimple conforme explicado no procedimento a seguir. Você precisará usar o software de emulação de terminal para concluir esta etapa. Para obter mais informações, consulte [Acabamento de uso para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Etapa 4: Concluir a configuração de dispositivo mínimos

Para a configuração de dispositivo mínimos do seu dispositivo de StorSimple, você será solicitado a: 

- Configure o servidor DNS secundário.
- Habilite iSCSI em pelo menos uma interface de rede.
- Atribua endereços IP fixos para ambos os os controladores.

Execute as seguintes etapas no Portal de gerenciamento para concluir a configuração de dispositivo mínimos.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Etapa 5: Criar um contêiner de volume

Um contêiner de volume tem conta de armazenamento, largura de banda e configurações de criptografia para todos os volumes contidas nele. Você precisará criar um contêiner de volume antes de iniciar volumes em seu dispositivo de StorSimple de provisionamento. 

Execute as seguintes etapas no Portal de gerenciamento para criar um contêiner de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Etapa 6: Criar um volume

Depois de criar um contêiner de volume, você pode provisionar um volume de armazenamento no dispositivo StorSimple para os servidores. Execute as seguintes etapas no Portal de gerenciamento para criar um volume.

> [AZURE.IMPORTANT] Gerenciador de StorSimple pode criar ambos fina e volumes totalmente provisionados. No entanto, é possível criar volumes parcialmente provisionados. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Etapa 7: Montar, inicializar e formatar um volume

As etapas a seguir são executadas em seu host do Windows Server. 


> [AZURE.IMPORTANT]

> - Para a alta disponibilidade de sua solução de StorSimple, recomendamos que você configure MPIO nos seus servidores de host (opcionais) antes de configurar o iSCSI. Configuração de MPIO em servidores host garantirá que os servidores podem suportar um link, rede ou falha de interface.

> - Para MPIO iSCSI instalação e configuração instruções e host do Windows Server, vá para [Configurar MPIO para seu dispositivo de StorSimple](storsimple-configure-mpio-windows-server.md). Estes também incluirá as etapas necessárias para montar, inicializar e formatar volumes de StorSimple.

> - Para MPIO e iSCSI instalação e configuração instruções em um host Linux, vá para [Configurar MPIO do seu host StorSimple Linux](storsimple-configure-mpio-on-linux.md)

Se você decidir não configurar MPIO, execute as seguintes etapas para montar, inicializar e formatar seus volumes de StorSimple em um host do Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Etapa 8: Fazer um backup

Backups oferecem proteção no momento de volumes e melhoram a capacidade de recuperação minimizando tempos de restauração. Você pode levar dois tipos de backup no seu dispositivo de StorSimple: instantâneos locais e instantâneos de nuvem. Cada um desses tipos de backup pode ser **agendado** ou **Manual**. 

Execute as seguintes etapas no Portal de gerenciamento para criar um backup agendado.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Você pode levar um manual de backup a qualquer momento. Para obter procedimentos, vá para [criar um backup manual](#create-a-manual-backup). 

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta de armazenamento para o serviço

Esta é uma etapa opcional que você precisa realizar somente se você não ativou a criação automática de uma conta de armazenamento com o seu serviço. Uma conta de armazenamento do Microsoft Azure é necessário para criar um contêiner de volume StorSimple.

Se você precisa criar uma conta de armazenamento do Azure em uma região diferente, consulte [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md) para obter instruções passo a passo.

Execute as seguintes etapas no Portal de gerenciamento, na página do **Gerenciador de StorSimple serviço** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Usar acabamento para se conectar ao console serial do dispositivo

Para se conectar ao Windows PowerShell para StorSimple, você precisa usar o software de emulação de terminal como acabamento. Você pode usar acabamento ao acessar o dispositivo diretamente através do console serial ou abrindo uma sessão telnet de um computador remoto.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## <a name="scan-for-and-apply-updates"></a>Examinar e aplicar atualizações

Atualizar seu dispositivo pode levar horas. Execute as seguintes etapas para examinar e aplicar atualizações em seu dispositivo.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Para atualizar o seu dispositivo

1.  Na página de **Início rápido** do dispositivo, clique em **dispositivos**. Selecione o dispositivo físico, clique em **manutenção** e clique em **Verificar atualizações**.  

2.  Um trabalho de verificação de atualizações disponíveis é criado. Se as atualizações estiverem disponíveis, as **Atualizações digitalizar** é alterado para **Instalar atualizações**. Clique em **instalar atualizações**. 

3.  Um trabalho de atualização será criado. Monitore o status da sua atualização navegando para **trabalhos**.

    > [AZURE.NOTE] Quando o trabalho de atualização for iniciado, ele imediatamente exibe o status como 50%. O status é alterado para 100 por cento somente depois que o trabalho de atualização for concluído. Não há nenhum status em tempo real para o processo de atualização.

4.  Após o dispositivo com êxito for atualizado, ative interfaces de rede de dados 2 e 3 de dados se eles foram desativados.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtenha o IQN de um host do Windows Server

Execute as seguintes etapas para obter o iSCSI qualificado nome (IQN) de um host do Windows que está executando o Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar um backup manual

Execute as seguintes etapas no Portal de gerenciamento para criar um backup manual de sob demanda para um único volume em seu dispositivo de StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>Próximas etapas

- Configure um [dispositivo virtual](storsimple-virtual-device-u2.md).

- Use o [serviço do Gerenciador de StorSimple](storsimple-manager-service-administration.md) para gerenciar o dispositivo StorSimple.
 
