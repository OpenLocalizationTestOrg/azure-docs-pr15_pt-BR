<properties
    pageTitle="Gerenciar um serviço Web usando o portal de Serivces de Web de aprendizado de máquina Azure | Microsoft Azure"
    description="Gerenciar o acesso aos espaços de trabalho de aprendizado de máquina do Azure e implantar e gerenciar serviços de web API ML"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gerenciar um serviço Web usando o portal de serviços de Web de aprendizado de máquina do Azure

Você pode gerenciar seu novo de aprendizado de máquina e serviços Web clássico usando o portal de serviços da Web de aprendizado de máquina do Microsoft Azure. Como serviços Web clássico e serviços Web novo são baseados em diferentes tecnologias subjacentes, você tem recursos de gerenciamento ligeiramente diferentes para cada um deles.

No portal de serviços de Web de aprendizado de máquina, você pode:

- Monitore como o serviço da web está sendo usado.
- Configurar a descrição, atualizar as chaves para a web do serviço (somente para o novo), atualize seu armazenamento conta chave (novo somente), Habilitar log e habilitar ou desabilitar dados de exemplo.
- Exclua o serviço web.
- Criar, excluir ou atualizar cobrança planos (somente para o novo).
- Adicionar e excluir pontos de extremidade (somente clássico)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Gerenciar serviços da Web novo

Para gerenciar seus serviços de nova Web:

1.  Entre portal de [Serviços da Web de aprendizado de máquina do Microsoft Azure](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure - usar a conta que está associada a assinatura do Azure.
2.  No menu, clique em **Serviços da Web**.

Isso exibe uma lista de serviços Web implantados para sua assinatura. 

Para gerenciar um serviço da Web, clique em serviços da Web. Na página de serviços Web, você pode:

- Clique no serviço da web para gerenciá-lo.
- Clique na cobrança planejar para o serviço web para atualizá-lo.
- Exclua um serviço web.
- Copiar um serviço da web e implantá-lo para outra região.

Quando você clica em um serviço da web, abre a página de início rápido do serviço web. A página de início rápido do serviço web tem duas opções de menu que permitem que você gerencie seu serviço da web:

- **Painel** - permite que você exiba o uso do serviço Web.
- **Configurar** – permite que você adicione texto descritivo, atualize a chave da conta de armazenamento associado ao serviço da Web e habilitar ou desabilitar a dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitoramento como o serviço da web está sendo usado ###

Clique na guia do **painel** .

No painel, você pode exibir o uso geral do serviço Web durante um período de tempo. Você pode selecionar o período para exibir no menu suspenso período no canto superior direito dos gráficos uso. O painel mostra as seguintes informações:

- **Solicitações ao longo do tempo** exibe um gráfico de etapa do número de solicitações ao longo do período de tempo selecionado. Ele pode ajudar a identificar se você estiver enfrentando picos de uso.
- **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu sobre o período de tempo selecionado e falha quantos deles.
- **O tempo de computação de solicitação-resposta médio** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Solicitações em lote** exibe o número total de solicitações de lote o serviço recebeu sobre o período de tempo selecionado e falha quantos deles.
- **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Erros** exibe o número de agregação de erros ocorridos em chamadas para o serviço da web.
- **Os custos de serviços** exibe os encargos para o plano de cobrança associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurando o serviço web ###

Clique na opção de menu **Configurar** .

Você pode atualizar as seguintes propriedades:

* **Descrição** permite que você insira uma descrição para o serviço da Web.
* **Título** permite que você insira um título para o serviço da Web
* **Chaves** permite girar suas chaves de API principais e secundários.
* **Chave da conta de armazenamento** permite que você atualize a chave para a conta de armazenamento associada às alterações de serviço Web. 
* **Dados de exemplo habilitar** permite que você fornecer dados de exemplo que você pode usar para testar o serviço de solicitação-resposta. Se você criou o serviço da web no Studio de aprendizado de máquina, os dados de exemplo são interpretados dos dados de seu usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados são obtidos os dados de exemplo fornecidos como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gerenciando planos de cobranças ###

Clique na opção de menu **planos** da página de início rápido de serviços da web. Você também pode clicar o plano associado ao serviço da Web específico para gerenciar o plano.

* **Novo** permite que você crie um novo plano.
* **Adicionar ou remover plano instância** permite que você "dimensionar" um plano existente para adicionar capacidade.
* **Atualização/DownGrade** permite que você "dimensionar" um plano existente para adicionar capacidade.
* **Excluir** permite que você exclua um plano.

Clique em um plano para visualizar seu painel. O painel fornece uso instantâneo ou plano durante um determinado período de tempo. Para selecionar o período de tempo para exibir, clique na lista suspensa de **período** na parte superior direita do painel. 

O painel de plano fornece as seguintes informações:

* **Descrição do plano** exibe informações sobre os custos e a capacidade associados ao plano.
* **Plano de uso** exibe o número de transações e as horas de computação que foram cobradas contra o plano.
* **Serviços Web** exibe o número de serviços da Web que estão usando este plano.
* **Início por chamadas de serviço Web** exibe os serviços da Web de início quatro que estão fazendo chamadas que são carregadas contra o plano.
* **Serviços Web do início por calcular h** exibe serviços da Web início quatro que estão usando os recursos de computação que são carregados contra o plano.

## <a name="manage-classic-web-services"></a>Gerenciar serviços da Web clássico

> [AZURE.NOTE] Os procedimentos desta seção são relevantes para gerenciar serviços web do clássico através do portal de serviços de Web de aprendizado de máquina Azure. Para obter informações sobre como gerenciar serviços de Web clássico através do Studio de aprendizado de máquina e o portal de clássico Azure, consulte [Gerenciar um espaço de trabalho de aprendizado de máquina do Azure](machine-learning-manage-workspace.md).

Para gerenciar seus serviços Web clássico:

1.  Entre portal de [Serviços da Web de aprendizado de máquina do Microsoft Azure](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure - usar a conta que está associada a assinatura do Azure.
2.  No menu, clique em **Clássico serviços Web**.

Para gerenciar um serviço Web clássico, clique em **Clássico serviços Web**. Na página de serviços de Web clássico, você pode:

- Clique no serviço da web para exibir os pontos de extremidade associados.
- Exclua um serviço web.

Quando você gerencia um serviço Web clássico, você gerenciar cada um dos pontos de extremidade separadamente. Quando você clica em um serviço da web na página serviços Web, a lista de pontos de extremidade associado ao serviço é aberta. 

Na página de ponto de extremidade do serviço da Web clássico, você pode adicionar e excluir pontos de extremidade do serviço. Para obter mais informações sobre a adição de pontos de extremidade, consulte [Criando pontos de extremidade](machine-learning-create-endpoint.md).

Clique em um dos pontos de extremidade para abrir a página de início rápido do serviço web. Na página de início rápido, há duas opções de menu que permitem que você gerencie seu serviço da web:

- **Painel** - permite que você exiba o uso do serviço Web.
- **Configurar** – permite que você adicione texto descritivo, ativar o log de erro e desativar, atualizar a chave para o armazenamento de conta associado ao serviço da Web e ativar e desativar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitoramento como o serviço da web está sendo usado ###

Clique na guia do **painel** .

No painel, você pode exibir o uso geral do serviço Web durante um período de tempo. Você pode selecionar o período para exibir no menu suspenso período no canto superior direito dos gráficos uso. O painel mostra as seguintes informações:

- **Solicitações ao longo do tempo** exibe um gráfico de etapa do número de solicitações ao longo do período de tempo selecionado. Ele pode ajudar a identificar se você estiver enfrentando picos de uso.
- **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu sobre o período de tempo selecionado e falha quantos deles.
- **O tempo de computação de solicitação-resposta médio** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Solicitações em lote** exibe o número total de solicitações de lote o serviço recebeu sobre o período de tempo selecionado e falha quantos deles.
- **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
- **Erros** exibe o número de agregação de erros ocorridos em chamadas para o serviço da web.
- **Os custos de serviços** exibe os encargos para o plano de cobrança associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurando o serviço web ###

Clique na opção de menu **Configurar** .

Você pode atualizar as seguintes propriedades:

* **Descrição** permite que você insira uma descrição para o serviço da Web. Descrição é um campo obrigatório.
* **Registro em log** permite habilitar ou desabilitar o log no ponto de extremidade de erros. Para obter mais informações sobre logs, consulte Habilitar [registro em log para serviços da web de aprendizado de máquina](machine-learning-web-services-logging.md).
* **Dados de exemplo habilitar** permite que você fornecer dados de exemplo que você pode usar para testar o serviço de solicitação-resposta. Se você criou o serviço da web no Studio de aprendizado de máquina, os dados de exemplo são interpretados dos dados de seu usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados são obtidos os dados de exemplo fornecidos como parte do pacote JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Conceder ou suspender o acesso aos serviços da Web para usuários no portal

Usando o portal de clássico Azure, você pode permitir ou negar o acesso a usuários específicos.

### <a name="access-for-users-of-new-web-services"></a>Acesso de usuários dos serviços de nova Web

Para permitir que outros usuários possam trabalhar com seus serviços da Web no portal de serviços de Web de aprendizado de máquina Azure, você deve adicioná-los como co-administradores na sua assinatura do Azure.

Entre [portal de clássico Azure](https://manage.windowsazure.com/) usando sua conta do Microsoft Azure - usar a conta que está associada a assinatura do Azure.

1. No painel de navegação, clique em **configurações**, clique em **administradores**.
2. Na parte inferior da janela, clique em **Adicionar**. 
3. Na caixa de diálogo ADD A CO-ADMINISTRATOR, digite o endereço de email da pessoa que você deseja adicionar como colega administrador e, em seguida, selecione a assinatura que deseja que o administrador co para acessar.
4. Clique em **Salvar**.

### <a name="access-for-users-of-classic-web-services"></a>Acesso de usuários de serviços Web clássico

Para gerenciar um espaço de trabalho:

Entre [portal de clássico Azure](https://manage.windowsazure.com/) usando sua conta do Microsoft Azure - usar a conta que está associada a assinatura do Azure.

1. No painel de serviços Microsoft Azure, clique em **APRENDIZADO de máquina**.
1. Clique no espaço de trabalho que você deseja gerenciar.
1. Clique na guia **Configurar** .

Na guia Configuração, você pode suspender acessem o espaço de trabalho de aprendizado de máquina clicando em **Negar**. Os usuários não poderão abrir o espaço de trabalho no Studio de aprendizado de máquina. Para restaurar o acesso, clique em **Permitir**.

Para usuários específicos:

Para gerenciar contas adicionais que têm acesso ao espaço de trabalho no Studio de aprendizado de máquina, clique em **entrar no Studio ML** na guia do **painel** . Isso abre o espaço de trabalho no Studio de aprendizado de máquina. A partir daqui, clique na guia **configurações** e **usuários**. Você pode clicar em **CONVIDAR usuários mais** para dar aos usuários acesso ao espaço de trabalho, ou selecione um usuário e clique em **Remover**.

> [AZURE.NOTE] O link de **entrada no ML Studio** abre Studio de aprendizado de máquina usando a Account da Microsoft no momento, você está conectado ao. A Microsoft Account usada para entrar portal do Azure clássico para criar um espaço de trabalho automaticamente não tem permissão para abrir o espaço de trabalho. Para abrir um espaço de trabalho, você deve estar conectado Account da Microsoft que foi definido como o proprietário do espaço de trabalho, ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho.
