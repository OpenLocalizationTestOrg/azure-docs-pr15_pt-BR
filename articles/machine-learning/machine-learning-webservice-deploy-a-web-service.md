<properties
   pageTitle="Implantando um novo serviço Web"
   description="O fluxo de trabalho de implantação de um BRAÇO de serviço web baseado em"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Implantar um novo serviço web

Aprendizado agora de máquina do Microsoft Azure fornece serviços da web que são baseados no [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permitindo novas opções de plano de cobrança e implantando o seu serviço da web para várias regiões.

O fluxo de trabalho geral para implantar um serviço web usando serviços Web de aprendizado de máquina do Microsoft Azure é:

* Criar um experimento previsão
* implantá-lo
* configurar seu nome
* plano de cobrança
* teste-
* consumi-lo.

O gráfico a seguir ilustra o fluxo de trabalho.

![Fluxo de trabalho de implantação de serviço Web][1]
 
## <a name="deploy-web-service-from-studio"></a>Implantar o serviço da web do Studio 

Implantar um experimento como um novo serviço web. Entre no Studio de aprendizado de máquina e criar um novo serviço web de previsão. 

**Observação**: se você já implantou um experimento como um serviço web clássico não implantá-lo como um novo serviço web.
 
Clique em **Executar** na parte inferior da tela experimento e clique em **Implantar o serviço da Web** e **Implantar o serviço da Web [novo]**. A página de implantação do Gerenciador de serviço de Web de aprendizado de máquina será aberta.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Gerenciador de serviço de Web de aprendizado de máquina implantar experimento página
Na página implantar experimento, insira um nome para o serviço web.
Selecione um plano de preços. Se você tiver um plano de preços existente, que você poderá selecioná-lo, caso contrário, você deve criar um novo plano de preço para o serviço. 

1.  No **Plano de preço de** lista suspensa, selecione um plano existente ou selecione a opção **Selecionar novo plano** .
2.  Em **Nome do plano**, digite um nome que identifique o plano na fatura.
3.  Selecione uma das **Camadas de plano mensal**. Observe que o padrão de níveis de plano para os planos para sua região padrão e seu serviço da web é implantado nessa região.

Clique em **implantar** e a página de início rápido para sua abre de serviço web.

## <a name="quickstart-page"></a>Página de início rápido
A página de início rápido do serviço web fornece acesso e orientações sobre as tarefas mais comuns que você irá executar depois de criar um novo serviço web. A partir daqui, você pode acessar facilmente a página de **teste** e a página de **Consume** .

## <a name="testing-your-web-service"></a>Teste seu serviço da web

Na página do início rápido, clique em Testar serviço web em tarefas comuns.   

Para testar o serviço web como um serviço de solicitação-resposta (RR):

* Na barra de menus, clique em **Testar** .
* Clique em **Solicitação-resposta**.
* Insira os valores adequados para as colunas de entrada do seu experimento.
* Clique em teste **Solicitação-resposta**.

Resultados exibirá no lado direito da página.

Para testar um serviço da web de serviço de execução de lote (BES), você usará um arquivo CSV:

* Na barra de menus, clique em **Testar** .
* Clique em **lote**.
* Em sua entrada, clique em Procurar e navegue até seu arquivo de dados de exemplo.
* Clique em **Testar**.

O status do seu teste é exibido em **Testar trabalhos de lote**.

## <a name="consuming-your-web-service"></a>Consumindo seu serviço da Web

Quando implantado como um serviço web, experiências de aprendizado de máquina do Azure fornecem uma API REST que pode ser consumida por uma ampla variedade de dispositivos e plataformas. Isso ocorre porque o simples API REST aceita e responde com JSON mensagens formatadas. O portal de aprendizado de máquina do Azure fornece código que pode ser usado para chamar o serviço web em R, c# e Python.
 
Na página consomem, você pode encontrar:

* A chave API e URI para consumir o serviço da web em aplicativos.
* Modelos de aplicativo do Excel e web para iniciar iniciar o processo de consumo.
* Código de exemplo em c#, python e R para iniciá-lo.

Para obter mais informações sobre o consumo de serviços web, consulte [como consumir um serviço da web de aprendizado de máquina Azure que tenha sido implantado de uma experimento de aprendizado de máquina](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o consumo de serviços web, consulte:

[Como consumir um serviço da web de aprendizado de máquina do Azure que tenha sido implantado de uma experimento de aprendizado de máquina](machine-learning-consume-web-services.md)

[Serviços da Web do aprendizado de máquina Azure: Implantação e consumo](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
