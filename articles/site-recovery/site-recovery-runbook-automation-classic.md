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


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Adicionar automação Azure runbooks para planos de recuperação - clássico


Este tutorial descreve como recuperação de Site do Azure integra-se com a automação do Azure para fornecer extensibilidade para planos de recuperação. Planos de recuperação podem organizar recuperação de suas máquinas virtuais protegidos usando recuperação de Site do Azure para replicação nuvem secundário e replicação para cenários Azure. Elas também ajudam a tornar a recuperação **precisas**, **repetitivo**e **automatizada**. Se você estiver falhando sobre suas máquinas virtuais no Azure, integração com o Azure automação estende os planos de recuperação e fornece capacidade de executar runbooks, permitindo que tarefas de automação poderosas.

Se você não tiver ouvido sobre automação do Azure ainda, inscreva-se [aqui](https://azure.microsoft.com/services/automation/) e baixe seus scripts de exemplo [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre [Recuperação de Site do Azure](https://azure.microsoft.com/services/site-recovery/) e como coordenar a recuperação no Azure usando planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste tutorial breve, veremos como você pode integrar runbooks de automação do Azure planos de recuperação. Abordaremos automatizar tarefas simples que anteriormente necessária intervenção manual e ver como converter uma recuperação de etapa de vários em uma ação de recuperação de único clique. Veremos também como você pode solucionar um script simples se ele errado.

## <a name="protect-the-application-to-azure"></a>Proteger o aplicativo no Azure

Vamos começa com um aplicativo simples que consiste em duas máquinas virtuais. Aqui, temos um aplicativo de HRweb da Fabrikam. Fabrikam-HRweb-frontend e Fabrikam-Hrweb-back-end são as duas máquinas virtuais protegidas no Azure usando a recuperação de Site do Azure. Para proteger as máquinas virtuais usando a recuperação de Site Azure, siga as etapas abaixo.

1.  Ative a proteção para suas máquinas virtuais.

2.  Verifique se as máquinas virtuais concluiu replicação inicial e são replicar.

3.  Aguarde até que a replicação inicial for concluído e o status de replicação diz protegido.

![](media/site-recovery-runbook-automation/01.png)
---------------------

Neste tutorial, vamos criar um plano de recuperação para o aplicativo Fabrikam HRweb failover o aplicativo no Azure. E em seguida, podemos será integram um runbook que criará um ponto de extremidade na Falha ao longo do Azure máquina virtual para servir páginas da web na porta 80.

Primeiro, vamos criar um plano de recuperação de nosso aplicativo.

## <a name="create-the-recovery-plan"></a>Criar o plano de recuperação

Para recuperar o aplicativo no Azure, você precisa criar um plano de recuperação.
Usando um plano de recuperação, que você pode especificar a ordem de recuperação das máquinas virtuais. Na máquina virtual colocada no grupo 1 será recuperar iniciar primeira e depois a máquina virtual no grupo 2.

Crie um plano de recuperação que parece abaixo.

![](media/site-recovery-runbook-automation/12.png)

Para ler mais sobre planos de recuperação, leia a documentação [aqui](https://msdn.microsoft.com/library/azure/dn788799.aspx "aqui").

Em seguida, vamos criar os artefatos necessários na automação do Azure.

## <a name="create-the-automation-account-and-its-assets"></a>Criar a conta de automação e seus ativos

Você precisa de uma conta de automação do Azure para criar runbooks. Se você não tiver uma conta, navegue até o guia de automação do Azure indicado por ![](media/site-recovery-runbook-automation/02.png)e criar uma nova conta.

1.  Dê um nome para identificar com a conta.

2.  Especifique uma região geográfica onde você deseja colocar a conta.

É recomendável para colocar a conta na mesma região como o Cofre de recuperação automatizada do sistema.

![](media/site-recovery-runbook-automation/03.png)

Em seguida, crie os seguintes ativos na conta.

### <a name="add-a-subscription-name-as-asset"></a>Adicione um nome de assinatura como ativo

1.  Adicionar uma nova configuração ![](media/site-recovery-runbook-automation/04.png) no ativos de automação do Azure e escolha a![](media/site-recovery-runbook-automation/05.png)

2.  Selecione o tipo de variável como **cadeia de caracteres**

3.  Especifique o nome da variável como **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Especifique o nome de assinatura do Azure real como o valor da variável.

    ![](media/site-recovery-runbook-automation/07_1.png)

Você pode identificar o nome da sua assinatura na página Configurações da sua conta no portal do Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Adicionar uma credencial de login Azure como ativo

Automação Azure usa Azure PowerShell para se conectar à assinatura e opera em artefatos lá. Para isso, você precisará autenticar usando sua conta da Microsoft ou uma conta corporativa ou escolar.
Você pode armazenar as credenciais de conta de um ativo para ser usado com segurança em runbook.

1.  Adicionar uma nova configuração ![](media/site-recovery-runbook-automation/04.png) no ativos de automação do Azure e escolha![](media/site-recovery-runbook-automation/09.png)

2.  Selecione o tipo de credencial como **Credencial do Windows PowerShell**

3.  Especificar o nome como **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Especifique o nome de usuário e senha para entrar com.

Agora essas duas configurações estão disponíveis em seus ativos.

![](media/site-recovery-runbook-automation/11.png)

Obter mais informações sobre como se conectar à sua assinatura por meio do PowerShell é dado [aqui](../powershell-install-configure.md).

Em seguida, você criará um runbook na automação do Azure que pode adicionar um ponto de extremidade da máquina virtual front-end após failover.

## <a name="azure-automation-context"></a>Contexto de automação Azure

Recuperação automatizada do sistema passa uma variável de contexto para runbook para ajudá-lo a escrever scripts determinantes. Alguém poderia argumentar que os nomes de serviço de nuvem e a máquina Virtual são previsíveis, mas acontece que não é sempre o caso devido determinados cenários como o onde o nome da máquina virtual pode ter alterado devido a caracteres sem suporte no Azure. Portanto, essa informação é passada para o plano de recuperação de ASR como parte do *contexto*.

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


Identificar a VmMap Key no contexto que você também poderia ir para a página de propriedades de máquina virtual em recuperação automatizada do sistema e examine a propriedade GUID de VM.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Autor um runbook de automação

Agora crie runbook para abrir a porta 80 na máquina virtual front-end.

1.  Criar um novo runbook na conta do Azure automação com o nome **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Navegue até o modo de exibição de autor do runbook e insira o modo de rascunho.

3.  Primeiro, especifique a variável para usar como o contexto de plano de recuperação

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Conectar-se próximo à assinatura usando o nome de credencial e assinatura

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Observe que você usa os ativos Azure – **AzureCredential** e **AzureSubscriptionName** aqui.

5.  Agora, especifique os detalhes do ponto de extremidade e o GUID da máquina virtual para o qual você deseja expor o ponto de extremidade. Nesse caso a máquina virtual front-end.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Especifica o protocolo de ponto de extremidade Azure, porta local a máquina virtual e sua porta pública mapeada. Essas variáveis são parâmetros necessários pelos comandos Azure que adicionar pontos de extremidade para VMs. O VMGUID contém o GUID da máquina virtual que você precisa operam.

6.  O script agora extrair o contexto para o GUID de VM determinada e criar um ponto de extremidade na máquina virtual referenciada por ele.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Quando isso estiver concluído, acertar publicar ![](media/site-recovery-runbook-automation/20.png) para permitir que o script esteja disponível para execução.

O script completo é fornecido abaixo para sua referência

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Adicione o script para o plano de recuperação

Depois que o script está pronto, você pode adicioná-la ao plano de recuperação que você criou anteriormente.

1.  No plano de recuperação criada, escolha Adicionar um script após o grupo 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Especifique um nome de script. Este é apenas um nome amigável para este script para mostrando no plano de recuperação.

3.  No failover para script Azure – selecione o nome da conta de automação do Azure.

4.  No Runbooks Azure, selecione runbook criados por você.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Scripts de lado primária

Quando você está executando um failover para o Azure, você também pode optar por executar scripts de lado primária. Esses scripts serão executadas no servidor VMM durante tolerância a falhas.
Scripts de lado primária só estão disponíveis apenas para pré-desligamento e postam estágios de desligamento. Isso ocorre porque esperamos que o site principal para estar normalmente disponível quando um acidente.
Durante um failover não planejado, somente se você optar por usar em para operações de site principal, ele tentará executar os scripts de lado primária. Se não estiverem acessíveis ou tempo limite, o failover continuará a recuperar as máquinas virtuais.
Scripts de lado primária são não disponíveis para Sites de VMware/físico/Hyper-v sem o VMM protegido Azure - enquanto você failover no Azure.
No entanto, quando você failback do Azure para o local, scripts de lado primária (Runbooks) pode ser usado para todos os destinos, exceto VMware.

## <a name="test-the-recovery-plan"></a>Testar o plano de recuperação

Depois que você adicionou runbook para o plano que você pode iniciar um failover de teste e veja em ação. Sempre é recomendável para executar um failover de teste para testar seu aplicativo e o plano de recuperação para garantir que não existem erros.

1.  Selecione o plano de recuperação e iniciar um failover de teste.

2.  Durante a execução de plano, você pode ver se runbook executou ou não por meio de seu status.

    ![](media/site-recovery-runbook-automation/17.png)

3.  Você também pode ver o status de execução de runbook detalhadas na página de trabalhos de automação do Azure para runbook.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Após o failover, exceto o resultado de execução de runbook, você pode ver se a execução for bem-sucedida ou não visitar a página do Azure máquina virtual e olhando para os pontos de extremidade.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Exemplos de scripts

Enquanto percorremos automatizar um comumente usados tarefa da adição de um ponto de extremidade para uma máquina virtual Azure neste tutorial, você pode fazer um número de outras tarefas de automação poderosos usando automação Azure. Microsoft e à comunidade de automação do Azure fornecem runbooks de amostra que pode ajudar a começar a criar suas próprias soluções e runbooks utilitário, que você pode usar como blocos de construção maiores das tarefas de automação. Começar a usá-los na galeria e criar planos de recuperação de um único clique poderosos para seus aplicativos usando a recuperação de Site do Azure.

## <a name="additional-resources"></a>Recursos adicionais

[Visão geral de automação Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Visão geral de automação Azure")

[Exemplos de Scripts de automação Azure] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Exemplos de Scripts de automação Azure")
