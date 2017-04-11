<properties 
    pageTitle="Azure RemoteApp largura de banda - diretrizes gerais | Microsoft Azure"
    description="Compreenda algumas diretrizes de largura de banda de rede básica para seus conjuntos de RemoteApp do Azure e aplicativos."
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
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp largura de banda - diretrizes gerais (se você não pode testar seu próprio)

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se você não tiver a hora ou o recurso para executar [testes de largura de banda de rede](remoteapp-bandwidthtests.md) para o Azure RemoteApp, aqui estão algumas diretrizes bastante genéricos que podem ajudá-lo a estimar a largura de banda de rede por usuário.

Se você tiver uma mistura desses cenários, não recomendamos nada menor que (ou igual a) 10 MB/s como a largura de banda de rede mínimo para os aplicativos modernos conectado à Internet, em um ambiente remoto. (Embora, conforme discutido, isso não garante uma melhor que experiência média do usuário.)

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint complexa, PowerPoint simple

RemoteApp Azure faz melhor em LAN de 100 MB. No perfil da rede 10 MB/s, quando variação acima 120 ms é mais de 5%, o usuário verá uma experiência média. A 1 MB/s diferentes é evidente - por exemplo, em uma apresentação de slides, o usuário pode não veja transições animadas em todos os porque quadros são ignorados.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, misto PDF, PDF, texto

Perfil de rede 10 MB/s é fechar para LAN na maioria dos aspectos. 1 MB/s fornecerá uma experiência Okey, embora possa haver algumas variação quando o usuário rola enquanto há imagens na tela.

## <a name="flash-video-youtube"></a>Vídeo em Flash (YouTube)

100 MB/s LAN fornece a melhor experiência, enquanto 10 MB/s é aceitável (que significa acompanhar a taxa de quadro, mas aumenta de variação). A 1 MB/s, a variação é muito alto e perceptível.

## <a name="word-typing-word-remote-input"></a>Word digitação (entrada de remoto de Word)
Esse é um cenário de uso de largura de banda baixa. Em 256 KB/s fornecemos tão boa de uma experiência como LAN.

## <a name="learn-more"></a>Saiba Mais
- [Estimar Azure RemoteApp uso de largura de banda de rede](remoteapp-bandwidth.md)

- [RemoteApp Azure - como largura de banda de rede e qualidade da experiência trabalhar juntos?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - tseting seu uso de largura de banda de rede com alguns cenários comuns](remoteapp-bandwidthtests.md)