<properties
    pageTitle="Tutorial de início rápido para o idioma de R de aprendizado de máquina | Microsoft Azure"
    description="Use este tutorial de programação R para começar rapidamente usando a linguagem de R com Studio de aprendizado de máquina do Azure para criar uma solução de previsão."
    keywords="início rápido, idioma r, linguagem de programação de r, tutorial de programação de r"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="garye"/>

# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Tutorial de início rápido para o R linguagem de programação para aprendizado de máquina do Azure

Stephen F Elston, Ph.D.

##  <a name="introduction"></a>Introdução

Este tutorial de início rápido ajuda você a começar rapidamente a estender aprendizado de máquina do Azure, usando a linguagem de programação de R. Siga este tutorial de programação de R para criar, testar e executar código R dentro de aprendizado de máquina do Azure. Enquanto você trabalha tutorial, você criará uma solução completa de previsão usando a linguagem de R no aprendizado de máquina do Azure.  

Aprendizado de máquina do Microsoft Azure contém muitos módulos manipulação de aprendizagem e dados de máquina poderosas. A poderosa linguagem de R foi descrita como a língua franca da análise. Felizmente, manipulação de dados e análise no aprendizado de máquina do Azure pode ser estendida usando R. Esta combinação fornece a escalabilidade e facilidade de implantação de aprendizado de máquina do Azure com a flexibilidade e a análise profunda de R.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


###<a name="forecasting-and-the-dataset"></a>Previsão e o conjunto de dados

Previsão é um método de analítico amplamente empregado e bastante útil. Comum usa o intervalo de previsão de vendas de itens sazonais, determinando níveis de estoque ideal para prever macroeconomic variáveis. Previsão geralmente é feito com modelos da série de tempo.

Dados da série de tempo são dados no qual os valores tem um índice de tempo. O índice de tempo pode ser normal, por exemplo, a cada mês ou a cada minuto, ou irregulares. Um modelo da série de tempo baseia-se nos dados de série de tempo. A linguagem de programação de R contém uma estrutura flexível e ampla análise de dados da série de tempo.

Neste guia de início rápido podemos serão trabalhando com produção laticínio Califórnia e dados de preços. Esses dados incluem mensais informações sobre a produção de várias Laticínios e preço de fat leite, uma mercadoria de referência.

Os dados usados neste artigo, juntamente com scripts de R, podem ser [baixado aqui][download]. Estes dados foi originalmente sintetizados de informações disponíveis da Universidade de Wisconsin em http://future.aae.wisc.edu/tab/production.html.

### <a name="organization"></a>Organização

Nós será progresso por meio de várias etapas ao aprender como criar, testar e executar o código de manipulação R de dados e análise no ambiente de aprendizado de máquina do Azure.  

* Primeiro, exploraremos Noções básicas de usando a linguagem de R no ambiente do Azure Studio de aprendizado de máquina.

* Em seguida, podemos progresso abordando vários aspectos de i/o de dados, código de R e gráficos no ambiente de aprendizado de máquina do Azure.

* Podemos então será construir a primeira parte da nossa solução previsão pela criação de código para limpeza de dados e transformação.

* Com nossos dados preparados podemos executará uma análise das correlações entre várias das variáveis em nosso conjunto de dados.

* Por fim, vamos criar um modelo de previsão de série de tempo sazonal para produção Leite.

##<a id="mlstudio"></a>Interagir com linguagem de R no Studio de aprendizado de máquina

Esta seção apresenta algumas noções básicas de interação com a linguagem de programação de R no ambiente Studio de aprendizado de máquina. O idioma de R fornece uma ferramenta poderosa para criar análises personalizadas e módulos de manipulação de dados dentro do ambiente de aprendizado de máquina do Azure.

Vou usar RStudio para desenvolver, testar e depurar código R em uma pequena escala. Este código é, em seguida, recortar e colar em um [Script de R executar] [ execute-r-script] módulo no Studio de aprendizado de máquina pronto para executar.  

###<a name="the-execute-r-script-module"></a>O módulo executar scripts de R

Dentro do Studio de aprendizado de máquina, os scripts de R são executados dentro do [Executar scripts de R] [ execute-r-script] módulo. Um exemplo de [Executar scripts de R] [ execute-r-script] módulo no Studio de aprendizado de máquina é mostrado na Figura 1.

 ![Linguagem de programação de R: módulo de R executar o Script selecionado no Studio de aprendizado de máquina][1]

*Figura 1. O ambiente de Studio de aprendizado de máquina mostrando o módulo de executar scripts de R selecionado.*

Consultando a Figura 1, vejamos algumas das principais partes do ambiente Studio de aprendizado de máquina para trabalhar com o [Executar scripts de R] [ execute-r-script] módulo.

- Os módulos do experimento são mostrados no painel central.

- A parte superior do painel direito contém uma janela para exibir e editar seus scripts de R.  

- A parte inferior do painel direito mostra algumas propriedades do [Executar scripts de R][execute-r-script]. Você pode exibir os logs de erro e saída clicando nos pontos apropriados que esse painel.

Naturalmente, abordaremos o [Executar scripts de R] [ execute-r-script] detalhadamente o restante deste documento.

Ao trabalhar com funções de R complexas, é recomendável que você edite, testa e depurar em RStudio. Assim como qualquer desenvolvimento de software, estenda o código de forma incremental e testá-lo em pequenos casos de teste simples. Em seguida, recortar e colar suas funções em janela de script R do [Executar scripts de R] [ execute-r-script] módulo. Essa abordagem permite que você aproveite o ambiente de desenvolvimento integrado do RStudio (IDE) e potência de aprendizado de máquina do Azure.  

####<a name="execute-r-code"></a>Executar código R

Qualquer código R no [Executar scripts de R] [ execute-r-script] módulo será executada quando você executar o experimento clicando no botão **Executar** . Quando a execução for concluída, uma marca de seleção aparecerá no [Executar scripts de R] [ execute-r-script] ícone.

####<a name="defensive-r-coding-for-azure-machine-learning"></a>Codificação de R defesa para aprendizado de máquina do Azure

Se você estiver desenvolvendo código R para, digamos, um serviço web usando o aprendizado de máquina do Azure, você deve planejar definitivamente como seu código lidará com uma entrada de dados inesperados e exceções. Para manter a clareza, eu não incluíram muito a respeito das marcando ou na maioria dos exemplos de código mostrados de manipulação de exceção. No entanto, como podemos continuar eu lhe dará vários exemplos de funções usando o recurso de manipulação de exceção do R.  

Se precisar de um tratamento mais completo R de manipulação de exceção, é recomendável que você leia que as seções aplicáveis do livro por Wickham listada no [Apêndice B - leitura adicional](#appendixb).


####<a name="debug-and-test-r-in-machine-learning-studio"></a>Depurar e testar R no Studio de aprendizado de máquina

Para repetir, é recomendável testar e depurar seu código R em uma pequena escala em RStudio. No entanto, há casos em que você precisará rastrear problemas de código de R no [Executar scripts de R] [ execute-r-script] em si. Além disso, é recomendável verificar os resultados no Studio de aprendizado de máquina.

Saída da execução de seu código de R e na plataforma de aprendizado de máquina do Azure encontra-se principalmente em la. Algumas informações adicionais serão vistas no Error.  

Se ocorrer um erro no Studio de aprendizado de máquina enquanto executa o seu código de R, seu primeiro curso de ação deve ser examinar Error. Este arquivo pode conter mensagens de erro úteis para ajudá-lo a compreender e corrigir o erro. Para exibir Error, clique em **log de erros do modo de exibição** no **painel Propriedades** do [Executar scripts de R] [ execute-r-script] que contém o erro.

Por exemplo, eu tiver executado o seguinte código de R, com uma variável y indefinido, em um [Script de R executar] [ execute-r-script] módulo:

    x <- 1.0
    z <- x + y

Este código falha ao executar, resultando em uma condição de erro. Clicando no **log de erros do modo de exibição** no **painel Propriedades** produz a exibição mostrada na Figura 2.

  ![Mensagem de erro pop-up][2]

*Figura 2. Mensagem de erro pop-up.*

Parece que precisamos examinar la para ver a mensagem de erro de R. Clique em [Executar scripts de R] [ execute-r-script] e clique no item **la de modo de exibição** no **painel Propriedades** à direita. Abre uma nova janela do navegador e posso ver o seguinte.


    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found
    
    
    object 'y' not found
    ----------- End of error message from R -----------

Essa mensagem de erro contém sem surpresas e Identifique claramente o problema.

Para inspecionar o valor de qualquer objeto em R, você pode imprimir esses valores para o arquivo de saída. log. As regras para examinar os valores de objeto são essencialmente iguais a uma sessão de R interativa. Por exemplo, se você digitar um nome de variável em uma linha, o valor do objeto serão impressas para o arquivo de saída. log.  

####<a name="packages-in-machine-learning-studio"></a>Pacotes no Studio de aprendizado de máquina

Azure aprendizado de máquina vem com mais de 350 pacotes de idioma R pré-instalados. Você pode usar o seguinte código no [Executar scripts de R] [ execute-r-script] módulo para recuperar uma lista dos pacotes pré-instalados.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Se você não entende a última linha desse código no momento, leia mais. O restante deste documento extensivamente abordaremos usando R no ambiente de aprendizado de máquina do Azure.

### <a name="introduction-to-rstudio"></a>Introdução ao RStudio

RStudio é um IDE amplamente usado para R. Vou usar RStudio para edição, testar e depurar alguns dos códigos R usados neste guia de início rápido. Depois que o código de R é testado e pronto, você simplesmente recortar e colar do editor de RStudio em um Studio de aprendizado de máquina [Executar scripts de R] [ execute-r-script] módulo.  

Se você não tiver a linguagem de programação de R instalada em seu computador desktop, é recomendável que você faça isso agora. Downloads gratuitos de idioma de abrir origem R estão disponíveis na abrangente R arquivamento rede (CRAN) em [http://www.r-project.org/](http://www.r-project.org/). Existem downloads disponíveis para Windows, Mac OS e Linux/UNIX. Escolha um espelho próximo e siga as instruções de download. Além disso, CRAN contém uma grande variedade de pacotes de manipulação de dados e análise úteis.

Se você estiver começando a usar o RStudio, baixe e instale a versão de área de trabalho. Você pode encontrar que o RStudio downloads para Windows, Mac OS e Linux/UNIX em http://www.rstudio.com/products/RStudio/. Siga as instruções fornecidas para instalar o RStudio em seu computador desktop.  

Uma introdução tutorial de RStudio está disponível em https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Posso forneça algumas informações adicionais sobre como usar RStudio no [Apêndice A][appendixa].  

##<a id="scriptmodule"></a>Obter dados e sair o módulo executar scripts de R

Nesta seção, abordaremos como você obtém dados dentro e fora do [Executar scripts de R] [ execute-r-script] módulo. Examinaremos como lidar com vários tipos de dados, leia dentro e fora do [Executar scripts de R] [ execute-r-script] módulo.

O código completo para essa seção é no arquivo zip que você baixou anteriormente.

###<a name="load-and-check-data-in-machine-learning-studio"></a>Carregar e verificar os dados no Studio de aprendizado de máquina

####<a id="loading"></a>Carregar o conjunto de dados

Vamos começar carregando o arquivo **csdairydata.csv** em Studio de aprendizado de máquina do Azure.

- Inicie o seu ambiente do Azure Studio de aprendizado de máquina.

- Clique em __+ novo__ no canto inferior esquerdo da tela e selecione **Dataset**.

- Selecione **Do arquivo Local**e, em seguida, **Navegue** para selecionar o arquivo.

- Verifique se que você selecionou o **arquivo CSV genérico com cabeçalho (. csv)** como o tipo para o conjunto de dados.

- Clique na marca de seleção.

- Após o conjunto de dados foi carregado, você deve ver o novo dataset clicando na guia **conjuntos de dados** .  

####<a name="create-an-experiment"></a>Criar um experimento

Agora que temos alguns dados no Studio de aprendizado de máquina, precisamos criar um experimento para fazer a análise.  

- Clique em __+ novo__ no canto inferior esquerdo e selecione **experimento**, **Experimento em branco**.

- Você pode nomear seu experimento selecionando e modificando, o título **experimento criado em …** na parte superior da página. Por exemplo, alterá-la para a **Análise de Laticínios CA**.

- À esquerda da página experimento, expanda **Os conjuntos de dados salvo**e **Meus conjuntos de dados**. Você deve ver os **cadairydata.csv** que você carregou anteriormente.

- Arraste e solte o **conjunto de dados de csdairydata.csv** no experimento.

- Na caixa de **pesquisa experimentar itens** na parte superior do painel à esquerda, digite [Executar scripts de R][execute-r-script]. Você verá o módulo aparecem na lista de pesquisa.

- Arraste e solte o [Executar scripts de R] [ execute-r-script] módulo em sua paleta.  

- Conecte a saída do **conjunto de dados de csdairydata.csv** à entrada mais à esquerda (**Dataset1**) do [Executar scripts de R][execute-r-script].

- **Não se esqueça de clique em 'Salvar'!**  

Neste ponto seu experimento deve parecer com Figura 3.

![A análise de Laticínios CA experimentar dataset e módulo executar scripts de R][3]

*Figura 3. A análise de Laticínios CA experimentar dataset e módulo executar scripts de R.*

####<a name="check-on-the-data"></a>Verificar os dados

Vamos dar uma olhada os dados que estamos carregou no nosso experimento. O experimento, clique na saída do **conjunto de dados cadairydata.csv** e selecione **Visualizar**. Você verá algo parecido com Figura 4.  

![Resumo do dataset cadairydata.csv][4]

*Figura 4. Resumo do dataset cadairydata.csv.*

Neste modo de exibição, podemos ver muitas informações úteis. Podemos ver as primeiras várias linhas desse conjunto de dados. Se podemos selecionar uma coluna, a seção de estatísticas mostra mais informações sobre a coluna. Por exemplo, a linha de tipo de recurso mostra a nos quais tipos de dados do Azure Studio de aprendizado de máquina atribuído à coluna. Ter uma rápida olhada esta é uma verificação de integridade de boa antes de começar a fazer qualquer trabalho sério.

### <a name="first-r-script"></a>Primeiro script de R

Vamos criar um script de R primeiro simples para experimentar no Azure Studio de aprendizado de máquina. Criou e testado o seguinte script em RStudio.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Agora, preciso transferir esse script para Studio de aprendizado de máquina do Azure. Eu poderia simplesmente recortar e colar. No entanto, nesse caso, eu irá transferir meu script R por meio de um arquivo zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo executar scripts de R

Vamos dar uma olhada as entradas para [Executar scripts de R] [ execute-r-script] módulo. Neste exemplo podemos lê os dados de Laticínios Califórnia no [Executar scripts de R] [ execute-r-script] módulo.  

Há três entradas possíveis para [Executar scripts de R] [ execute-r-script] módulo. Você pode usar qualquer uma ou todas essas entradas, dependendo do seu aplicativo. Também é perfeitamente razoável utilizar um script de R que não leva nenhuma entrada de forma alguma.  

Vamos examinar cada um dessas entradas, indo da esquerda para a direita. Você pode ver os nomes de cada uma das entradas posicionando o cursor sobre a entrada e lendo a dica de ferramenta.  

#### <a name="script-bundle"></a>Pacote de script

O pacote de Script de entrada permite que você passar o conteúdo de um arquivo zip para [Executar scripts de R] [ execute-r-script] módulo. Você pode usar um dos comandos a seguir para ler o conteúdo do arquivo zip em seu código de R.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [AZURE.NOTE] Azure aprendizado de máquina trata arquivos em zip como se eles estão no src / diretório, então você precisa prefixar seus nomes de arquivo com este nome de diretório. Por exemplo, se o zip contém os arquivos `yourfile.R` e `yourData.rdata` na raiz do zip, endereço como `src/yourfile.R` e `src/yourData.rdata` ao usar `source` e `load`.

Já discutimos carregamento de conjuntos de dados ao [carregar o conjunto de dados](#loading). Após ter criado e testado o script de R mostrado na seção anterior, faça o seguinte:

1. Salve o script R em um. Arquivo de R. Para chamar o meu arquivo de script "simpleplot. R". Aqui está o conteúdo.

        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')

2.  Criar um arquivo zip e copie o script para este arquivo zip. No Windows, você pode com o botão direito no arquivo e selecione __Enviar para__e __pasta compactada__. Isso criará um novo arquivo zip que contém o "simpleplot. Arquivo de R".

3.  Adicione o seu arquivo para os **conjuntos de dados** no Studio de aprendizado de máquina, especificando o tipo como **zip**. Agora você deve ver o arquivo zip em seus conjuntos de dados.

4.  Arraste e solte o arquivo zip de **conjuntos de dados** para a **tela de ML Studio**.

5.  Conecte a saída do ícone **zip dados** para a entrada de **Pacote de Script** do [Executar scripts de R] [ execute-r-script] módulo.

6.  Digite o `source()` função com seu nome de arquivo zip na janela de código para [Executar scripts de R] [ execute-r-script] módulo. No meu caso eu digitado `source("src/simpleplot.R")`.  

7.  Certifique-se de clicar em **Salvar**.

Assim que essas etapas forem concluídas, o [Script de R executar] [ execute-r-script] módulo executará o script R no arquivo zip quando o experimento é executado. Neste ponto seu experimento deve parecer com Figura 5.

![Experimente usar compactado script R][6]

*Figura 5. Experimente usando script de R compactado.*

#### <a name="dataset1"></a>DataSet1

Você pode passar uma tabela retangular de dados ao seu código de R usando a entrada de Dataset1. Em nosso script simple a `maml.mapInputPort(1)` função lê os dados de porta 1. Esses dados são atribuídos a um nome de variável dataframe em seu código. Em nosso script simple a primeira linha de código realiza a atribuição.

    cadairydata <- maml.mapInputPort(1)

Execute seu experimento clicando no botão **Executar** . Quando termina a execução, clique em [Executar scripts de R] [ execute-r-script] módulo e clique em **log de saída do modo de exibição** no painel Propriedades. Uma nova página deve aparecer em seu navegador mostrando o conteúdo do arquivo de saída. log. Quando você rola para baixo, você verá algo semelhante ao seguinte.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Futuramente a página para baixo é que informações mais detalhadas sobre as colunas, que se parecerá com o seguinte.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Esses resultados são principalmente conforme o esperado, com 228 observações e 9 colunas na dataframe. Podemos ver os nomes de coluna, o tipo de dados R e uma amostra de cada coluna.

> [AZURE.NOTE] Este mesma saída impressa é convenientemente disponível a partir da saída de dispositivo R do [Executar scripts de R] [ execute-r-script] módulo. Abordaremos saídas do [Executar scripts de R] [ execute-r-script] módulo na próxima seção.  

####<a name="dataset2"></a>Dataset2

O comportamento da entrada Dataset2 é idêntico do Dataset1. Usando essa entrada, você pode passar uma segunda tabela retangular dos dados em seu código de R. A função `maml.mapInputPort(2)`, com o argumento 2, é usado para passar dados.  

###<a name="execute-r-script-outputs"></a>Executar scripts de R saídas

####<a name="output-a-dataframe"></a>Um dataframe de saída

Você pode gerar uma saída o conteúdo de um dataframe R como uma tabela retangular através da porta Dataset1 resultado usando a `maml.mapOutputPort()` função. Em nosso script R simple, isso é executado pela seguinte linha.

    maml.mapOutputPort('cadairydata')

Após a execução do experimento, clique na porta de saída de resultado Dataset1 e clique em **Visualizar**. Você verá algo parecido com Figura 6.

![A visualização da saída dos dados Laticínios Califórnia][7]

*Figura 6. A visualização da saída dos dados Laticínios Califórnia.*

Esta saída parece idêntica à entrada, exatamente como esperado.  

### <a name="r-device-output"></a>Saída do dispositivo R

A saída de dispositivo do [Executar scripts de R] [ execute-r-script] módulo contém saída de mensagens e elementos gráficos. Ambas as mensagens de saída e erro padrão padrão de R são enviadas para a porta de saída do dispositivo R.  

Para exibir a saída de dispositivo R, clique na porta e em **Visualizar**. Podemos ver a saída padrão e o erro padrão do script R na Figura 7.

![Saída padrão e erro padrão da porta dispositivo R][8]

*Figura 7. Saída padrão e erro padrão da porta dispositivo R.*

Rolar para baixo podemos ver a saída de elementos gráficos do nosso script R na Figura 8.  

![Saída de elementos gráficos da porta dispositivo R][9]

*Figura 8. Gráficos de saída da porta dispositivo R.*  

##<a id="filtering"></a>Filtragem de dados e transformação

Nesta seção, podemos executará alguns dados básicos filtragem e operações de transformação nos dados Laticínios Califórnia. No final desta seção teremos dados em um formato adequado para a criação de um modelo analítico.  

Mais especificamente, nesta seção nós executará várias tarefas comuns de dados limpeza e transformação: digite transformação, filtrando dataframes, adicionando novas colunas calculadas e transformações de valor. Este plano de fundo deverá ajudá-lo a lidar com as variações muitos encontradas em problemas reais.

O código de R completo para esta seção está disponível no arquivo zip que você baixou anteriormente.

### <a name="type-transformations"></a>Transformações de tipo

Agora que podemos ler os dados de Laticínios Califórnia no código R no [Executar scripts de R] [ execute-r-script] módulo, precisamos garantir que os dados nas colunas tem tipo pretendido e formato.  

R é uma linguagem dinamicamente digitada, o que significa que tipos de dados são forçados entre si conforme necessário. Os tipos de dados atômica em R incluem numéricos, lógico e caractere. O tipo de fator é usado para armazenar compacta dados categóricos. Você pode encontrar muito mais informações sobre tipos de dados nas referências no [Apêndice B - leituras adicionais](#appendixb).

Quando os dados tabulares é lido em R de uma fonte externa, sempre é uma boa ideia verificar os tipos de resultantes nas colunas. Você pode querer uma coluna de caractere de tipo, mas em muitos casos isso aparecerá como fator ou vice-versa. Em outros casos uma coluna que você acha que deve ser numérico é representado por dados de caracteres, por exemplo, '1,23' em vez de número de ponto de 1.23 como uma flutuante.  

Felizmente, é fácil converter um tipo para outro, como mapeamento é possível. Por exemplo, é possível converter 'Nevada' em um valor numérico, mas você poderá convertê-lo um fator (variável categórica). Como outro exemplo, você pode converter um 1 numéricos em um caractere '1' ou um fator.  

A sintaxe para qualquer uma dessas conversões é simples: `as.datatype()`. Essas funções de conversão de tipo incluem o seguinte.

* `as.numeric()`

* `as.character()`

* `as.logical()`

* `as.factor()`

Examinando os tipos de dados das colunas nós de entrada na seção anterior: todas as colunas são do tipo numérico, exceto para a coluna rotulada 'Mês', que é do caractere de tipo. Vamos convertê-lo para um fator e testar os resultados.  

Posso ter excluído a linha que criou a matriz de scatterplot e adicionou uma linha converter a coluna 'Mês' um fator. Em minha experimento eu apenas será recortar e colar o código de R na janela de código do [Script de R executar] [ execute-r-script] módulo. Você também pode atualizar o arquivo zip e carregue-o no Studio de aprendizado de máquina do Azure, mas isso o leva várias etapas.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Vamos executar esse código e examine o log de saída para o script R. Os dados relevantes do registro são mostrados na Figura 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 9. Resumo da dataframe com uma variável de fator.*

O tipo de mês agora deve dizer '**fator com 14 níveis**'. Isso é um problema, pois há apenas 12 meses do ano. Você também pode verificar se o tipo em **Visualizar** da porta resultado Dataset é '**Categorical**'.

O problema é que a coluna de 'Mês' não foi codificada sistemática. Em alguns casos, um mês é chamado abril e em outros, ele é abreviado como abr. Nós pode resolver esse problema cortando a cadeia de caracteres para caracteres de 3. A linha de código agora se parece com o seguinte:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Execute novamente o experimento e exibir o log de saída. Os resultados esperados são mostrados na Figura 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 10. Resumo do dataframe com o número de níveis de fator correto.*

Nossa variável fator agora tem os níveis de 12 desejados.

###<a name="basic-data-frame-filtering"></a>Filtragem de quadro de dados básico

R dataframes suporte a recursos de filtragem avançados. Conjuntos de dados podem ser subdividida usando filtros lógicos em linhas ou colunas. Em muitos casos, os critérios de filtro complexo será necessários. As referências no [Apêndice B - leituras adicionais](#appendixb) contém exemplos de amplo de filtragem dataframes.  

Há um pouco de filtragem devemos fazer em nosso conjunto de dados. Se você examinar as colunas na dataframe cadairydata, você verá duas colunas desnecessárias. A primeira coluna contém apenas um número de linha, que não é muito útil. A segunda coluna, Year.Month, contém informações redundantes. Nós pode excluir facilmente essas colunas usando o seguinte código de R.

> [AZURE.NOTE] De agora em diante nesta seção, eu apenas mostrará o código adicional eu estiver adicionando no [Executar scripts de R] [ execute-r-script] módulo. Vou adicionar cada nova linha **antes do** `str()` função. Eu use esta função para verificar meus resultados no Azure Studio de aprendizado de máquina.

Posso adicionar a seguinte linha ao meu código R no [Executar scripts de R] [ execute-r-script] módulo.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Executar este código no seu experimento e verificar o resultado do log de saída. Esses resultados são mostrados na Figura 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 11. Resumo da dataframe com duas colunas removidos.*

Boa notícia! Podemos obter os resultados esperados.

###<a name="add-a-new-column"></a>Adicionar uma nova coluna

Para criar modelos de série de tempo será conveniente ter uma coluna que contém os meses desde o início da série de tempo. Vamos criar uma nova coluna 'Month.Count'.

Para ajudar a organizar o código vamos criar nossa primeira função simple, `num.month()`. Em seguida, podemos aplicará esta função para criar uma nova coluna na dataframe. O novo código é da seguinte maneira.

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

Agora, execute o experimento atualizado e usar o log de saída para exibir os resultados. Esses resultados são mostrados na Figura 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 12. Resumo da dataframe com a coluna adicional.*

Parece que tudo está funcionando. Temos a nova coluna com os valores esperados em nosso dataframe.

###<a name="value-transformations"></a>Transformações de valor

Nesta seção, nós executará algumas transformações simples nos valores de algumas das colunas da nossa dataframe. O idioma de R suporta transformações de valor quase aleatório. As referências no [Apêndice B - leitura adicional](#appendixb) contém exemplos de considerável.

Se você examinar os valores nos resumos dos nosso dataframe você verá algo ímpar aqui. É mais sorvete que leite produzida na Califórnia? Não, claro não, como isso faz sentido, SODA como esse fato podem ser de alguns dos EUA amantes de sorvete. As unidades são diferentes. O preço é em unidades de nós libras, leite é em unidades de 1 milhão EUA libras, sorvete é em unidades de 1.000 conosco galões e queijo cottage é em unidades de 1.000 libras EUA. Considerando que sorvete avalia sobre 6.5 libras por galão, pode facilmente fazemos a multiplicação para converter esses valores para que eles estão todos em unidades iguais de 1.000 libras.

Para nosso modelo de previsão usamos um modelo multiplicativos de tendência e ajuste sazonal desses dados. Uma transformação de log nos permite usar um modelo linear, simplificar esse processo. Podemos aplicar a transformação de log na mesma função onde o multiplicador é aplicado.

No código a seguir, posso definir uma nova função, `log.transform()`e aplicá-lo para as linhas que contêm os valores numéricos. R `Map()` função é usada para aplicar o `log.transform()` função para as colunas selecionadas da dataframe. `Map()`é semelhante a `apply()` , mas permite mais de uma lista de argumentos para a função. Observe que uma lista de multiplicadores fornece o segundo argumento para a `log.transform()` função. O `na.omit()` função é usada como um pouco de limpeza para garantir que não temos valores ausentes ou indefinidos na dataframe.

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

Não há bastante ocorra bit no `log.transform()` função. Na maioria deste código está verificando possíveis problemas com os argumentos ou lidar com exceções, que ainda podem ocorrer durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

O objetivo da programação de defesa é evitar a falha de uma única função que impede que o processamento de continuar. Uma falha de abrupta de uma análise de execução longa pode ser muito frustrante para os usuários. Para evitar essa situação, valores de retorno padrão devem ser escolhidos que limita danos para processamento de downstream. Uma mensagem também é gerada para alertar os usuários que algo deu errado.

Se você não estiver acostumado a programação de defesa em R, todo esse código pode parecer um pouco sobrecarregado. Posso apresentará as etapas principais:

1. Um vetor de quatro mensagens é definido. Essas mensagens são usadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com este código.

2. Para retornar um valor de ND para cada caso. Há muitas outras possibilidades que podem ter menos efeitos de lado. Eu poderia retornar um vetor de zeros ou o vetor de entrada original, por exemplo.

3. Verificações são executadas nos argumentos para a função. Em cada caso, se um erro for detectado, será retornado um valor padrão e uma mensagem é produzida pela `warning()` função. Estou usando o `warning()` em vez de `stop()` como o último terminará execução, exatamente o que estou tentando evitar. Observe que eu escreveu este código em um estilo de procedimento, como neste caso uma abordagem funcional pareceu complexo e obscuros.

4. Os cálculos de log dispostos em `tryCatch()` para que exceções não provocará uma parada abrupta para processamento. Sem `tryCatch()` a maioria dos erros gerados pelo resultado de funções de R em um sinal de parada, que faz exatamente isso.

Executar esse código R em seu experimento e ter uma olhada na saída impressa no arquivo de saída. log. Agora você verá os valores transformados das quatro colunas no log, conforme mostrado na Figura 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figura 13. Resumo dos valores transformados na dataframe.*

Podemos ver que os valores foram transformados. Agora a produção leite bastante excede todos os outra produção de produto Laticínios, recuperando que estamos agora está vendo uma escala logarítmica.

Neste ponto nossos dados é limpa e estamos prontos para alguns modelagem. Olhando para a visualização resumo para a saída de conjunto de dados de resultado de nosso [Executar scripts de R] [ execute-r-script] módulo, você verá a coluna de 'Mês' é 'Categorical' com 12 valores exclusivos, novamente, basta como podemos desejados.

##<a id="timeseries"></a>Análise de correlação e objetos de série de tempo

Nesta seção abordaremos explore alguns objetos básicos de série de tempo R e analisar as correlações entre algumas das variáveis. Nosso objetivo é um dataframe que contém as informações de correlação dupla em várias lento de saída.

O código de R completo para essa seção é no arquivo zip que você baixou anteriormente.

###<a name="time-series-objects-in-r"></a>Objetos de série de tempo em R

Como já mencionado, tempo série é uma série de valores de dados indexados por vez. Objetos de série de tempo de R são usados para criar e gerenciar o índice de tempo. Há diversas vantagens em usar objetos de série de tempo. Objetos de série de tempo livre você de muitos detalhes de gerenciar os valores de índice de série de tempo que são encapsulados no objeto. Além disso, objetos de série de tempo permite que você usar os vários métodos de série de tempo para plotar, imprimindo, modelagem, etc.

A classe de série de tempo de POSIXct geralmente é usada e é relativamente simple. Esta série temporal classe tempo de medidas desde o início da época, 1 de janeiro de 1970. Usaremos objetos de série de tempo de POSIXct neste exemplo. Outras classes de objeto série de tempo amplamente usados R incluem zoo e xts, série temporal extensível.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Exemplo de objeto de série de tempo

Vamos começar com o nosso exemplo. Arraste e solte um **novo** [Executar scripts de R] [ execute-r-script] módulo em seu experimento. Conectar-se a porta de saída de resultado Dataset1 do existente [Executar scripts de R] [ execute-r-script] porta do novo [Executar R Script] de entrada do módulo para o Dataset1[ execute-r-script] módulo.

Como fazia para os exemplos de primeiro, como podemos passam por exemplo, em alguns pontos para mostrará apenas as linhas adicionais incrementais de código de R em cada etapa.  

####    <a name="reading-the-dataframe"></a>O dataframe de leitura

A primeira etapa, vamos lido em uma dataframe e certificar-se de que podemos obter os resultados esperados. O seguinte código deve fazer o trabalho.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Agora, execute o experimento. O log da nova forma de executar scripts de R deve parecer com Figura 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figura 14. Resumo da dataframe no módulo executar scripts de R.*

Esses dados são dos tipos esperados e formato. Observe que a coluna de 'Mês' do fator de tipo e tem o número de níveis esperado.

####<a name="creating-a-time-series-object"></a>Criando um objeto de série de tempo

Precisamos adicionar um objeto de série de tempo à nossa dataframe. Substitua o código atual com o seguinte, que adiciona uma nova coluna da classe POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Agora, verifique o log. Ele deve parecer com Figura 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 15. Resumo do dataframe com um objeto de série de tempo.*

Podemos ver o resumo que a nova coluna é, na verdade da classe POSIXct.

###<a name="exploring-and-transforming-the-data"></a>Explorando e transformar os dados

Vamos explorar algumas das variáveis neste DataSet. Uma matriz de scatterplot é uma boa maneira para produzir uma rápida olhada. Eu estou substituindo o `str()` função no código R anterior com a seguinte linha.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Executar este código e ver o que acontece. A plotagem produzida na porta de dispositivo R deve parecer com Figura 16.

![Matriz de Scatterplot de variáveis selecionadas][17]

*Figura 16. Matriz de Scatterplot de variáveis selecionadas.*

Há algumas estruturas estranha nas relações entre essas variáveis. Talvez isso surge de tendências nos dados e do fato de que não adotaram as variáveis.

###<a name="correlation-analysis"></a>Análise de correlação

Para executar análise de correlação precisamos eliminação de tendências tanto padronizar as variáveis. Podemos simplesmente usar a R `scale()` função, que centraliza tanto escalas de variáveis. Esta função também poderá se deparar com mais rapidez. No entanto, quero mostrar a você um exemplo de programação de defesa em R.

O `ts.detrend()` função mostrada abaixo executa ambas essas operações. As duas linhas de código a seguir eliminação tendência os dados e, em seguida, padronizar os valores.

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
    )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

Não há bastante ocorra bit no `ts.detrend()` função. Na maioria deste código está verificando possíveis problemas com os argumentos ou lidar com exceções, que ainda podem ocorrer durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

Já discutimos um exemplo de programação de defesa em [transformações de valor](#valuetransformations). Ambos os blocos de computação dispostos em `tryCatch()`. Para alguns erros faz sentido para retornar o vetor de entrada original e em outros casos, para retornar um vetor de zeros.  

Observe que a usada para eliminação tendências de regressão linear é uma regressão de série de tempo. A variável de previsão é um objeto de série de tempo.  

Uma vez `ts.detrend()` é definido podemos aplicá-lo para as variáveis de interesse em nosso dataframe. Nós deve forçar a lista resultante criada por `lapply()` para dataframe de dados usando `as.data.frame()`. Por causa de defesa aspectos de `ts.detrend()`, Falha ao processo de uma das variáveis não impedirá processamento correto dos outros.  

A linha final do código cria um scatterplot par. Após executar o código de R, os resultados do scatterplot são mostrados na Figura 17.

![Scatterplot dupla de série temporal eliminação tendência e padronizado][18]

*Figura 17. Scatterplot dupla de série temporal eliminação tendência e padronizado.*

Você pode comparar esses resultados àqueles mostrado na Figura 16. Com a tendência removidos e as variáveis padronizadas, podemos ver muito menos estrutura nas relações entre essas variáveis.

O código para calcular as correlações como objetos ccf R é da seguinte maneira.

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

Executar este código produz o log mostrado na Figura 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Figura 18. Lista de ccf objetos da análise de correlação par.*

Há um valor de correlação para cada tempo de latência. Nenhum desses valores de correlação é grande o suficiente para ser significativo. Portanto, podemos possa concluir que podemos modelar cada variável independente.

###<a name="output-a-dataframe"></a>Um dataframe de saída

Podemos ter computado as correlações dupla como uma lista de objetos de R ccf. Isso apresenta um pouco de um problema como a porta de saída do conjunto de dados do resultado realmente requer um dataframe. Além disso, o objeto ccf é uma lista e queremos que somente os valores no primeiro elemento dessa lista, as correlações na vários lento.

O código a seguir extrai os valores de tempo de latência na lista de objetos de ccf, que são listas.

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

A primeira linha de código é um pouco complicada e alguma explicação pode ajudá-lo a compreendê-lo. Trabalho de dentro para fora, temos o seguinte:

1.  O '**[[**' operador com o argumento '**1**' seleciona o vetor de correlações na lento do primeiro elemento da lista de objetos ccf.

2.  O `do.call()` função se aplica a `rbind()` função sobre os elementos da lista retorna por `lapply()`.

3.  O `data.frame()` função impõe o resultado produzido por `do.call()` para um dataframe.

Observe que os nomes de linha em uma coluna da dataframe. Fazer assim preserva a linha nomes quando elas são saída do [Executar scripts de R][execute-r-script].

Executando o código gera a saída mostrada na Figura 19 quando posso **Visualizar** a saída da porta de conjunto de dados do resultado. Os nomes de linha são na primeira coluna, como previsto.

![Resultados da análise de correlação][20]

*Figura 19. Resultados da análise de correlação de saída.*

##<a id="seasonalforecasting"></a>Exemplo de série de tempo: previsão sazonal

Nossos dados agora estão em um formato adequado para análise e podemos ter determinado que não há nenhuma correlações significativas entre as variáveis. Vamos continuar e criar uma série de tempo no modelo de previsão. Usando esse modelo podemos preveja produção de leite Califórnia para a 12 meses de 2013.

Nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal. A previsão concluída é o produto desses dois componentes. Esse tipo de modelo é conhecido como um modelo multiplicativos. A alternativa é um modelo aditivo. Nós já aplicou uma transformação de log para as variáveis de interesse, que torna essa análise manejável.

O código de R completo para essa seção é no arquivo zip que você baixou anteriormente.

### <a name="creating-the-dataframe-for-analysis"></a>Criando a dataframe para análise

Comece adicionando um **novo** [Executar scripts de R] [ execute-r-script] módulo para seu experimento. Conecte a saída de **Conjunto de dados do resultado** do existente [Executar scripts de R] [ execute-r-script] módulo na entrada **Dataset1** do novo módulo. O resultado deve parecer com figura 20.

![O experimento com o novo módulo de executar scripts de R adicionado][21]

*Figura 20. O experimento com o novo módulo de executar scripts de R adicionado.*

Como com a análise de correlação que podemos acabou de ser concluídos, precisamos adicionar uma coluna com um objeto de série de tempo de POSIXct. O código a seguir fará apenas isso.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Executar este código e examinar o log. O resultado deve parecer com Figura 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figura 21. Um resumo da dataframe.*

Com esse resultado, estamos prontos para começar a nossa análise.

###<a name="create-a-training-dataset"></a>Criar um conjunto de dados de treinamento

Com o dataframe construído precisamos criar um conjunto de dados de treinamento. Esses dados incluirá todas as observações, exceto o último 12, do ano 2013, que é o nosso conjunto de dados de teste. O seguinte código subconjuntos a dataframe e cria plotar das variáveis de produção e preço Laticínios. Então criar plotar da produção quatro e variáveis de preços. Uma função anônima é usada para definir algumas amplia a plotagem e, em seguida, iteramos sobre a lista dos outros dois argumentos com `Map()`. Se você estiver considerando que um para loop seria trabalhou fino aqui, você está corretas. Mas, como R é uma linguagem funcional eu estou mostrando uma abordagem funcional.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

Executando o código produz a série de tempo série plota da saída do dispositivo R mostrada na Figura 22. Observe que o eixo de tempo é em unidades de datas, uma bela vantagem do tempo série plotar método.

![Primeiro de plotar de série de hora dos dados de produção e preço de laticínios de Califórnia](./media/machine-learning-r-quickstart/unnamed-chunk-161.png)

![Segundo do plotar de série de hora dos dados de produção e preço de laticínios de Califórnia](./media/machine-learning-r-quickstart/unnamed-chunk-162.png)

![Terceiro de plotar de série de hora dos dados de produção e preço de laticínios de Califórnia](./media/machine-learning-r-quickstart/unnamed-chunk-163.png)

![Quarto de plotar de série de hora dos dados de produção e preço de laticínios de Califórnia](./media/machine-learning-r-quickstart/unnamed-chunk-164.png)

*Figura 22. Plotar séries de tempo de produção laticínio Califórnia e dados de preços.*

### <a name="a-trend-model"></a>Um modelo de tendência

Tendo criado um objeto de série de tempo e tendo uma olhada os dados, vamos começar construir um modelo de tendência Califórnia leite dados de produção. Podemos fazer isso com uma regressão de série de tempo. Entretanto, é claro a plotagem que abordaremos precisa de mais de uma inclinação e intercepção para modelar precisamente a tendência observada nos dados de treinamento.

Considerando a pequena escala dos dados, posso será criar o modelo para tendência em RStudio recortar e colar o modelo resultante em aprendizado de máquina do Azure. RStudio fornece um ambiente interativo para esse tipo de análise interativa.

Como uma primeira tentativa, eu tentará uma regressão polinomial com potências até 3. Não há um perigo real de ajuste excessivo esses tipos de modelos. Portanto, é melhor evitar termos de ordem alta. O `I()` função inibe interpretação do conteúdo (interprete o conteúdo 'como está') e permite que você escrever uma função interpretada literalmente em uma equação de regressão.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Isso gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Dos valores de P (Pr (> | t |)) nesta saída, podemos ver que o termo quadrado pode não ser significativo. Usarei o `update()` função para modificar este modelo soltando o termo quadrado.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Isso gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Isso parece melhor. Todos os termos são significativos. No entanto, o valor de 2e-16 é um valor padrão e não deve ser aceito muito seriamente.  

Como um teste de integridade, vamos fazer uma plotagem de série de hora dos dados de produção laticínios de Califórnia com a curva de tendência mostrada. Posso ter adicionado o seguinte código no aprendizado de máquina do Azure [Executar scripts de R] [ execute-r-script] modelo (não RStudio) para criar o modelo e faça uma plotagem. O resultado é mostrado na Figura 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Dados de produção Califórnia leite com o modelo de tendência mostrado](./media/machine-learning-r-quickstart/unnamed-chunk-18.png)

*Figura 23. Dados de produção Califórnia leite com o modelo de tendência mostrado.*

Parece que o modelo de tendência se ajusta aos dados bem. Além disso, não parece haver evidências de ajuste excessivo, como incompleto wiggles na curva de modelo.  

###<a name="seasonal-model"></a>Modelo sazonal

Com um modelo de tendência em mãos, precisamos push e incluir os efeitos sazonais. Usaremos o mês do ano como uma variável fictícia no modelo linear para capturar o efeito de mês a mês. Observe que quando você introduzir variáveis de fator em um modelo, a intercepção deve não ser calculada. Se você não fizer isso, a fórmula excesso é especificada e R será solte um dos fatores desejados, mas manter o termo de intercepção.

Como temos um modelo de tendência satisfatórias podemos usar a `update()` função para adicionar os novos termos ao modelo existente. O -1 na fórmula atualização descarta o termo de intercepção. Continuar no RStudio no momento:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Isso gera o seguinte.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,   Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Vemos que o modelo não tem um termo de intercepção e tem 12 fatores de mês significativo. Isso é exatamente o que queremos ver.

Vamos fazer outra plotagem de série hora dos dados de produção laticínios de Califórnia para ver como o modelo sazonal está trabalhando. Posso ter adicionado o seguinte código no aprendizado de máquina do Azure [Executar scripts de R] [ execute-r-script] para criar o modelo e fazer uma plotagem.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Executando este código no aprendizado de máquina do Azure produz a plotagem mostrada na Figura 24.

![Produção de leite Califórnia com modelo, incluindo efeitos sazonais](./media/machine-learning-r-quickstart/unnamed-chunk-20.png)

*Figura 24. Produção de leite Califórnia com modelo, incluindo efeitos sazonais.*

O ajuste aos dados mostrados na Figura 24 é preferir animadores. A tendência e o efeito sazonal (variação mensal) parecem razoáveis.

Como outra verificação em nosso modelo, vamos dar uma olhada os resíduos. O código a seguir calcula os valores previstos de nossos dois modelos, calcula os resíduos para o modelo sazonal e, em seguida, plota estas resíduos para os dados de treinamento.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

A plotagem residual é mostrada na Figura 25.

![Resíduos do modelo sazonal para os dados de treinamento](./media/machine-learning-r-quickstart/unnamed-chunk-21.png)

*Figura 25. Resíduos do modelo sazonal para os dados de treinamento.*

Estas resíduos parecer razoáveis. Não há nenhuma estrutura específica, exceto o efeito de recessão 2008-2009, que nosso modelo não conta particularmente bem.

A plotagem mostrada na Figura 25 é útil para detectar qualquer padrões dependentes de tempo nos resíduos. A abordagem explícita de computação e a plotagem os resíduos usei coloca os resíduos na ordem de tempo no gráfico. Se, por outro lado, eu tinha plotados `milk.lm$residuals`, a plotagem não teria sido na ordem de tempo.

Você também pode usar `plot.lm()` para produzir uma série de diagnóstico plotar.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Este código gera uma série de diagnóstico plotar mostrado na Figura 26.

![Primeiro de diagnóstico plotar para o modelo sazonal](./media/machine-learning-r-quickstart/unnamed-chunk-221.png)

![Segundo do diagnóstico plotar para o modelo sazonal](./media/machine-learning-r-quickstart/unnamed-chunk-222.png)

![Terceiro de diagnóstico plotar para o modelo sazonal](./media/machine-learning-r-quickstart/unnamed-chunk-223.png)

![Quarto de diagnóstico plotar para o modelo sazonal](./media/machine-learning-r-quickstart/unnamed-chunk-224.png)

*Figura 26. Diagnóstico plota para o modelo sazonal.*

Há alguns pontos altamente influentes identificados nestas plotar, mas nada para fazer com que o problema de ótima. Além disso, podemos ver da plotagem de Normal Q-Q que os resíduos são Fechar para costuma ser distribuído, uma suposição importante para os modelos lineares.

###<a name="forecasting-and-model-evaluation"></a>Avaliação de previsão e modelo

Há apenas uma coisa mais para concluir o nosso exemplo. Precisamos calcular previsões e medir o erro contra os dados reais. Nossa previsão será para a 12 meses de 2013. Nós pode calcular uma medida de erro para esta previsão para os dados reais que não faz parte de nosso conjunto de dados de treinamento. Além disso, podemos comparar desempenho em 18 anos de dados de treinamento a 12 meses de dados de teste.  

Um número de métricas é usado para medir o desempenho dos modelos da série de tempo. Em nosso caso usaremos o erro de raiz quadrada de média (RMS). A função a seguir calcula o erro RMS entre duas séries.  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
        warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

Como com a `log.transform()` função discutimos na seção "Valor transformações", há muito do código de recuperação de verificação e de exceção de erro nessa função. Os princípios empregados são os mesmos. O trabalho é feito em dois lugares empacotadas em `tryCatch()`. Primeiro, a série temporal é exponentiated, desde que temos trabalhado com os logs dos valores. Segundo, o erro real do RMS é calculado.  

Vamos equipado com uma função para medir o erro RMS, criar e um dataframe contendo os erros de RMS de saída. Podemos incluirá termos para o modelo de tendência sozinho e o modelo completo com fatores sazonais. O código a seguir faz o trabalho usando os dois modelos lineares que podemos construído.

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

Executar este código produz a saída mostrada na Figura 27 na porta de saída de conjunto de dados do resultado.

![Comparação de erros do RMS para os modelos][26]

*Figura 27. Comparação de erros do RMS para os modelos.*

Esses resultados, podemos ver que adicionando os fatores sazonais ao modelo reduz o erro RMS significativamente. Não muito obviamente, o erro RMS para os dados de treinamento é um pouco menor do que para a previsão.

##<a id="appendixa"></a>Apêndice a: Guia para RStudio

RStudio é bem documentada, portanto neste apêndice eu fornecerá alguns links para as seções principais da documentação RStudio para iniciá-lo.

1.  Criar projetos

    Você pode organizar e gerenciar seu código R em projetos usando RStudio. A documentação que usa projetos pode ser encontrada no https://support.rstudio.com/hc/articles/200526207-Using-Projects.

    É recomendável que você siga estas orientações e cria um projeto para os exemplos de código de R neste documento.  

2.  Edição e a execução de código de R

    RStudio fornece um ambiente integrado para edição e a execução de código de R. Documentação pode ser encontrada em https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.

3.  Depuração

    RStudio inclui recursos poderosos de depuração. Documentação para esses recursos está em https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.

    Os recursos de solução de problemas do ponto de interrupção estão documentados em https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

##<a id="appendixb"></a>B: Apêndice leituras adicionais

Esse tutorial de programação R aborda as Noções básicas do que você precisa para usar o idioma de R com o Azure Studio de aprendizado de máquina. Se você não estiver familiarizado com R, duas apresentações estão disponíveis em CRAN:

- R para iniciantes por Emmanuel Paradis é um bom lugar para começar em http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  

- Uma introdução ao R por n w. Venables et. Al. entra em um pouco mais profundidade, em http://cran.r-project.org/doc/manuals/R-intro.html.

Existem muitos livros R que pode ajudá-lo a começar. Aqui estão algumas que eu úteis:

- A arte de programação de R: um Tour de estatísticas Design de Software por Norman Matloff é uma excelente introdução à programação no R.  

- Livro de receitas R por Paul Teetor fornece uma abordagem de problema e a solução usando R.  

- R em ação por Robert Kabacoff é outro catálogo introdutório útil. O site de R rápida companion é um recurso útil, http://www.statmethods.net/.

- R Inferno por Patrick Burns é um livro obviamente divertido que lida com um número de complicado e difícil tópicos que podem ser encontrados ao programar no R. O livro está disponível gratuitamente em http://www.burns-stat.com/documents/books/the-r-inferno/.

- Se você quiser detalhamento tópicos avançados em R, dar uma olhada no catálogo avançado R por Hadley Wickham. A versão online deste livro está disponível gratuitamente em http://adv-r.had.co.nz/.

Um catálogo de pacotes de série de tempo de R pode ser encontrado na exibição de tarefa CRAN para análise de séries de tempo: http://cran.r-project.org/web/views/TimeSeries.html. Para obter informações sobre hora específicas série objeto pacotes, você deve consultar a documentação do pacote.

O livro introdutório série temporal com R por Paul Cowpertwait e Andrew Metcalfe fornece uma introdução ao uso R para análise de séries de tempo. Muitos textos mais teóricos fornecem exemplos de R.

Alguns ótimos recursos de internet:

- DataCamp: DataCamp ensina R no conforto de seu navegador com vídeo lições e exercícios codificação. Há tutoriais interativos sobre as últimas técnicas de R e pacotes. Levar o tutorial R interativo livre em https://www.datacamp.com/courses/introduction-to-r  

- Um rápido tutorial R por Kelly Black da Universidade de Clarkson http://www.cyclismo.org/tutorial/R/

- Recursos de R 60 listados no http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/machine-learning-r-quickstart/fig1.png
[2]: ./media/machine-learning-r-quickstart/fig2.png
[3]: ./media/machine-learning-r-quickstart/fig3.png
[4]: ./media/machine-learning-r-quickstart/fig4.png
[5]: ./media/machine-learning-r-quickstart/fig5.png
[6]: ./media/machine-learning-r-quickstart/fig6.png
[7]: ./media/machine-learning-r-quickstart/fig7.png
[8]: ./media/machine-learning-r-quickstart/fig8.png
[9]: ./media/machine-learning-r-quickstart/fig9.png
[10]: ./media/machine-learning-r-quickstart/fig10.png
[11]: ./media/machine-learning-r-quickstart/fig11.png
[12]: ./media/machine-learning-r-quickstart/fig12.png
[13]: ./media/machine-learning-r-quickstart/fig13.png
[14]: ./media/machine-learning-r-quickstart/fig14.png
[15]: ./media/machine-learning-r-quickstart/fig15.png
[16]: ./media/machine-learning-r-quickstart/fig16.png
[17]: ./media/machine-learning-r-quickstart/fig17.png
[18]: ./media/machine-learning-r-quickstart/fig18.png
[19]: ./media/machine-learning-r-quickstart/fig19.png
[20]: ./media/machine-learning-r-quickstart/fig20.png
[21]: ./media/machine-learning-r-quickstart/fig21.png
[22]: ./media/machine-learning-r-quickstart/fig22.png

[26]: ./media/machine-learning-r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
