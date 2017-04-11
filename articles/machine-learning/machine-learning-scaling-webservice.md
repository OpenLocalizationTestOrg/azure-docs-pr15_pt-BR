<properties
   pageTitle="Dimensionamento de serviço web | Microsoft Azure"
   description="Aprenda a escala de um serviço web por aumentando a concorrência e pontos de extremidade de novo."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure aprendizado de máquina, serviços web, operationalization, dimensionamento, ponto de extremidade, concorrência"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Dimensionamento de um serviço da Web

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço Web clássico de aprendizado de máquina. 

Por padrão, cada serviço da Web publicado está configurado para oferecer suporte a 20 solicitações simultâneas e pode ser tão alto quanto 200 solicitações simultâneas. Enquanto o portal de clássico Azure fornece uma maneira de definir esse valor, aprendizado de máquina do Azure otimiza automaticamente a configuração para fornecer o melhor desempenho para o serviço web e o valor de portal será ignorado. 

Se você planeja chame o API com uma carga maior que um valor máximo de chamadas simultâneas de 200 oferecerá suporte, você deve criar vários pontos de extremidade no serviço da Web mesmo. Em seguida, você pode distribuir aleatoriamente a carga em todos eles.

## <a name="add-new-endpoints-for-same-web-service"></a>Adicionar novos pontos de extremidade para o mesmo serviço da web

O dimensionamento de um serviço Web é uma tarefa comum. Alguns motivos para dimensionar são oferecer suporte a mais de 200 solicitações simultâneas, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer pontos de extremidade separados para o serviço da web. Você pode aumentar a escala adicionando mais pontos de extremidade do serviço da Web através do [portal de clássico Azure](https://manage.windowsazure.com/) ou o portal de [Serviço da Web de aprendizagem do Azure máquina](https://services.azureml.net/) .

Para obter mais informações sobre como adicionar novos pontos de extremidade, consulte [Criando pontos de extremidade](machine-learning-create-endpoint.md).

Tenha em mente que pode ser prejudicial se você não estiver chamando a API com correspondente alta taxa usando uma contagem de concorrência alta. Talvez você veja tempos limite esporádica e/ou picos na latência se você colocar uma carga relativamente baixa em uma API configurada para alta carga.

As APIs síncronas geralmente são usadas em situações onde uma baixa latência é desejada. Latência aqui indica o tempo necessário para a API concluir uma solicitação e não conta para os atrasos de rede. Digamos que você tenha uma API com uma latência de 50-ms. Consumir totalmente a capacidade disponível com alto nível de aceleração e o máximo de chamadas simultâneas = 20, você precisa chamar essa API 20 * 1000 / 50 = 400 vezes por segundo. Estendendo isso ainda mais, um máximo de chamadas simultâneas de 200 permite que você chame os tempos de API 4000 por segundo, supondo que uma latência de 50-ms.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
