<properties
   pageTitle="Manipulação de erro de automação Azure | Microsoft Azure"
   description="Este artigo fornece erros básicos manipulação etapas para solucionar problemas e corrigir erros comuns de automação do Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="Erro de automação, de tratamento de erro"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Dicas para erros de automação do Azure comuns de manipulação de erro

Este artigo explica alguns dos erros de automação do Azure comuns que você pode enfrentar e sugere etapas de manipulação de erro possível.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Solucionar problemas de autenticação ao trabalhar com runbooks de automação do Azure  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Cenário: Entre na conta do Azure falha

**Erro:** Você receberá o erro "Unknown_user_type: tipo desconhecido do usuário" ao trabalhar com os cmdlets adicionar AzureAccount ou AzureRmAccount de Login.

**Motivo para o erro:** Este erro ocorre se o nome de ativos de credencial não é válido ou se o nome de usuário e a senha que você usou para configurar o ativo de credencial de automação não são válidos.

**Dicas de solução de problemas:** Para determinar o que está errado, siga estes passos:  

1. Certifique-se de que não tem quaisquer caracteres especiais, incluindo o **@** caracteres no nome automação credencial ativo que você está usando para se conectar ao Azure.  

2. Verifique que você pode usar o nome de usuário e a senha que estão armazenados na credencial de automação do Azure em seu local editor do PowerShell ISE. Você pode fazer isso executando os seguintes cmdlets do PowerShell ISE:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Se a autenticação falhar localmente, isso significa que você ainda não configurou o suas credenciais do Active Directory do Azure corretamente. Consulte a postagem de blog [autenticando no Azure usando o Active Directory do Azure](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) para acessar a conta do Active Directory do Azure configurada corretamente.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Cenário: Não é possível localizar a assinatura do Azure

**Erro:** Você recebe o erro "a assinatura denominada ``<subscription name>`` não encontrado" ao trabalhar com os cmdlets selecione AzureSubscription ou selecione AzureRmSubscription.

**Motivo para o erro:** Este erro ocorre se o nome de assinatura não é válido ou se o usuário do Active Directory do Azure que está tentando obter os detalhes de assinatura não está configurado como administrador da assinatura.

**Dicas de solução de problemas:** Para determinar se você tiver autenticado corretamente para o Azure e tem acesso à assinatura que você está tentando selecionar, siga estes passos:  

1. Certifique-se de que você executa a **Adicionar AzureAccount** antes de executar o cmdlet **Select-AzureSubscription** .  

2. Se você ainda pode ver esta mensagem de erro, modifique seu código, adicionando o cmdlet **Get-AzureSubscription** seguindo o cmdlet **Add-AzureAccount** e, em seguida, executar o código.  Agora, verifique se a saída de Get-AzureSubscription contém detalhes da sua assinatura.  
    * Se você não vir quaisquer detalhes da assinatura na saída, isso significa que a assinatura não está ainda inicializada.  
    * Se você vir os detalhes da assinatura na saída, confirme que você estiver usando o nome da assinatura correto ou a ID com o cmdlet **Select-AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Cenário: Autenticação Azure falha porque a autenticação multifator está habilitada

**Erro:** Você recebe o erro "Adicionar AzureAccount: AADSTS50079: inscrição de autenticação forte (prova-up) é necessária" ao autenticar no Azure Azure nome de usuário e senha.

**Motivo para o erro:** Se você tiver autenticação multifator em sua conta do Azure, é possível usar um usuário do Active Directory do Azure para autenticar Azure.  Em vez disso, você precisa usar um certificado ou um serviço principal para autenticar no Azure.

**Dicas de solução de problemas:** Para usar um certificado com os cmdlets de gerenciamento de serviço do Azure, consulte [Criando e adicionando um certificado para gerenciar os serviços do Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com os cmdlets do Gerenciador de recursos do Azure, consulte [Criando principal do serviço usando o portal do Azure](./resource-group-create-service-principal-portal.md) e [autenticar um principal de serviço com o Gerenciador de recursos do Azure.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Solucionar erros comuns ao trabalhar com runbooks

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Cenário: Runbook falha devido ao objeto desserializado

**Erro:** Seu runbook falha com o erro "não é possível vincular o parâmetro ``<ParameterName>``. Não é possível converter o ``<ParameterType>`` valor do tipo Deserialized ``<ParameterType>`` digitar ``<ParameterType>``".

**Motivo para o erro:** Se seu runbook é um fluxo de trabalho do PowerShell, ele armazena objetos complexos em um formato desserializado para manter seu estado de runbook se o fluxo de trabalho está suspensa.  

**Dicas de solução de problemas:**  
Qualquer uma das seguintes três soluções corrigirá esse problema:

1. Se você estiver tubulação objetos complexos de um cmdlet para outro, quebre esses cmdlets em um InlineScript.  
2. Passe o nome ou o valor que você precisa do objeto complexo em vez de passar todo o objeto.  

3. Use um runbook PowerShell em vez de um fluxo de trabalho do PowerShell runbook.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Cenário: O trabalho de Runbook falhou porque a alocado cota excedida

**Erro:** Seu trabalho runbook falha com o erro "a cota para o trabalho total mensal tempo de execução para essa assinatura foi atingida".

**Motivo para o erro:** Este erro ocorre quando a execução do trabalho excede a cota de 500 minutos gratuita para sua conta. Essa cota se aplica a todos os tipos de tarefas de execução de trabalho como testes um trabalho, iniciando um trabalho a partir do portal, executando um trabalho usando webhooks e agendar um trabalho para executar usando o portal de Azure ou no seu data center. Para saber mais sobre preços para automação consulte [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

**Dicas de solução de problemas:** Se você quiser usar mais de 500 minutos de processamento por mês você precisará alterar sua assinatura do livre camada para a camada básica. Você pode atualizar para a camada básica fazendo as seguintes etapas:  

1. Entrar para sua assinatura do Azure  
2. Selecione a conta de automação que deseja atualizar  
3. Clique em **configurações** > **nível de preços e o uso** > **nível de preço**  
4. Na lâmina **Escolher sua camada de preços** , selecione **básica**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Cenário: Cmdlet não reconhecido ao executar uma runbook

**Erro:** Seu trabalho runbook falhar com o erro "``<cmdlet name>``: O termo ``<cmdlet name>`` não é reconhecido como o nome de um cmdlet, função, arquivo de script ou programa operável."

**Motivo para o erro:** Este erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando em seu runbook.  Isso pode ser porque o módulo que contém o cmdlet está ausente da conta, não há um conflito de nome com um nome de runbook, ou o cmdlet também existe em outro módulo e automação não consegue resolver o nome.

**Dicas de solução de problemas:** Qualquer uma das seguintes soluções corrigirá o problema:  

- Verifique se digitou o nome do cmdlet corretamente.  

- Verifique se o cmdlet existe em sua conta de automação e que não há nenhum conflito. Para verificar se o cmdlet estiver presente, abra um runbook no modo de edição e procure o cmdlet que você deseja localizar na biblioteca ou executar **Get-Command ``<CommandName>`` **.  Após validar que o cmdlet está disponível para a conta e que não há nenhum conflito de nome com outros cmdlets ou runbooks, adicioná-lo à tela e certifique-se de que você está usando um parâmetro válido definir na sua runbook.  

- Se você tiver um conflito de nome e o cmdlet está disponível em dois módulos diferentes, você pode resolver isso usando o nome totalmente qualificado para o cmdlet. Por exemplo, você pode usar **ModuleName\CmdletName**.  

- Se você está executando o runbook no local em um grupo de trabalho híbrida, em seguida, verifique se o módulo/cmdlet está instalado na máquina que hospeda o trabalhador híbrido.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Cenário: Uma longa duração runbook consistentemente falhará com a exceção: "o trabalho não pode continuar executando porque ele repetidamente foi removido do mesmo ponto de verificação".

**Motivo para o erro:** Esse é o comportamento de design devido ao monitoramento "Compartilhar justo" de processos dentro de automação do Azure, que suspende automaticamente um runbook se ele executa mais de 3 horas. No entanto, a mensagem de erro retornada não fornecem "e depois" opções. Um runbook pode ser suspenso para por vários motivos. Suspende acontecer principalmente devido a erros. Por exemplo, uma exceção não tratada em um runbook, uma falha de rede ou uma falha na trabalhador Runbook executando runbook, todos causará runbook ser suspenso e iniciar a partir de seu último ponto de verificação quando retomado.

**Dicas de solução de problemas:** A solução documentada para evitar esse problema é usar pontos de verificação de fluxo de trabalho.  Para saber que mais se referir aos [Fluxos de trabalho de PowerShell de aprendizado](automation-powershell-workflow.md#Checkpoints).  Uma explicação mais completa "feira de compartilhamento de" e ponto de verificação pode ser encontrada neste artigo no blog [Usando pontos de verificação no Runbooks](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Solucionar erros comuns ao importar módulos

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Cenário: Módulo falha importar ou cmdlets não pode ser executada após a importação

**Erro:** Um módulo falha importar ou importa com êxito, mas nenhuma cmdlets são extraídas.

**Motivo para o erro:** Alguns motivos comuns que um módulo não pode importar com êxito à automação do Azure são:  

- A estrutura não corresponder à estrutura que precisa de automação no.  

- O módulo depende de outro módulo que não tenha sido implantado à sua conta de automação.  

- O módulo está faltando suas dependências na pasta.  

- O cmdlet **New-AzureRmAutomationModule** está sendo usado para carregar o módulo, e você não têm o caminho completo do armazenamento ou não carregou o módulo usando uma URL acessível publicamente.  

**Dicas de solução de problemas:**  
Qualquer uma das seguintes soluções corrigirá o problema:  

- Certifique-se de que o módulo segue o seguinte formato:  
ModuleName.Zip **->** ModuleName ou número de versão **->** (ModuleName.psm1, ModuleName.psd1)

- Abra o arquivo .psd1 e veja se o módulo apresenta quaisquer dependências.  Em caso afirmativo, carregue esses módulos para a conta de automação.  

- Certifique-se de que qualquer DLLs referenciados estão presentes na pasta de módulo.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Solucionar erros comuns ao trabalhar com a configuração de estado de desejado (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Cenário: Nó é o status de falha com um erro "Não encontrado"

**Erro:** O nó tem um relatório com status **Falha** e que contém o erro "tentativa de obter a ação do servidor https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction falha porque uma configuração válida ``<guid>`` não pode ser encontrado."

**Motivo para o erro:** Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC. Servidor Web).  

**Dicas de solução de problemas:**  

- Certifique-se de que você está atribuindo o nó com "nome de configuração de nó" e não o "configuração nome".  

- Você pode atribuir uma configuração de nó para um nó usando o portal Azure ou com um cmdlet do PowerShell.
    - Para atribuir uma configuração de nó para um nó usando o portal Azure, abra a lâmina **DSC nós** , e em seguida, selecione um nó e clique no botão **atribuir nó configuração** .  
    - Para atribuir uma configuração de nó para um nó usando o cmdlet do PowerShell, use o cmdlet **Set-AzureRmAutomationDscNode**


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Cenário: Nenhuma configuração de nó (arquivos MOF) produzidas quando uma configuração é compilada

**Erro:** Seu trabalho de compilação de DSC suspende com o erro: "compilação foi concluída com êxito, mas nenhuma .mofs de configuração de nó geradas".

**Motivo para o erro:** Quando a expressão após que a palavra-chave **nó** na configuração DSC avalia $null e em seguida, nenhuma configuração de nó será produzida.    

**Dicas de solução de problemas:**  
Qualquer uma das seguintes soluções corrigirá o problema:  

- Certifique-se de que a expressão ao lado da palavra-chave **nó** na definição de configuração não está avaliando a $null.  
- Se você estiver passando ConfigurationData ao compilar a configuração, certifique-se de que você está passando os valores esperados que requer a configuração de [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Cenário: O relatório de nó DSC fica preso estado "em andamento"

**Erro:** Agente de DSC não saídas "Nenhuma instância localizada com valores de propriedade especificados."

**Motivo para o erro:** Você atualizou sua versão WMF e ter corrompido WMI.  

**Dicas de solução de problemas:** Siga as instruções na postagem do blog [DSC problemas conhecidos e limitações](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) para corrigir o problema.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Cenário: Não é possível usar uma credencial em uma configuração de DSC

**Erro:** Seu trabalho de compilação de DSC foi suspenso com o erro: "Erro System. InvalidOperationException propriedade credencial do tipo de processamento ``<some resource name>``: convertendo e armazenar uma senha criptografada como texto sem formatação é permitido somente se PSDscAllowPlainTextPassword for definido como true".

**Motivo para o erro:** Você usou uma credencial em uma configuração, mas não oferece PRI **ConfigurationData** para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó.  

**Dicas de solução de problemas:**  
- Verifique se passar o PRI **ConfigurationData** para definir **PSDscAllowPlainTextPassword** como true para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [ativos no Azure automação DSC](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Próximas etapas

Se você tiver seguido as etapas de solução de problemas acima e necessária ajuda adicional em qualquer ponto neste artigo, você pode:

- Obtenha ajuda do Azure especialistas. Enviar seu problema para o [fóruns MSDN Azure ou estouro de pilha.](https://azure.microsoft.com/support/forums/)

- Arquivo um incidente de suporte Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obtenha suporte** em **suporte técnico e cobrança**.

- Poste uma solicitação de Script no [Centro de Script](https://azure.microsoft.com/documentation/scripts/) se você estiver procurando uma solução de runbook de automação do Azure ou um módulo de integração.

- Poste solicitações de comentários ou recurso de automação do Azure em [Voz do usuário](https://feedback.azure.com/forums/34192--general-feedback).
