<properties
    pageTitle="Treinar novamente uma modelo de aprendizado de máquina | Microsoft Azure"
    description="Saiba como treinar novamente um modelo e atualizar o serviço da Web para usar o modelo de treinamento recentemente no aprendizado de máquina do Azure."
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
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Treinar novamente uma modelo de aprendizado de máquina

Como parte do processo de operationalization de modelos de aprendizado de máquina no aprendizado de máquina do Azure, seu modelo está treinamento e salvo. Em seguida, usá-lo para criar um serviço Web predicative. O serviço da Web, em seguida, pode ser consumido em sites da web, painéis e aplicativos móveis. 

Modelos que você cria usando o aprendizado de máquina geralmente não são estáticos. Que novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados modelo precisa ser retrained. 

Treinamento pode ocorrer com frequência. Com o recurso de programação API de treinamento, você pode programaticamente treinar novamente o modelo usando as APIs de treinamento e atualizar o serviço Web com o modelo de treinamento recentemente. 

Este documento descreve o processo de novos treinamentos e mostra como usar as APIs de treinamento.

## <a name="why-retrain-defining-the-problem"></a>Por que treinar novamente: definindo o problema  

Como parte da processo de treinamento de aprendizado de máquina, um modelo é treinamento usando um conjunto de dados. Modelos que você cria usando o aprendizado de máquina geralmente não são estáticos. Que novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados modelo precisa ser retrained.

Nesses cenários, uma API de programação oferece uma maneira conveniente para permitir que você ou o consumidor de seu APIs para criar um cliente que pode, numa base pontual ou regulares, treinar novamente o modelo usando seus próprios dados. Em seguida, eles podem avaliar os resultados de treinamento e atualizar a API do serviço Web para usar o modelo recentemente treinamento.

>[AZURE.NOTE] Se você tiver um experimento de treinamento existente e o serviço Web novos, você talvez queira fazer check-out Retrain um serviço Web previsão existente em vez de seguir a instruções passo a passo mencionada na seção a seguir.

## <a name="end-to-end-workflow"></a>Fluxo de trabalho de ponta a ponta 

O processo envolve os seguintes componentes: um experimento de treinamento e um experimento previsão publicados como um serviço da Web. Para habilitar o treinamento de um modelo de treinamento, o experimento treinamento deve ser publicado como um serviço da Web com a saída de um modelo de treinamento. Isso permite o acesso de API para o modelo de treinamento. 

As etapas a seguir se aplicam aos serviços de novo e Web clássico:

Crie o serviço Web de previsão inicial:

* Criar um experimento de treinamento
* Criar um experimento web previsão
* Implantar um serviço web de previsão

Treinar novamente o serviço da Web:

* Atualizar experimento de treinamento para permitir o treinamento
* Implantar o serviço web novos treinamentos
* Use o código de serviço de execução de lote treinar novamente o modelo

Para uma explicação das etapas anteriores, consulte [modelos de aprendizado de máquina treinar novamente programaticamente](machine-learning-retrain-models-programmatically.md).

Se você implantou um serviço Web clássico:

* Criar uma nova empresa no serviço da Web de previsão
* Obter a URL de PATCH e o código
* Usar a URL PATCH para apontar o novo ponto de extremidade para o modelo de retrained 

Para uma explicação das etapas anteriores, consulte [treinar novamente um serviço Web clássico](machine-learning-retrain-a-classic-web-service.md).

Se você enfrentar dificuldades treinamento um serviço Web clássico, consulte [solução de problemas o treinamento de um serviço da Web do clássico de aprendizado de máquina do Azure](machine-learning-troubleshooting-retraining-models.md).

Se você implantou um serviço Web nova:

* Entre sua conta do Gerenciador de recursos do Azure
* Obter a definição de serviço Web
* Exporte a definição de serviço Web como JSON
* Atualizar a referência para o `ilearner` blob no JSON
* Importar o JSON para uma definição de serviço Web
* Atualizar o serviço da Web com a nova definição de serviço Web

Para uma explicação das etapas anteriores, consulte [treinar novamente um serviço de nova Web usando os cmdlets do PowerShell de gerenciamento de aprendizado de máquina](machine-learning-retrain-new-web-service-using-powershell.md).

O processo de configuração de treinamento para um serviço Web clássico envolve as seguintes etapas:

![Visão geral do processo de treinamento][1]

O processo de configuração de treinamento para um serviço Web novo envolve as seguintes etapas:

![Visão geral do processo de treinamento][7]

## <a name="other-resources"></a>Outros recursos

- [Modelos de treinamento e aprendizado de máquina Azure atualizando com fábrica de dados do Azure](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Criar muitos modelos de aprendizado de máquina e web pontos de extremidade do serviço a partir de um experimento usando o PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- O vídeo [AML treinamento modelos usando APIs](https://www.youtube.com/watch?v=wwjglA8xllg) mostra como treinar novamente os modelos de aprendizado de máquina criados no aprendizado de máquina do Azure usando as APIs de treinamento e PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

