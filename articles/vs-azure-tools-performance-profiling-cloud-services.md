<properties 
   pageTitle="Testes de desempenho de um serviço de nuvem | Microsoft Azure"
   description="Testar o desempenho de um serviço de nuvem usando o criador de perfil do Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="testing-the-performance-of-a-cloud-service"></a>Testes de desempenho de um serviço de nuvem 

##<a name="overview"></a>Visão geral

Você pode testar o desempenho de um serviço de nuvem das seguintes maneiras:

- Use o diagnóstico do Azure para coletar informações sobre solicitações e conexões e revisar as estatísticas de site que mostram como o serviço executa a partir da perspectiva do cliente. Para começar a usar, consulte [Configurando diagnóstico de serviços de nuvem do Azure e máquinas virtuais]( http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Use o criador de perfil do Visual Studio para obter uma análise aprofundada dos aspectos de computação de como o serviço é executado. Como este tópico descreve, você pode usar o criador de perfil para medir o desempenho como um serviço é executado no Azure. Para obter informações sobre como usar o criador de perfil para medir o desempenho como um serviço é executado localmente em um emulador de computação, consulte [Testando o desempenho de um Azure nuvem serviço localmente no calcular emulador usando o criador de perfil Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## <a name="choosing-a-performance-testing-method"></a>Escolhendo um método de teste de desempenho

###<a name="use-azure-diagnostics-to-collect"></a>Use o diagnóstico do Azure para coletar:###

- Estatísticas em páginas da web ou serviços, como solicitações e conexões.

- Estatísticas em funções, como quantas vezes uma função seja reiniciada.

- Geral conjunto de informações sobre o uso da memória, como a porcentagem de tempo que leva o coletor de lixo ou a memória de uma função em execução.

###<a name="use-the-visual-studio-profiler-to"></a>Use o gerador de perfil do Visual Studio para:###

- Determine quais funções levam mais tempo.

- Meça quanto tempo leva de cada parte de um programa intenso de computação.

- Compare relatórios detalhados de desempenho para duas versões de um serviço.

- Analise alocação de memória em mais detalhes do que o nível de alocações de memória individuais.

- Analise problemas de concorrência em código multithread.

Quando você usa o criador de perfil, você pode coletar dados quando um serviço de nuvem é executado localmente ou no Azure.

###<a name="collect-profiling-data-locally-to"></a>Colete dados de perfil localmente para:###

- Teste o desempenho de uma parte de um serviço de nuvem, como a execução de função de trabalho específica, que não exige uma carga simulada realista.

- Teste o desempenho de um serviço de nuvem em isolamento, em condições controladas.

- Teste o desempenho de um serviço de nuvem antes de implantá-lo no Azure.

- Teste o desempenho de um serviço de nuvem em particular, sem afetar as implantações existentes.

- Teste o desempenho do serviço sem cobrado para em execução no Azure.

###<a name="collect-profiling-data-in-azure-to"></a>Colete dados de perfil no Azure para:###

- Teste o desempenho de um serviço de nuvem sob uma carga simulada ou real.

- Use o método de instrumentação de coleta de dados de perfil, como este tópico descreve mais tarde.

- Teste o desempenho do serviço no mesmo ambiente quando o serviço é executado em produção.

Você normalmente simular uma carga para condições de carga ou serviços de nuvem de teste em normal.

## <a name="profiling-a-cloud-service-in-azure"></a>Definindo o perfil de um serviço de nuvem no Azure

Quando você publica seu serviço de nuvem do Visual Studio, você pode o serviço de perfil e especifique as configurações de perfil que fornecem as informações desejadas. Uma sessão de criação de perfil é iniciada para cada instância de uma função. Para obter mais informações sobre como publicar seu serviço do Visual Studio, consulte [publicação em um serviço de nuvem do Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Para compreender mais sobre o perfil de desempenho no Visual Studio, consulte o [Guia para principiantes de perfil de desempenho](https://msdn.microsoft.com/library/azure/ms182372.aspx) e [Analisar o desempenho do aplicativo por usando ferramentas de criação de perfil](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE] Você pode habilitar IntelliTrace ou perfil quando você publica seu serviço de nuvem. Você não pode ativar ambos.

###<a name="profiler-collection-methods"></a>Métodos de coleta de criador de perfil

Você pode usar métodos de outro conjunto para criar o perfil, com base em seus problemas de desempenho:

- **Amostra de CPU** - esse método coleta estatísticas de aplicativo que são úteis para análise inicial dos problemas de utilização de CPU. Amostragem de CPU é o método sugerido para iniciar a maioria das investigações de desempenho. Há um baixo impacto no aplicativo que você esteja criando o perfil quando você coletar dados de amostragem de CPU.

- **Instrumentação** -esse método coleta dados detalhados de tempo que são útil para análise com foco e para analisar problemas de desempenho de entrada/saída. O método de instrumentação registros cada entrada, de saída e chamada de função das funções em um módulo durante uma geração de perfil. Este método é útil para coletar informações detalhadas de tempo sobre uma seção de seu código e para compreender o impacto das operações de entrada e saídas no desempenho do aplicativo. Este método é desabilitado para um computador que executa um sistema operacional de 32 bits. Essa opção estará disponível somente quando executa o serviço de nuvem no Azure, não localmente no emulador computação.

- **Alocação de memória .NET** - esse método coleta dados de alocação de memória do .NET Framework usando a método de criação de perfil de amostragem. Os dados coletados incluem o número e o tamanho dos objetos alocados.

- **Concorrência** - esse método coleta dados de disputa de recursos e dados de execução do processo e segmento que é útil em aplicativos multithread e vários processos de análise. O método de concorrência coleta de dados para cada evento que a execução de blocos de seu código, como quando um thread aguarda bloqueado acesso a um recurso de aplicativo liberar. Este método é útil para analisar aplicativos multithread.

- Você também pode habilitar a **Criação de perfil de interação de camadas**, que fornece informações adicionais sobre os tempos de execução do ADO.NET síncrono chama em funções de aplicativos de vários níveis que se comunicar com um ou mais bancos de dados. Você pode coletar dados de interação de camadas com qualquer um dos métodos perfil. Para obter mais informações sobre criação de perfil de interação de camada, consulte [Exibir de interações de camada](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Definir configurações de perfil

A ilustração a seguir mostra como configurar suas configurações de perfil da caixa de diálogo Publicar aplicativos do Azure.

![Configurar definições de criação de perfil](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] Para habilitar a caixa de seleção **Habilitar perfis** , você deve ter o criador de perfil instalado no computador local que você está usando para publicar seu serviço de nuvem. Por padrão, o criador de perfil é instalado quando você instala o Visual Studio.

### <a name="to-configure-profiling-settings"></a>Para definir configurações de perfil

1. No Solution Explorer, abra o menu de atalho para o seu projeto Azure e escolha **Publicar**. Para obter etapas detalhadas sobre como publicar um serviço na nuvem, consulte [uma nuvem de publicação de serviço usando as ferramentas do Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Na caixa de diálogo **Publicar aplicativo do Azure** , escolha a guia **Configurações avançadas** .

1. Para habilitar a criação de perfil, marque a caixa de seleção **Habilitar perfis** .

1. Para configurar suas configurações de perfil, escolha o hiperlink **configurações** . Aparece a caixa de diálogo de configurações de perfil.

1. Os botões de opção **qual método de criação de perfil que você gostaria de usar** , escolha o tipo de perfil que você precisa.

1. Para coletar os dados de perfil de interação de camada, marque a caixa de seleção **Habilitar o perfil da interação com a camada** .

1. Para salvar as configurações, escolha o botão de **Okey** .

    Quando você publica este aplicativo, essas configurações são usadas para criar a sessão de criação de perfil para cada função.

## <a name="viewing-profiling-reports"></a>Exibir relatórios de criação de perfil

Uma sessão de criação de perfil é criada para cada instância de uma função em seu serviço de nuvem. Para exibir seus relatórios do perfil de cada sessão do Visual Studio, você pode exibir a janela Server Explorer e escolha o nó de computação do Azure para selecionar uma instância de uma função. Você pode exibir o relatório de perfil conforme mostrado na ilustração a seguir.

![Exibir relatório de perfil do Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Exibir relatórios de perfil

1. Para exibir a janela Gerenciador de servidor no Visual Studio, na barra de menus escolha View, Server Explorer.

1. Escolha o nó de computação do Azure e escolha o nó de implantação do Azure para o serviço de nuvem que você selecionou para o perfil quando você publicou do Visual Studio.

1. Para exibir relatórios de perfil para uma instância, escolha a função no serviço, abra o menu de atalho para uma instância específica e escolha **Exibir o relatório de perfil**.

    O relatório, um arquivo. vsp, agora é baixado do Azure e o status do download aparece no Log de atividade do Azure. Quando o download for concluído, o relatório de perfil aparece em uma guia no editor do Visual Studio chamada <Role name> _<Instance Number>_ <identifier>Vsp. Dados de resumo para o relatório é exibida.

1. Para exibir diferentes modos de exibição do relatório, na lista modo de exibição atual, escolha o tipo de modo de exibição desejado. Para obter mais informações, consulte [Exibições de relatório de ferramentas de criação de perfil](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Próximas etapas

[Depuração de serviços de nuvem](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicação em um serviço de nuvem Azure do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

