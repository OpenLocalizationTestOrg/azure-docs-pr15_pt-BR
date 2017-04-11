
<properties
    pageTitle="Requisitos de imagem do Azure RemoteApp | Microsoft Azure"
    description="Saiba mais sobre os requisitos para a criação de imagens para ser usado com o Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="requirements-for-azure-remoteapp-images"></a>Requisitos do Azure RemoteApp imagens

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure usa uma imagem do Windows Server 2012 R2 para hospedar todos os programas que você deseja compartilhar com seus usuários. Para criar uma imagem personalizada, você pode começar com uma imagem existente ou [Crie um novo](remoteapp-create-custom-image.md).

> [AZURE.TIP] Você sabia que sua assinatura do Azure RemoteApp lhe dá acesso a uma imagem do Windows Server 2012 R2 na Galeria de máquina virtual do Azure que você pode usar para criar sua própria imagem de modelo? [Check-out](remoteapp-image-on-azurevm.md).  


Os requisitos para a imagem que podem ser carregados para uso com o Azure RemoteApp são:


- Aplicativos personalizados não armazenam dados localmente na imagem. Essas imagens são sem estado e devem conter apenas os aplicativos.
- A imagem não contém dados que podem ser perdidos.
- O tamanho da imagem deve ser um múltiplo de MB. Se você tentar carregar uma imagem que não é um múltiplo exato, o carregamento falhará.
- O tamanho da imagem deve ser 127 GB ou menor.
- Ele deve estar em um arquivo VHD (VHDX arquivos não são atualmente suportados).
- O VHD não deve ser uma máquina virtual de geração 2.
- O VHD pode ser tamanho fixo ou expansível dinamicamente. Um VHD expansível dinamicamente é recomendado porque ele leva menos tempo para carregar no Azure que um arquivo VHD de tamanho fixo.
- O disco deve ser inicializado usando o registro de inicialização de mestre (MBR) estilo de partição. Não há suporte para o estilo de partição GUID partição GPT (tabela).
- O VHD deve conter uma única instalação do Windows Server 2012 R2. Ele pode conter vários volumes, mas somente um que contém uma instalação do Windows.
- A função de Host de sessão de área de trabalho remota (RDSH) e o recurso Experiência Desktop devem ser instalados.
- A função do agente de Conexão de área de trabalho remota deve *não* ser instalado.
- O arquivo EFS (sistema) deve ser desabilitado.
- A imagem deve ser submetida a Sysprep usando os parâmetros **/oobe /generalize /shutdown** (não use o parâmetro **/mode:vm** ).
- Carregar seu VHD de uma cadeia de instantâneo não é suportado.

Para obter mais informações sobre a criação de imagens do Azure RemoteApp, consulte [criar uma imagem de RemoteApp do Azure](remoteapp-imageoptions.md) .
