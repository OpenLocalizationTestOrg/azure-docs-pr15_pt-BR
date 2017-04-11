<properties
    pageTitle="Importar dados para Studio de aprendizado de máquina de outro experimento | Microsoft Azure"
    description="Como salvar dados de treinamento em Studio de aprendizado de máquina do Azure e usá-lo em outro experimento."
    keywords="Importar dados, dados, fontes de dados, dados de treinamento"
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


# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importar seus dados para Azure Studio de aprendizado de máquina de outro experimento

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Haverá ocasiões em que você desejará fazer um resultado intermediário de um experimento e usá-lo como parte de outro experimento.  Para fazer isso, você salve o módulo como um conjunto de dados:

1. Clique na saída do módulo que você deseja salvar como um conjunto de dados.

2. Clique em **Salvar como conjunto de dados**.

3. Quando solicitado, digite um nome e uma descrição que permite identificar facilmente o conjunto de dados.

4. Clique na marca de seleção **Okey** .

Quando termina de salvar, o conjunto de dados estará disponível para uso em qualquer experimento em seu espaço de trabalho. Você pode encontrá-lo na lista de **Conjuntos de dados salvo** na paleta de módulo.

