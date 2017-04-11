<properties
   pageTitle="Implantar Array Virtual StorSimple 1 - preparação do Portal"
   description="Primeiro tutorial para implantar StorSimple array virtual envolve Preparando o portal"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Implantar StorSimple Virtual Array - preparar o portal

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Visão geral

Este artigo se aplica à versão do Microsoft Azure StorSimple Array Virtual (também conhecido como o dispositivo virtual do StorSimple local ou um dispositivo virtual StorSimple) em execução março de 2016 disponibilidade geral (GA). Este é o primeiro artigo da série de tutoriais de implantação necessárias para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor de iSCSI. Este artigo descreve a preparação necessária para criar e configurar o serviço de Gerenciador de StorSimple antes de uma matriz virtual de provisionamento. Este artigo também vincula check-out para uma lista de verificação de configuração de implantação, bem como configuração pré-requisitos.

Você precisará privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que você examine a lista de verificação de configuração de implantação antes de começar. A preparação do portal levará menos de 10 minutos.

As informações publicadas neste artigo se aplica à implantação de matrizes Virtual StorSimple no portal clássico Azure bem como nuvem de governo do Microsoft Azure.

### <a name="get-started"></a>Introdução

O fluxo de trabalho de implantação consiste em Preparando o portal, provisionamento uma matriz virtual em seu ambiente virtualizado e concluir a instalação. Para começar com a implantação de Array Virtual StorSimple como um servidor de arquivos ou um servidor iSCSI, você precisará consulte os seguintes recursos tabulado (artigos e vídeos).

#### <a name="deployment-articles"></a>Artigos de implantação

Consulte os artigos a seguir na sequência indicada para implantar sua matriz Virtual StorSimple.

| **#** | **Nesta etapa**                          | **Você fará isso …**                                                         | **Use esses documentos.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Configurar o portal de clássico Azure**       | Criar e configurar o serviço de Gerenciador de StorSimple antes de provisionamento de um dispositivo virtual StorSimple.  |[Preparar o portal](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Provisionar a matriz Virtual**           | Hyper-v, provisionar e se conectar a um dispositivo virtual StorSimple em um sistema de host executando Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para VMware, provisionar e se conectar a um dispositivo de virtual do local StorSimple em um sistema de host executando VMware ESXi 5.5 e acima.<br></br>| [Provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Provisionar uma matriz virtual em VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Configurar a matriz Virtual**              | Para o seu servidor de arquivos, executar a configuração inicial, registrar seu servidor de arquivos StorSimple e concluir a configuração de dispositivo. Em seguida, você pode provisionar compartilhamentos SMB. <br></br> <br></br> Para o seu servidor de iSCSI, executar a configuração inicial, registrar seu servidor de iSCSI StorSimple e concluir a configuração de dispositivo. Em seguida, você pode provisionar volumes iSCSI.| [Configurar o array virtual como servidor de arquivos](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurar array virtual como servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Vídeos de implantação

| **Realizar esta etapa...** |  **Assista a este vídeo.**|
|----------------|-------------|
| Instruções passo a passo para começar a usar a matriz Virtual StorSimple. | [Começar a usar a matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Instruções passo a passo para provisionar uma matriz Virtual StorSimple no Hyper-V.|[Criar uma matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Instruções passo a passo para configurar e registrar uma matriz Virtual StorSimple|[Configurar uma matriz de Virtual StorSimple](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Instruções passo a passo para criar compartilhamentos, fazer backup de compartilhamentos e restaurar os dados em uma matriz de Virtual StorSimple configurado como um servidor de arquivos|[Usar a matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Instruções detalhadas para failover e recuperação de desastres de uma matriz de Virtual StorSimple|[Recuperação de Array Virtual StorSimple](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Agora você pode começar a configurar o Azure portal clássico.

## <a name="configuration-checklist"></a>Lista de verificação de configuração

A lista de verificação de configuração descreve as informações que você precisa reunir antes de configurar o software em seu dispositivo de StorSimple. Preparar essas informações antecedência ajudarão a simplificar o processo de implantação de dispositivo StorSimple no seu ambiente. Dependendo se seu dispositivo virtual StorSimple será implantado como um servidor de arquivos ou um servidor iSCSI, será necessário um dos seguintes listas de verificação.

-   Baixe a [lista de verificação do StorSimple Array Virtual arquivo servidor configuração](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Baixe o [Array Virtual StorSimple iSCSI lista de verificação de configuração de servidor](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui você encontrará os pré-requisitos de configuração para seu serviço de Gerenciador de StorSimple, seu dispositivo virtual StorSimple e a rede do data center.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gerenciador de StorSimple

Antes de começar, verifique se:

-   Você tem uma conta da Microsoft credenciais de acesso.

-   Você tem a sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

-   Sua assinatura do Microsoft Azure deve estar habilitada para o serviço Gerenciador de StorSimple.

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple

Antes de implantar um dispositivo virtual, verifique se:

-   Você tem acesso a um sistema de host executando Hyper-V no Windows Server 2008 R2 ou posterior ou VMware (ESXi 5,5 ou posterior) que pode ser usados para um provisionar um dispositivo.

-   O sistema de host é capaz de dedicar os recursos a seguir para configurar seu dispositivo virtual:

    -   Mínimo de 4 cores.

    -   Pelo menos 8 GB de RAM.

    -   Uma interface de rede.

    -   Um disco virtual 500 GB para dados de sistema.

### <a name="for-the-datacenter-network"></a>Para a rede de data center

Antes de começar, verifique se:

-   A rede em seu data center está configurada de acordo com os requisitos de rede para o seu dispositivo StorSimple. Para obter mais informações, consulte os [Requisitos de sistema do StorSimple Virtual matriz](storsimple-ova-system-requirements.md).

-   Seu dispositivo de virtual StorSimple tem uma largura de banda 5 por Mbps Internet dedicada (ou mais) disponível em todos os momentos. Essa largura de banda não deve ser compartilhada com outros aplicativos.

## <a name="step-by-step-preparation"></a>Preparação passo a passo

Use as seguintes instruções passo a passo para preparar seu portal para o serviço do Gerenciador de StorSimple.

## <a name="step-1-create-a-new-service"></a>Etapa 1: Criar um novo serviço

Uma única instância do serviço do Gerenciador de StorSimple pode gerenciar vários dispositivos de StorSimple 1200. Execute as seguintes etapas para criar uma nova instância do serviço do Gerenciador de StorSimple. Se você tiver um serviço existente do Gerenciador de StorSimple para gerenciar seus dispositivos de 1200, pule esta etapa e vá para [etapa2: obter a chave do registro de serviço](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Se você não ativou a criação automática de uma conta de armazenamento com o seu serviço, você precisará criar pelo menos uma conta de armazenamento após ter criado com êxito um serviço.
>

> - Se você não criou uma conta de armazenamento automaticamente, vá para [Configurar uma nova conta de armazenamento para o serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
>

> - Se você habilitou a criação automática de uma conta de armazenamento, acesse [etapa 2: obter a chave do registro de serviço](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Etapa 2: Obter a chave do registro de serviço


Depois que o serviço Gerenciador de StorSimple está em execução, você precisará obter a chave do registro de serviço. Esta chave é usada para registrar e conectar seu dispositivo StorSimple com o serviço.

Execute as seguintes etapas no [Azure portal clássico](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> A chave do registro de serviço é usada para registrar todos os dispositivos de Gerenciador de StorSimple que precisam registrar com o serviço do Gerenciador de StorSimple.

## <a name="step-3-download-the-virtual-device-image"></a>Etapa 3: Baixar a imagem de dispositivo virtual

Depois que a chave do registro de serviço, você precisará baixar a imagem de dispositivo virtual apropriado para provisionar um dispositivo virtual em seu sistema de host. As imagens de dispositivo virtual são específicos de sistema operacional e podem ser baixadas a partir da página de início rápido no portal de clássico do Azure.

> [AZURE.IMPORTANT] O software em execução no Array Virtual StorSimple só pode ser usado em conjunto com o serviço do Gerenciador de Storsimple.


Execute as seguintes etapas no [Azure portal clássico](https://manage.windowsazure.com/).

#### <a name="to-get-the-virtual-device-image"></a>Para obter a imagem de dispositivo virtual

1.  Na página **serviço Gerenciador de StorSimple** , clique no serviço que você criou. Você será levado para a página de **Início rápido** . (Você pode clicar no ícone de início rápido ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) para acessar a página de **Início rápido** a qualquer momento.)

1.  Clique no link correspondente para a imagem que você deseja baixar do Microsoft Download Center. Os arquivos de imagem são aproximadamente 4,8 GB.

    -   VHDX Hyper-v no Windows Server 2012 e posterior

    -   VHD Hyper-v no Windows Server 2008 R2 e posterior

    -   VMDK para VMWare ESXi 5.5 e versões posteriores

2.  Baixe e descompacte o arquivo para uma unidade local, fazendo uma anotação de onde se encontra o arquivo descompactado.

![ícone de vídeo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **vídeo disponível**

Assista ao vídeo para obter instruções passo a passo para começar a usar a matriz Virtual StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Etapa opcional: configurar uma nova conta de armazenamento para o serviço

Esta é uma etapa opcional que precisa ser executadas somente se você não ativou a criação automática de uma conta de armazenamento com o seu serviço.

Se você precisa criar uma conta de armazenamento do Azure em uma região diferente, consulte [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter instruções passo a passo.

Execute as seguintes etapas do [portal clássico Azure](https://manage.windowsazure.com/) na página Gerenciador de StorSimple serviço para adicionar uma conta de armazenamento do Microsoft Azure existente.

#### <a name="to-add-a-storage-account"></a>Para adicionar uma conta de armazenamento

1.  O serviço de Gerenciador de StorSimple página inicial, selecione seu serviço e clique duas vezes nela. Você será levado para a página de **Início rápido** . Selecione a página de **Configurar** .

2.  Clique em **Adicionar/Editar conta de armazenamento**. Na caixa de diálogo **Add/Edit conta de armazenamento** , faça o seguinte:

    1.  Clique em **Adicionar novo**.

    1.  Forneça um nome para a sua conta de armazenamento.

    1.  Fornece a primária **Tecla de acesso** para sua conta de armazenamento do Microsoft Azure.

    1.  Selecione **o modo de ativar SSL** para criar um canal seguro para comunicação de rede entre seu dispositivo e a nuvem. Desmarque a caixa de seleção **Habilitar SSL modo** somente se você estiver operando dentro de uma nuvem privada.

    1.  Clique no ícone de seleção ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Você será notificado depois que a conta de armazenamento foi criada com êxito.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  A conta de armazenamento recém-criado será exibida na página **Configurar** em **contas de armazenamento**. Clique em **Salvar** para salvar a conta de armazenamento recém-criado. Clique em **Okey** quando solicitado para confirmação.


## <a name="next-step"></a>Próxima etapa

A próxima etapa é configurar uma máquina virtual para seu dispositivo virtual StorSimple. Dependendo do sistema operacional host, consulte as instruções detalhadas no:

-   [Provisionar uma matriz Virtual StorSimple no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Provisionar uma matriz de Virtual StorSimple em VMware](storsimple-ova-deploy2-provision-vmware.md)
