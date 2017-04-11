<properties
   pageTitle="Adicionar automação Azure runbooks para planos de recuperação | Microsoft Azure"
   description="Este artigo descreve como recuperação de Site do Azure agora permite que você estenda planos de recuperação usando automação do Azure para concluir tarefas complexas durante a recuperação no Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar automação Azure runbooks para planos de recuperação


Este tutorial descreve como recuperação de Site do Azure integra-se com a automação do Azure para fornecer extensibilidade para planos de recuperação. Planos de recuperação podem organizar recuperação de suas máquinas virtuais protegidos usando recuperação de Site do Azure para replicação nuvem secundário e replicação para cenários Azure. Elas também ajudam a tornar a recuperação **precisas**, **repetitivo**e **automatizada**. Se você estiver falhando sobre suas máquinas virtuais no Azure, integração com o Azure automação estende os planos de recuperação e fornece capacidade de executar runbooks, permitindo que tarefas de automação poderosas.

Se você não tiver ouvido sobre automação do Azure ainda, inscreva-se [aqui](https://azure.microsoft.com/services/automation/) e baixe seus scripts de exemplo [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre [Recuperação de Site do Azure](https://azure.microsoft.com/services/site-recovery/) e como coordenar a recuperação no Azure usando planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste tutorial, veremos como você pode integrar runbooks de automação do Azure planos de recuperação. Abordaremos automatizar tarefas simples que anteriormente necessária intervenção manual e ver como converter uma recuperação de várias etapa em uma ação de recuperação de único clique. Veremos também como você pode solucionar um script simples se ele errado.

## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação

1. Vamos começar pela operning a lâmina de recursos do plano de recuperação. Você pode ver que o plano de recuperação tem duas máquinas virtuais adicionadas a ele para recuperação. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Clique no botão Personalizar para começar a adicionar um runbook. Isso abrirá o plano de recuperação personalizar blade.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Clique com o botão direito no grupo iniciar 1 e selecione Adicionar uma ação de postagem' Adicionar'.

4. Selecione esta opção para escolher um script na nova lâmina.

5. Nomeie o script 'Hello World'.

6. Escolha um nome de conta de automação. Esta é a conta de automação do Azure. Observe que esta conta pode estar em qualquer geografia Azure mas deve estar na mesma assinatura como o Cofre de recuperação do Site.

7. Selecione um runbook a conta de automação. Este é o script que será executado durante a execução do plano de recuperação após a recuperação do primeiro grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Selecione Okey para salvar o script. Isso adicionará o script para o grupo de ação de postagem de grupo 1: Iniciar.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Pontos de adicionando um script destaque

1. Você pode clique com o botão direito no script e escolha 'Excluir etapa' ou 'Atualizar script'.

2. Um script pode ser executados no Azure durante o failover do local para o Azure e pode executar no Azure como um script de lado primária antes do desligamento, durante failback do Azure para o local.

3. Quando um script é executado, ele irá inserir um contexto de plano de recuperação.

Abaixo está um exemplo de como a variável de contexto parece.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


A tabela a seguir contém o nome e uma descrição para cada variável no contexto.

**Nome da variável** | **Descrição**
---|---
RecoveryPlanName | Nome do plano está sendo executado. Ajuda você a agir com base no nome usando o mesmo script
FailoverType | Especifica se o failover é testar, planejado ou não.
FailoverDirection | Especifique se a recuperação está ao primário ou secundário
ID do grupo | Identificar o número de grupo no plano de recuperação quando o plano está em execução
VmMap | Matriz de todas as máquinas virtuais do grupo
Chave VMMap | Chave exclusiva (GUID) para cada máquina virtual. É a mesma que a ID de VMM da máquina virtual onde aplicável.
Nome de função | Nome da máquina virtual do Azure que está sendo recuperado
CloudServiceName | Nome de serviço de nuvem Azure em qual máquina virtual é criada.
CloudServiceName (no modelo de implantação do Gerenciador de recursos) | Nome do grupo de recursos do Azure em qual máquina virtual é criada.


## <a name="using-complex-variables-per-recovery-plan"></a>Usando variáveis de complexo por plano de recuperação

Às vezes, um runbook requer mais informações do que apenas o RecoveryPlanContext. Não há nenhum mecanismo de primeira classe para passar um parâmetro para um runbook. No entanto, se você quiser usar o mesmo script por meio de vários planos de recuperação você pode usar a variável de contexto de plano de recuperação 'RecoveryPlanName' e usar o abaixo experimental técnica para usar uma variável de complexos de automação do Azure em um runbook. O exemplo a seguir mostra como você pode criar três ativos de outra variável complexo e usá-las em runbook com base no nome do plano de recuperação.

Considere a possibilidade de que você deseja usar 3 parâmetros adicionais em um runbook. Deixe-nos codificá-los em um formulário JSON {"Var1": "testautomation", "Var2": "Planejado", "Var3": "PrimaryToSecondary"}

Use [AA complexo variável](../automation/automation-variables.md#variable-types Complex variable) para criar um novo ativo de automação.
Nomeie a variável como <RecoveryPlanName>- parâmetros.
Você pode usar a referência aqui para criar uma [variável de complexos](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Para planos de recuperação diferentes, nomeie a variável como

1. recoveryPlanName1 >-parâmetros
2. recoveryPlanName2 >-parâmetros
3. recoveryPlanName3 >-parâmetros

Agora, o script referem-se a parâmetros como

1. Obtenha o nome RP do $rpname = $Recoveryplancontext variável
2. Obter ativo da $paramValue = "$($rpname)-parâmetros"
3. Use este documento como uma variável complexo para o plano de recuperação chamando Get-AzureAutomationVariable [-AutomationAccountName] <String> -nome $paramValue.

Como exemplo, para obter o complexo variável/parâmetro para o plano de recuperação de SharepointApp, crie uma variável de complexos de automação do Azure chamada 'SharepointApp-parâmetros'.

Usá-lo no plano de recuperação extraindo a variável do ativo usando a instrução Get-AzureAutomationVariable [-AutomationAccountName] <String> [-nome] $paramValue. [Fazer referência a isso para obter mais detalhes](https://msdn.microsoft.com/library/dn913772.aspx)

Dessa maneira o mesmo script pode ser usada para o plano de recuperação diferentes armazenando a variável complexo específica do plano nos ativos.

## <a name="sample-scripts"></a>Exemplos de scripts

Para um repositório de scripts que você pode importar diretamente para sua conta de automação, consulte [repositório OMS de Kristian Nese para scripts](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

O script aqui é um modelo de Gerenciador de recursos do Azure que implantará todos os abaixo de scripts

* NSG

Runbook NSG atribuirá endereços IP públicos para cada máquina virtual dentro do plano de recuperação e anexar seus adaptadores de rede virtual a um grupo de segurança de rede que permitirão comunicação padrão

* PublicIP

Runbook IP público atribuirá endereços IP públicos para cada máquina virtual dentro do plano de recuperação. Acesso a máquinas e aplicativos dependerá as configurações de firewall dentro de cada convidado


* CustomScript

Runbook CustomScript atribuirá endereços IP públicos para cada máquina virtual dentro do plano de recuperação e instalar uma extensão de script personalizado que busca o script que você se referir a durante a implantação do modelo

* NSGwithCustomScript

O NSGwithCustomScript runbook atribuirá endereços IP públicos para cada máquina virtual dentro o plano de recuperação, instalar um personalizado usando a extensão de script e conecte os adaptadores de rede virtual para permitir um NSG padrão de comunicação de entrada e saída para acesso remoto

## <a name="additional-resources"></a>Recursos adicionais

[Serviço de automação Azure executado como conta](../automation/automation-sec-configure-azure-runas-account.md)

[Visão geral de automação Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Visão geral de automação Azure")

[Exemplos de Scripts de automação Azure] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Exemplos de Scripts de automação Azure")
