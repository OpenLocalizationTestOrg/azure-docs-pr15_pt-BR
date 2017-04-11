<properties
    pageTitle="Habilitar afinidade de sessão usando o Kit de ferramentas do Azure para Eclipse"
    description="Saiba como habilitar afinidade de sessão usando o Kit de ferramentas do Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Habilitar afinidade de sessão #

Dentro do Kit de ferramentas do Azure para Eclipse, você pode habilitar afinidade de sessão HTTP ou "sessões conjunta," para suas funções. A imagem a seguir mostra as propriedades de **Balanceamento de carga de** caixa de diálogo usada para ativar o recurso de afinidade de sessão:

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Para habilitar afinidade de sessão para sua função ##

1. A função no Explorador de projeto do Eclipse de atalho, clique **Azure**e clique em **Balanceamento de carga**.
1. Na caixa de diálogo **Propriedades de balanceamento de carga de WorkerRole1** :
    1. Verificar **afinidade de sessão de Enable HTTP (sessões conjunta) para esta função.**
    1. **Ponto de extremidade de entrada para usar**, selecione um ponto de extremidade de entrada para usar, por exemplo, **http (público: 80, particular: 8080)**. Seu aplicativo deve usar esse ponto de extremidade como seu ponto de extremidade HTTP. Você pode habilitar vários pontos de extremidade para sua função, mas você pode selecionar apenas uma para dar suporte a sessões conjunta.
    1. Recrie seu aplicativo.

Quando habilitada, se você tiver mais de uma instância de função, solicitações HTTP provenientes de um determinado cliente continuará sendo tratada pela mesma instância de função.

O Kit de ferramentas do Eclipse permite isso instalando um módulo IIS especial, chamado roteamento de solicitação de aplicativo (ARR) em cada um dos seus instâncias de função. ARR redireciona solicitações HTTP para a instância de função adequada. O Kit de ferramentas reconfigura automaticamente o ponto de extremidade selecionado para que o tráfego HTTP de entrada é primeiro roteado para o software ARR. O Kit de ferramentas também cria uma nova empresa interna que o servidor Java está configurado para ouvir. Esse é o ponto de extremidade usado pelo ARR para redirecionar o tráfego HTTP para a instância de função adequada. Dessa maneira, cada instância de função em sua implantação de várias instâncias serve como um proxy reverso para todas as outras instâncias, permitindo que sessões conjunta.

## <a name="notes-about-session-affinity"></a>Observações sobre afinidade de sessão ##

* Afinidade de sessão não funciona no emulador computação. As configurações podem ser aplicadas no emulador computação sem interferir com seu processo de criação ou calcular a execução do emulador, mas o recurso em si não funcionar dentro o emulador de computação.
* Ativação afinidade de sessão resultará em um aumento na quantidade de espaço em disco ocupado pelos sua implantação do Azure, como softwares adicionais serão baixadas e instaladas em seu instâncias de função quando o serviço é iniciado na nuvem Azure.
* O tempo para inicializar cada função demorará mais.
* Um ponto de extremidade interno, para funcionar como uma rerouter de tráfego conforme mencionado acima, será added.ss

Para obter um exemplo de como manter dados de sessão quando afinidade de sessão está habilitada, veja [como manter dados de sessão com afinidade de sessão][].

## <a name="see-also"></a>Consulte também ##

[Kit de ferramentas de Azure para Eclipse][]

[Criando um aplicativo Hello World para Azure no Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

[Como manter dados de sessão com afinidade de sessão][]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Como manter dados de sessão com afinidade de sessão]: http://go.microsoft.com/fwlink/?LinkID=699539
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
