<properties
   pageTitle="Recurso Azure Explorer | Microsoft Azure"
   description="Descreve o Gerenciador de recursos do Azure e como ele pode ser usado para exibir e atualizar implantações através do Gerenciador de recursos do Azure"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Use o Gerenciador de recursos do Azure para exibir e modificar recursos
O [Gerenciador de recursos do Azure](https://resources.azure.com) é uma ótima ferramenta para olhando para recursos que você já tiver criado em sua assinatura. Usando essa ferramenta, você pode compreender como os recursos são estruturados e ver as propriedades atribuídas a cada recurso. Você pode aprender sobre as operações de API REST e cmdlets do PowerShell que estão disponíveis para um tipo de recurso, e você pode emitir comandos por meio da interface. Gerenciador de recursos pode ser particularmente úteis quando você estiver criando modelos do Gerenciador de recursos porque ele permite exibir as propriedades de recursos existentes.

A fonte para a ferramenta de Resource Explorer está disponível no [github](https://github.com/projectkudu/ARMExplorer), que fornece uma valiosa referência se você precisar implementar um comportamento semelhante em seus próprios aplicativos.

## <a name="view-resources"></a>Exibir recursos
Navegue até [https://resources.azure.com](https://resources.azure.com) e entre com as mesmas credenciais que você usaria para o [Portal do Azure](https://portal.azure.com).

Uma vez carregado, a exibição em árvore no canto esquerdo permite fazer busca detalhada em suas assinaturas e grupos de recursos:

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Conforme você analisar um grupo de recursos, você verá os provedores para o qual há recursos nesse grupo:

![provedores](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

A partir daí, você pode iniciar fazer buscas detalhadas nas instâncias do recurso. Captura de tela abaixo, você verá o `sltest` instância do SQL Server em treeview. No lado direito, você pode ver informações sobre as solicitações de API REST, que você pode usar com esse recurso. Navegando até o nó para um recurso, o recurso Explorer toma automaticamente solicitação GET para recuperar informações sobre o recurso. Na área de texto grande abaixo a URL, você verá a resposta da API. 

Como você se familiarizar com os modelos do Gerenciador de recursos o conteúdo do corpo começa a parecer familiar! A seção **Propriedades** da resposta corresponde os valores que você pode fornecer na seção **Propriedades** do modelo.

![do SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Recurso Explorer permite que você mantenha fazer a busca detalhada para explorar os recursos de filho, no caso do banco de dados do SQL Server, existem recursos filho para coisas como bancos de dados e regras de firewall.

Explorando a um banco de dados mostra as propriedades de banco de dados. Captura de tela abaixo, podemos ver que o banco de dados `edition` é `Standard` e `serviceLevelObjective` (ou nível de banco de dados) é `S1`.

![banco de dados SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Alterar os recursos

Depois de navegar para um recurso, você pode selecionar o botão Editar para tornar o conteúdo JSON editável. Em seguida, você pode usar o Resource Explorer para editar o JSON e enviar uma solicitação de colocar para alterar o recurso. Por exemplo, a imagem abaixo mostra o nível de banco de dados alterado para `S0`:

![banco de dados - solicitação de colocar](./media/resource-manager-resource-explorer/are-05-database-put.png)

Selecionando **colocar** você envia a solicitação. 

Depois que a solicitação foi enviada Resource Explorer emite novamente a solicitação GET para atualizar o status. Nesse caso, podemos ver que o `requestedServiceObjectiveId` foi atualizado e é diferente da `currentServiceObjectiveId` indicando que uma operação de escala está em andamento. Você pode clicar no botão obter para atualizar o status manualmente.

![banco de dados - obter request2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Executar ações em recursos

Na guia **ações** permite ver e executar operações de REST adicionais. Por exemplo, quando você tiver selecionado um recurso de site da web, na guia Ações apresenta uma lista longa de operações disponíveis, alguns dos quais são mostrados abaixo.

![Web - solicitação POST](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>Chamando a API por meio do PowerShell
Guia PowerShell no Resource Explorer mostra os cmdlets usar para interagir com o recurso que você está explorando no momento. Dependendo do tipo de recurso selecionado, o script do PowerShell exibido pode variar de cmdlets simples (como `Get-AzureRmResource` e `Set-AzureRmResource`) para cmdlets mais complicada (como trocando slots em um site). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Para obter mais informações sobre cmdlets do PowerShell do Azure, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](powershell-azure-resource-manager.md)

## <a name="summary"></a>Resumo
Ao trabalhar com o Gerenciador de recursos, o Gerenciador de recursos pode ser uma ferramenta muito útil. É uma ótima maneira de descobrir maneiras de usar o PowerShell para consultar e fazer alterações. Se você estiver trabalhando com a API REST é uma ótima maneira de Introdução e testar rapidamente chamadas API antes de você começa a escrever código. E se você estiver escrevendo modelos pode ser uma ótima maneira de entender a hierarquia de recursos e localizar onde colocar configuração - você pode fazer uma alteração no Portal e em seguida, localize as entradas correspondentes no Gerenciador de recursos!

Para saber mais, assista o [vídeo com Scott Hanselman e David Ebbo do canal 9](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


