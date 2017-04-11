
<properties 
    pageTitle="Estimar o uso de largura de banda de rede do Azure RemoteApp | Microsoft Azure"
    description="Saiba mais sobre os requisitos de largura de banda de rede para as coleções de RemoteApp do Azure e os aplicativos."
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

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Estimar Azure RemoteApp uso de largura de banda de rede 

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure usa o protocolo de área de trabalho remota (RDP) para comunicar entre aplicativos em execução na nuvem Azure e seus usuários. Este artigo fornece algumas diretrizes básicas que você pode usar para estimar a esse uso de rede e potencialmente avaliar o uso de largura de banda de rede por usuário do Azure RemoteApp.

Estimando o uso de largura de banda por usuário é muito complexo e requer a executar vários aplicativos simultaneamente em cenários de multitarefas onde aplicativos podem afetar o desempenho uns dos outros com base em sua demanda para largura de banda de rede. Mesmo do tipo de cliente de área de trabalho remota (como o cliente de Mac versus HTML5 cliente) pode levar a resultados de largura de banda diferentes. Para ajudá-lo a trabalhar com esses complicações, podemos vai dividir os cenários de uso em várias categorias comuns para replicar cenários reais. (Onde o cenário real é, naturalmente, uma mistura de categorias e difere pelo usuário.)

Antes de prosseguir mais - Observe que vamos supor RDP fornece uma boa experiência excelente para a maioria dos cenários de uso em redes com latência abaixo ms 120 e largura de banda com mais de 5 MB - baseia-se na capacidade do RDP para ajustar dinamicamente usando a largura de banda de rede disponível e a largura de banda do aplicativo estimado necessidades. Este artigo ultrapassa aqueles "maioria dos cenários de uso" para examinar a borda, onde cenários começam a desenrolar e experiência do usuário começará a ser prejudicado.

Agora Confira os artigos a seguir para obter os detalhes, incluindo fatores a considerar, recomendações de linha de base e o que podemos não incluiu no nossas estimativas.

- [Como largura de banda de rede e qualidade da experiência trabalhar juntos?](remoteapp-bandwidthexperience.md)
- [Teste seu uso de largura de banda de rede com alguns cenários comuns](remoteapp-bandwidthtests.md)
- [Se você não tiver o tempo ou capacidade para testar as diretrizes rápidas](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>O que nós não está incluindo?

Ao revisar os testes de proposta e nossas recomendações gerais (e claramente genéricas), lembre-se de que há vários fatores que não consideramos. Por exemplo, as complicações de experiência do usuário fornecidas pela natureza assimétrica de carregamento versus baixar largura de banda. Além disso, a natureza assimétrica da maioria das redes Wi-Fi afetarão o desempenho e a percepção de experiência do usuário. Para cenários interativos o tráfego downstream pode ter uma prioridade menor do que o superior, que pode aumentar o número de quadros de vídeo ou áudio perdidos e, portanto, afetar a percepção do usuário da experiência do fluxo. Você pode executar suas próprias experiências para ver o que é bom para o seu caso específico de uso e a rede.

Embora vamos abordar o redirecionamento de dispositivo, podemos não levar em consideração o impacto de largura de banda o tráfego de rede causado por dispositivos conectados, como o armazenamento, impressoras, scanners, câmeras da web e outros dispositivos USB. O efeito desses dispositivos geralmente picos as necessidades de largura de banda temporariamente e desaparece quando a tarefa estiver concluída. Mas se feito com frequência, que exigem a largura de banda pode ser bastante perceptível.

Também não discutimos como um usuário pode afetar outros usuários dentro da mesma rede. Por exemplo, um usuário consumindo 4K vídeo em uma rede de 100 MB/s significativamente pode afetar outros usuários na mesma rede tentar fazer a mesma tarefa. Infelizmente obtém cada vez mais difícil de determinar o impacto de uso simultâneo para dar uma recomendação completa ou comuns sobre como o sistema executa a agregação. Tudo o que podemos dizer é que a tecnologia subjacente do protocolo tornará o melhor uso da largura de banda de rede disponível, mas ela tem suas limitações.