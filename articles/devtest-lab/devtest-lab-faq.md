<properties
    pageTitle="DevTest Azure Labs perguntas Frequentes | Microsoft Azure"
    description="Encontre respostas para perguntas comuns do Azure DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>DevTest Azure Labs perguntas Frequentes

Este artigo responde algumas das perguntas mais comuns sobre Azure DevTest Labs.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Geral
- [E se minha dúvida não seja resolvida aqui?](#what-if-my-question-isnt-answered-here)
- [Por que devo usar o Azure DevTest Labs?](#why-should-i-use-azure-devtest-labs) 
- [O que significa "preocupações, autoatendimento"?](#what-does-quotworry-free-self-servicequot-mean)
- [Como posso usar o Azure DevTest Labs?](#how-can-i-use-azure-devtest-labs) 
- [Como estou cobrado para Azure DevTest laboratórios?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Segurança 
- [Quais são os níveis de segurança diferentes nos exercícios de DevTest do Azure?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Como crio uma função para permitir que os usuários executem uma tarefa específica?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>Automação e integração de CI/CD 
 
- [Azure DevTest Labs integrar com o meu toolchain CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Máquinas virtuais 
 
- [Por que não consigo ver determinadas VMs na lâmina máquinas virtuais do Azure que ver dentro do Azure DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [Qual é a diferença entre imagens personalizadas e as fórmulas?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Como para criar várias VMs do mesmo modelo ao mesmo tempo?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Como faço para mover minhas VMs Azure existente em meu laboratório Azure DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [Pode anexar vários discos em meu VMs?](#can-i-attach-multiple-disks-to-my-vms) 
- [Como automatizar o processo de carregar arquivos VHD para criar imagens personalizadas?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Como é possível automatizar o processo de exclusão todas as VMs em meu laboratório?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Artefatos 
 
- [O que são artefatos?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Configuração de laboratório 
 
- [Como criar um laboratório de um modelo do Gerenciador de recursos do Azure?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Por que meu VMs são criadas em grupos de recursos diferentes com nomes aleatório? É possível renomear ou modificar a esses grupos de recursos?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Quantos laboratórios pode criar em mesma assinatura?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Quantos VMs pode criar por laboratório?](#how-many-vms-can-i-create-per-lab)
- [Como faço para compartilhar um link direto para meu laboratório?](#how-do-i-share-a-direct-link-to-my-lab)
- [O que é uma conta da Microsoft?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Solução de problemas 
 
- [Meu artefato falhou durante a criação de máquina virtual. Como soluciono-lo?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Por que não é meu virtual rede salvando corretamente?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>E se minha dúvida não seja resolvida aqui?
Se sua dúvida não estiver listada aqui, fale conosco e ajudaremos você a encontrar uma resposta.

- Poste uma pergunta no [segmento Disqus](#comments) no final dessas perguntas Frequentes e entre em contato com a equipe de Cache do Azure e outros membros da comunidade sobre este artigo.
- Para alcançar um público maior, poste uma pergunta no [Fórum do Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)e entre em contato com a equipe do Azure DevTest Labs e outros membros da comunidade.
- Para fazer uma solicitação de recurso, envie suas solicitações e ideias o [Azure DevTest Labs usuário Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Por que devo usar o Azure DevTest Labs? 
Azure Labs DevTest pode salvar seu tempo de equipe e dinheiro. Os desenvolvedores podem criar seus próprios ambientes usando vários diferentes bases e usar artefatos para implantar e configurar aplicativos rapidamente. Usar fórmulas e imagens personalizadas, máquinas virtuais podem ser salvas como modelos e facilmente reproduzidas. Além disso, laboratórios oferecem várias políticas configuráveis que permitem aos administradores de laboratório reduzir o desperdício e gerenciar ambientes da equipe. Essas políticas incluem autodesligamento, limite de custo, VMs máximos por usuário e tamanhos máximos de máquina virtual. Para obter uma explicação mais detalhada dos DevTest Azure, leia a [Visão geral](devtest-lab-overview.md) ou assista ao [vídeo introdutório](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>O que significa "preocupações, autoatendimento"?
"Autoatendimento preocupações," significa que os desenvolvedores e testadores criam seus próprios ambientes conforme necessário, e os administradores têm a segurança de saber que Azure DevTest Labs ajuda a minimizar percam e controlar o custo. Os administradores podem especificar quais tamanhos de máquina virtual são permitidos, o número máximo de VMs, e quando VMs são iniciadas e desligadas. Azure Labs DevTest também torna mais fácil monitorar custos e definir alertas para ficar atento como recursos no laboratório estão sendo usados. 

### <a name="how-can-i-use-azure-devtest-labs"></a>Como posso usar o Azure DevTest Labs? 
Azure Labs DevTest é útil sempre que você exige desenvolvimento ou ambientes de teste e deseja reproduzi-los rapidamente e/ou gerenciá-los com Salvar políticas de custo. 

Aqui estão alguns cenários que nossos clientes usam Azure DevTest Labs por: 

- Gerenciando ambientes de desenvolvimento e teste em um só lugar, utilizar políticas para reduzir custos e imagens personalizadas para compartilhar cria entre a equipe.
- Desenvolver um aplicativo usando imagens personalizadas para salvar o estado de disco durante as fases de desenvolvimento.
- Controlando o custo em relação de andamento. 
- Criando ambientes de teste em massa para testes de garantia de qualidade.
- Usando fórmulas e artefatos configurar facilmente e reproduzir um aplicativo em vários ambientes. 
- Distribuir as VMs para hackathons (trabalho colaborativo de desenvolvimento ou teste) e, em seguida, facilmente eliminação de provisionamento-los quando o evento termina. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Como estou cobrado para Azure DevTest laboratórios? 
Azure Labs de DevTest é um serviço gratuito, significando que criando laboratórios e configurando as políticas, modelos e artefatos são gratuito. Você paga apenas para os recursos Azure usados no seu labs, como máquinas virtuais, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo dos recursos de laboratório, leia sobre [preços do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Quais são os níveis de segurança diferentes nos exercícios de DevTest do Azure?  
Acesso de segurança é determinado pelo [Controle de acesso de Azure Role-Based (RBAC)](../active-directory/role-based-access-built-in-roles.md). Para compreender como funciona o acesso, ele ajuda a compreender as diferenças entre um escopo, conforme definido pelo RBAC, uma função e uma permissão.

- **Permissão** - uma permissão é um acesso definido para uma ação específica. Por exemplo, uma permissão poderia ser máquinas de acesso de leitura a todos virtuais. 
- **Função** - uma função é um conjunto de permissões que podem ser agrupados e atribuída a um usuário. Por exemplo, um "proprietário da assinatura" tem acesso a todos os recursos dentro de uma assinatura. 
- **Escopo** - um escopo é um nível dentro da hierarquia do recurso Azure. Por exemplo, um escopo pode ser um grupo de recursos ou um único laboratório ou a assinatura inteira. 
 
Dentro do escopo do Azure DevTest Labs, há dois tipos de funções para definir permissões de usuário: laboratório proprietário e usuário de laboratório.

- **Proprietário de laboratório** - um proprietário de laboratório tem o acesso para os recursos dentro do laboratório. Portanto, eles podem modificar políticas, leia escrever qualquer VMs, altere a rede virtual e assim por diante. 
- **Usuário de laboratório** - um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas eles não podem modificar políticas ou qualquer VMs criadas por outros usuários. Também é possível criar funções personalizadas nos exercícios de DevTest do Azure e você pode saber como fazer no artigo, [conceder permissões de usuário às políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Como escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, eles são concedidos automaticamente essas permissões em cada escopo de nível inferior contido. Por exemplo, se um usuário é atribuído à função de proprietário da assinatura, em seguida, eles têm acesso a todos os recursos em uma assinatura. Estes recursos incluem todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. Um proprietário de laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Portanto, um proprietário de laboratório não é capaz de ver máquinas virtuais ou redes virtuais ou quaisquer recursos que estão fora do laboratório. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Como crio uma função para permitir que os usuários executem uma tarefa específica?
Um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a essa função pode ser encontrado aqui. Aqui está um exemplo de um script que cria a função "DevTest Labs avançadas" usuário, que tem permissão para iniciar e parar todas VMs no laboratório:
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest Labs integrar com o meu toolchain CI/CD? 
Se você estiver usando o VSTS, há uma [extensão de Azure DevTest Labs tarefas](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite que você automatize o pipeline de lançamento nos exercícios de DevTest do Azure. Alguns dos usos dessa extensão incluem:

- Criar uma máquina virtual automaticamente e implantar configurá-lo com a versão mais recente usando cópia de arquivo do Azure ou PowerShell VSTS tarefas. 
- Capturar o estado de uma máquina virtual automaticamente após o teste para reproduzir um bug na mesma VM para investigação posterior. 
- Excluindo a máquina virtual no final do pipeline de lançamento quando ele não for mais necessário. 

As seguintes postagens fornecem orientações e informações sobre como usar a extensão VSTS:
 
- [Azure DevTest Labs – extensão VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Implantar uma nova VM em uma AzureDevTestLab existente do VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [Usando o gerenciamento de lançamento do VSTS de implantações contínuas para AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Para outros toolchains CI/CD, todos os cenários mencionados anteriormente que podem ser obtidos através da extensão de tarefas VSTS podem ser obtidos da mesma forma por meio de implantação de [modelos do Gerenciador de recursos do Azure](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) usando [cmdlets do PowerShell do Azure](../resource-group-template-deploy.md) e [.NET SDKs](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Você também pode usar [APIs REST para DevTest laboratórios](http://aka.ms/dtlrestapis) a integração com seu toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Por que não consigo ver determinadas VMs na lâmina máquinas virtuais do Azure que ver dentro do Azure DevTest Labs?
Quando uma máquina virtual é criada nos exercícios de DevTest do Azure, permissão é dada para acessar essa máquina virtual. Você é capaz de exibi-lo no lâmina laboratórios e a lâmina de **máquinas virtuais** . Usuários na função DevTest Labs podem ver todas as máquinas virtuais criadas no laboratório através de blade de **todas as máquinas virtuais** do laboratório. No entanto, usuários na função DevTest Labs não recebem acesso de leitura aos recursos de máquina virtual que outras pessoas criados automaticamente. Portanto, essas VMs não são exibidas na lâmina **máquinas virtuais** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Qual é a diferença entre imagens personalizadas e as fórmulas? 
Uma imagem personalizada é um VHD (disco rígido virtual), enquanto uma fórmula é uma imagem que você pode configurar com configurações adicionais que você pode salvar e reproduzir. Uma imagem personalizada pode ser preferível se você quiser criar rapidamente vários ambientes com a mesma imagem básica, imutável. Uma fórmula pode ser melhor se você deseja reproduzir a configuração da sua máquina virtual com os bits mais recentes, uma rede/sub-rede virtual ou um tamanho específico. Para obter uma explicação mais detalhada, consulte o artigo, [imagens personalizadas comparando e fórmulas nos exercícios de DevTest](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como para criar várias VMs do mesmo modelo ao mesmo tempo? 
Você pode usar a [extensão de tarefas do VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) ou [gerar um modelo do Gerenciador de recursos do Azure](devtest-lab-add-vm-with-artifacts.md#save-arm-template) durante a criação de uma máquina virtual e [implantar o modelo do Gerenciador de recursos do Azure do Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Como faço para mover minhas VMs Azure existente em meu laboratório Azure DevTest Labs? 
Nós são projetar uma solução para mover diretamente VMs ao Azure DevTest Labs, mas no momento você pode copiar suas VMs existentes ao Azure DevTest Labs da seguinte maneira: 

1. Copie o arquivo VHD da sua máquina de virtual existente usando este [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Criar a imagem personalizada](devtest-lab-create-template.md) dentro de laboratório Azure DevTest Labs. 
1. Criar uma máquina virtual no laboratório de sua imagem personalizada 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Pode anexar vários discos em meu VMs? 
Anexar vários discos em VMs é suportada.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como automatizar o processo de carregar arquivos VHD para criar imagens personalizadas? 
Há duas opções:

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) podem ser usados para copiar ou carregar arquivos VHD para a conta de armazenamento associada ao laboratório.
- [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que é executado em Windows, OSX e Linux.   
 
Para localizar a conta de armazenamento de destino associada ao seu laboratório, siga estas etapas:

1. Entrar no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Selecione **Grupos de recursos** no painel esquerdo. 
1. Localize e selecione o grupo de recursos associado ao seu laboratório. 
1. Na lâmina **Visão geral** , selecione uma das contas de armazenamento. 
1. Selecione **Blobs**.
1. Procure carregamentos na lista. Se não houver nenhum, retorne para a etapa 4 e tente outra conta de armazenamento.
1. Use a **URL** como seu destino no comando AzCopy.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como é possível automatizar o processo de exclusão todas as VMs em meu laboratório?

Além de excluindo VMs do seu laboratório no portal do Azure, você pode excluir todas as VMs em seu laboratório usando um script do PowerShell. No exemplo a seguir, basta modificar os valores de parâmetro sob o comentário de **valores para alterar** . Você pode recuperar o `subscriptionId`, `labResourceGroup`, e `labName` valores da lâmina laboratório no portal do Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>O que são artefatos? 
Artefatos são elementos personalizáveis que podem ser usados para implantar os bits mais recentes ou as ferramentas de desenvolvimento em uma máquina virtual. Eles são anexados a sua máquina virtual durante a criação com apenas alguns cliques simples, e depois a máquina virtual está provisionada, os artefatos implantar e configurar sua máquina virtual. Há vários artefatos preexistentes no nosso [repositório de Github público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), mas você pode também facilmente [criar seus próprio artefatos](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Como criar um laboratório de um modelo do Gerenciador de recursos do Azure? 
Fornecemos um [repositório de Github dos modelos do Gerenciador de recursos do Azure de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) que você pode implantar como-é ou modificar para criar modelos personalizados para seus laboratórios. Cada um desses modelos tem um link que pode ser clicado para implantar o laboratório como-está em sua própria assinatura do Azure, ou você pode personalizar o modelo e [implantar usando o PowerShell ou CLI do Azure](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Por que meu VMs são criadas em grupos de recursos diferentes com nomes aleatório? É possível renomear ou modificar a esses grupos de recursos? 
Grupos de recursos são criados dessa maneira em ordem para Azure DevTest laboratórios gerenciar as permissões de usuário e o acesso às máquinas virtuais. Enquanto você pode mover a máquina virtual para outro grupo de recurso com seu nome desejado, fazendo assim não é recomendado. Estamos trabalhando na melhoria esta experiência para permitir mais flexibilidade.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos laboratórios pode criar em mesma assinatura? 
Não há nenhum limite específico no número de laboratórios que podem ser criados por assinatura. No entanto, os recursos usados são limitados por assinatura. Você pode ler sobre os [limites e cotas impostas assinaturas do Azure](../azure-subscription-service-limits.md) e [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>Quantos VMs pode criar por laboratório? 
Não há nenhum limite específico no número de VMs que podem ser criados por laboratório. No entanto, atualmente laboratório suporta somente cerca de 40 VMs executando ao mesmo tempo no armazenamento padrão e 25 VMs executando simultaneamente em armazenamento premium. Atualmente estamos trabalhando no aumento esses limites. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como faço para compartilhar um link direto para meu laboratório?

Para compartilhar um link direto para seus usuários de laboratório, você pode executar o procedimento a seguir.

1. Navegue até o laboratório no portal do Azure.
2. Copie a URL de laboratório em seu navegador e compartilhá-la com seus usuários de laboratório. 

>[AZURE.NOTE] Se seus usuários de laboratório são usuários externos com uma [conta MSA](#what-is-a-microsoft-account) e não pertencem ao Active directory da sua empresa, eles podem receber um erro ao navegar para o link fornecido. Se eles receberam um erro, instrua os clique em seu nome no canto superior direito do portal do Azure e selecione a pasta onde o laboratório existe da seção de **diretório** do menu.

### <a name="what-is-a-microsoft-account"></a>O que é uma conta da Microsoft?

Uma conta da Microsoft é o que você usa para quase tudo o que fazer com serviços e dispositivos do Microsoft. É um endereço de email e a senha que você usa para entrar no Skype, Outlook.com, OneDrive, Windows Phone e Xbox LIVE – e significa que arquivos, fotos, contatos e configurações podem seguir você para qualquer dispositivo. 

>[AZURE.NOTE] Conta da Microsoft usada para ser chamado "Windows Live ID".
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefato falhou durante a criação de máquina virtual. Como soluciono-lo? 
Consulte a postagem no blog [como solucionar falhando artefatos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - escrito por um dos nossos MVPs - para saber como obter registros com relação ao seu artefato falha. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que não é meu virtual rede salvando corretamente?  
Uma possibilidade é que o seu nome de rede virtual contém períodos. Em caso afirmativo, tente remover os períodos ou substituindo-os com hifens e tente salvar a rede virtual novamente.
