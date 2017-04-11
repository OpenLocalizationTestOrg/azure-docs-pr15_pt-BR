<properties
    pageTitle="Criar uma imagem de RemoteApp Azure | Microsoft Azure"
    description="Saiba mais sobre as opções disponíveis para a criação de imagens para RemoteApp do Azure"
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



# <a name="create-an-azure-remoteapp-image"></a>Criar uma imagem de RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure usa imagens para manter os aplicativos que você compartilha com seus usuários. (Podemos fazer sua imagem e usá-lo para criar VMs - que do que o acesso de usuários quando eles entrarem Azure RemoteApp.) Para criar um conjunto de RemoteApp Azure com sua opção de aplicativos, seja ele nuvem ou híbrido, você começar pela criação de uma imagem com os aplicativos instalados. Em seguida, crie uma coleção que usa a imagem, atribuir aos usuários para o conjunto e publicar aplicativos aos usuários.

Você tem várias opções para a criação ou uso de imagens. O [requisito](remoteapp-imagereqs.md) básico de uma imagem é que executar o Windows Server 2012 R2 e tem a função de Host de sessão de área de trabalho remota (RDSH) instalada. Como obter que é onde as coisas ficam interessantes.

Quando se trata de imagens, você tem as seguintes opções:

- Você pode importar e usar uma [imagem com base em uma máquina virtual Azure](remoteapp-image-on-azurevm.md). Isso é bom para aplicativos de linha de negócios que exigem configurações personalizadas. Você pode personalizar a imagem para trabalhar para o aplicativo.
- Você pode [criar e carregar uma imagem personalizada](remoteapp-create-custom-image.md). Isso é bom se você já tiver uma imagem que você usa para a sua implantação de serviços de área de trabalho remota local.
- Você pode usar uma das [imagens de modelo](remoteapp-images.md) incluído em sua assinatura de RemoteApp. Essas imagens são criadas e mantidas pela equipe de RemoteApp e contêm alguns aplicativos padrão (como o pacote do Office) que você pode disponibilizar para seus usuários. Observe que apenas a imagem Office 365 Pro Plus pode ser usada em uma configuração de produção.

Independentemente de onde você recebe sua imagem ou como criá-lo, você desejará certificar-se de que você entende os [requisitos do aplicativo](remoteapp-appreqs.md) para garantir que seu aplicativo funcione bem na RemoteApp. Em seguida, a próxima etapa é criar um conjunto de [nuvem](remoteapp-create-cloud-deployment.md) ou [híbrido](remoteapp-create-hybrid-deployment.md) .
