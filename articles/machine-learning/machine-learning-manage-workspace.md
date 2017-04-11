<properties
    pageTitle="Gerenciar um espaço de trabalho de aprendizado de máquina | Microsoft Azure"
    description="Gerenciar o acesso aos espaços de trabalho de aprendizado de máquina do Azure e implantar e gerenciar serviços de web API ML"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Gerenciar um espaço de trabalho de aprendizado de máquina do Azure

>[AZURE.NOTE] Os procedimentos neste artigo são relevantes para os serviços de aprendizado de máquina Azure Web clássico. Para obter informações sobre como gerenciar serviços da Web no portal de serviços de Web de aprendizado de máquina, consulte [Gerenciar um serviço Web usando o portal de serviços de Web de aprendizado de máquina Azure](machine-learning-manage-new-webservice.md).

Usando o portal de clássico Azure, você pode gerenciar seus espaços de trabalho de aprendizado de máquina para:

- Monitorar como o espaço de trabalho está sendo usado
- Configurar o espaço de trabalho para permitir ou negar acesso
- Gerenciar serviços da Web criados no espaço de trabalho
- Excluir o espaço de trabalho

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Além disso, a guia Painel oferece uma visão geral do uso do espaço de trabalho e a compreensão das suas informações de espaço de trabalho.  

> [AZURE.TIP] No Studio Azure de aprendizado de máquina, na guia **Serviços WEB** , você pode adicionar, atualizar ou excluir um serviço Web de aprendizado de máquina.

Para gerenciar um espaço de trabalho:

1.  Entre [portal de clássico Azure](https://manage.windowsazure.com/) usando sua conta do Microsoft Azure - usar a conta que está associada a assinatura do Azure.
2.  No painel de serviços Microsoft Azure, clique em **APRENDIZADO de máquina**.
3.  Clique no espaço de trabalho que você deseja gerenciar.

A página de espaço de trabalho tem três guias:

- **Painel** - permite que você para ver o uso de espaço de trabalho e informações
- **Configurar** – permite que você gerencie o acesso ao espaço de trabalho
- **Serviços WEB** - permite que você gerencie serviços da Web que foram publicados deste espaço de trabalho

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Monitorar como o espaço de trabalho está sendo usado

Clique na guia do **painel** .

No painel, você pode exibir uso geral do espaço de trabalho e obter a compreensão das informações de espaço de trabalho.

- O gráfico **CALCULAR** mostra os recursos de computação sendo usados pelo espaço de trabalho. Você pode alterar o modo de exibição desejado relativas ou valores absolutos e você pode alterar o período de tempo exibido no gráfico.
- **Visão geral de uso** exibe o armazenamento do Azure sendo usado pelo espaço de trabalho.
- **Compreensão** fornece um resumo das informações de espaço de trabalho e links úteis.

> [AZURE.NOTE] O link de **entrada no ML Studio** abre Studio de aprendizado de máquina usando a Account da Microsoft no momento, você está conectado ao. A Microsoft Account usada para entrar portal do Azure clássico para criar um espaço de trabalho automaticamente não tem permissão para abrir o espaço de trabalho. Para abrir um espaço de trabalho, você deve estar conectado Account da Microsoft que foi definido como o proprietário do espaço de trabalho, ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho.


## <a name="to-grant-or-suspend-access-for-users"></a>Para conceder ou suspender o acesso de usuários ##

Clique na guia **Configurar** .

Na guia Configuração, você pode:

- Suspenda o acesso ao espaço de trabalho de aprendizado de máquina clicando em Negar. Os usuários não poderão abrir o espaço de trabalho no Studio de aprendizado de máquina. Para restaurar o acesso, clique em permitir.

Para gerenciar contas adicionais que têm acesso ao espaço de trabalho no Studio de aprendizado de máquina, clique em **entrar no Studio ML** na guia **DASHBOARD** (consulte a observação anterior com relação ao **entrar no ML Studio**). Isso abre o espaço de trabalho no Studio de aprendizado de máquina. A partir daqui, clique na guia **configurações** e **usuários**. Você pode clicar em **CONVIDAR usuários mais** para dar aos usuários acesso ao espaço de trabalho, ou selecione um usuário e clique em **Remover**.


## <a name="to-manage-web-services-in-this-workspace"></a>Gerenciar serviços web neste espaço de trabalho

Clique na guia **Serviços da WEB** .

Isso exibe uma lista de serviços da web publicado deste espaço de trabalho.
Para gerenciar um serviço da web, clique no nome na lista para abrir a página de serviço Web.

Um serviço da Web pode ter um ou mais pontos de extremidade definidos.

- Você pode definir pontos de extremidade mais além o ponto de extremidade de "Padrão". Para adicionar o ponto de extremidade, clique em **Gerenciar pontos de extremidade** na parte inferior do painel de controle para abrir o portal de serviços de Web de aprendizado de máquina Azure.

- Para excluir um ponto de extremidade (você não pode excluir o ponto de extremidade de "Padrão"), clique em caixa de seleção no início da linha de ponto de extremidade e clique em **Excluir**. Isso remove o ponto de extremidade do serviço da Web.

    > [AZURE.NOTE] Se um aplicativo está usando o ponto de extremidade do serviço web quando o ponto de extremidade é excluído, o aplicativo receberá um erro na próxima vez em que ele tenta acessar o serviço.

Clique no nome de um ponto de extremidade do serviço Web para abri-lo. 

No painel, você pode exibir o uso geral do serviço Web durante um período de tempo. Você pode selecionar o período para exibir no menu suspenso período no canto superior direito dos gráficos uso. O painel mostra as seguintes informações:

- **Solicitações ao longo do tempo** exibe um gráfico de etapa do número de solicitações ao longo do período de tempo selecionado. Ele pode ajudar a identificar se você estiver enfrentando picos de uso.
- **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu sobre o período de tempo selecionado e falha quantos deles.
- **O tempo de computação de solicitação-resposta médio** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Solicitações em lote** exibe o número total de solicitações de lote o serviço recebeu sobre o período de tempo selecionado e falha quantos deles.
- **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Erros** exibe o número de agregação de erros ocorridos em chamadas para o serviço da web.
- **Os custos de serviços** exibe os encargos para o plano de cobrança associado ao serviço.

Na página Configurar, você pode atualizar as seguintes propriedades:

* **Descrição** permite que você insira uma descrição para o serviço da Web. Descrição é um campo obrigatório.
* **Registro em log** permite habilitar ou desabilitar o log no ponto de extremidade de erros. Para obter mais informações sobre logs, consulte Habilitar [registro em log para serviços da web de aprendizado de máquina](machine-learning-web-services-logging.md).
* **Dados de exemplo habilitar** permite que você fornecer dados de exemplo que você pode usar para testar o serviço de solicitação-resposta. Se você criou o serviço da web no Studio de aprendizado de máquina, os dados de exemplo são interpretados dos dados de seu usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados são obtidos os dados de exemplo fornecidos como parte do pacote JSON.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
