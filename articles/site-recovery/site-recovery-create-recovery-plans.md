<properties
    pageTitle="Criar planos de recuperação | Microsoft Azure" 
    description="Crie planos de recuperação com a recuperação de Site do Azure para falhar e recuperar grupos de máquinas virtuais e servidores físicos." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>Criar planos de recuperação

O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md).


## <a name="overview"></a>Visão geral

Este artigo fornece informações sobre como criar e personalizar os planos de recuperação. 

Planos de recuperação consistem em um ou mais grupos ordenados que contêm máquinas virtuais ou servidores físicos que você deseja alternar juntos. Planos de recuperação faça o seguinte:

- Defina grupos de máquinas que falharem e então inicie juntos.
- Modelos dependências entre máquinas agrupando-as em um grupo de plano de recuperação. Exemplo, se você quiser falhar e abra um aplicativo específico que você faria grupo máquinas virtuais do aplicativo no mesmo grupo plano de recuperação.
- Automatizar e estender failover. Você pode executar um teste, planejado, ou failover não planejado em um plano de recuperação. Você pode personalizar os planos de recuperação com scripts, automação Azure e ações manuais.

Planos de recuperação são exibidos nos **Planos de recuperação** no portal de recuperação do Site.


Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de começar

Observe o seguinte:

- Um plano de recuperação não deve misturar VMs com adaptadores de rede de uma ou várias. Isso ocorre porque misturar essas VMs não é permitido em um serviço de nuvem Azure.
- Você pode estender planos de recuperação com scripts e ações manuais. Observe o seguinte:
    - Escreva scripts usando o Windows PowerShell.
    - Certifique-se de que os scripts usam blocos try-catch para que as exceções são tratadas normalmente. Se houver uma exceção no script parar a execução e a tarefa mostra como falha.  Se ocorrer um erro, qualquer parte restante do script não será executado. Se isso acontecer quando você estiver executando um failover não planejado, o plano de recuperação continuará. Se isso acontecer quando você estiver executando um failover planejado, o plano de recuperação será interrompida. Se isso acontecer, corrigir o script, certificar-se de que ele executa conforme esperado e execute o plano de recuperação novamente.
    - O comando Write-Host não funciona em um script de plano de recuperação e o script falhará. Se você quiser criar saída, crie um script de proxy que por sua vez executa o script principal e certifique-se de que toda a saída é obsoletos usando o >> comando.
    - O script atinge o tempo limite se ele não retornar dentro de 600 segundos.
    - Se nada for escrito check-out para STDERR, o script será classificado como falha. Esta informação será exibida na seção detalhes da execução de scripts.
    - Se você estiver usando o VMM na sua implantação Observe que:

        - Executar scripts em um plano de recuperação no contexto da conta de serviço VMM. Verifique se que esta conta tem permissões de leitura no compartilhamento remoto no qual o script está localizado e testar o script a ser executado no nível de privilégio da conta de serviço do VMM.
        - Cmdlets VMM são entregues em um módulo do Windows PowerShell. O módulo VMM Windows PowerShell é instalado quando você instala o console do VMM. O módulo do VMM pode ser carregado no seu script usando o seguinte comando no script: módulo de importar-nome virtualmachinemanager. [Para obter mais detalhes](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Certifique-se de que você tenha pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão o caminho de compartilhamento de biblioteca de um servidor VMM está localizado localmente no servidor VMM com o nome da pasta MSCVMMLibrary.
        - Se seu caminho de compartilhamento de biblioteca for remoto (ou local mas não compartilhado com MSCVMMLibrary, configurar o compartilhamento da seguinte maneira (usando \\libserver2.contoso.com\share\ como exemplo):
            - Abra o Editor do registro e navegue até HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration.
            -  Editar o valor ScriptLibraryPath e coloque o valor como \\libserver2.contoso.com\share\. especificar o FQDN completo. Forneça permissões para o local de compartilhamento.
            -  Certifique-se de que você teste o script com uma conta de usuário que tenha as mesmas permissões que a conta de serviço do VMM, para garantir que os scripts testadas autônomos executado da mesma forma que ela fique em planos de recuperação. No servidor VMM, defina a política de execução para ignorar da seguinte maneira:
                -  Abra o console do Windows PowerShell de 64 bits usando privilégios elevados.
                -  Tipo: **Set-executionpolicy ignorar**. [Para obter mais detalhes](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

A maneira em que você criar um plano de recuperação depende de sua implantação de recuperação do Site.

- **Duplicar VMware VMs e servidores físicos**— você criar um plano e adicionar grupos de replicação que contêm máquinas virtuais e servidores físicos para um plano de recuperação.
- **Duplicar VMs Hyper-V (em nuvens do VMM)**— você criar um plano e adicionar protegidas máquinas virtuais de Hyper-V de uma nuvem VMM para um plano de recuperação.
- **Duplicar VMs Hyper-V (não no nuvens do VMM)**— criar um plano e adicionar protegidas máquinas virtuais de Hyper-V de um grupo de proteção para um plano de recuperação.
- **Replicação de SAN**— criar um plano e adicionar um grupo de replicação que contém máquinas virtuais ao plano de recuperação. Selecionar um grupo de replicação em vez de máquinas virtuais específicas porque todas as máquinas virtuais em um grupo de replicação falhará com relação juntos (failover ocorre na camada de armazenamento primeiro).


Crie um plano de recuperação da seguinte maneira:

1. Clique em **Planos de recuperação** guia > **Plano de recuperação de criar**.
Especifique um nome para o plano de recuperação e uma origem e destino. O servidor de origem deve ter máquinas virtuais que estão habilitadas para failover e recuperação.

    - Se você estiver replicação de VMM para VMM selecione **Tipo de fonte** > **VMM**e os servidores de origem e destino VMM. Clique em **Hyper-V** para ver nuvens que estão configurados para usar Hyper-V Replica. 
    - Se você estiver replicação de VMM para VMM usando SAN selecionar **Tipo de fonte** > **VMM**e os servidores de origem e destino VMM. Clique em **SAN** para ver nuvens que estão configurados para replicação SAN.
    - Se você estiver replicação de VMM para Azure selecione **Tipo de fonte** > **VMM**.  Selecione o servidor do VMM de origem e o **Azure** como o destino.
    - Se você estiver replicar de um site de Hyper-V selecione **Tipo de fonte** > **site Hyper-V**. Selecione o site como origem e **Azure **como o destino.
    - Se você estiver replicar do VMware ou um servidor local físico no Azure, selecione um servidor de configuração como a origem e o **Azure** como o destino

2. Em **Selecionar máquinas virtuais** selecione as máquinas virtuais (ou grupo de replicação) que você deseja adicionar ao grupo padrão (grupo 1) no plano de recuperação.

## <a name="customize-recovery-plans"></a>Personalizar os planos de recuperação

Depois de adicionar protegido máquinas virtuais ou grupos de replicação para o grupo de plano de recuperação de padrão e criado o plano que você pode personalizá-lo:

- **Adicionar novos grupos**— você pode adicionar grupos de plano de recuperação adicionais. Grupos adicionado são numerados na ordem em que você adicioná-los. Você pode adicionar até sete grupos. Você pode adicionar mais máquinas ou grupos de replicação a esses novos grupos. Observe que uma máquina virtual ou um grupo de replicação só pode ser incluído no grupo de plano de recuperação de um.
- **Adicionar um script **— você pode adicionar scripts antes ou após a recuperação do grupo de planos. Quando você adiciona um script-adiciona um novo conjunto de ações para o grupo. Por exemplo um conjunto de etapas pré-lançamento para o grupo 1 será criado com o nome: grupo 1: pré-etapas. Todas as pré-etapas de serão listadas dentro deste conjunto. Observe que você só pode adicionar um script no local principal se você tiver um servidor VMM implantado.
- **Adicionar uma ação manual**— você pode adicionar manuais ações que são executadas antes ou depois de um grupo de plano de recuperação. Quando o plano de recuperação é executado, parar no ponto em que você inseriu a ação manual e uma caixa de diálogo solicitará que você especifique que a ação manual foi concluída.

## <a name="extend-recovery-plans-with-scripts"></a>Estender planos de recuperação com scripts

Você pode adicionar um script ao seu plano de recuperação:

- Se você tiver um site de origem VMM, você pode criar um script do servidor VMM e incluí-lo em seu plano de recuperação.
- Se você estiver replicar no Azure você pode integrar automação Azure runbooks seu plano de recuperação

### <a name="create-a-vmm-script"></a>Criar um script VMM


Crie o script da seguinte maneira:

1. Criar uma nova pasta no compartilhamento de biblioteca, por exemplo \<VMMServerName > \MSSCVMMLibrary\RPScripts. Colocá-lo na origem e destino servidores VMM.
2. Criar o script (por exemplo, RPScript) e verifique a que ela funciona conforme esperado.
3. Coloque o script no local \<VMMServerName > \MSSCVMMLibrary nos servidores VMM origem e destino.

### <a name="create-an-azure-automation-runbook"></a>Criar um runbook automação Azure

Você pode estender seu plano de recuperação executando um runbook automação Azure como parte do plano. [Leia mais](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Adicionar configurações personalizadas para um plano de recuperação

1. Abra o plano de recuperação que você deseja personalizar.
2. Clique para adicionar um máquinas virtuais ou novo grupo.
3. Para adicionar um script ou manual ação clique em qualquer item na lista **etapa** e clique em **Script** ou **Ação Manual**. Especifique se deseja adicionar o script ou ação antes ou após o item selecionado. Use os botões de comando **Mover para cima** e **Mover para baixo** para mover a posição do script para cima ou para baixo.
4. Se você estiver adicionando um script VMM, selecione **Failover script VMM**e no **Caminho de Script** , digite o caminho relativo para o compartilhamento. Então, por nosso exemplo onde o compartilhamento está localizado em \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
5. Se você estiver adicionando uma catálogo de execução de automação Azure, especificar a **Conta de automação do Azure** na qual runbook está localizado e selecione o apropriado **Azure Runbook Script**.
5. Faça um failover do plano de recuperação para garantir que o script funcione conforme o esperado.


## <a name="next-steps"></a>Próximas etapas

Você pode executar diferentes tipos de failovers no plano de recuperação, incluindo um failover de teste para verificar seu ambiente e failovers planejados ou não. [Saiba mais](site-recovery-failover.md).


 
