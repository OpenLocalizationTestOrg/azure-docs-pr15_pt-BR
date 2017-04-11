<properties 
    pageTitle="Acessar conjuntos de dados com a biblioteca de cliente Python de aprendizado de máquina | Microsoft Azure" 
    description="Instalar e usar a biblioteca de cliente Python para acessar e gerenciar dados de aprendizado de máquina do Azure com segurança de um ambiente local do Python." 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Conjuntos de dados do Access com Python usando a biblioteca de cliente Python de aprendizado de máquina do Azure 

A visualização da biblioteca de cliente Python de aprendizado de máquina do Microsoft Azure pode habilitar o acesso seguro para os conjuntos de dados do Azure aprendizado de máquina de um ambiente local do Python e permite a criação e o gerenciamento de conjuntos de dados em um espaço de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca de cliente Python de aprendizado de máquina 
* acessar e carregue conjuntos de dados, incluindo as instruções sobre como obter autorização para acessar conjuntos de dados de aprendizado de máquina do Azure do seu ambiente local do Python
*  acessar conjuntos intermediários de experiências
*  usar a biblioteca de cliente Python para enumerar conjuntos de dados, acessar metadados, ler o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Pré-requisitos

A biblioteca de cliente Python foi testada nos ambientes a seguir:

 - Windows, Mac e Linux
 - Python 2.7, 3.3 e 3.4

Ela tem uma dependência nos pacotes a seguir:

 - solicitações
 - Python dateutil
 - pandas

Recomendamos usando uma distribuição de Python como [Anaconda](http://continuum.io/downloads#all) ou [abóbada](https://store.enthought.com/downloads/), que acompanham Python, IPython e três pacotes listados acima instalada. Embora IPython não é obrigatória, é um ótimo ambiente para manipular e visualizando dados interativamente.


###<a name="installation"></a>Como instalar a biblioteca de cliente Python de aprendizado de máquina do Azure

A biblioteca de cliente do Azure Python de aprendizado de máquina também deve ser instalada para concluir as tarefas descritas neste tópico. Ele está disponível do [Índice de pacote de Python](https://pypi.python.org/pypi/azureml). Para instalá-lo em seu ambiente de Python, execute o seguinte comando do ambiente de Python local:

    pip install azureml

Como alternativa, você pode baixar e instalar a partir das fontes no [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se você tiver gito instalado no computador, você pode usar pip instalar diretamente do repositório gito:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Use trechos de código Studio para acessar conjuntos de dados

A biblioteca de cliente Python fornece acesso através de programação para os conjuntos de dados existentes de experiências que foram executadas.

Da interface da web Studio, você pode gerar trechos de código que incluem todas as informações necessárias para baixar e desserializar conjuntos de dados como objetos Pandas DataFrame em sua máquina local.

### <a name="security"></a>Segurança para acesso a dados

Os trechos de código fornecidos pelo Studio para uso com a biblioteca de cliente Python inclui sua id de espaço de trabalho e a autorização token. Estas fornecem acesso total ao seu espaço de trabalho e devem ser protegidas, como uma senha.

Por razões de segurança, a funcionalidade de trecho de código só está disponível para usuários que têm suas funções definido como **proprietário** do espaço de trabalho. Sua função é exibida no Azure Studio de aprendizado de máquina na página **usuários** em **configurações**.

![Segurança][security]

Se sua função não estiver definida como **proprietário**, você pode tanto solicitar a ser novamente convidado como um proprietário ou peça ao proprietário do espaço de trabalho para fornecer o trecho de código.

Para obter o token de autorização, você pode fazer um destes procedimentos:



- Solicite um token de um proprietário. Proprietários podem acessar seus tokens de autorização da página Configurações do seu espaço de trabalho no Studio. Selecione **configurações** no painel esquerdo e clique em **TOKENS de autorização** para ver os tokens primárias e secundários.  Embora primário ou os tokens de autorização secundário podem ser usados no trecho de código, é recomendável que os proprietários de compartilham somente os tokens de autorização secundário.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Peça para ser promovido a função do proprietário.  Para fazer isso, um proprietário atual do espaço de trabalho deve primeiro você remova o espaço de trabalho e convide você novamente-lo como um proprietário.

Depois que os desenvolvedores obteve a identificação de espaço de trabalho e a autorização token, eles são capazes de acessar o espaço de trabalho usando o trecho de código, independentemente de suas funções.

Tokens de autorização são gerenciados na página **TOKENS de autorização** em **configurações**. Você pode gerá-los novamente, mas esse procedimento revoga acessem o token anterior.

### <a name="accessingDatasets"></a>Conjuntos de dados do Access de um aplicativo de Python local

1. No Studio de aprendizado de máquina, clique em **conjuntos de dados** na barra de navegação à esquerda.

2. Selecione o conjunto de dados que você deseja acessar. Você pode selecionar qualquer um dos conjuntos de dados na lista de **Conjuntos de dados meu** ou na lista de **exemplos** .

3. Na barra de ferramentas inferior, clique em **Gerar código de acesso de dados**. Se os dados estiverem em um formato incompatível com a biblioteca de cliente Python, este botão é desabilitado.

    ![Conjuntos de dados][datasets]

4. Selecione o trecho de código da janela exibida e copiá-lo para a área de transferência.

    ![Código de acesso][dataset-access-code]

5. Cole o código no bloco de anotações do seu aplicativo de Python local.

    ![Bloco de anotações][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Intermediários conjuntos de dados do Access de experiências de aprendizado de máquina

Depois de um experimento é executado no Studio aprendizado de máquina, será possível acessar conjuntos intermediários de nós de saída de módulos. Conjuntos intermediários são dados que foi criados e usados para etapas intermediárias quando uma ferramenta de modelo foi executada.

Conjuntos intermediários podem ser acessados como o formato de dados é compatível com a biblioteca de cliente Python.

Há suporte para os seguintes formatos (constantes para esses estão na `azureml.DataTypeIds` classe):

 - Texto sem formatação
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Você pode determinar o formato passando o mouse sobre um nó de saída do módulo. Ele é exibido juntamente com o nome do nó, em uma dica de ferramenta.

Alguns dos módulos, como a [divisão] [ split] módulo, em um formato de chamada de saída `Dataset`, que não é suportado pela biblioteca cliente Python.

![Formato de conjunto de dados][dataset-format]

Você precisa usar um módulo de conversão, como [Converter em CSV][convert-to-csv], para obter uma saída em um formato compatível.

![Formato de GenericCSV][csv-format]

As etapas a seguir mostram um exemplo que cria um experimento, executa e acessa o conjunto de dados intermediário.

1. Crie um novo experimento.

2. Inserir um módulo de **dataset adultos classificação binária Renda de censo** .

3. Inserir uma [divisão] [ split] módulo e se conectar a sua entrada para a saída de módulo do conjunto de dados.

4. Inserir um [Converter CSV] [ convert-to-csv] módulo e conecte sua entrada a um de [divisão] [ split] saídas de módulo.

5. Salvar o experimento, executá-lo e aguardar o término da execução.

6. Clique no nó de saída na [Converter CSV] [ convert-to-csv] módulo.

7. Quando aparece o menu de contexto, selecione **Gerar código de acesso de dados**.

    ![Menu de contexto][experiment]

8. Selecionar o trecho de código e copie-o para sua área de transferência da janela exibida.

    ![Código de acesso][intermediate-dataset-access-code]

9. Cole o código no seu bloco de anotações.

    ![Bloco de anotações][ipython-intermediate-dataset]

10. Você pode visualizar os dados usando matplotlib. Isso exibe em um histograma para a coluna de idade:

    ![Histograma][ipython-histogram]


##<a name="clientApis"></a>Usar a biblioteca de cliente Python de aprendizado de máquina para acessar, ler, criar e gerenciar conjuntos de dados

### <a name="workspace"></a>Espaço de trabalho

O espaço de trabalho é o ponto de entrada para a biblioteca de cliente Python. Forneça o `Workspace` token de classe com sua id de espaço de trabalho e a autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar conjuntos de dados

Enumerar todos os conjuntos de dados em um determinado espaço de trabalho:

    for ds in ws.datasets:
        print(ds.name)

Enumerar apenas criados pelo usuário conjuntos de dados:

    for ds in ws.user_datasets:
        print(ds.name)

Enumerar apenas os conjuntos de dados de exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Você pode acessar um conjunto de dados por nome (que diferencia maiusculas de minúsculas):

    ds = ws.datasets['my dataset name']

Ou você pode acessá-lo pelo índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados

Conjuntos de dados têm metadados, além de conteúdo. (Os conjuntos intermediários são uma exceção a essa regra e não tem quaisquer metadados).

Alguns valores de metadados são atribuídos pelo usuário no momento da criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Outras são valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte o `SourceDataset` classe para saber mais sobre os metadados disponíveis.


### <a name="read-contents"></a>Ler conteúdo

Os trechos de código fornecidos pelo Studio de aprendizado de máquina automaticamente Baixe e desserializar o conjunto de dados a um objeto de Pandas DataFrame. Isso é feito com o `to_dataframe` método:

    frame = ds.to_dataframe()

Se você preferir baixar os dados processados e realizar a desserialização sozinho, que é uma opção. No momento, esta é a única opção para formatos como 'ARFF', que não é possível desserializar a biblioteca de cliente Python.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler o conteúdo como binário:

    binary_data = ds.read_as_binary()

Você também pode abrir um fluxo ao conteúdo:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados

A biblioteca de cliente Python permite carregar conjuntos de dados do seu programa de Python. Esses conjuntos de dados, em seguida, estão disponíveis para uso em seu espaço de trabalho.

Se você tiver dados em uma DataFrame Pandas, use o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se seus dados já for serializados, você pode usar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca de cliente Python é capaz de serializar uma DataFrame Pandas nos seguintes formatos (constantes para esses estão na `azureml.DataTypeIds` classe):

 - Texto sem formatação
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente

Se você tentar carregar um novo dataset com um nome que corresponda a um conjunto de dados existente, você deve obter um erro de conflito.

Para atualizar um conjunto de dados existente, primeiro é necessário para obter uma referência para o conjunto de dados existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Use `update_from_dataframe` para serializar e substitua o conteúdo do conjunto de dados no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se você quiser serializar os dados em um formato diferente, especificar um valor para o opcional `data_type_id` parâmetro.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, você pode definir uma nova descrição, especificando um valor para o `description` parâmetro.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, você pode definir um novo nome, especificando um valor para o `name` parâmetro. De agora em diante, você irá recuperar o conjunto de dados usando o novo nome. O código a seguir atualiza os dados, o nome e a descrição.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

O `data_type_id`, `name` e `description` parâmetros são opcionais e padrão para seu valor anterior. O `dataframe` parâmetro sempre é necessário.

Se seus dados já for serializados, use `update_from_raw_data` em vez de `update_from_dataframe`. Se você acabou de passar no `raw_data` em vez de `dataframe`, ele funciona de maneira similar.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
