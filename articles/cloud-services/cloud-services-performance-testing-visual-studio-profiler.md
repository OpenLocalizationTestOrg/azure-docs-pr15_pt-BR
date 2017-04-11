<properties 
    pageTitle="Definindo o perfil de um serviço de nuvem localmente no emulador computação | Microsoft Azure" 
    services="cloud-services"
    description="Investigar problemas de desempenho nos serviços de nuvem com o criador de perfil do Visual Studio" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testes de desempenho de um serviço de nuvem localmente no emulador computação Azure usando o criador de perfil do Visual Studio

Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho de serviços de nuvem.
Quando você publica um serviço de nuvem para o Azure, você pode ter Visual Studio coletar dados de perfil e analisá-lo localmente, como descrito em [um aplicativo do Azure de criação de perfil][1].
Você também pode usar diagnósticos para controlar uma variedade de contadores de desempenho, conforme descrito em [usando contadores de desempenho no Azure][2].
Talvez você também queira perfil seu aplicativo localmente no emulador computação antes de implantá-lo na nuvem.

Este artigo discute o método de amostragem de CPU de criação de perfil, que pode ser feito localmente no emulador. Amostragem de CPU é um método de criação de perfil que não é muito intrusivo. Em um intervalo de amostragem designado, o criador de perfil tira um instantâneo da pilha de chamadas. Os dados são coletados durante um período de tempo e mostrados em um relatório. Este método de criação de perfil tende indicar onde em um aplicativo intenso de computação maioria do trabalho CPU está sendo feito.  Isso oferece a oportunidade para focalizar o "caminho quente" onde seu aplicativo está gastando mais tempo.



## <a name="1-configure-visual-studio-for-profiling"></a>1: configurar o Visual Studio para criação de perfil

Primeiro, há algumas opções de configuração do Visual Studio que podem ser úteis ao criar o perfil. Para fazer sentido dos relatórios de perfil, você precisará símbolos (arquivos. PDB) para seu aplicativo e também símbolos para bibliotecas do sistema. Você desejará certificar-se de que você fizer referência os servidores de símbolos disponíveis. Para fazer isso, no menu **Ferramentas** no Visual Studio, escolha **Opções**e escolha **depuração**, em seguida, **símbolos**. Certifique-se de que os servidores de símbolo da Microsoft está listado em **locais de arquivos (. PDB) de símbolo**.  Você também pode consultar http://referencesource.microsoft.com/symbols, que pode ter arquivos de símbolos adicionais.

![Opções de símbolo][4]

Se desejar, você pode simplificar os relatórios que o criador de perfil gera definindo Just My Code. Com Just My Code ativado, pilhas de chamada de função são simplificadas para que as chamadas inteiramente internas do .NET Framework e de bibliotecas de estão ocultas dos relatórios. No menu **Ferramentas** , escolha **Opções**. Em seguida, expanda o nó de **Ferramentas de desempenho** e escolha **Geral**. Selecione a caixa de seleção para **Habilitar Just My Code para relatórios do criador de perfil**.

![Opções de meu código apenas][17]

Você pode usar essas instruções com um projeto existente ou um novo projeto.  Se você criar um novo projeto para tentar as técnicas descritas abaixo, escolha um projeto c# **Serviço de nuvem do Azure** e selecione uma **Função da Web** e uma **Função de trabalho**.

![Funções de projeto de serviço de nuvem Azure][5]

Por exemplo, fins, adicionar algum código ao seu projeto que demora muito tempo e demonstra alguns problemas de desempenho óbvios. Por exemplo, adicione o seguinte código a um projeto de função de trabalho:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Ligue para este código do método RunAsync na classe de RoleEntryPoint derivado da função trabalhador. (Ignore o aviso sobre o método executando em sincronia.)

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Criar e executar o seu serviço de nuvem localmente sem depuração (Ctrl + F5), com a configuração de solução definida para **lançamento**. Isso garante que todos os arquivos e pastas são criadas para executar o aplicativo localmente e garante que todos os emuladores são iniciados. Inicie a interface de usuário do emulador calcular na barra de tarefas para verificar se sua função de trabalho está em execução.

## <a name="2-attach-to-a-process"></a>2: anexar a um processo

Em vez de criar o perfil do aplicativo por iniciá-lo no IDE do Visual Studio 2010, você deve anexar o criador de perfil a um processo em execução. 

Para anexar o criador de perfil a um processo, no menu de **análise** , escolha **perfil** e **Anexar/desanexar**.

![Anexar a opção de perfil][6]

Para uma função de trabalho, localize o processo de WaWorkerHost.exe.

![Processo de WaWorkerHost][7]

Se sua pasta de projeto estiver em uma unidade de rede, o criador de perfil solicitará que você forneça outro local para salvar os relatórios de perfil.

 Você também pode anexar a uma função de web anexando a WaIISHost.exe.
Se houver vários processos de função de trabalho em seu aplicativo, você precisa usar o processID para diferenciá-los. Você pode consultar o processID programaticamente acessando o objeto de processo. Por exemplo, se você adicionar este código para o método de execução da classe derivada RoleEntryPoint em uma função, você pode examinar o log a IU de emulador calcular saber qual processo para se conectar ao.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para exibir o log, inicie a interface de usuário do emulador calcular.

![Iniciar o emulador de computação UI][8]

Abra a janela do console do operador função log a IU de emulador calcular clicando na barra de título da janela do console. Você pode ver a identificação do processo no log.

![ID do processo de modo de exibição][9]

Um anexou, execute as etapas na interface do usuário do seu aplicativo (se necessário) para reproduzir o cenário.

Quando você quiser parar criação de perfil, escolha o link **Parar criação de perfil** .

![Interromper a opção de criação de perfil][10]

## <a name="3-view-performance-reports"></a>3: exibir relatórios de desempenho

O relatório de desempenho para o seu aplicativo é exibido.

Neste ponto, o criador de perfil interrompe a execução, salva dados em um arquivo. vsp e exibe um relatório que mostra uma análise de dados.

![Relatório do criador de perfil][11]


Se você vir String.wstrcpy no caminho quente, clique em Just My Code para alterar o modo de exibição para mostrar apenas o código do usuário.  Se você vir String. Concat, tente pressionar o botão Mostrar todos os códigos.

Você deve ver o método concatenar e String. Concat ocupar grande parte do tempo de execução.

![Análise de relatório][12]

Se você adicionou o código de concatenação de cadeia de caracteres neste artigo, você verá um aviso na lista de tarefas para isso. Você também pode ver um aviso de que não há uma quantidade excessiva de coleta de lixo, que é devido ao número de cadeias de caracteres que são criadas e descartado.

![Avisos de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: fazer alterações e comparar o desempenho

Você também pode comparar o desempenho antes e após uma alteração de código.  Interromper o processo em execução e editar o código para substituir a operação de concatenação de cadeia de caracteres com o uso de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Não executar outra de desempenho e, em seguida, compare o desempenho. No Explorador de desempenho, se o é executado estiverem na mesma sessão, você pode apenas selecionar os dois relatórios, abrir o menu de atalho e escolha **Comparar relatórios de desempenho**. Se você quiser comparar com uma execução em outra sessão de desempenho, abra o menu de **análise** e escolha **Comparar relatórios de desempenho**. Especifique os dois arquivos na caixa de diálogo que aparece.

![Comparar a opção de relatórios de desempenho][15]

Os relatórios realçam as diferenças entre o duas executa.

![Relatório de comparação][16]

Parabéns! Você não obteve iniciou com o criador de perfil.

## <a name="troubleshooting"></a>Solução de problemas

- Verifique se você é uma compilação de lançamento de criação de perfil e iniciar sem depuração.

- Se a opção de anexar/desanexar não estiver habilitada no menu gerador de perfil, execute o Assistente de desempenho.

- Use a interface de usuário do emulador calcular para exibir o status do seu aplicativo. 

- Se você tiver problemas ao iniciar aplicativos no emulador ou anexar o criador de perfil, desligar para baixo o emulador de computação e reiniciá-lo. Se isso não resolver o problema, tente reinicializar. Esse problema pode ocorrer se você usar o emulador de computação para suspender e remover implantações em execução.

- Se você usou qualquer um dos comandos de criação de perfil da linha de comando, especialmente as configurações globais, verifique se que VSPerfClrEnv /globaloff foi chamado e que VsPerfMon.exe foi desligado.

- Se quando amostragem, você verá a mensagem "PRF0025: nenhum dado foi coletado," Verifique se o processo anexado ao tem atividade da CPU. Aplicativos que não fazem qualquer trabalho de computação não podem produzir os dados de amostra.  Também é possível que o processo foi encerrado antes de qualquer amostragem foi feita. Verificar que o método de execução para uma função que estiver criando o perfil não encerra.

## <a name="next-steps"></a>Próximas etapas

Instrumentação Azure binários no emulador não é compatível com o criador de perfil do Visual Studio, mas se você quiser testar a alocação de memória, você pode escolher essa opção ao criar o perfil. Você pode também escolher concorrência perfis, que ajudará a determinar se threads são desperdício de tempo que competem de bloqueios ou perfis de interação de camadas, que ajuda você a rastrear problemas de desempenho durante a interação entre os níveis de um aplicativo, com mais frequência entre a camada de dados e uma função de trabalho.  Você pode exibir as consultas de banco de dados que seu aplicativo gera e usar os dados de perfil para melhorar seu uso do banco de dados. Para obter informações sobre criação de perfil de interação de camada, consulte o postagem de blog [passo a passo: usando o criador de perfil de interação de camadas no Visual Studio Team System 2010][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 