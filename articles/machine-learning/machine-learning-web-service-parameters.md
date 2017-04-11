<properties 
    pageTitle="Usar parâmetros de serviço Web de aprendizado de máquina Azure | Microsoft Azure" 
    description="Como usar parâmetros de serviço Web do aprendizado de máquina Azure para modificar o comportamento de seu modelo quando o serviço web for acessado." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>Usar parâmetros de serviço Web de aprendizado de máquina Azure

Um serviço da web de aprendizado de máquina do Azure é criado por um experimento que contém módulos com parâmetros configuráveis de publicação. Em alguns casos, talvez você queira alterar o comportamento de módulo enquanto o serviço da web está em execução. *Parâmetros de serviço da Web* permitem que você realizar esta tarefa. 

Um exemplo comum é configurar a [Importar dados] [ reader] módulo para que o usuário do serviço da web publicado pode especificar uma fonte de dados diferente quando o serviço web for acessado. Ou configurando a [Exportar dados] [ writer] módulo para que um destino diferente pode ser especificado. Alguns outros exemplos alterando o número de bits para o [Recurso de hash] [ feature-hashing] módulo ou o número de desejado recursos para a [seleção de recursos com base no filtro] [ filter-based-feature-selection] módulo. 

Você pode definir parâmetros de serviço Web e associá-los a um ou mais parâmetros de módulo no seu experimento, e você pode especificar se eles são obrigatórios ou opcionais. O usuário do serviço da web, em seguida, pode fornecer valores para esses parâmetros quando eles chamam o serviço web. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>Como configurar e usar os parâmetros de serviço Web

Você pode definir um parâmetro de serviço Web clicando no ícone ao lado de parâmetro para um módulo e selecionando "Definir como parâmetro de serviço web". Isso cria um novo parâmetro de serviço Web e conecta a esse parâmetro de módulo. Em seguida, quando o serviço web for acessado, o usuário pode especificar um valor para o parâmetro de serviço Web e é aplicado ao parâmetro módulo.

Depois de definir um parâmetro de serviço Web, ele ficará disponível para qualquer outro parâmetro de módulo do experimento. Se você definir um parâmetro de serviço Web associados a um parâmetro para um módulo, você pode usar esse mesmo parâmetro de serviço Web para qualquer outro módulo, desde que o parâmetro espera o mesmo tipo de valor. Por exemplo, se o parâmetro de serviço Web é um valor numérico, em seguida, ele pode apenas ser usado para os parâmetros de módulo que espera um valor numérico. Quando o usuário define um valor para o parâmetro de serviço Web, ele será aplicado a todos os parâmetros de módulo associado.

Você pode optar por fornecer um valor padrão para o parâmetro de serviço Web. Nesse caso, o parâmetro é opcional para o usuário do serviço da web. Se você não fornecer um valor padrão, o usuário é necessário inserir um valor quando o serviço web for acessado.

A documentação da API do serviço da web inclui informações para o usuário de serviço web sobre como especificar o parâmetro de serviço Web programaticamente ao acessar o serviço da web.

>[AZURE.NOTE] A documentação da API para um serviço web clássico é fornecida por meio do link de **página de ajuda de API** no serviço da web **DASHBOARD** no Studio de aprendizado de máquina. A documentação da API para um novo serviço web é fornecida por meio do portal de [Serviços de Web de aprendizado de máquina Azure](https://services.azureml.net/Quickstart) nas páginas **Consume** e **Swagger API** para o serviço web.


##<a name="example"></a>Exemplo

Como exemplo, vamos supor que temos um experimento com um [Exportar dados] [ writer] módulo que envia informações para o armazenamento de blob do Microsoft Azure. Vamos definir um parâmetro de serviço Web denominada "Blob caminho" que permite que o usuário de serviço da web alterar o caminho para o armazenamento de blob quando o serviço for acessado.

1.  No Studio de aprendizado de máquina, clique em [Exportar dados] [ writer] módulo para selecioná-la. Suas propriedades são mostradas no painel Propriedades à direita da tela experimento.

2.  Especifique o tipo de armazenamento:

    - Em **Especifique o destino de dados**, selecione "Armazenamento de Blob do Azure".
    - Em **Especifique o tipo de autenticação**, selecione "Conta".
    - Insira as informações de conta para o armazenamento de blob do Microsoft Azure. 
    <p />

3.  Clique no ícone à direita do **caminho para blob que começa com o parâmetro de contêiner**. Ele fica assim:

    ![Ícone de parâmetro de serviço Web][icon]

    Selecione "Definir como parâmetro de serviço web".

    Uma entrada é adicionada em **Parâmetros de serviço Web** na parte inferior do painel de propriedades com o nome "Caminho para blob que começa com o contêiner". Este é o parâmetro de serviço Web que agora está associada ao [Exportar dados] [ writer] parâmetro de módulo.

4.  Para renomear o parâmetro de serviço Web, clique no nome, insira "Blob caminho" e pressione a tecla **Enter** . 
 
5.  Para fornecer um valor padrão para o parâmetro de serviço Web, clique no ícone à direita do nome, selecione "Fornecer valor padrão", insira um valor (por exemplo, "container1/output1.csv") e pressione a tecla **Enter** .

    ![Parâmetro de serviço Web][parameter]

6.  Clique em **Executar**. 

7.  Clique em **Implantar o serviço da Web** e selecione **implantar Web [clássico]** ou **Implantar o serviço Web [novo] para implantar o serviço da web** .

O usuário do serviço da web agora pode especificar um novo destino para [Exportar dados] [ writer] módulo ao acessar o serviço da web.

##<a name="more-information"></a>Mais informações

Para obter um exemplo mais detalhado, consulte a entrada de [Parâmetros de serviço Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) no [Blog de aprendizado de máquina](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obter mais informações sobre como acessar um serviço da web aprendizado de máquina, consulte [como consumir uma serviço web de aprendizado de máquina publicada](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
