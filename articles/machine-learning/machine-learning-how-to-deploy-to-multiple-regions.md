<properties
    pageTitle="Como implantar um serviço Web para várias regiões | Microsoft Azure"
    description="Etapas para implantar regiões (copiar) um novo serviço Web para outra."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Como implantar um serviço Web para várias regiões

Os novos serviços Web do Azure permitem que você facilmente implantar um serviço web para várias regiões sem precisar de várias assinaturas ou espaços de trabalho. 

Preços são região específico, que portanto você deve definir um plano de cobrança para cada região no qual você implantará o serviço da web.

## <a name="to-create-a-plan-in-another-region"></a>Para criar um plano em outra região

1. Entrar no [Microsoft Azure máquina serviços Web de aprendizagem](https://services.azureml.net/).
2. Clique na opção de menu **planos** .
3. Os planos pela página de visualização, clique em **novo**.
4. No menu suspenso de **assinatura** , selecione a assinatura em que o novo plano residirão.
5. No menu suspenso de **região** , selecione uma região para o novo plano. As opções de plano para a região selecionada será exibida na seção **Opções de plano** da página.
6. No menu suspenso de **Grupo de recursos** , selecione um grupo de recursos para o plano. Mais informações sobre grupos de recursos, consulte [recursos de gerenciar Azure por meio do portal](../azure-portal/resource-group-portal.md).
7. Em **Nome de plano** , digite o nome do plano.
8. Em **Opções de plano**, clique no nível de cobrança para o novo plano.
9. Clique em **criar**.


## <a name="deploying-the-web-service-to-another-region"></a>Implantando o serviço web para outra região

1. Clique na opção de menu de **Serviços da Web** .
2. Selecione o serviço da Web que você está implantando uma nova região.
3. Clique em **Copiar**.
4. Em **Nome do serviço Web**, digite um novo nome para o serviço da web.
5. Em **Descrição do serviço Web**, digite uma descrição para o serviço web.
6. No menu suspenso de **assinatura** , selecione a assinatura em que o novo serviço web residirão.
7. No menu suspenso de **Grupo de recursos** , selecione um grupo de recursos para o serviço web. Mais informações sobre grupos de recursos, consulte [recursos de gerenciar Azure por meio do portal](../azure-portal/resource-group-portal.md).
8. No menu suspenso de **região** , selecione a região na qual implantar o serviço da web.
9. No menu suspenso de **conta de armazenamento** , selecione uma conta de armazenamento na qual deseja armazenar o serviço da web.
10. No menu suspenso de **Plano de preço** , selecione um plano na região que você selecionou na etapa 8.
11. Clique em **Copiar**.

