<properties
    pageTitle="Conectar o Operations Manager a análise de Log | Microsoft Azure"
    description="Para manter seu investimento existente em System Center Operations Manager e usar os recursos de estendido com a análise de Log, você pode integrar o Operations Manager com o seu espaço de trabalho do OMS."
    services="log-analytics"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="magoedte"/>

# <a name="connect-operations-manager-to-log-analytics"></a>Conectar o Operations Manager a análise de Log

Para manter seu investimento existente em System Center Operations Manager e usar os recursos de estendido com a análise de Log, você pode integrar o Operations Manager com o seu espaço de trabalho do OMS.  Isso permite que você aproveite as oportunidades de OMS enquanto continua a usar o Operations Manager para:

- Continuar a monitorar a integridade dos seus serviços de TI com o Operations Manager
- Manter integração com suas soluções ITSM incidente e gerenciamento de problemas de suporte
- Gerenciar o ciclo de vida de agentes implantados em locais e máquinas virtuais do nuvem pública IaaS que monitorar com o Operations Manager

Integração com o System Center Operations Manager adiciona o valor à sua estratégia de operações de serviço aproveitando a velocidade e a eficiência do OMS em coletar, armazenar e analisar dados de Operations Manager.  OMS ajuda correlação e trabalhar em direção identificando as falhas de problemas e faceamento reoccurrences ao seu processo de gerenciamento de problema existente.   A flexibilidade o mecanismo de pesquisa para examinar o desempenho, eventos e dados de alerta, com painéis avançados e recursos de relatórios para expor dados de maneiras significativas, demonstra a força que OMS apresenta na como complemento Operations Manager.

Os agentes de relatório para o grupo de gerenciamento do Operations Manager coletam dados de seus servidores com base nas fontes de dados de análise de Log e as soluções que você habilitou em sua assinatura do OMS.  Dependendo da solução que você habilitou, dados dessas soluções são enviadas diretamente a partir de um servidor de gerenciamento do Operations Manager para o serviço da web OMS ou devido o volume de dados coletados no sistema gerenciado por agente, são enviadas diretamente do agente de serviço da web OMS. O servidor de gerenciamento diretamente encaminha os dados OMS ao serviço da web OMS, jamais é gravada no banco de dados OperationsManager ou OperationsManagerDW.  Quando um servidor de gerenciamento perde a conectividade com o serviço da web OMS, ele armazena os dados localmente até comunicação é novamente estabelecida com o OMS.  Se o servidor de gerenciamento estiver offline devido a interrupção planejada de manutenção planejada, outro servidor de gerenciamento do grupo de gerenciamento continuará a conectividade com o OMS.  

O diagrama a seguir ilustra a conexão entre os servidores de gerenciamento e agentes em um grupo de gerenciamento do System Center Operations Manager e OMS, incluindo a direção e portas.   

![OMS-operações-manager--diagrama de integração](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## <a name="system-requirements"></a>Requisitos do sistema
Antes de começar, examine os seguintes detalhes para verificar que atendem aos pré-requisitos necessários.

- OMS suporta apenas Operations Manager 2012 SP1 UR6 e maiores e Operations Manager 2012 R2 UR2 e maior.  Suporte de proxy foi adicionado no Operations Manager 2012 SP1 UR7 e UR3 do Operations Manager 2012 R2.
- Todos os agentes do Operations Manager devem atender aos requisitos mínimos de suporte. Certifique-se de que os agentes são até a atualização mínima, caso contrário, o tráfego de agente do Windows falhará e muitos erros podem preencher o log de eventos do Operations Manager.
- Uma assinatura do OMS.  Para obter mais informações, examine a [começar a usar a análise de Log](log-analytics-get-started.md).

## <a name="connecting-operations-manager-to-oms"></a>Conectando Operations Manager ao OMS
Realizar a série de etapas para configurar seu grupo de gerenciamento do Operations Manager para se conectar a um de seus espaços de trabalho do OMS seguir.

1. No console do Operations Manager, selecione o espaço de trabalho de **Administração** .
2. Expanda o nó do pacote de gerenciamento de operações e clique em **Conexão**.
3. Clique no link **Register no pacote de gerenciamento de operações** .
4. Sobre o **Assistente de integração do pacote de gerenciamento de operações: autenticação** página, insira o endereço de email ou o número de telefone e a senha da conta de administrador que está associada a sua assinatura do OMS e clique em **entrar**.
5. Depois que você está autenticado com êxito, na **Assistente de integração do pacote de gerenciamento de operações: selecione o espaço de trabalho** de página, você será solicitado para selecionar o seu espaço de trabalho do OMS.  Se você tiver mais de um espaço de trabalho, selecione o espaço de trabalho que você deseja registrar o grupo de gerenciamento do Operations Manager na lista suspensa e clique em **Avançar**.

    >[AZURE.NOTE] Operations Manager só oferece suporte a um espaço de trabalho do OMS por vez. A conexão e os computadores que foram registrados para OMS com o espaço de trabalho anterior são removidos do OMS.

6. Sobre o **Assistente de integração do pacote de gerenciamento de operações: Resumo** página, confirme suas configurações e se eles estiverem corretos, clique em **criar**.
7. Sobre o **Assistente de integração do pacote de gerenciamento de operações: término** página, clique em **Fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores gerenciados por agente
Após configurar a integração com seu espaço de trabalho do OMS, isso só estabelece uma conexão com o OMS, sem dados são coletados dos agentes de relatório ao seu grupo de gerenciamento. Isso não acontecer até depois de configurar os computadores gerenciados por agente específicos coletará dados para análise de Log. Você pode selecionar os objetos de computador individualmente ou você pode selecionar um grupo que contém objetos de computador do Windows. Você não pode selecionar um grupo que contém as instâncias da classe outro, como discos lógicos ou bancos de dados SQL.

1. Abra o console do Operations Manager e selecione o espaço de trabalho de **Administração** .
2. Expanda o nó do pacote de gerenciamento de operações e clique em **Conexão**.
3. Clique no link **Adicionar um computador/grupo** nas ações de título no lado direito do painel.
4. Na caixa de diálogo de **Pesquisa do computador** , você pode pesquisar para computadores ou grupos monitorados pelo Operations Manager. Selecionar computadores ou grupos para integrado ao OMS, clique em **Adicionar**e, em seguida, clique em **Okey**.

Você pode exibir computadores e grupos configurados para coletar dados a partir do nó de computadores gerenciados em pacote de gerenciamento de operações do espaço de trabalho de **Administração** do console de operações.  A partir daqui, você pode adicionar ou remover computadores e grupos conforme necessário.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Definir configurações de proxy do OMS no console de operações
Execute as etapas a seguir se um servidor de proxy interno for entre o grupo de gerenciamento e o serviço da web do OMS.  Essas configurações centralmente são gerenciadas a partir do grupo de gerenciamento e distribuídas para sistemas gerenciado por agente que estão incluídos no escopo para coletar dados para OMS.  Isso é útil quando determinadas soluções ignoram o servidor de gerenciamento e enviar dados diretamente para o serviço da web do OMS.

1. Abra o console do Operations Manager e selecione o espaço de trabalho de **Administração** .
2. Expanda o pacote de gerenciamento de operações e, em seguida, clique em **conexões**.
3. No modo de exibição OMS Conexão, clique em **Configurar o servidor de Proxy**.
4. Em **Assistente do pacote de gerenciamento de operações: servidor Proxy** página, selecione **usar um servidor proxy para acessar o pacote de gerenciamento de operações**, digite a URL com o número da porta, por exemplo, http://corpproxy:80 e, em seguida, clique em **Concluir**.

Se seu servidor proxy requer autenticação, execute as seguintes etapas para configurar credenciais e configurações que precisa para se propagarem para computadores gerenciados que irá relatar OMS no grupo de gerenciamento.

1. Abra o console do Operations Manager e selecione o espaço de trabalho de **Administração** .
2. Em **Configuração de RunAs**, selecione **perfis**.
3. Abra o perfil **Central Advisor executar como perfil Proxy do sistema** .
4. No executar como Assistente de perfil, clique em Adicionar para usar uma conta executar como. Você pode criar uma nova [conta executar como](https://technet.microsoft.com/library/hh321655.aspx) ou usar uma conta existente. Essa conta precisa ter permissões suficientes para passar pelo servidor proxy.
5. Para configurar a conta para gerenciar, escolha **uma classe selecionada, o grupo ou o objeto**, clique em **Selecionar...** e, em seguida, clique em **Agrupar...** Para abrir a caixa de **Pesquisa do grupo** .
6. Procure e selecione **Microsoft sistema Center Advisor Monitoring Server Group**.  Clique em **Okey** depois de selecionar o grupo para fechar a caixa de **Pesquisa do grupo** .
7.  Clique em **Okey** para fechar a caixa **Adicionar uma conta executar como** .
8.  Clique em **Salvar** para concluir o assistente e salvar suas alterações.

Depois que a conexão é criada e você configurar quais agentes serão coletar e relatar dados OMS, a seguinte configuração é aplicada no grupo de gerenciamento, não necessariamente nesta ordem:

- A conta executar como **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** é criado.  Ele está associado com o perfil executar como **Microsoft System Center Advisor executar como perfil Blob** e é direcionamento duas classes - **Servidor de coleta** e **Grupo de gerenciamento do Operations Manager**.
- Dois conectores são criados.  A primeira é denominada **Microsoft.SystemCenter.Advisor.DataConnector** e é automaticamente configurada com uma assinatura que encaminhará todos os alertas gerados pelo instâncias de classes todos no grupo de gerenciamento de análise de Log de OMS. O segundo conector é **Supervisor de conector**, que é responsável por comunicar-se com o serviço da web OMS e compartilhamento de dados.
- Grupos que você selecionou para coletar dados no grupo de gerenciamento e agentes serão adicionados ao **Microsoft sistema Center Advisor Monitoring Server Group**.

## <a name="management-pack-updates"></a>Atualizações de pacote de gerenciamento
Após a configuração, o grupo de gerenciamento do Operations Manager estabelece uma conexão com o serviço OMS.  O servidor de gerenciamento sincronizar com o serviço da web e receber informações de configuração atualizados no formulário de pacotes de gerenciamento para as soluções que você ativou o que se integram ao Operations Manager.   Operations Manager verificará atualizações para esses gerenciamento pacotes baixar automaticamente e importação-los quando estiverem disponíveis.  Há duas regras em particular que controlar esse comportamento:

- **Microsoft.SystemCenter.Advisor.MPUpdate** - atualiza os pacotes de gerenciamento de OMS base. Executa a cada doze (12) horas por padrão.
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - pacotes de gerenciamento de solução de atualizações habilitados no seu espaço de trabalho. Por padrão, é executado a cada cinco (5) minutos.

Você pode substituir essas duas regras para evitar o download automático por desabilitá-los, ou modificar a frequência com que frequência o servidor de gerenciamento sincroniza com OMS para determinar se um novo pacote de gerenciamento está disponível e deve ser baixado.  Siga as etapas [como substituir uma regra ou Monitor](https://technet.microsoft.com/library/hh212869.aspx) para modificar o parâmetro de **frequência** com um valor em segundos para alterar o agendamento de sincronização ou modifique o parâmetro **Enabled** para desabilitar as regras.  Direcione as substituições a todos os objetos da classe de grupo de gerenciamento do Operations Manager.

Se você quiser continue seguindo o processo de controle de alterações existente para controlar versões do pacote de gerenciamento do seu grupo de gerenciamento de produção, você pode desabilitar as regras e habilitá-los durante horários específicos quando são permitidas atualizações. Se você tiver um desenvolvimento ou grupo de gerenciamento de perguntas e respostas em seu ambiente e tiver conectividade com a Internet, você pode configurar esse grupo de gerenciamento com um espaço de trabalho do OMS para dar suporte a esse cenário.  Isso permitirá que você examine e avaliar as versões iterativas dos pacotes de gerenciamento de OMS antes de liberá-las em seu grupo de gerenciamento de produção.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Alternar um grupo do Operations Manager para um novo espaço de trabalho de OMS
1. Faça logon em sua assinatura OMS e criar novo espaço de trabalho no [Pacote de gerenciamento de operações do Microsoft](http://oms.microsoft.com/).
2. Abra o console do Operations Manager com uma conta que é um membro da função administradores de Gerenciador de operações e selecione o espaço de trabalho de **Administração** .
3. Expanda o pacote de gerenciamento de operações e selecione **conexões**.
4. Selecione o link do **Pacote de gerenciamento de operação configurar novamente** no lado do meio do painel.
5. Siga o **Assistente de integração do pacote de gerenciamento de operações** e insira o endereço de email ou o número de telefone e a senha da conta de administrador que está associada ao seu novo espaço de trabalho do OMS.

    > [AZURE.NOTE] O **Assistente de integração do pacote de gerenciamento de operações: selecione o espaço de trabalho** página apresentará o espaço de trabalho existente que está em uso.


## <a name="validate-operations-manager-integration-with-oms"></a>Validar a integração do Gerenciador de operações com o OMS
Há algumas maneiras diferentes, você pode verificar se sua OMS a integração com o Operations Manager é bem-sucedido.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Para confirmar a integração do portal OMS

1.  No portal do OMS, clique no bloco **configurações**
2.  Selecione **conectado fontes**.
3.  Na tabela na seção System Center Operations Manager, você deve ver o nome do grupo de gerenciamento relacionado com o número de agentes e status quando dados última foi recebidos.

    ![OMS-configurações-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

4.  Observe o valor de **Identificação de espaço de trabalho** à esquerda da página Configurações.  Você irá validá-lo em relação a seu grupo de gerenciamento do Operations Manager abaixo.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração do console de operações

1.  Abra o console do Operations Manager e selecione o espaço de trabalho de **Administração** .
2.  Selecione **Pacotes de gerenciamento** e no **Procure:** **Supervisor** de tipo de caixa de texto ou de **inteligência de dados**.
3.  Dependendo das soluções que você tiver ativado, você verá um pacote de gerenciamento correspondente listado nos resultados da pesquisa.  Por exemplo, se você habilitou a solução de gerenciamento de alerta, o pacote de gerenciamento gerenciamento de alerta do Microsoft sistema Center Advisor será na lista.
4.  Do modo de exibição de **monitoramento** , vá para o modo de exibição de **Estado de Suite\Health de gerenciamento de operações** .  Selecione um servidor de gerenciamento no painel de **Estado de servidor de gerenciamento** e no painel de **Exibição de detalhes** confirme que o valor da propriedade **URI do serviço de autenticação** corresponde a ID do espaço de trabalho do OMS.

    ![OMS-OpsMgr-MG-authsvcuri-Property-MS](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)


## <a name="remove-integration-with-oms"></a>Remover a integração com o OMS
Quando você não precisa mais integração entre o seu grupo de gerenciamento do Operations Manager e o espaço de trabalho OMS, há várias etapas necessárias para remover corretamente a conexão e a configuração do grupo de gerenciamento. O procedimento a seguir terão que você atualizar seu espaço de trabalho do OMS excluindo a referência do seu grupo de gerenciamento, excluir os conectores OMS e exclua os pacotes de gerenciamento OMS de suporte.   

1.  Abra o Shell de comando do Operations Manager com uma conta que seja um membro da função administradores de Gerenciador de operações.

    >[AZURE.WARNING] Verifique se você não tem quaisquer pacotes de gerenciamento personalizados com o word Supervisor ou IntelligencePack no nome antes de prosseguir, caso contrário, as etapas a seguir os excluirá do grupo de gerenciamento.

2.  No prompt de comando shell, digite`Get-SCOMManagementPack -name "*advisor*" | Remove-SCOMManagementPack`

3.  Próximo tipo,`Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack`

4.  Abra o console de operações do Operations Manager com uma conta que é um membro da função administradores de Gerenciador de operações.
5.  Em **Administração**, selecione o nó de **Pacotes de gerenciamento** e no **Procure:** digite **Advisor** e verifique se os seguintes pacotes de gerenciamento ainda serão importados em seu grupo de gerenciamento:

    - Microsoft System Center Advisor
    - Microsoft System Center Advisor interno

6. No portal do OMS, clique no bloco **configurações** .
7.  Selecione **conectado fontes**.
8.  Na tabela na seção System Center Operations Manager, você deve ver o nome do grupo de gerenciamento que você deseja remover do espaço de trabalho.  Em coluna **Última dados**, clique em **Remover**.  

    >[AZURE.NOTE] **Remover** link não serão disponível até depois de 14 dias se há nenhuma atividade detectada do grupo de gerenciamento conectados.  
   
9.  Uma janela aparecerá pedindo para você confirmar que você deseja continuar com a remoção.  Clique em **Sim** para continuar. 

Para excluir os dois conectores - Microsoft.SystemCenter.Advisor.DataConnector e Supervisor de conector, salve o script PowerShell abaixo no seu computador e execute usando os exemplos a seguir.

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

>[AZURE.NOTE] O computador que você execute esse script a partir de, se não for um servidor de gerenciamento, deve ter o shell de comando Operations Manager 2012 SP1 ou R2 instalado dependendo da versão do seu grupo de gerenciamento.

```
    `param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

No futuro se você planeja reconectando seu grupo de gerenciamento para um espaço de trabalho do OMS, você precisará reimportar a `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` arquivo de pacote de gerenciamento do mais recente pacote cumulativo de atualizações aplicado ao seu grupo de gerenciamento.  Você pode encontrar este arquivo na `%ProgramFiles%\Microsoft System Center 2012` ou o `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` pasta.

## <a name="next-steps"></a>Próximas etapas

- [Soluções de adicionar a análise de Log da Galeria de soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e reunir dados.
- [Definir configurações de proxy e firewall na análise de Log](log-analytics-proxy-firewall.md) se sua organização usa um servidor proxy ou firewall para que agentes possam se comunicar com o serviço de análise de Log.
