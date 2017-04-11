<properties
    pageTitle="Suplemento para serviços Web de aprendizado de máquina do Excel | Microsoft Azure"
    description="Como usar serviços Web de aprendizado de máquina Azure diretamente no Excel sem escrever nenhum código."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Suplemento do Excel para serviços Web de aprendizado de máquina do Azure

Excel torna mais fácil chamar serviços Web diretamente sem a necessidade de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Etapas para usar um serviço Web existente na pasta de trabalho

1. Abra o [arquivo do Excel de exemplo](http://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros sobre o Titanic.
2. Escolha o serviço Web clicando nela-"previsão de sobrevivência Titanic (suplemento Excel exemplo) [pontuação]" neste exemplo.

    ![Selecione o serviço da Web][01]

3. Isso levará você à seção **Predict** .  Esta pasta de trabalho já contém dados de exemplo, mas para uma pasta de trabalho em branco, você pode selecionar uma célula no Excel e clique em **dados de exemplo do uso**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Verifique se que a caixa "Meus dados possuem cabeçalhos" está marcada.
5. Em **saída**, insira o número de célula onde deseja que a saída para ser, por exemplo "H1" aqui.
6. Clique em **prever**.

    ![Prever seção][02]

## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um serviço Web novo

Implantar um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [passo a passo etapa 5: implantar o serviço Web de aprendizado de máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md).

Obter a chave de API para seu serviço da Web. Onde você fazer isso depende de se você publicou um serviço da Web de aprendizado de máquina clássico de um serviço Web de aprendizado de máquina nova.

**Usar um serviço Web clássico** 

1. No Studio de aprendizado de máquina, clique na seção de **Serviços da WEB** no painel esquerdo e, em seguida, selecione o serviço da Web.

    ![Selecione Studio um serviço da Web][04]

2. Copie a chave de API do serviço da Web.

    ![Chave Studio API][05]

3. Na guia **painel** do serviço da Web, clique no link de **Solicitação/resposta** .
4. Procure a seção de **URI de solicitação** .  Copie e salve a URL.

>[AZURE.NOTE] Agora é possível entrar no portal do [Azure serviços de Web de aprendizado de máquina](https://services.azureml.net) para obter a chave de API para um serviço Web clássico de aprendizado de máquina.

**Usar um serviço Web novo**

1. No portal de [Serviços de Web de aprendizado de máquina Azure](https://services.azureml.net) , clique em **Serviços Web**, selecione o serviço da Web. 
2. Clique em **consumir**.
3. Procure a seção de **informações de consumo básica** . Copie e salve a **Chave primária** e a URL de **Solicitação-resposta** .


## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um serviço Web novo

1. Implantar um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [passo a passo etapa 5: implantar o serviço Web de aprendizado de máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md).
2. Clique em **consumir**.
3. Procure a seção de **informações de consumo básica** . Copie e salve a **Chave primária** e a URL de **Solicitação-resposta** .
2. No Excel, vá para a seção de **Serviços da Web** (se você estiver na seção **Predict** , clique na seta para voltar para ir para a lista de serviços da Web).

    ![Vá para seleção de serviço Web][03]
    
3. Clique em **Adicionar serviço Web**.
4. Cole a URL na caixa de texto de suplemento Excel rotulada **URL**.
5. Cole a chave de API/primário para a caixa de texto rotulada **chave API**.
6. Clique em **Adicionar**.

    ![Chave API e a URL para um serviço Web clássico.][06]

10. Para usar o serviço da Web, siga as instruções anteriores, "Etapas para usar um serviço da Web existente."

## <a name="sharing-your-workbook"></a>Compartilhar sua pasta de trabalho

Se você salvar sua pasta de trabalho, a chave de API/primário para os serviços Web que você adicionou também é salvas. Isso significa que você só deve compartilhar a pasta de trabalho com pessoas que você confia.

Perguntas qualquer na seção de comentário seguinte ou em nosso [Fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
