<properties
    pageTitle="Solução de problemas a Retraining de um serviço da Web do clássico de aprendizado de máquina do Azure | Microsoft Azure"
    description="Identifique e corrija encontrou comuns de problemas quando você estiver treinamento o modelo para um serviço Web de aprendizado de máquina de Azure."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Solução de problemas o treinamento de um serviço da Web do clássico de aprendizado de máquina do Azure

## <a name="retraining-overview"></a>Visão geral de treinamento

Quando você implanta um experimento previsão como um serviço web pontuação é um modelo estático. Conforme novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo deve ser retrained. 

Para uma explicação completa do processo novos treinamentos de um serviço Web clássico, consulte [Treinar novamente máquina aprendizagem modelos por programação](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>Processo de treinamento

Quando você precisar treinar novamente o serviço da Web, você deve adicionar algumas partes adicionais:

* Um serviço Web implantado do experimento treinamento. O experimento deve ter um módulo de **Saída de serviço Web** anexado a saída do módulo **Modelo trem** .  

    ![Anexe a saída de serviço web para o modelo de trem.][image1]

* Uma nova empresa adicionada à sua pontuação serviço da Web.  Você pode adicionar o ponto de extremidade por programação usando o código de amostra referenciado nos modelos de aprendizado de máquina treinar novamente programaticamente tópico ou por meio do portal de clássico Azure.

Em seguida, você pode usar o código c# de exemplo da página de ajuda de API do serviço Web treinamento treinar novamente o modelo. Depois de ter avaliado os resultados e estiver satisfeito com elas, você atualizar o modelo de treinamento pontuação serviço da web usando o novo ponto de extremidade que você adicionou.

Com todas as peças no local, as etapas principais que deve ser tomadas para treinar novamente o modelo são da seguinte maneira:

1.  Chamar o serviço da Web de treinamento: A chamada é para o lote de execução serviço (BES), não a solicitação de resposta serviço (RR). Você pode usar o código do exemplo c# na página da API de ajuda para fazer a chamada. 
2.  Encontre os valores para o *BaseLocation*, *RelativeLocation*e *SasBlobToken*: esses valores são retornados na saída da sua chamada para o serviço da Web de treinamento. 
      ![mostrando a saída de amostra novos treinamentos e os valores BaseLocation, RelativeLocation e SasBlobToken.][image6]
3.  Atualizar o adicionado ponto de extremidade do serviço da web pontuação com o novo modelo de treinamento: usando o código de exemplo fornecido nos modelos de aprendizado de máquina treinar novamente programaticamente, atualize o novo ponto de extremidade que você adicionou ao modelo de pontuação com o modelo recentemente treinamento do serviço da Web de treinamento.

## <a name="common-obstacles"></a>Obstáculos comuns

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verifique se você tem a URL correta de PATCHES

A URL de PATCH que você estiver usando deve ser aquele associado com o novo ponto de extremidade pontuação que você adicionou ao serviço da Web pontuação. Há várias maneiras de obter a URL de PATCH:

**Opção 1: programaticamente**

Para obter a URL correta PATCH:

1.  Execute o código de amostra de [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2.  Da saída de AddEndpoint, localize o valor de *HelpLocation* e copie a URL.

    ![HelpLocation na saída da amostra addEndpoint.][image2]

3.  Cole a URL em um navegador para navegar até uma página que fornece links de ajuda para o serviço Web.
4.  Clique no link do **Recurso de atualização** para abrir a página de ajuda de patch.

**Opção 2: Usar o portal de clássico do Azure**

1.  Entrar no [portal clássico Azure](https://manage.windowsazure.com).
2.  Abra a guia de aprendizado de máquina. 
     ![Guia de inclinada de máquina.][image4]
3.  Clique em seu nome de espaço de trabalho, em seguida, **Web Services**.
4.  Clique no serviço Web pontuação com que você está trabalhando. (Se você não modificaram o nome padrão do serviço da web, ele terminará no [pontuação Exp]..)
5.  Clique em **Adicionar ponto de extremidade**.
6.  Após o ponto de extremidade for adicionado, clique no nome do ponto de extremidade. Clique em **Recurso de atualização** para abrir a página de ajuda patch.

>[AZURE.NOTE] Se você tiver adicionado o ponto de extremidade para o serviço da Web de treinamento em vez do serviço Web de previsão, você receberá o seguinte erro quando você clica no link do **Recurso de atualização** : Desculpe, mas esse recurso não é suportada ou disponível neste contexto. Este serviço da Web não tem atualizáveis recursos. Pedimos desculpas pela inconveniência e está trabalhando no aprimoramento este fluxo de trabalho.

![Novo painel de controle do ponto de extremidade.][image3]

A página de ajuda de PATCH contém a URL PATCH, você deve usar e fornece código de exemplo, que você pode usar para chamá-lo.

![URL de patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Verifique que você está atualizando o ponto de extremidade de pontuação correto

* Não corrigir o serviço da Web de treinamento: A operação de patch deve ser executada no serviço da Web pontuação.
* Não corrigir o ponto de extremidade padrão no serviço Web: A operação de patch deve ser executada na extremidade do serviço Web nova pontuação que você adicionou.

Você pode verificar qual serviço Web o ponto de extremidade está em visitando o Azure portal clássico. 

>[AZURE.NOTE] Certifique-se de que você está adicionando o ponto de extremidade para o serviço Web previsão, não o serviço da Web de treinamento. Se você tiver implantado corretamente um treinamento e um serviço Web previsão, você verá dois serviços Web separados listados. O serviço da Web de previsão deve terminar com "[previsão exp.]".

1.  Entrar no [portal clássico Azure](https://manage.windowsazure.com).
2.  Abra a guia de aprendizado de máquina. 
     ![Interface do usuário do espaço de trabalho de aprendizado de máquina.][image4]
3.  Selecione seu espaço de trabalho.
4.  Clique em **serviços da Web**.
5.  Selecione o serviço Web de previsão.
6.  Verificar se o seu novo ponto de extremidade foi adicionado ao serviço da Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Verificar o espaço de trabalho que seu serviço da web está em garantir que ele esteja na região correto

1.  Entrar no [portal clássico Azure](https://manage.windowsazure.com).
2.  Selecione o menu de aprendizado de máquina.
      ![Região de aprendizado de máquina interface do usuário.][image4]
3.  Verifique se o local do seu espaço de trabalho.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png