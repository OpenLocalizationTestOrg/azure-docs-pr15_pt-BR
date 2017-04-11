
<properties
    pageTitle="Solucionar problemas de conjuntos de nuvem RemoteApp - criação | Microsoft Azure"
    description="Saiba como solucionar problemas de falhas de criação de conjunto de nuvem RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Solucionar problemas de criar conjuntos de nuvem RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se você estiver tendo problemas ao criar um conjunto de nuvem, confira as informações a seguir.

## <a name="your-image-is-invalid"></a>Sua imagem é inválida ##
Se você vir uma mensagem como, "GoldImageInvalid" quando você está esperando Azure provisionar seu conjunto, significa que sua imagem de modelo não atender a [necessidades de imagem definidas pelo](remoteapp-imagereqs.md). Portanto, vá ler esses [requisitos](remoteapp-imagereqs.md), corrigir sua imagem e tentar criar seu conjunto novamente.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Erros comuns vistos no portal de gerenciamento do Azure

    DNS server could not be reached
    ProvisioningTimeout

Coleções de nuvem geralmente falha durante a criação devido a que você está usando imagens personalizadas.  Se você vir um dos erros acima e você estiver usando uma imagem personalizada para criar o conjunto, verifique as seguintes ações:

- Certifique-se de que a imagem personalizada que você carregou atende aos requisitos de imagem.
- Geralmente o problema comum é que a imagem não foi corretamente submetida a Sysprep.  
- Verifique se a imagem pode inicializar dentro do Hyper-V ou tentar criando uma VM IAAS diretamente em sua assinatura do Azure usando a imagem. Se a máquina virtual falhar ao inicializar e não iniciar, em seguida, isso geralmente indica que a imagem personalizada não foi preparada corretamente.  Verifique se a imagem personalizada foi criada acompanhando como criar uma imagem de modelo personalizado para RemoteApp

Se você estiver usando uma das imagens Microsoft incluídas em sua assinatura, tente criar novamente o conjunto. Se o problema persistir, contate o suporte da Microsoft.

    PlatformImageTrialModeOnly

Se você ver esse erro Isso geralmente significa que você foi atualizado para uma conta paga, mas você está tentando usar uma imagem de fornecido pela Microsoft é válida apenas durante o modo de avaliação do serviço. Nesse caso, experimente criar novamente sua coleção de nuvem, mas certifique-se de especificar a imagem correta.
