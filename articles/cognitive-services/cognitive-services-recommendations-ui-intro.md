<properties
    pageTitle="Criar um modelo com a UI Recommnendations | Microsoft Azure"
    description="Azure recomendações de aprendizado de máquina - construindo um modelo com a interface de usuário de recomendações"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Construindo um modelo com a interface de usuário de recomendações

Este documento é um guia passo a passo. Nosso objetivo é orientá-lo pelas etapas necessárias para treinar um modelo usando a [Interface de usuário de recomendações](https://recommendations-portal.azurewebsites.net/).
Percorrer o exercício permite compreender o processo para criar um modelo antes de fazer isso programaticamente. Ele também familiariza você com a interface do usuário, que é útil ao começar a usar o serviço.

Neste exercício leva cerca de 30 minutos.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Etapa 1 - logon para as recomendações de interface do usuário ##

1. Se você não tiver feito isso, você precisará [inscrição](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para uma nova assinatura de [API de recomendações](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) . Você pode se inscrever para o serviço no **Azure** em [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) e entre com sua conta do Azure. Instruções detalhadas sobre o processo de inscrição são fornecidas na *tarefa 1* do [Guia de Introdução](cognitive-services-recommendations-quick-start.md) 

1. Após ter obtido uma **chave** para sua assinatura de API de recomendações, vá para [Recomendações da interface do usuário](https://recommendations-portal.azurewebsites.net/). 

1. Faça logon no portal por meio de sua chave do campo de **Chave da conta** e clique no botão de **logon** .

    ![Recomendações de interface do usuário: Diálogo de entrada.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Etapa 2 - Vamos reunir alguns dados de treinamento ##

Antes de criar uma compilação, o mecanismo precisa duas partes de informações: um arquivo de catálogo e um conjunto de arquivos de uso. 

O arquivo de catálogo contém informações sobre os itens que você estiver oferecendo a seu cliente. Um arquivo de uso contém informações sobre como esses itens são usados, ou as transações de sua empresa.

Normalmente você prefere consultar seu banco de dados de armazenamento para essas partes de informações. Hoje, fornecemos alguns dados de exemplo para você para que você pode aprender como usar a API de recomendações.

Você pode baixar os dados do [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copiar e descompactar o arquivo **Books.Zip** para uma pasta no computador local. Por exemplo, **c:\data**.

Informações detalhadas sobre o esquema dos arquivos do catálogo e o uso podem ser encontradas no artigo [Coletando dados treinar seu modelo](cognitive-services-recommendations-collecting-data.md) .
 
Para este exercício, vamos trabalhar com um pequeno arquivo para que você não precisa muito esperar muito tempo para treinamento. Se você quiser tentar um arquivo mais realista, podemos também colocou **MsStoreData.zip** que contém transações de amostra da Microsoft Store no [mesmo local](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Etapa 3 - criar um projeto e carregar dados de uso e de catálogo ##

Após fazer logon com a [Interface de usuário de recomendações](https://recommendations-portal.azurewebsites.net/), você verá a página de projetos. Se você tiver criado anteriormente quaisquer projetos, você deve vê-los aqui.
Um projeto (também conhecido como *um modelo* na Referência API) é um contêiner para os seus dados de uso e de catálogo. Você pode criar várias *compilações* dentro do projeto. Podemos orientará você durante o processo nas próximas etapas.

1. Para criar um novo projeto, digite o nome na caixa de texto (algo como "MyFirstModel" funcionaria) e clique em **Adicionar projeto**.
 
    ![Recomendações de interface do usuário: Página de projetos.][reco_projects]

1. Depois que o projeto é criado, clique no botão **Procurar arquivo** na seção **Adicionar um arquivo de catálogo** . Carregue o catálogo obtido na etapa 2. Se você salvou-la em *c:\data*, você precisa navegar para essa pasta.

    ![Recomendações de interface do usuário: Adicionando dados a um projeto.][reco_firstmodel]

1. Uma vez carregado o catálogo, clique no botão de **Procurar arquivo** na seção **Adicionar arquivos de uso** . Adicione o arquivo usage_large.txt.

> **E se eu tiver um arquivo grande de dados de uso?**
>
> Somente uso arquivos menores do que 200 MB pode ser carregado. Dito isto, o sistema pode suportar vale 2 GB de dados de transação, para que você pode carregar mais de um arquivo, se necessário.
> Talvez você não precise que quantidade de dados para gerar um bom modelo, não é apenas o tamanho dos dados que é importante, mas a qualidade dos dados. É comum para ver os dados de uso onde a maioria das transações são apenas em alguns itens populares e há em "pouco sinalizar" para outros itens.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Etapa 4 - vamos fazer algum treinamento! ##

Agora que você carregou o catálogo e os dados de uso, estamos prontos para treinar o mecanismo de forma que ele pode aprender padrões de nossos dados.

1.  Clique no botão **Criar novo** .

1.  Selecione **recomendações** como o tipo de compilação. Observe que oferecemos suporte para construir uma classificação e um FBT (frequentemente comprou juntos) criar tipos também.

    ![Recomendações de interface do usuário: Construir diálogo.][reco_build_dialog.png]


    Uma compilação FBT permite identificar padrões para os produtos que são geralmente adquiridos/consumidas na mesma transação.
    Uma compilação de classificação é usada para identificar recursos de interesse. 
    Não entraremos muito profundamente FBT ou classificação baseia-se neste workshop, mas se você estiver interessado você deve check-out de [criar tipos e página de documentação de qualidade do modelo](cognitive-services-recommendations-buildtypes.md).

1. Clique no botão **Construir** . O processo de compilação leva cerca de cinco minutos, se você estiver usando os dados de livros. Demora mais tempo em conjuntos de dados maiores.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Etapa 5 - vamos descobrir o que aprendeu máquina! ##

Depois que sua compilação é concluída, você observará uma nova compilação na lista de versões. Esta compilação pode ser consultada para recomendações de item e usuário.

1. Depois que sua compilação é concluída, clique em **pontuação**. Isso permite que você seja executado com o modelo e ver quais itens são recomendados.

    ![Recomendações de interface do usuário: Botão de pontuação][reco_score_button]

1. Selecione um item para ver quais itens serão retornados como recomendações para aquele item. Observe que, se não houver transações suficientes para prever uma recomendação para um item específico, o sistema não retornará recomendações para aquele item.  Se por algum motivo você tiver um item que não retorna nenhum recomendações, tente outros itens de pontuação.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Etapa 6 - próximos passos ##
Parabéns! Você tem treinamento um modelo e, em seguida, obtido recomendações do modelo.  A interface do usuário recomendações é uma ferramenta útil que permite que você veja o estado de seus projetos e cria. 

Agora que você tem um modelo, talvez você queira saber como fazer todas as etapas acima programaticamente. Para saber chamar a API programaticamente, convida você verificar a [Referência à API recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) e baixar o [Aplicativo de exemplo de recomendações](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
