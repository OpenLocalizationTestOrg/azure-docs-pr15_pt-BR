<properties 
    pageTitle="RemoteApp Azure - como largura de banda de rede e qualidade da experiência trabalhar juntos? | Microsoft Azure"
    description="Saiba como a largura de banda de rede no Azure RemoteApp pode afetar de qualidade da experiência do usuário."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>RemoteApp Azure - como largura de banda de rede e qualidade da experiência trabalhar juntos?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Quando você está vendo a [largura de banda geral](remoteapp-bandwidth.md) necessária para Azure RemoteApp, tenha em mente os seguintes fatores - esses fazem parte de um sistema dinâmico que afeta a experiência geral do usuário. 

- **Largura de banda de rede disponível e condições atuais da rede** - um conjunto de parâmetros (variação de perda de latência,) na mesma rede em um determinado momento pode afetar o aplicativo experiência de streaming, o que significa uma experiência de usuário geral reduzido. A largura de banda disponível em sua rede é uma função de congestionamento, perda aleatória, latência porque todos esses parâmetros afetam o mecanismo de controle de congestionamento, que por sua vez controles a velocidade de transmissão para evitar conflitos.  Por exemplo, uma rede com perdas ou rede com alta latência tornará o usuário enfrentar mau mesmo em uma rede com largura de banda de 1.000 MB. A latência e perda variam com base no número de usuários que estão na mesma rede e o que os usuários estão fazendo (por exemplo, assistir vídeos, download ou o upload de arquivos grandes, impressão).
- **Cenário de uso** - a experiência depende de que os usuários estão fazendo indivíduos e como um grupo na mesma rede. Por exemplo, um slide de leitura requer apenas um quadro a ser atualizada; Se o usuário aborda e rola sobre o conteúdo de um documento de texto, será necessário um número maior de quadros serem atualizados por segundo. A comunicação e voltar para o servidor neste cenário eventualmente irá consumir mais largura de banda de rede. Considere também um exemplo extremo: vários usuários são assistir a vídeos de alta definição (como resolução de 4K), mantendo chamadas de conferência de alta definição, jogar videogames 3D ou trabalhando em sistemas de CAD. Todas essas podem tornar mesmo uma rede de largura de banda realmente alta praticamente inutilizável.
- **Resolução de tela e o número de telas** - mais largura de banda de rede é necessária para atualização completa telas maiores que telas menores. A tecnologia subjacente faz um trabalho boa de codificação e transmitir apenas as regiões das telas que foram atualizadas, mas uma vez em quando, a tela inteira precisa ser atualizado. Quando o usuário tem uma tela de resolução superior (por exemplo 4K resolução), essa atualização requer mais largura de banda de rede que uma tela com resolução menor (como 1024x768px). Essa mesma lógica se aplica se você usar mais de uma tela para redirecionamento. Largura de banda precisa aumentar com o número de telas.
- **Redirecionamento de área de transferência e dispositivo** - esse é um problema não é muito óbvio, mas em muitos casos se um usuário armazena um grande bloco de dados para a área de transferência, leva algum tempo para que elas transferir do cliente de área de trabalho remota para o servidor. A experiência de downstream pode ser afetada pela experiência de enviar o conteúdo da área de transferência upstream. O mesmo se aplica para redirecionamento de dispositivo - se um scanner ou webcam produz muitos dados que precisam ser enviada ascendente para o servidor ou uma impressora precisa receber um documento grande, armazenamento local precisa estar disponível para um aplicativo em execução na nuvem para copiar um arquivo grande, os usuários podem notar quadros ignorados ou temporariamente "congelada" porque os dados necessários para o redirecionamento de dispositivo está aumentando a largura de banda de rede necessidades do vídeo. 

Quando você avaliar suas necessidades de largura de banda de rede, verifique se a considerar todos esses fatores trabalhando como um sistema.

Agora, volte para o [artigo de largura de banda de rede principal](remoteapp-bandwidth.md)ou ir para teste sua [largura de banda de rede](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Saiba Mais
- [Estimar Azure RemoteApp uso de largura de banda de rede](remoteapp-bandwidth.md)

- [Azure RemoteApp - teste seu uso de largura de banda de rede com alguns cenários comuns](remoteapp-bandwidthtests.md)

- [Azure RemoteApp largura de banda - diretrizes gerais (se você não pode testar seu próprio)](remoteapp-bandwidthguidelines.md)