<properties 
    pageTitle="Operações comuns da API de recomendações de aprendizado de máquina | Microsoft Azure" 
    description="Aplicativo de amostra do Azure ML recomendação" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Explicação passo a passo de aplicativo do exemplo de API de recomendações

>[AZURE.NOTE]Você deve começar a usar o serviço de cognitivas API recomendações em vez de nesta versão. O serviço de cognitivas recomendações será substituído por esse serviço e todos os novos recursos serão desenvolvidos lá. Ela tem novos recursos como processamento em lotes de suporte, uma melhor API do Explorer, uma experiência de inscrição/cobrança mais consistente, superfície de API limpeza, etc.
> Saiba mais sobre a [migração para o novo serviço cognitivas](http://aka.ms/recomigrate)

##<a name="purpose"></a>Finalidade

Este documento mostra o uso da API de recomendações de aprendizagem do Azure máquina através de um [aplicativo de exemplo](https://code.msdn.microsoft.com/Recommendations-144df403).

Este aplicativo não é usado para incluir todas as funcionalidades, nem utilizo todas as APIs. Ele demonstra alguns operações comuns a serem executadas quando você primeiro quiser brincar com o serviço de recomendação de aprendizado de máquina. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Introdução ao serviço de recomendação de aprendizado de máquina

Recomendações por meio do serviço de recomendação de aprendizado de máquina são habilitadas quando você cria um modelo de recomendação baseado nos dados a seguir:

* Um repositório dos itens que você deseja recomendar, também conhecido como um catálogo
* Dados que representam o uso de itens por usuário ou sessão (Isso pode ser adquirido ao longo do tempo por meio de aquisição de dados, não como parte do aplicativo amostra)

Depois de um modelo de recomendação é criado, você pode usá-lo para prever itens que um usuário pode estar interessado, de acordo com a um conjunto de itens (ou um único item) o usuário seleciona.

Para habilitar o cenário anterior, faça o seguinte no serviço de recomendação de aprendizado de máquina:

* Criar um modelo: Este é um recipiente que contém os dados (catálogo e uso) e os modelos de previsão. Cada contêiner de modelo é identificado por meio de uma ID exclusiva, que é alocada quando ele é criado. Essa ID é chamado a ID do modelo e ele é usado pela maioria das APIs. 
* Carregar catálogo: quando um contêiner de modelo é criado, você pode associar a ele um catálogo.

**Observação**: criar um modelo e carregar a um catálogo geralmente são executadas uma vez para o ciclo de vida do modelo.

* Carregar uso: Isso adiciona dados de uso para o recipiente de modelo.
* Criar um modelo de recomendação: depois de ter dados suficientes, você pode criar o modelo de recomendação. Essa operação usa os algoritmos de aprendizado de máquina superiores para criar um modelo de recomendação. Cada compilação está associada a uma ID exclusiva. Você precisa manter um registro dessa ID porque é necessário para a funcionalidade do algumas APIs.
* Monitorar o processo de construção: uma compilação de modelo de recomendação é uma operação assíncrona e pode levar de alguns minutos a várias horas, dependendo da quantidade de dados (catálogo e uso) e os parâmetros de compilação. Portanto, você precisa monitorar a compilação. Um modelo de recomendação é criado somente se sua compilação associadas for concluído com êxito.
* (Opcional) Escolha uma compilação de modelo de ativos recomendação: essa etapa só é necessária se você tiver mais de um modelo de recomendação criado em seu contêiner de modelo. Qualquer solicitação para obter recomendações sem indicando que o modelo de recomendação ativa é redirecionada automaticamente pelo sistema para a compilação de ativo padrão. 

**Observação**: um modelo de recomendação ativa é produção pronta e ele foi criado para carga de trabalho de produção. Isso é diferente de um modelo de recomendação não estiver ativo, que permanece em um ambiente de teste (às vezes chamado de preparação).

* Obter recomendações: depois que você tiver um modelo de recomendação, você poderá disparar recomendações para um único item ou uma lista de itens que você selecionar. 

Geralmente, você irá chamar recomendação obter por um determinado período de tempo. Durante esse período de tempo, você pode redirecionar dados de uso para o sistema de recomendação de aprendizado de máquina, que adiciona estes dados ao contêiner de modelo especificado. Quando você tiver bastante dados de uso, você pode criar um novo modelo de recomendação que incorpora os dados de uso adicionais. 

##<a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2013
* Acesso à Internet 
* Assinatura as recomendações API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Solução de aplicativo de exemplo de aprendizado de máquina Azure

Esta solução contém o código-fonte, exemplo de uso, arquivo de catálogo e diretivas para baixar os pacotes necessários para compilação.

##<a name="the-apis-used"></a>As APIs usadas

O aplicativo usa a funcionalidade de recomendação de aprendizado de máquina por meio de um subconjunto das APIs disponíveis. As seguintes APIs são demonstrou no aplicativo:

* Criar modelo: criar um contêiner lógico para armazenar os modelos de dados e recomendação. Um modelo é identificado por um nome e não é possível criar mais de um modelo com o mesmo nome.
* Carregar arquivo de catálogo: usar para carregar dados do catálogo.
* Carregar arquivo de uso: usar para carregar dados de uso.
* Disparar compilação: usar para criar um modelo de recomendação.
* Monitorar execução de compilação: usar para monitorar o status de uma compilação de modelo de recomendação.
* Escolha um modelo criado para recomendação: Use para indicar o modelo de recomendação ser usado por padrão para um determinado recipiente de modelo. Essa etapa é necessária somente se você tiver mais de um modelo de recomendação e você deseja ativar uma compilação inativo como o modelo de recomendação ativa.
* Obtenha recomendação: usar para recuperar itens Recomendados acordo com um único item determinado ou um conjunto de itens. 

Para obter uma descrição completa das APIs, consulte a documentação do Microsoft Azure Marketplace. 

**Observação**: um modelo pode ter várias compilações ao longo do tempo (não simultaneamente). Cada compilação é criada com a mesma ou um catálogo atualizado e dados de uso adicionais.

## <a name="common-pitfalls"></a>Armadilhas comuns

* Você precisa fornecer seu nome de usuário e sua chave de conta principal do Microsoft Azure Marketplace para executar o aplicativo de amostra.
* Executando o aplicativo de amostra consecutivamente falhará. O fluxo de aplicativo inclui criação, carregando, criando o monitor e Obtendo recomendações de um modelo predefinido; Portanto, ele falhará em execução consecutiva se você não altere o nome do modelo entre chamadas.
* Recomendações podem retornar sem dados. O aplicativo de amostra usa um arquivo muito pequeno de catálogo e o uso. Portanto, alguns itens do catálogo terá sem itens recomendados.

## <a name="disclaimer"></a>Aviso de isenção
O aplicativo de amostra não se destina a ser executado em um ambiente de produção. Os dados fornecidos no catálogo forem muito pequenos, e ele não fornecerá um modelo de recomendação significativo. Os dados são fornecidos como uma demonstração. 
 
