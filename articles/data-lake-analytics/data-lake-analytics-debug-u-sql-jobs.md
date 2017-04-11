<properties 
   pageTitle="Depurar trabalhos de U-SQL | Microsoft Azure" 
   description="Saiba como depurar vértice falha U-SQL usando o Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#<a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>Depurar código c# em U-SQL para trabalhos de dados Lucerne Analytics 

Saiba como usar ferramentas do Azure dados Lucerne Visual Studio para depurar trabalhos U-SQL falhou devido a bugs dentro de código do usuário. 

A ferramenta do Visual Studio permite que você baixar código compilado e dados de vértice necessárias do cluster para rastrear e depurar trabalhos com falha.

Sistemas de grande volume normalmente fornecem o modelo de extensibilidade por meio de idiomas como Java, c#, Python, etc. Muitos desses sistemas fornecem limitado runtime depuração de informações, que torna mais difícil de depurar erros de tempo de execução no código personalizado. As ferramentas mais recentes do Visual Studio vem com um recurso chamado "Falha vértice depurar". Usando esse recurso, você pode baixar os dados de tempo de execução do Azure para estação de trabalho local para que você possa depurar Falha c# código personalizado usando o mesmo runtime e dados de entrada exatos da nuvem.  Depois que os problemas são corrigidos, você pode executar novamente o código revisado no Azure das ferramentas de.

Para uma apresentação em vídeo desse recurso, consulte [depurar seu código personalizado no Azure dados Lucerne Analytics](https://mix.office.com/watch/1bt17ibztohcb).

>[AZURE.NOTE] Visual Studio pode travar ou falhar se você não tem as seguintes atualizações de duas janelas: [Microsoft Visual C++ 2015 redistribuível atualização 2](https://www.microsoft.com/download/details.aspx?id=51682), [Universal C Runtime para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##<a name="prerequisites"></a>Pré-requisitos
-   Passou entre o artigo [Introdução](data-lake-analytics-data-lake-tools-get-started.md) .

## <a name="create-and-configure-debug-projects"></a>Criar e configurar projetos de depuração

Quando você abre um trabalho falha na ferramenta de dados Lucerne Visual Studio, você receberá um alerta. As informações de erro detalhadas serão mostradas na guia erro e na barra de alerta amarela na parte superior da janela. 

![Azure vértice de download do visual studio depuração de dados Lucerne Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Baixar vértice e criar uma solução de depuração**

1.  Abra um trabalho de U-SQL falhou no Visual Studio.
2.  Clique em **Baixar** para baixar todos os recursos necessários e fluxos de entrada. Clique em **Repetir** se falha no download.
3.  Clique em **Abrir** após o download for concluído para criar um projeto de depuração local. Uma nova solução do Visual Studio chamada **VertexDebug** com um projeto vazio chamado **LocalVertexHost** será criada.

Se os operadores de definidas pelo usuário são usados no código de U-SQL atrás (Script.usql.cs), você deve criar um projeto de classe biblioteca c# com o código de operadores definidos pelo usuário e incluir o projeto na solução VertexDebug.

Se você tiver registrado. dll conjuntos seu banco de dados de análise de Lucerne de dados, você deve adicionar o código-fonte de conjuntos de à solução VertexDebug.
 
Se você criou uma c# classe biblioteca separada para seu código U-SQL e montagens. dll registrado seu banco de dados de análise de Lucerne de dados, você precisa adicionar projeto c# de conjuntos de origem à solução VertexDebug.

Em alguns casos raros, você pode usar operadores de definidas pelo usuário em U-SQL arquivo code-behind (Script.usql.cs) da solução original. Se você quiser que funcione corretamente, é necessário criar uma biblioteca c# contendo o código-fonte e altere o nome de assembly para aquele registrado no cluster. Você pode obter o nome de assembly registrado no cluster, verificando o script que você tem em execução no cluster. Você pode fazer isso abrindo o trabalho U-SQL e clique em "script" no painel de trabalho. 

**Configurar a solução**

1.  Do Solution explorer, clique com botão direito do projeto c# recém-criado e clique em **Propriedades**.
2.  Defina o caminho de saída como projeto LocalVertexHost caminho do diretório de trabalho. Você pode obter o caminho de pasta de trabalho de projeto LocalVertexHost através das propriedades de LocalVertexHost.
3.  Construir seu projeto c# para colocar o arquivo. PDB no projeto LocalVertexHost diretório de trabalho, ou você pode copiar o arquivo. PDB para essa pasta manualmente.
4.  Em **Configurações de exceção**, verifique exceções de tempo de execução de idiomas comuns:

![Configuração de visual studio de depuração de dados Lucerne Analytics U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##<a name="debug-the-job"></a>Depurar o trabalho

Após ter criado uma solução de depuração baixando o vértice e configurou o ambiente, você pode começar a depurar seu código U-SQL.

1.  Do Solution Explorer, clique com botão direito do projeto **LocalVertexHost** que você acabou de criar, aponte para **Depurar**e, em seguida, clique em **Iniciar nova instância**. O LocalVertexHost deve ser definido como o projeto de inicialização. Você pode ver a seguinte mensagem pela primeira vez que você pode ignorar. Pode levar até um minuto para acessar a tela de depuração.
 
    ![Aviso de visual studio de depuração de dados Lucerne Analytics U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.  Use o Visual Studio com base em experiência de depuração (inspeção variáveis, etc.) para solucionar o problema. 
5.  Depois de ter identificado um problema, corrigir o código e, em seguida, recriar o projeto c# antes de testá-lo novamente até que todos os problemas são resolvidos. Após a depuração foi concluída com êxito, a janela de saída mostrando a seguinte mensagem 

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##<a name="resubmit-the-job"></a>Reenviar o trabalho

Depois de concluir depurar seu código U-SQL, você pode reenviar o trabalho falha.

1. Registre novos conjuntos. dll para seu banco de dados ADLA.

    1.  Do Server Explorer/nuvem Explorer na ferramenta do dados Lucerne Visual Studio, expanda o nó de **bancos de dados** 
    2.  Conjuntos de atalho para conjuntos de registro. 
    3.  Registre seus novos conjuntos. dll no banco de dados de ADLA.
 
2.  Ou copie seu código c# para script.usql.cs - c# arquivo code-behind.
3.  Reenvie seu trabalho.

##<a name="next-steps"></a>Próximas etapas

- [Tutorial: Começar a usar o idioma de análise de Lucerne de dados U-SQL Azure](data-lake-analytics-u-sql-get-started.md)
- [Tutorial: desenvolver scripts U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desenvolver operadores definidos pelo usuário de U-SQL para trabalhos de análise de Lucerne de dados do Azure](data-lake-analytics-u-sql-develop-user-defined-operators.md)

