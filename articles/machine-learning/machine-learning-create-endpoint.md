<properties
    pageTitle="Criação de pontos de extremidade de serviço Web no aprendizado de máquina | Microsoft Azure"
    description="Criação de pontos de extremidade de serviço Web no aprendizado de máquina do Azure"
    services="machine-learning"
    documentationCenter=""
    authors="hiteshmadan"
    manager="padou"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/04/2016"
    ms.author="himad"/>


# <a name="creating-endpoints"></a>Criação de pontos de extremidade

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço Web clássico.

Quando você cria serviços Web que você comercializa encaminhar aos seus clientes, você precisa fornecer modelos de treinamento para cada cliente que ainda estão vinculadas a experiência do qual o serviço da Web foi criado. Além disso, todas as atualizações para o experimento devem ser aplicadas seletivamente para um ponto de extremidade sem substituir as personalizações.

Para fazer isso, aprendizado de máquina do Azure permite criar vários pontos de extremidade de um serviço Web implantado. Cada ponto de extremidade no serviço da Web é endereçado, limitado e gerenciado independentemente. Cada ponto de extremidade é uma URL exclusiva e a chave de autorização que você pode distribuir aos seus clientes.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Adicionar pontos de extremidade de um serviço da Web

Há três maneiras de adicionar um ponto de extremidade de um serviço da Web.

* Programaticamente
* Por meio do portal de serviços de Web de aprendizado de máquina do Azure
* Embora o Azure portal clássico

Quando o ponto de extremidade é criado, você pode consumi-la por meio de APIs síncrono, lote APIs e planilhas do excel. Além de adicionar pontos de extremidade através esta UI, você também pode usar as APIs de gerenciamento de ponto de extremidade para adicionar pontos de extremidade por programação.

 >[AZURE.NOTE] Se você tiver adicionado mais pontos de extremidade para o serviço Web, você não pode excluir o ponto de extremidade padrão.

## <a name="adding-an-endpoint-programmatically"></a>Adicionando um ponto de extremidade por programação

Você pode adicionar um ponto de extremidade ao serviço Web programaticamente usando o código de exemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Adicionando um ponto de extremidade usando o portal de serviços de Web de aprendizado de máquina do Azure

1. No Studio de aprendizado de máquina, na coluna de navegação esquerdo, clique em serviços Web.
2. Na parte inferior do painel de serviço Web, clique em **gerenciar pontos de extremidade**. O portal de serviços de Web de aprendizado de máquina Azure abre a página de pontos de extremidade do serviço da Web.
3. Clique em **novo**.
4. Digite um nome e uma descrição para o novo ponto de extremidade. Nomes de ponto de extremidade devem ser 24 caracteres ou menos de comprimento e devem ser constituídos de letras minúsculas ou números. Selecione o nível de log e se os dados de exemplo estão habilitados. Para obter mais informações sobre logs, consulte [Habilitar o log para serviços Web de aprendizado de máquina](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Adicionando um ponto de extremidade usando o portal de clássico Azure


1. Entre [portal do Azure clássico](http://manage.windowsazure.com), clique em **Aprendizado de máquina** na coluna à esquerda. Clique no espaço de trabalho que contém o serviço da Web na qual você está interessado.

    ![Navegue até o espaço de trabalho](./media/machine-learning-create-endpoint/figure-1.png)

2. Clique em **serviços da Web**.

    ![Navegue até serviços da Web](./media/machine-learning-create-endpoint/figure-2.png)

3. Clique no serviço da Web que você está interessado para ver a lista de pontos de extremidade disponíveis.

    ![Navegue até o ponto de extremidade](./media/machine-learning-create-endpoint/figure-3.png)

4. Na parte inferior da página, clique em **Adicionar ponto de extremidade**. Digite um nome e descrição, certifique-se de que não há nenhum outro ponto de extremidade com o mesmo nome nesse serviço da Web. Deixe o nível de aceleração com seu valor padrão, a menos que tenha requisitos especiais. Para saber mais sobre a limitação, consulte [Dimensionamento API de pontos de extremidade](machine-learning-scaling-webservice.md).

    ![Criar ponto de extremidade](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Próximas etapas

[Como consumir um serviço Web de aprendizado de máquina do Azure publicado](machine-learning-consume-web-services.md).
