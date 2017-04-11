<properties 
    pageTitle="Testando um runbook no Azure automação | Microsoft Azure"
    description="Antes de publicar uma runbook no Azure automação, você pode testá-lo para garantir que funciona como esperado.  Este artigo descreve como testar uma runbook e exibir sua saída."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="testing-a-runbook-in-azure-automation"></a>Testar uma runbook em automação do Azure
Quando você testa um runbook, a [versão de rascunho](automation-creating-importing-runbook.md#publishing-a-runbook) é executado e as ações que ele executa são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos de [saída](automation-runbook-output-and-messages.md#output-stream) e [aviso e erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no teste painel de saída. Mensagens com o [fluxo detalhado](automation-runbook-output-and-messages.md#message-streams) são exibidas no painel de saída somente se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) é definida para continuar.

Embora a versão de rascunho está sendo executada, o runbook ainda o fluxo de trabalho é executado normalmente e executa qualquer ações contra recursos no ambiente. Por esse motivo, você deve testar apenas runbooks em recursos de não produção.

O procedimento para cada [tipo de runbook](automation-runbook-types.md) teste é a mesma e há nenhuma diferença em testes entre o editor textual e o editor de gráfico no portal do Azure.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Para testar uma runbook no portal do Azure

Você pode trabalhar com qualquer [tipo de runbook](automation-runbook-types.md) no portal do Azure.

1. Abra a versão de rascunho do runbook no [editor de texto](automation-editing-a-runbook.md#Portal) ou [editor gráfico](automation-graphical-authoring-intro.md).
2. Clique no botão de **teste** para abrir a lâmina de teste.
3. Se runbook tiver parâmetros, eles serão listados no painel esquerdo, onde você pode fornecer valores a serem usados para o teste.
4. Se você quiser executar o teste em um [Operador de Runbook híbrido](automation-hybrid-runbook-worker.md), alterar **Configurações de executar** a **Híbrido de trabalho** e selecione o nome do grupo de destino.  Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
5. Clique no botão **Iniciar** para iniciar o teste.
6. Se runbook [Fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou [Graphical](automation-runbook-types.md#graphical-runbooks), você pode parar ou suspendê-lo enquanto ele está sendo testado com os botões abaixo do painel de saída. Quando você suspende runbook, ela seja concluída a atividade atual antes sendo suspenso. Depois de runbook for suspensa, você pode interrompê-lo ou reiniciá-lo.
7. Inspecione a saída do runbook no painel de saída.


## <a name="next-steps"></a>Próximas etapas

- Para saber como criar ou importar um runbook, consulte [Criando ou importando um runbook na automação do Azure](automation-creating-importing-runbook.md)
- Para saber mais sobre a criação de gráficos, consulte [Graphical authoring na automação do Azure](automation-graphical-authoring-intro.md)
- Para começar a usar runbooks de fluxo de trabalho do PowerShell, consulte [Minha primeira runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre como configurar runboks para retornar mensagens de status e erros, incluindo as práticas recomendadas, consulte [Runbook saída e mensagens em automação do Azure](automation-runbook-output-and-messages.md)