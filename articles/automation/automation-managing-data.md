<properties 
   pageTitle="Gerenciando dados de automação do Azure | Microsoft Azure"
   description="Este artigo contém vários tópicos de gerenciamento de um ambiente de automação do Azure.  Atualmente inclui retenção de dados e fazer backup de recuperação de automação Azure na automação do Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren;sngun" />

# <a name="managing-azure-automation-data"></a>Gerenciamento de dados de automação do Azure

Este artigo contém vários tópicos de gerenciamento de um ambiente de automação do Azure.

## <a name="data-retention"></a>Retenção de dados

Quando você exclui um recurso no Azure automação, ele é mantido por 90 dias para fins de auditoria antes de serem removidos permanentemente.  Você não pode ver ou use o recurso durante esse período.  Essa política também se aplica aos recursos que pertencem a uma conta de automação que seja excluída.

Automação Azure exclui automaticamente e remove permanentemente trabalhos mais de 90 dias.

A tabela a seguir resume a política de retenção para diferentes recursos.

|Dados|Política|
|:---|:---|
|Contas|Removido permanentemente 90 dias após a conta será excluída por um usuário.|
|Ativos|Removido permanentemente 90 dias após o ativo é excluído por um usuário ou 90 dias após a conta que detém que o ativo é excluído por um usuário.|
|Módulos|Removido permanentemente 90 dias após o módulo é excluído por um usuário ou 90 dias após a conta que detém que o módulo é excluído por um usuário.|
|Runbooks|Removido permanentemente 90 dias após o recurso é excluído por um usuário ou 90 dias após a conta que detém que o recurso é excluído por um usuário.|
|Trabalhos|Excluídos e permanentemente removidos 90 dias após a última sejam modificados. Isso pode ser após o trabalho for concluído, for interrompido ou está suspenso.|
|Arquivos de configurações/MOF nó| Configuração de nó antiga é removida permanentemente 90 dias após uma nova configuração de nó é gerada.|
|Nós DSC| Removido permanentemente 90 dias após o nó é cancelado de conta de automação usando o portal do Azure ou o cmdlet [AzureRMAutomationDscNode cancela o registro](https://msdn.microsoft.com/library/mt603500.aspx) no Windows PowerShell. Nós são também permanentemente removidos 90 dias após a conta que detém que o nó é excluído por um usuário. |
|Relatórios de nó| Removido permanentemente 90 dias após um novo relatório é gerado para esse nó|

A política de retenção se aplica a todos os usuários e atualmente não pode ser personalizada.

## <a name="backing-up-azure-automation"></a>Fazer backup de automação do Azure

Quando você exclui uma conta de automação no Microsoft Azure, todos os objetos na conta são excluídos incluindo runbooks, módulos, configurações, configurações, trabalhos e ativos. Os objetos não podem ser recuperados depois que a conta será excluída.  Você pode usar as informações a seguir para fazer backup o conteúdo da sua conta de automação antes de excluí-lo. 

### <a name="runbooks"></a>Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal de gerenciamento do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) no Windows PowerShell.  Esses arquivos de script podem ser importados para outra conta de automação, conforme discutido em [Criando ou importando um Runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### <a name="integration-modules"></a>Módulos de integração

Você não pode exportar módulos de integração de automação do Azure.  Você deve certificar-se de que elas estão disponíveis fora da conta de automação.

### <a name="assets"></a>Ativos

Você não pode exportar [ativos](https://msdn.microsoft.com/library/dn939988.aspx) de automação do Azure.  Usando o Portal de gerenciamento do Azure, você deve observar os detalhes de variáveis, credenciais, certificados, conexões e agendas.  Você deve criar manualmente qualquer ativos que são usados pelo runbooks que você importa para outra automação.

Você pode usar [cmdlets do Azure](https://msdn.microsoft.com/library/dn690262.aspx) para recuperar detalhes da ativos não criptografados e salvá-los para referência futura ou criar ativos equivalentes em outra conta de automação.

Você não pode recuperar o valor de variáveis criptografadas ou o campo de senha de credenciais usando cmdlets.  Se você não souber esses valores, você poderá recuperá-las de uma runbook usando as atividades [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) .

Você não pode exportar certificados de automação do Azure.  Você deve garantir que todos os certificados estão disponíveis fora do Azure.

### <a name="dsc-configurations"></a>Configurações de DSC

Você pode exportar suas configurações para arquivos de script usando o Portal de gerenciamento do Azure ou o cmdlet de [Exportação AzureRmAutomationDscConfiguration](https://msdn.microsoft.com/library/mt603485.aspx) no Windows PowerShell. Essas configurações podem ser importadas e usadas em outra conta de automação.


##<a name="geo-replication-in-azure-automation"></a>Replicação geográfica na automação Azure

Replicação de localização geográfica, o padrão em contas de automação do Azure, faça backup dos dados da conta uma região geográfica diferentes para redundância. Você pode escolher uma região primária ao configurar sua conta e, em seguida, uma região secundária é atribuída a ela automaticamente. Os dados secundários, copiados da região primária, são atualizados continuamente em caso de perda de dados.  

A tabela a seguir mostra os pares de região disponíveis primário e secundário.

|Principal            |Secundário
| ---------------   |----------------
|Centro Sul dos EUA   |Centro Norte dos EUA
|Leste dos EUA 2          |Centro dos EUA
|Europa Ocidental        |Norte da Europa
|Sudeste da Ásia    |Da Ásia Oriental
|Japão Leste         |Japão Oeste

No evento provável que um primário região de dados é perdida, o Microsoft tenta recuperá-lo. Se os dados primários não podem ser recuperados, então geográfica failover é executada e os clientes afetados serão notificados sobre isso através de sua assinatura.

