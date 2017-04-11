<properties 
    pageTitle="Usando o Gerenciador de Conexão híbrido | Microsoft Azure" 
    description="Instalar e configurar o Gerenciador de Conexão híbrido e conecte aos conectores de locais nos aplicativos de lógica" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Conectar-se aos conectores de locais usando o Gerenciador de Conexão híbrido

>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica. Disponibilidade geral de aplicativos de lógica (GA) usa um gateway para conectividade de local. Leia mais sobre o novo [gateway](app-service-logic-gateway-connection.md) e [Lógica aplicativos GA](https://azure.microsoft.com/documentation/services/logic-apps/).

Para usar um sistema de local, aplicativos de lógica usa o Gerenciador de Conexão de híbrido. Alguns conectores podem se conectar a um sistema local, como o SQL Server, SAP, SharePoint e assim por diante. 

O Gerenciador de Conexão de híbrido (HCM) é um clique-uma vez installer instalada em um servidor IIS em sua rede, atrás do firewall. Usando uma retransmissão de barramento de serviço do Azure, HCM autentica o sistema local com o conector no Azure. 

> [AZURE.NOTE] Gerenciador de Conexão de híbrida é necessário somente se você estiver se conectando a um recurso local por trás do firewall. Se você não estiver se conectando a um sistema local, você não precisa o Gerenciador de Conexão híbrido.

Para começar, você precisa:

- Azure barramento de serviço retransmissão namespace SAS conexão cadeia de caracteres. Consulte [Preços de serviço de barramento](https://azure.microsoft.com/pricing/details/service-bus/) para determinar qual nível inclui retransmissões.
- Local entrar informações do sistema, incluindo o nome de usuário e senha. Por exemplo, se você estiver se conectando a um SQL Server local, você precisará a conta de logon do SQL Server e a senha.
- Informações do servidor local, incluindo o nome de servidor e número da porta. Por exemplo, se você estiver se conectando a um SQL Server local, será necessário o nome do SQL Server e o número da porta TCP.

## <a name="get-the-service-bus-connection-string"></a>Obter a cadeia de Conexão do barramento de serviço

No portal do Azure, copie a raiz de barramento de serviço SAS cadeia de Conexão. Essa cadeia de caracteres de conexão se conecta a seu conector Azure ao seu sistema local. 

1. No [portal de clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), selecione seu namespace barramento de serviço e selecione **Informações de Conexão**:

    ![][SB_ConnectInfo]

2. Copie a cadeia de Conexão SAS:

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Instalar o Gerenciador de Conexão híbrido

1. No [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selecione o conector que você criou. Para abri-lo, você pode selecione **Procurar**, selecione **Aplicativos de API**e selecione o conector ou API App. 
<br/><br/>
Na **Conexão de híbrido**, a configuração é **incompleta**:
<br/>
![][2] 

2. Selecione a **Conexão de híbrido**. A cadeia de conexão de serviço barramento inseridos anteriormente será listada.
3. Copie a **cadeia de caracteres de configuração principais**:
<br/>
![][PrimaryConfigString]

4. Em **Gerenciador de Conexão de híbrido local**, você pode baixar o Gerenciador de Conexão híbrido ou instalá-lo diretamente a partir do portal. 
<br/><br/>
Para instalar diretamente do portal, vá ao seu servidor IIS local, navegue até o portal e selecione **baixar e configurar**.
<br/><br/>
Para baixar o Gerenciador de Conexão híbridos, vá para seu servidor IIS local e vá para o **aplicativo ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). A instalação é iniciado automaticamente para que você possa executá-lo.

5. Na janela do **Programa de instalação do ouvinte** , insira a **Cadeia de caracteres de configuração primária** colado anteriormente (na etapa 3) e selecione **instalar**.

Quando a instalação estiver concluída, exibe o seguinte:
<br/>
![][3] 

Agora quando você navega ao conector novamente, o status de conexão híbrido está **conectado**. Você pode ter fechar o conector e reabri-lo:
<br/>
![][4] 

> [AZURE.NOTE] Para alternar para a cadeia de conexão secundária, execute novamente a configuração híbrida Conexão e insira a **Cadeia de caracteres de configuração secundário**.


## <a name="tcp-ports-and-security"></a>Portas TCP e segurança

Quando você cria uma conexão de híbrido, um site é criado no servidor IIS local no local. O servidor IIS pode estar em um DMZ. Os requisitos de porta TCP no servidor IIS incluem:

Portas TCP | Por que
--- | ---
 | Nenhuma entradas portas TCP são necessárias.
9350 - 9354 | Essas portas são usadas para transmissão de dados. O Gerenciador de retransmissão barramento de serviço testes porta 9350 para determinar se a conectividade TCP está disponível. Se estiver disponível, ele pressupõe que porta 9352 também está disponível. Tráfego de dados apresenta porta 9352. <br/><br/>Permita conexões de saída para essas portas.
5671 | Quando a porta 9352 é usada para tráfego de dados, porta 5671 é usada como o canal de controle. <br/><br/>Permita conexões de saída para esta porta. 
80, 443 | Se as portas 9352 e 5671 não são úteis, *então* as portas 80 e 443 estão as portas fallback usadas para transmissão de dados e o canal de controle.<br/><br/>Permita conexões de saída para essas portas.
Porta de sistema local | No sistema local, abra a porta usada pelo sistema. Por exemplo, o SQL Server normalmente usa porta 1433. Abra esta porta TCP.

[Hospedagem atrás de um Firewall com barramento de serviço](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Solução de problemas

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Solução de problemas no local

1. No servidor IIS, confirme a função do IIS web está instalada e todos os serviços do IIS são iniciados.
2. No servidor IIS, confirme o Gerenciador de Conexão híbrida é instalado e em execução:
 - No Gerenciador do IIS (inetmgr), o site ***MicrosoftAzureBizTalkHybridListener*** deve estar listado e estar em execução. 
 - Este site usa o ***HybridListenerAppPool*** que é executado como a conta de usuário interno local *NetworkService* . Este AppPool também deve ser iniciado.
3. No servidor IIS, confirme que o conector é instalado e em execução: 
 - Um site é criado para o conector. Por exemplo, se você criou um conector SQL, há um site ***MicrosoftSqlConnector_nnn*** . No Gerenciador do IIS (inetmgr), confirme este site está listado e iniciado. 
 - Este site usa seu próprio pool de aplicativos do IIS denominado ***HybridAppPoolnnn***. Este AppPool é executado como a conta de usuário interno local *NetworkService* . Este site e AppPool devem ambos ser iniciado. 
 - Procure o conector de local. Por exemplo, se seu site de conector usa a porta 6569, navegue até http://localhost:6569. Um documento padrão não estiver configurado para uma `HTTP Error 403.14 - Forbidden error` é esperada.
4. Em seu firewall, confirme as portas TCP listados neste tópico estão abertas.
5. Examine o sistema de origem ou de destino:
 - Alguns sistemas locais requerem arquivos de dependência adicionais. Por exemplo, se você estiver se conectando ao local SAP, alguns arquivos adicionais do SAP devem ser instalados no servidor IIS.
 - Verificar a conectividade com o sistema com a conta de logon. Por exemplo, a porta TCP usada pelo sistema deve ser aberta, como a porta 1433 para o SQL Server. A conta de logon inseridas no portal do Azure deve ter acesso ao sistema.
6. No servidor IIS, verifique os logs de eventos de erros. 
7. Limpeza e reinstale o Gerenciador de Conexão híbrido: 
 - No IIS, exclua manualmente o site conector e seu pool de aplicativos. 
 - Execute novamente o Gerenciador de Conexão híbrido e confirme a que inserir a correta **Primária cadeia de caracteres de configuração** para o conector.



### <a name="in-the-azure-classic-portal"></a>No portal de clássico do Azure

1. Confirme que o namespace barramento de serviço tem um estado **ativo** .
2. Quando você cria o conector, insira a cadeia de conexão de serviço barramento SAS. Não digite a cadeia de conexão do ACS.


## <a name="faq"></a>Perguntas Freqüentes

**Pergunta**: há dois gerenciadores de Conexão híbrido. Qual é a diferença? 

**Resposta**: há a tecnologia de [Conexões híbrido](../biztalk-services/integration-hybrid-connection-overview.md) que é usada principalmente por Web Apps (antigo sites) e aplicativos de Mobile (serviços antigo móveis) para se conectar ao local. Este Gerenciador de conexões híbrida é sua própria [configuração](../biztalk-services/integration-hybrid-connection-create-manage.md) e usa um serviço de BizTalk Azure (os bastidores). Ele oferece suporte apenas protocolos TCP e HTTP.

Com conectores de serviço de aplicativo do Azure, também temos um gerente de Conexão híbrido.  Este Gerenciador de Conexão híbrido faz *não* usar um BizTalk Azure do serviço (os bastidores) e suporta mais do que os protocolos TCP e HTTP. Consulte a [lista de aplicativos de API e conectores](app-service-logic-connectors-list.md).

Ambos usam barramento de serviço do Azure para conexão com o sistema local.

**Pergunta**: ao criar uma App API personalizado, posso usar o Gerenciador de Conexão do aplicativo serviço híbrido para se conectar ao local? 

**Resposta**: não no sentido tradicional. Você pode usar um conector interno, configure o Gerenciador de Conexão do aplicativo serviço híbrido para se conectar ao sistema local. Em seguida, use esse conector com seu personalizado API App, possivelmente usando um aplicativo de lógica. Atualmente, você não pode desenvolver ou criar sua própria híbrido API App (como o conector SQL ou arquivo).

Se a sua API personalizado usa uma porta TCP ou HTTP, você pode usar [Conexões híbrido](../biztalk-services/integration-hybrid-connection-overview.md) e seu gerente de Conexão de híbrido. Neste cenário, um serviço de BizTalk Azure é usado. [Conectar-se a no SQL Server local de um aplicativo web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) pode ajudar.  


## <a name="read-more"></a>Leia mais

[Monitore seus aplicativos de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Barramento preços do serviço](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
