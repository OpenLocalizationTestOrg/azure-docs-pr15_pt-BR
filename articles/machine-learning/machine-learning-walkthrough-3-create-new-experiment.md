<properties
    pageTitle="Etapa 3: Criar uma nova experiência de aprendizado de máquina | Microsoft Azure"
    description="Etapa 3 da desenvolver um passo a passo de solução de previsão: criar uma nova experiência de treinamento no Azure Studio de aprendizado de máquina."
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
    ms.date="10/05/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Explicação passo a passo etapa 3: Criar uma nova experiência de aprendizado de máquina do Azure

Esta é a terceira etapa da explicação, [desenvolver uma solução de análise de previsão no aprendizado de máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar um espaço de trabalho de aprendizado de máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregue os dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  **Criar uma nova experiência**
4.  [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implantar o serviço da web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acessar o serviço web](machine-learning-walkthrough-6-access-web-service.md)

----------

A próxima etapa neste passo a passo é criar um experimento no Studio de aprendizado de máquina que usa o conjunto de dados que são carregados.  

1.  No Studio, clique em **+ nova** na parte inferior da janela.
2.  Selecione **EXPERIMENTO**e selecione "Experimento em branco". Selecione o nome de experimento padrão na parte superior da tela e renomeie-a para algo significativo

    > [AZURE.TIP] É uma boa prática preencha **Resumo** e uma **Descrição** para o experimento no painel **Propriedades** . Essas propriedades oferecem a oportunidade do experimento do documento para que qualquer pessoa que vê-lo mais tarde compreendam suas metas e metodologia.

3.  Na paleta de módulo à esquerda da experiência da tela, expanda **Os conjuntos de dados salvo**.
4.  Localize o conjunto de dados que você criou em **Meu conjuntos de dados** e arraste-o para a tela. Você também pode encontrar o dataset digitando o nome na caixa de **pesquisa** acima na paleta.  

##<a name="prepare-the-data"></a>Preparar os dados
Você pode exibir as primeiras 100 linhas de dados e algumas informações de estatísticas para o conjunto de dados inteiro clicando a porta de saída do conjunto de dados (o pequeno círculo na parte inferior) e selecionando **Visualizar**.  

Porque o arquivo de dados não foi fornecido com títulos de coluna, Studio forneceu títulos genéricos (Col1, Col2, *etc*.). Títulos de boas não essenciais para a criação de um modelo, mas eles tornam mais fácil trabalhar com os dados do experimento. Além disso, quando podemos eventualmente publicar esse modelo em um serviço da web, os títulos ajudará a identificar as colunas para o usuário do serviço.  

Podemos adicionar títulos de coluna usando os [Metadados de editar] [ edit-metadata] módulo.
Você usa o [Editar metadados] [ edit-metadata] módulo alterar metadados associados a um conjunto de dados. Nesse caso, ele pode fornecer mais nomes amigáveis para títulos de coluna. 

Uso de [Metadados de editar][edit-metadata], primeiro você especificar quais colunas para modificar (nesse caso, todos eles.) Em seguida, especifique a ação a ser executada nessas colunas (nesse caso, alterar títulos de coluna.)

1.  Na paleta de módulo, digite "metadados" na caixa de **pesquisa** . Você verá [Editar metadados] [ edit-metadata] aparecerão na lista de módulo.
2.  Clique e arraste os [Metadados de editar] [ edit-metadata] módulo para a tela e solte-o abaixo do dataset adicionamos anteriormente.
3.  Conectar o dataset ao [Editar metadados][edit-metadata]: clique na porta de saída do conjunto de dados (o pequeno círculo na parte inferior do conjunto de dados.) Em seguida, arraste para a porta de entrada de [Editar metadados] [ edit-metadata] (o pequeno círculo na parte superior do módulo), em seguida, solte o botão do mouse. O conjunto de dados e o módulo permaneçam conectados mesmo se você move tanto em tela de desenho.

    O experimento agora deve ser algo parecido com isto:  

    ![Editar metadados de adição][2]
    
    O ponto de exclamação vermelho indica que ainda não definimos as propriedades para este módulo ainda. Faremos isso a seguir.
    
    > [AZURE.TIP] Você pode adicionar um comentário a um módulo clicando duas vezes no módulo e inserindo texto. Isso pode ajudá-lo a ver num relance o que o módulo está fazendo em seu experimento. Nesse caso, clique duas vezes em [Editar metadados] [ edit-metadata] módulo e digite os comentário "adicionar títulos de coluna". Clique em qualquer lugar na tela de desenho para fechar a caixa de texto. Para exibir o comentário, clique na seta para baixo no módulo.

4.  Selecione [Editar metadados][edit-metadata], no painel **Propriedades** à direita da tela, clique em **Iniciar o seletor de coluna**.
5.  Na caixa de diálogo **Selecionar colunas** , selecione todas as linhas em **Colunas disponíveis** e clique em > movê-los para **Colunas selecionadas**.
A caixa de diálogo deve ter esta aparência: ![seletor de coluna com todas as colunas selecionadas][4]
7.  Clique na marca de seleção **Okey** .
8.  De volta ao painel **Propriedades** , procure o parâmetro de **novos nomes de coluna** . Nesse campo, insira uma lista de nomes para as 21 colunas no dataset, separados por vírgulas e na coluna ordem. Você pode obter os nomes de colunas da documentação do dataset no site UCI, ou para sua conveniência, você pode copiar e colar a lista a seguir:  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Painel Propriedades ficará assim:

    ![Propriedades para editar metadados][1]

> [AZURE.TIP] Se você quiser verificar os títulos de coluna, execute o experimento (clique em **Executar** abaixo da tela de experimento). Quando ela terminar a execução (uma marca de seleção verde aparecerá em [Editar metadados][edit-metadata]), clique na porta de saída de [Editar metadados] [ edit-metadata] módulo e selecione **Visualizar**. Você pode exibir a saída de qualquer módulo da mesma maneira para exibir o andamento dos dados por meio do experimento.

##<a name="create-training-and-test-datasets"></a>Treinamento de criar e testar conjuntos de dados

A próxima etapa da experiência é gerar conjuntos de dados separados que usaremos para treinamento e teste o nosso modelo.

Para fazer isso, podemos usar os [Dados de divisão] [ split] módulo.  

1.  Localizar os [Dados de divisão] [ split] módulo, arraste-o para a tela e conectá-lo à última [Editar metadados] [ edit-metadata] módulo.
2.  Por padrão, a taxa de divisão é 0,5 e o parâmetro **Randomized dividida** está definido. Isso significa que um aleatória metade dos dados saída através de uma porta de [Dados dividido] [ split] módulo e metade por meio de outro. Você pode ajustar essas, bem como o parâmetro de **propagação aleatória** , para alterar a divisão entre dados de teste e treinamento. Neste exemplo, vamos deixá-los como-é.
    
    > [AZURE.TIP] A propriedade **fração das linhas no primeiro dataset saída** determina a quantidade de dados é saída através da porta de saída à esquerda. Por exemplo, se você definir a proporção para 0.7, em seguida, 70% de dados é saída através da porta à esquerda e 30% por meio da porta à direita.  
    
3. Clique duas vezes os [Dados de divisão] [ split] módulo e digite o comentário, "treinamento/testes dados dividir 50%". 

Podemos usar saídas dos [Dados de divisão] [ split] módulo porém nós como, mas vamos optar por usar a saída esquerda como dados de testes de saída de dados de treinamento e para a direita.  

Conforme mencionado no site UCI, o custo de classifique um risco de crédito alta como baixa é cinco vezes maior que o custo de classifique um risco de crédito baixa como alto. Para justificar isso, podemos gerar um novo dataset que reflete esta função de custo. No novo dataset, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo de baixo risco não é replicado.   

Podemos fazer essa replicação usando o código de R:  

1.  Localize e arraste o [Executar scripts de R] [ execute-r-script] módulo até o experimento tela e conecte-se a porta de saída à esquerda dos [Dados de divisão] [ split] módulo à primeira entrada porta ("Dataset1") do [Executar scripts de R] [ execute-r-script] módulo.
2. Clique duas vezes a [Executar scripts de R] [ execute-r-script] módulo e digite o comentário, "Set ajuste de custo".
2.  No painel **Propriedades** , exclua o texto padrão no parâmetro **R Script** e digite este script:

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Precisamos fazer esta mesma operação de replicação para cada saída dos [Dados de divisão] [ split] módulo para que os dados de treinamento e testes tenham o mesmo ajuste de custo.

1.  Clique com botão direito a [Executar scripts de R] [ execute-r-script] módulo e selecione **Copiar**.
2.  Clique com botão direito da tela experimento e selecione **Colar**.
3.  Conectar-se a primeira porta de entrada deste [Executar scripts de R] [ execute-r-script] porta dos [Dados de divisão] de saída do módulo para a direita[ split] módulo. 
4.  Na parte inferior da tela, clique em **Executar**. 

> [AZURE.TIP] A cópia do módulo executar scripts de R contém o mesmo script como o módulo original. Quando você copia e cola um módulo na tela de desenho, a cópia retém todas as propriedades da célula original.  

Nosso experimento agora pode ter esta aparência:

![Adicionando módulo de divisão e scripts de R][3]

Para obter mais informações sobre o uso de scripts de R de suas experiências, consulte [estender seu experimento com R](machine-learning-extend-your-experiment-with-r.md).

**Avançar: [trem e avaliar os modelos de](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
