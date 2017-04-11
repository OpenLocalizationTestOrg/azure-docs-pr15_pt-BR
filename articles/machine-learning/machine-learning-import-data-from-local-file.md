<properties
    pageTitle="Importar dados para Studio de aprendizado de máquina de um arquivo local | Microsoft Azure"
    description="Como importar seus dados de treinamento do Azure Studio de aprendizado de máquina de um arquivo local."
    keywords="Importar dados, formato de dados, tipos de dados, fontes de dados, dados de treinamento"
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
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importar seus dados de treinamento para Azure Studio de aprendizado de máquina de um arquivo local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Para usar seus próprios dados no Studio de aprendizado de máquina, você pode carregar um arquivo de dados antecedência do disco rígido local para criar um módulo de conjunto de dados em seu espaço de trabalho. 


## <a name="import-data-from-a-local-file"></a>Importar dados de um arquivo local

Você pode importar dados de um disco rígido local, fazendo o seguinte:

1. Clique em **+ nova** na parte inferior da janela Studio de aprendizado de máquina.
2. Selecione **DATASET** e **arquivo LOCAL FROM**.
3. Na caixa de diálogo **carregar um novo conjunto de dados** , navegue até o arquivo que você deseja carregar
4. Insira um nome, identifique o tipo de dados e, opcionalmente, insira uma descrição. Uma descrição é recomendável - -permite que você registre quaisquer características sobre os dados que você deseja lembrar ao usar os dados no futuro.
5. A caixa de seleção **Este é a nova versão de um dataset existente** permite que você atualize um dataset existente com novos dados. Basta clicar nesta caixa de seleção e insira o nome de um conjunto de dados existente.

Durante o carregamento, você verá uma mensagem de que o arquivo está sendo carregado. Carregar tempo dependerá do tamanho dos seus dados e a velocidade da sua conexão com o serviço.
Se você souber que o arquivo será levar muito tempo, você pode fazer outras coisas dentro Studio de aprendizado de máquina enquanto você aguarda. No entanto, fechar o navegador fará com que o upload de dados falha.

Depois que seus dados são carregados, ele está armazenado em um módulo de dataset e está disponível para qualquer experimento em seu espaço de trabalho.
Quando você estiver editando um experimento, você pode encontrar os conjuntos de dados que você criou na lista de **Conjuntos de dados meu** sob a lista de **Conjuntos de dados salvo** na paleta de módulo. Você pode arrastar e soltar o conjunto de dados para a tela experimento quando desejar usar o conjunto de dados para análise posterior e aprendizado de máquina.



