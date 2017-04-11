<properties
   pageTitle="Criar um servidor do Analysis Services no Azure | Microsoft Azure"
   description="Saiba como criar uma instância do servidor do Analysis Services no Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="create-an-analysis-services-server"></a>Criar um servidor do Analysis Services
Este artigo o orienta criando um novo recurso de servidor do Analysis Services em sua assinatura do Azure.

## <a name="before-you-begin"></a>Antes de começar
Para começar, você precisa:

- **Assinatura do Azure**: visite [Avaliação gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
- **Grupo de recursos**: usar um grupo de recursos que você já possui ou [Crie um novo](../azure-resource-manager/resource-group-overview.md).

> [AZURE.NOTE] Criando um servidor do Analysis Services pode resultar em um novo serviço faturável. Para saber mais, consulte preços do Analysis Services.

## <a name="create-an-analysis-services-server"></a>Criar um servidor do Analysis Services

1. Entrar no [portal do Azure](https://portal.azure.com).

2. Clique em **+ novo** > **inteligência + analytics** > **Analysis Services**.

3. Na lâmina **Analysis Services** , preencha os campos obrigatórios e pressione em **criar**.

    ![Criar servidor](./media/analysis-services-create-server/aas-create-server-blade.png)

    - **Nome do servidor**: digite um nome exclusivo usado para fazer referência ao servidor.

    - **Assinatura**: selecione a assinatura este servidor cobra para.

    - **Grupo de recursos**: estas são contêineres projetados para ajudá-lo a gerenciar um conjunto de recursos Azure. Para saber mais, consulte [grupos de recursos](../resource-group-overview.md).

    - **Local**: local do Azure Este data center hospeda o servidor. Escolha um local mais próximo de sua base de usuários maior.

    - **Nível de preços**: selecione um nível de preços. Até 100 GB de modelos de tabela são suportados. Você sempre pode alterar sua camada de preços mais tarde.

4. Clique em **criar**.

Criar normalmente leva menos de um minuto; muitas vezes apenas alguns segundos. Se você selecionou **Adicionar portal**, navegue até seu portal para ver o novo servidor. Ou, navegue até **mais serviços** > **Analysis Services** para ver se o servidor está pronto. Se não aparecer na lista de atualização.

 ![Painel de controle](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Próximas etapas
Depois que você criou seu servidor, você pode [implantar um modelo](analysis-services-deploy.md) nele usando SSDT ou com SSMS.

Se um modelo que você implantar ao seu servidor de se conectar a fontes de dados locais, você precisará instalar um [gateway de dados local](analysis-services-gateway.md) em um computador na sua rede.
