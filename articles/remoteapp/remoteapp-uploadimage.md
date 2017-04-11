
<properties
    pageTitle="Carregar uma imagem personalizada para Azure RemoteApp | Microsoft Azure"
    description="Saiba como carregar uma imagem personalizada para RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Carregar uma imagem personalizada para RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Agora que você criou sua imagem de modelo personalizado ou tiver atualizado com as alterações, você precisa carregar imagem para sua biblioteca de imagens do Azure RemoteApp. Use estas etapas.


## <a name="before-you-start"></a>Antes de começar

1.      Verifique se a que sua imagem personalizada atende aos [requisitos de imagem](remoteapp-imagereqs.md) e [requisitos do aplicativo](remoteapp-appreqs.md).
2.      Instale o [módulo do PowerShell do Azure](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Passo a passo sobre como carregar imagem personalizada

1.      Abra o Portal de gerenciamento do Azure e navegue até a página de RemoteApp.
2.      Na guia **imagens de modelos** , clique em **carregar** na parte inferior da página.
4.      Digite um nome amigável para sua imagem e especifique o local de conta de armazenamento. Certifique-se de que o local é o mesmo local que sua coleção de RemoteApp ou um local onde você deseja criar uma.
5.      Quando solicitado, baixe o script em seu PC local.
6.      Copie os parâmetros de comando na caixa de texto para sua área de transferência.
7.      Abra uma janela do Windows PowerShell elevada.
8.      Na janela de Windows PowerShell elevada, navegue para o mesmo diretório em que você baixou o script.
9.      Colar o comando copiado e pressione **Enter**.

    O processo de carregamento começará e duração pode depender de vários fatores, incluindo sua velocidade da rede e o tamanho da imagem

11.    Se o carregamento não funcionar devido a interrupção de rede ou coisas como essa, você sempre poderá reiniciar o processo de carregamento que você começou. Para retomar um carregamento, execute o script novamente usando a mesma linha de comando.

> [AZURE.WARNING] Nunca modifique o script de carregamento. Verificações específicas foram implementadas para garantir que a imagem atenda os requisitos de imagem e os requisitos do aplicativo.

## <a name="common-problems"></a>Problemas comuns

- Verifique se que você usa o Windows PowerShell, não do PowerShell do Azure. Você precisa instalar o módulo do PowerShell do Azure porque determinados módulos são necessárias durante o processo de carregamento.
- Nunca alterar o script, validações estão lá para sua conveniência.
- Se o arquivo vhd for bloqueado durante o carregamento, copie o arquivo ou movê-la para um novo upload local e tente novamente. Pode haver algum processo do Windows que está impedindo o carregamento.  
