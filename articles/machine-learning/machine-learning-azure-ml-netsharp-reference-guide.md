<properties 
    pageTitle="Guia para a linguagem de especificação de redes neurais líquido # | Microsoft Azure" 
    description="Sintaxe para a rede # neural redes linguagem de especificação, junto com exemplos de como criar um modelo de rede neural personalizado no Microsoft Azure ML usando líquido#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guia para a linguagem de especificação de rede neural líquido # para aprendizado de máquina do Azure

## <a name="overview"></a>Visão geral
Líquido # é uma linguagem desenvolvida pela Microsoft, que é usado para definir arquiteturas de rede neural para módulos de rede neural em aprendizado de máquina do Microsoft Azure. Neste artigo, você aprenderá:  

-   Conceitos básicos relacionados às redes neurais
-   Requisitos de rede neural e como definir os componentes principais
-   A sintaxe e palavras-chave da linguagem especificação líquido #
-   Exemplos de redes neurais personalizados criados usando líquido# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Noções básicas de rede neural
Uma estrutura de rede neural consiste em ***nós*** que são organizados em ***camadas***e ponderada ***conexões*** (ou ***bordas***) entre os nós. As conexões são direcionais e cada conexão tem um nó de ***origem*** e de um nó de ***destino*** .  

Cada ***camada trainable*** (oculto ou uma camada de saída) tem um ou mais ***pacotes de conexão***. Um pacote de conexão consiste em uma camada de origem e uma especificação das conexões a partir dessa camada de origem. Todas as conexões em um pacote de determinado compartilham a mesma ***camada de origem*** e a mesma ***camada de destino***. Em líquido #, um pacote de conexão é considerado como pertencente a camada de destino do conjunto.  
 
Líquido # oferece suporte a vários tipos de conexão pacotes, que permite que você personalize as entradas de maneira são mapeados para camadas ocultas e mapeados para saídas.   

As configurações padrão ou pacote padrão é um **pacote completo**, em que cada nó na camada fonte está conectado a cada nó na camada de destino.  

Além disso, líquido # suporta os seguintes quatro tipos de pacotes de conexão avançada:  

-   **Pacotes de filtrada**. O usuário pode definir um predicado usando os locais de nó de camada de origem e o nó de camada de destino. Nós são conectados sempre que o predicado for verdadeiro.
-   **Pacotes de convolutional**. O usuário pode definir vizinhanças pequenas de nós na camada fonte. Cada nó na camada de destino está conectada a um ambiente de nós na camada fonte.
-   **Pool de pacotes** e **pacotes de normalização de resposta**. Estes são semelhantes aos pacotes convolutional em que o usuário define vizinhanças pequenas de nós na camada fonte. A diferença é que os pesos das bordas nesses pacotes não são trainable. Em vez disso, uma função predefinida é aplicada aos valores de nó de origem para determinar o valor de nó de destino.  

Usar líquido # para definir a estrutura de uma rede neural possibilita definir estruturas complexas como redes neurais profunda ou convoluções de dimensões aleatório, que são conhecidas para melhorar a aprendizagem em dados como imagem, áudio ou vídeo.  

## <a name="supported-customizations"></a>Personalizações com suporte
A arquitetura de modelos de rede neural que você criar no aprendizado de máquina do Azure pode ser personalizada extensivamente usando líquido #. É possível:  

-   Criar camadas ocultas e controlar o número de nós em cada camada.
-   Especifique como camadas serão conectados uns aos outros.
-   Defina estruturas de conectividade especiais, como convoluções e espessura pacotes de compartilhamento.
-   Especifica funções de ativação diferente.  

Para obter detalhes sobre a sintaxe da linguagem de especificação, consulte [Especificação de estrutura](#Structure-specifications).  
 
Para obter exemplos de definindo redes neurais para algumas tarefas do simplex a complexos, de aprendizado de máquina comuns veja [exemplos](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Requisitos gerais
-   Deve haver saída exatamente uma camada, pelo menos uma camada de entrada e zero ou mais camadas ocultas. 
-   Cada camada tem um número fixo de nós, de forma conceitual organizadas em uma matriz retangular de dimensões aleatório. 
-   Camadas de entrada têm sem parâmetros de treinamento associados e representam o ponto onde os dados da instância entram na rede. 
-   Trainable camadas (camadas ocultas e saídas) associou parâmetros de treinamento, conhecidos como pesos e tendências. 
-   Os nós de origem e destino devem estar em camadas separadas. 
-   Conexões devem ser acíclica; em outras palavras, não pode ser uma cadeia de conexões levam volta para o nó de origem inicial.
-   A camada de saída não pode ser uma camada de origem de um pacote de conexão.  

## <a name="structure-specifications"></a>Especificações de estrutura
Uma especificação de estrutura de rede neural é composta por três seções: a **declaração de constante**, a **declaração de camada**, a **declaração de conexão**. Também há uma seção opcional **compartilhar declaração** . As seções podem ser especificadas em qualquer ordem.  

## <a name="constant-declaration"></a>Declaração de constante 
Uma declaração de constante é opcional. Ele fornece um meio para definir valores usados em outro lugar na definição de rede neural. A declaração consiste em um identificador seguido por um sinal de igual e uma expressão de valor.   

Por exemplo, a seguinte instrução define uma constante **x**:  


    Const X = 28;  

Para definir duas ou mais constantes simultaneamente, coloque os nomes de identificador e valores de chaves e separe-os usando ponto e vírgula. Por exemplo:  

    Const { X = 28; Y = 4; }  

À direita de cada expressão de atribuição pode ser um número inteiro, um número real, um valor booliano (verdadeiro ou falso) ou uma expressão matemática. Por exemplo:  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Declaração de camada
A declaração de camada é necessária. Ele define o tamanho e a origem da camada, incluindo seus atributos e pacotes de conexão. A declaração começa com o nome da camada (de entrada, ocultas ou de saída), seguido as dimensões da camada (uma tupla de inteiros positivos). Por exemplo:  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   O produto das dimensões é o número de nós na camada. Neste exemplo, há duas dimensões [5,20], que significa que há 100 nós na camada.
-   As camadas podem ser declaradas em qualquer ordem, com uma exceção: se mais de uma camada de entrada estiver definida, a ordem na qual elas são declaradas deve corresponder a ordem dos recursos nos dados de entrada.  


Para especificar que o número de nós em uma camada ser determinado automaticamente, use a palavra-chave **automática** . A palavra-chave **auto** tem efeitos diferentes, dependendo da camada:  

-   Em uma declaração de camada de entrada, o número de nós é o número de recursos nos dados de entrada.
-   Em uma declaração de camada oculta, o número de nós é o número especificado pelo valor do parâmetro de **número de nós ocultos**. 
-   Em uma declaração de camada de saída, o número de nós é 2 para classificação de dois classe, 1 de regressão e igual ao número de nós de saída para classificação multiclass.   

Por exemplo, a seguinte definição de rede permite que o tamanho de todas as camadas para ser determinado automaticamente:  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Uma declaração de camada para uma camada trainable (as camadas ocultas ou de saída), opcionalmente, pode incluir a saída função (também chamada de uma função de ativação), que usa como padrão **sigmoide** para modelos de classificação e **linear** para modelos de regressão. (Mesmo se você usa o padrão, você pode especificar claramente a função de ativação, se desejado para clareza.)

As seguintes funções de saída são suportadas:  

-   sigmoide
-   linear
-   Softmax
-   rlinear
-   quadrado
-   raiz
-   srlinear
-   Abs
-   TANH 
-   brlinear  

Por exemplo, a declaração a seguir usa a função **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Declaração de Conexão
Imediatamente depois de definir a camada trainable, você deve declarar as conexões entre as camadas que você definiu. A declaração de pacote de conexão começa com a palavra-chave **de**, seguido do nome da camada de origem do conjunto e o tipo de pacote de conexão para criar.   

Atualmente, há suporte para cinco tipos de pacotes de conexão:  

-   Pacotes **completo** , indicadas por palavra-chave **tudo**
-   Pacotes de **filtrado** , indicados pela palavra-chave **onde**, seguido por uma expressão de predicado
-   Pacotes de **Convolutional** , indicados pela palavra-chave **convolve**, seguido os atributos de convolução
-   Pacotes de **pool** , indicadas por palavras-chave **máximo do pool** ou **significam pool**
-   Pacotes de **normalização de resposta** , indicados pela palavra-chave **normais de resposta**      

## <a name="full-bundles"></a>Pacotes completos  

Um pacote de conexão completa inclui uma conexão de cada nó na camada fonte para cada nó na camada de destino. Este é o tipo de conexão de rede padrão.  

## <a name="filtered-bundles"></a>Pacotes filtrados
Uma especificação de pacote de conexão filtrada inclui um muito predicado, sintaticamente, expresso como uma expressão lambda c#. O exemplo a seguir define dois pacotes filtrados:  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   O predicado para _ByRow_, **s** é um parâmetro que representa um índice para a matriz retangular nós da camada de entrada, _Pixels_, e **d** é um parâmetro que representa um índice para a matriz de nós de camada oculta, _ByRow_. O tipo de **s** e **d** é uma tupla de inteiros de comprimento dois. Forma conceitual, **s** intervalos sobre todos os pares de inteiros com _0 < = s [0] < 10_ e _0 < = s[1] < 20_, e **d** intervalos sobre todos os pares de inteiros, com _0 < = d [0] < 10_ e _0 < = d[1] < 12_. 
-   No lado direito da expressão predicado, há uma condição. Neste exemplo, para cada valor de **s** e **d** , de forma que a condição for verdadeira, há uma borda do nó de camada de origem para o nó de camada de destino. Portanto, essa expressão de filtro indica que o pacote inclui uma conexão a partir do nó definido pelo **s** para o nó definido por **d** em todos os casos onde s [0] é igual a d [0].  

Opcionalmente, você pode especificar um conjunto de pesos para um conjunto filtrado. O valor do atributo **espessuras** deve ser uma tupla de valores de ponto flutuante com um comprimento que corresponde ao número de conexões definido pelo pacote. Por padrão, pesos são gerados aleatoriamente.  

Valores de peso são agrupadas pelo índice de nó de destino. Isto é, se o primeiro nó de destino estiver conectado a nós de origem K, os primeiros elementos _K_ da tupla **pesos** são os pesos do primeiro nó de destino, em ordem de índice de origem. O mesmo se aplica para os nós de destino restantes.  

É possível especificar pesos diretamente como valores constantes. Por exemplo, se você aprendeu os pesos anteriormente, você pode especificá-los como constantes usando esta sintaxe:

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Pacotes de convolutional
Quando os dados de treinamento tem uma estrutura homogênea, conexões de convolutional são usadas para aprender recursos de alto nível dos dados. Por exemplo, na imagem, dados de áudio ou vídeo, dimensionalidade espacial ou temporal podem ser bastante uniformes.  

Pacotes de convolutional utilizam retangular **kernels** que são deslize para através as dimensões. Essencialmente, cada núcleo define um conjunto de pesos aplicados em vizinhanças locais, chamados de **aplicativos de núcleo**. Cada aplicativo núcleo corresponde a um nó na camada de origem, que é conhecido como o **nó central**. Os pesos de um núcleo são compartilhados entre várias conexões. Em um pacote de convolutional, cada núcleo é retangular e todos os aplicativos de núcleo são do mesmo tamanho.  

Pacotes de convolutional suportam os seguintes atributos:

**InputShape** define a dimensionalidade da camada de origem para os fins esse pacote convolutional. O valor deve ser uma tupla de inteiros positivos. O produto dos inteiros deve ser igual o número de nós na camada fonte, mas caso contrário, ele não precisa corresponder a dimensionalidade declarada para a camada de origem. O comprimento dessa tupla torna-se o valor de **aridade** para o pacote convolutional. (Normalmente arity refere-se ao número de argumentos ou operandos que uma função pode levar.)  

Para definir a forma e localizações dos kernels, use os atributos **KernelShape**, **Stride**, **preenchimento**, **LowerPad**e **UpperPad**:   

-   **KernelShape**: (obrigatório) define a dimensionalidade dos cada núcleo para o pacote convolutional. O valor deve ser uma tupla de inteiros positivos com um comprimento igual a aridade do pacote. Cada componente dessa tupla deve ser não maior que o componente correspondente da **InputShape**. 
-   **STRIDE**: (opcional) define os tamanhos de etapa deslizante de convolução (tamanho de uma etapa para cada dimensão), que é a distância entre os nós central. O valor deve ser uma tupla de inteiros positivos com um tamanho que é a aridade do pacote. Cada componente dessa tupla deve ser não maior que o componente correspondente da **KernelShape**. O valor padrão é uma tupla com todos os componentes iguais a um. 
-   **Compartilhamento**: (opcional) define a espessura de compartilhamento para cada dimensão da convolução. O valor pode ser um único valor booliano ou uma tupla de valores booleanos com um comprimento que é a aridade do pacote. Um único valor booliano está estendido para ser uma tupla do comprimento correto com todos os componentes iguais ao valor especificado. O valor padrão é uma tupla que consiste em todos os valores True. 
-   **MapCount**: (opcional) define o número do recurso de mapas para o pacote convolutional. O valor pode ser um inteiro positivo único ou uma tupla de inteiros positivos com um tamanho que é a aridade do pacote. Um valor inteiro único está estendido para ser uma tupla do comprimento correto com os componentes primeiro iguais ao valor especificado e todos os componentes restantes igual a um. O valor padrão é um. O número total de mapas do recurso é o produto dos componentes da tupla. O cálculo deste número total nos componentes determina como os valores de mapa de recurso são agrupados em nós de destino. 
-   **Espessuras**: (opcional) define os pesos iniciais para o pacote. O valor deve ser uma tupla de flutuantes valores de ponto com um comprimento que é o número de vezes que kernels o número de espessuras por núcleo, conforme definido neste artigo. Os pesos padrão são gerados aleatoriamente.  

Há dois conjuntos de propriedades que controlam preenchimento, as propriedades sendo mutuamente exclusivos:

-   **Preenchimento**: (opcional) determina se a entrada deve ser preenchida usando um **esquema de preenchimento padrão**. O valor pode ser um único valor booliano ou pode ser uma tupla de valores booleanos com um comprimento que é a aridade do pacote. Um único valor booliano está estendido para ser uma tupla do comprimento correto com todos os componentes iguais ao valor especificado. Se o valor de uma dimensão for verdadeiro, a fonte logicamente é preenchida nessa dimensão com células com valor zero para dar suporte a aplicativos de núcleo adicionais, por exemplo, que os nós central dos kernels primeiro e últimos nessa dimensão são os nós primeiro e últimos nessa dimensão na camada fonte. Portanto, o número de nós "fictícios" em cada dimensão é determinado automaticamente, para caber exatamente _(InputShape [d] - 1) / Stride [d] + 1_ kernels para a camada de fonte macia. Se o valor de uma dimensão for falso, os kernels são definidos para que o número de nós em cada lado que são deixados check-out é a mesma (até uma diferença de 1). O valor padrão deste atributo é uma tupla com todos os componentes iguais a False.
-   **UpperPad** e **LowerPad**: (opcional) fornecer maior controle sobre a quantidade de preenchimento para usar. **Importante:** Esses atributos podem ser definidos somente se a propriedade de **preenchimento** acima é ***não*** definido. Os valores devem ser números inteiros tuplas com comprimentos da aridade do pacote de. Quando esses atributos são especificados, nós "fictícios" são adicionados às extremidades inferiores e superiores de cada dimensão da camada de entrada. O número de nós adicionados às extremidades inferiores e superiores em cada dimensão é determinado por **LowerPad**[i] e **UpperPad**[i], respectivamente. Para garantir que kernels correspondem apenas para nós "reais" e não para nós "fictícios", as seguintes condições devem ser atendidas:
    -   Cada componente da **LowerPad** deve ser terminantemente menor do que KernelShape [d] / 2. 
    -   Cada componente da **UpperPad** deve ser não maior que KernelShape [d] / 2. 
    -   O valor padrão desses atributos é uma tupla com todos os componentes iguais a 0. 

A configuração de **preenchimento** = true permite tantos preenchimento conforme for necessário para manter o "Centro" do núcleo dentro o "real" de entrada. Isso altera a matemática um pouco para computação o tamanho de saída. Geralmente, o tamanho de saída _D_ é calculado como _D = (I - K) / S + 1_, onde _I_ é o tamanho de entrada, _K_ é o tamanho de núcleo, _S_ é a distância, e _/_ é divisão (ARRED em direção a zero). Se você definir UpperPad = [1, 1], o tamanho da entrada _eu_ é efetivamente 29 e, portanto, _D = (29-5) / 2 + 1 = 13_. No entanto, quando **preenchimento** = verdadeiro, essencialmente _eu_ obtém aumentado em por _K - 1_; Portanto, _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. Especificando valores para **UpperPad** e **LowerPad** você obter muito mais controle sobre o preenchimento de se você acabou de definir **preenchimento** = true.

Para obter mais informações sobre redes convolutional e seus aplicativos, consulte estes artigos:  

-   [http://deeplearning.NET/tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.microsoft.com/pubs/68920/icdar03.PDF](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://People.csail.MIT.edu/jvb/papers/cnn_tutorial.PDF](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Pool de pacotes
Um **pool de pacote** aplica geometria semelhante a conectividade convolutional, mas usa funções predefinidas para valores de nó de origem para obter o valor de nó de destino. Portanto, pacotes de pool não tem nenhum estado trainable (espessuras ou tendências). Pacotes de pool oferecem suporte a todos os atributos convolutional exceto **compartilhamento**, **MapCount**e **espessuras**.  

Normalmente, os kernels resumidos por unidades de pool adjacentes não se sobreponham. Se a distância [d] é igual a KernelShape [d] em cada dimensão, a camada obtida é a tradicional camada de pool local, que normalmente é empregada em redes neurais convolutional. Cada nó de destino calcula o valor máximo ou a média das atividades de seu núcleo na camada fonte.  

O exemplo a seguir ilustra um pacote de pool: 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   A aridade do pacote de é 3 (o comprimento dos tuplas **InputShape**, **KernelShape**e **Stride**). 
-   O número de nós na camada fonte é _5 *24* 24 = 2880_. 
-   Isso é uma camada de pool local tradicional porque **KernelShape** e **Stride** são iguais. 
-   O número de nós na camada de destino é _5 *12* 12 = 1440_.  
    
Para obter mais informações sobre o pool de camadas, consulte estes artigos:  

-   [http://www.cs.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Seção 3.4)
-   [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.PDF](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.PDF](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Pacotes de normalização de resposta
**Normalização de resposta** é um esquema de normalização local que foi introduzido por Geoffrey Hinton, etc, em papel [ImageNet Classiﬁcation com profunda redes neurais Convolutional](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalização de resposta é usada para ajudar a generalização em redes neurais. Quando um neurônio é demissões em um nível de ativação muito alto, uma camada de normalização de resposta local elimina o nível de ativação de neurônios ao redor. Isso é feito usando três parâmetros (***α***, ***β***e ***k***) e a estrutura convolutional (ou forma de ambiente). Cada neurônio na camada de destino ***y*** corresponde a um neurônio ***x*** na camada fonte. O nível de ativação de ***y*** é fornecido pela fórmula a seguir, onde ***f*** é o nível de ativação de um neurônio e ***Nx*** é o núcleo (ou o conjunto que contém os neurônios cerca de ***x***), conforme definido pelo convolutional estrutura a seguir:  

![][1]  

Pacotes de normalização de resposta oferece suporte a todos os atributos convolutional exceto **compartilhamento**, **MapCount**e **espessuras**.  
 
-   Se o núcleo contiver neurônios no mesmo mapa como ***x***, o esquema de normalização é conhecido como **mesmo normalização de mapa**. Para definir o mesmo normalização de mapa, a primeira coordenadas no **InputShape** devem ter o valor 1.
-   Se o núcleo contiver neurônios na mesma posição espacial como ***x***, mas os neurônios estão em outros mapas, o esquema de normalização é chamado **em mapas normalização**. Esse tipo de normalização de resposta implementa uma forma de inhibition lateral tendo o tipo encontrado em neurônios reais, criando concorrência para níveis de ativação grande entre saídas neurônio computados em mapas diferentes. Para definir em mapas normalização, as coordenadas primeira devem ser um inteiro maior do que um e não maior que o número de mapas, e o restante das coordenadas deve ter o valor 1.  

Como os pacotes de normalização de resposta aplicam uma função predefinida a valores de nó de origem para determinar o valor de nó de destino, eles têm nenhum estado trainable (espessuras ou tendências).   

**Alerta**: nós na camada de destino correspondem aos neurônios que são os nós central dos kernels. Por exemplo, se KernelShape [d] for ímpar, então _KernelShape [d] / 2_ corresponde ao nó núcleo central. Se _KernelShape [d]_ for par, o nó central está no _KernelShape [d] / 2-1_. Portanto, se o **preenchimento**[d] for falso, a primeira e a última _KernelShape [d] / 2_ nós não possuem nós correspondentes na camada de destino. Para evitar essa situação, defina o **preenchimento** como [true, true,..., Verdadeiro].  

Além dos quatro atributos descritos anteriormente, pacotes de normalização de resposta também suportam os seguintes atributos:  

-   **Alfa**: (obrigatório) especifica um valor de ponto flutuante que corresponde ao ***α*** na fórmula anterior. 
-   **Beta**: (obrigatório) especifica um valor de ponto flutuante que corresponde ao ***β*** na fórmula anterior. 
-   **Deslocamento**: (opcional) especifica um valor de ponto flutuante que corresponde ao ***k*** na fórmula anterior. O padrão é 1.  

O exemplo a seguir define um pacote de normalização de resposta usando estes atributos:  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   A camada de fonte inclui cinco mapas, cada um com dimensão aof de 12 x 12, totalizando em nós 1440. 
-   O valor de **KernelShape** indica que se trata de uma camada de normalização mapa mesmo, onde o ambiente é um retângulo de 3x3. 
-   O valor padrão de **preenchimento** for falso, portanto, a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde a cada nó na camada fonte, adicione preenchimento = [verdadeiro, verdadeiro, Verdadeiro]; e altere o tamanho da RN1 para [5, 12, 12].  

## <a name="share-declaration"></a>Declaração de compartilhamento 
Líquido # opcionalmente dá suporte à definição múltiplos pacotes com pesos compartilhados. Os pesos de qualquer dois pacotes podem ser compartilhados se suas estruturas são iguais. A seguinte sintaxe define pacotes com espessuras compartilhadas:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

Por exemplo, a seguinte declaração de compartilhamento Especifica os nomes de camada, indicando que pesos e tendências devem ser compartilhadas:  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   Os recursos de entrada são particionados em duas camadas de entrada tamanho iguais. 
-   As camadas ocultas calcular os recursos de nível superiores das duas camadas de entrada. 
-   A declaração de compartilhamento Especifica que _H1_ e _H2_ devem ser calculadas da mesma maneira de suas respectivas entradas.  
 
Como alternativa, isso poderia ser especificado com duas separada para declarações de compartilhamento da seguinte maneira:  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Você pode usar a forma abreviada apenas quando as camadas contiverem um único pacote. Em geral, o compartilhamento é possível somente quando a estrutura relevante é idêntica, significando que eles tenham o mesmo tamanho, mesma geometria convolutional e assim por diante.  

## <a name="examples-of-net-usage"></a>Exemplos do uso de líquido #
Esta seção fornece alguns exemplos de como você pode usar líquido # para adicionar camadas ocultas, defina a maneira que camadas ocultas interagem com outras camadas e desenvolver redes convolutional.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definir uma rede neural personalizada simples: exemplo "Hello World"
Esse exemplo simples demonstra como criar um modelo de rede neural que possui uma única camada oculta.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

O exemplo ilustra alguns comandos básicos da seguinte maneira:  

-   A primeira linha define a camada de entrada (chamada _dados_). Quando você usa a palavra-chave **automática** , a rede neural inclui automaticamente todas as colunas de recurso nos exemplos a entrada. 
-   A segunda linha cria camada oculta. O nome _H_ é atribuído para a camada oculta, que possui 200 nós. Essa camada totalmente está conectada à camada de entrada.
-   A terceira linha define a camada de saída (chamada _O_), que contém 10 nós de saída. Se a rede neural é usada para classificação, há um nó de saída por classe. A palavra-chave **sigmoide** indica que a função de saída é aplicada à camada de saída.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir várias camadas ocultas: exemplo de visão de computador
O exemplo a seguir demonstra como definir uma rede neural um pouco mais complexa, com várias camadas ocultas personalizadas.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Este exemplo ilustra vários recursos da linguagem de especificação de redes neurais:  

-   A estrutura tem duas camadas de entrada, _Pixels_ e _metadados_.
-   A camada de _Pixels_ é uma camada de origem para dois pacotes de conexão, com camadas de destino, _ByRow_ e _ByCol_.
-   As camadas _reunir_ e o _resultado_ são camadas de destino em vários pacotes de conexão.
-   A camada de saída, o _resultado_, é uma camada de destino em dois pacotes de conexão; uma com o segundo nível oculta (coletar) como uma camada de destino e o outro com a camada de entrada (metadados) como uma camada de destino.
-   As camadas ocultas, _ByRow_ e _ByCol_, especifique conectividade filtrada usando expressões predicadas. Mais precisamente, o nó no _ByRow_ em [x, y] está conectado a nós em _Pixels_ que têm a primeira coordenadas de índice igual a coordenadas primeira do nó, x. Da mesma forma, o nó no _ByCol em [x, y] está conectado a nós em _Pixels_ que têm o segundo índice coordenar dentro de uma das coordenadas segunda do nó, y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir uma rede convolutional para classificação multiclass: exemplo de reconhecimento de dígito
A definição da seguinte rede foi projetada para reconhecer números, e ilustra algumas técnicas avançadas para personalizar uma rede neural.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   A estrutura tem uma única camada de entrada, _imagem_.
-   A palavra-chave **convolve** indica que as camadas chamadas _Conv1_ e _Conv2_ são convolutional camadas. Cada um dessas declarações de camada é seguida por uma lista dos atributos convolução.
-   A rede tem um terceiro ocultas camada, _Hid3_, que é totalmente conectado a segunda camada oculta, _Conv2_.
-   A camada de saída, _dígito_, estiver conectada somente a terceira camada oculta, _Hid3_. A palavra-chave **todas** indica que a camada de saída é totalmente conectada aos _Hid3_.
-   A aridade da convolução é três (o comprimento dos tuplas **InputShape**, **KernelShape**, **Stride**e **compartilhamento**). 
-   O número de espessuras por núcleo é _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. Ou 26 * 50 = 1300_.
-   Você pode calcular os nós em cada camada oculta da seguinte maneira:
    -   **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   O número total de nós pode ser calculado usando a dimensionalidade declarada da camada, [50, 5, 5], da seguinte maneira: _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Porque o **compartilhamento**[d] é falso somente para _d = = 0_, o número de kernels é _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Confirmações

A linguagem líquido # para personalizar a arquitetura de redes neurais foi desenvolvida na Microsoft por Shon Katzenberger (arquitetura, aprendizado de máquina) e Alexey Kamenev (engenharia de Software, Microsoft Research). Ele é usado internamente para projetos e aplicativos que varia de detecção de imagem a análise de texto de aprendizado de máquina. Para obter mais informações, consulte [Redes neurais no Azure ML - Introdução ao líquido #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
