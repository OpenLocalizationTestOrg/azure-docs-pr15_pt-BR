<properties 
    pageTitle="Criar e gerenciar conexões de híbrido | Microsoft Azure" 
    description="Saiba como criar uma conexão de híbrido, gerenciar a conexão e instalar o Gerenciador de Conexão híbrido. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Criar e gerenciar conexões de híbrido


## <a name="overview-of-the-steps"></a>Visão geral das etapas
1. Crie uma Conexão de híbrido digitando o **nome do host** ou o **FQDN** do recurso local na sua rede privada.
2. Vincule seus aplicativos web Azure ou aplicativos móveis do Azure para a Conexão de híbrido.
3. Instalar o Gerenciador de Conexão de híbrido em seu recurso local e conecte à Conexão híbrido específico. O portal do Azure fornece uma experiência de único clique para instalar e conectar.
4. Gerencie conexões de híbrido e suas chaves de conexão.

Este tópico lista estas etapas. 

> [AZURE.IMPORTANT] É possível definir um ponto de extremidade de Conexão híbrido para um endereço IP. Se você usar um endereço IP, você pode ou não pode alcançar o recurso de locais, dependendo do seu cliente. A Conexão de híbrido depende o cliente fazendo uma pesquisa de DNS. Na maioria dos casos, o __cliente__ é o código do seu aplicativo. Se o cliente não executará uma pesquisa DNS, (ela não tentar resolver o endereço IP como se fosse um nome de domínio (x.x.x. x)), e em seguida, o tráfego não é enviado por meio de Conexão a híbrido.
>
> Por exemplo (pseudocódigo), você pode definir **10.4.5.6** como seu host de locais:
> 
> **O cenário a seguir funciona:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **O cenário a seguir não funciona:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Criar uma Conexão de híbrido

Uma Conexão de híbrido podem ser criada no portal do Azure usando aplicativos Web **ou** usando os serviços do BizTalk. 

**Para criar conexões de híbrido usando Web Apps**, consulte [Conectar o Azure Web Apps para um recurso local ativado](../app-service-web/web-sites-hybrid-connection-get-started.md). Você também pode instalar o Gerenciador de Conexão de híbrido (HCM) de seu aplicativo web, que é o método preferencial. 

**Para criar conexões de híbrido nos serviços do BizTalk**:

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Serviços BizTalk** e selecione seu BizTalk Service. 

    Se você não tiver um Services BizTalk existente, você pode [criar um serviço BizTalk](biztalk-provision-services.md).
3. Selecione a guia **Conexões híbrido** :  
![Guia de conexões híbrido][HybridConnectionTab]

4. Selecione **criar uma Conexão de híbrido** ou selecione o botão **Adicionar** na barra de tarefas. Insira o seguinte:

    Propriedade | Descrição
--- | ---
Nome | O nome de Conexão híbrido deve ser exclusivo e não pode ser o mesmo nome que o BizTalk Service. Você pode inserir qualquer nome mas seja específico com sua finalidade. Alguns exemplos incluem:<br/><br/>Folha de pagamentos*SQL Server*<br/>SupplyList*SharepointServer*<br/>Clientes*OracleServer*
Nome do host | Insira o nome de host totalmente qualificado, somente o nome do host ou o endereço IPv4 do recurso local. Alguns exemplos incluem:<br/><br/>mySQLServer<br/>*mySQLServer*. *Domínio*. corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *yourCompany*.com<br/>10.100.10.10<br/><br/>Se você usar o endereço IPv4, observe que o código do seu cliente ou aplicativo pode não resolver o endereço IP. Consulte a observação importante na parte superior deste tópico.
Porta | Insira o número da porta do recurso local. Por exemplo, se você estiver usando aplicativos Web, insira a porta 80 ou 443. Se você estiver usando o SQL Server, insira a porta 1433.

5. Selecione a marca de seleção para concluir a configuração. 

#### <a name="additional"></a>Adicionais

- Várias conexões híbrido pode ser criado. Consulte o [Serviços BizTalk: gráfico das edições](biztalk-editions-feature-chart.md) para o número de conexões permitidas. 
- Cada Conexão híbrido é criado com um par de cadeias de caracteres de conexão: aplicativo chaves que chaves de envio e no local que ESCUTAM. Cada par tem um principal e uma chave secundária. 


## <a name="LinkWebSite"></a>Vincular seu Azure serviço de aplicativo Web App ou o aplicativo móvel

Para vincular um aplicativo Web ou aplicativo móvel em um serviço de aplicativo do Azure uma Conexão híbrido existente, selecione **usar uma Conexão de híbrido existente** na lâmina híbrido conexões. Consulte [acesso recursos usando conexões híbrido no serviço de aplicativo do Azure local](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Instalar o Gerenciador de Conexão de híbrido local

Após uma Conexão híbrido é criado, instale o Gerenciador de Conexão híbrido no recurso local. Ele pode ser baixado de seus aplicativos web Azure ou de seu BizTalk Service. Etapas de Serviços BizTalk: 

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Serviços BizTalk** e selecione seu BizTalk Service. 
3. Selecione a guia **Conexões híbrido** :  
![Guia de conexões híbrido][HybridConnectionTab]
4. Na barra de tarefas, selecione **Configuração de local**:  
![Configuração de local][HCOnPremSetup]
5. Selecione **instalar e configurar** para executar ou baixar o Gerenciador de Conexão de híbrido no sistema local. 
6. Selecione a marca de seleção para iniciar a instalação. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicionais
- Gerenciador de Conexão de híbrido pode ser instalado nos seguintes sistemas operacionais:

    - Windows Server 2008 R2 (.NET Framework 4,5 + e Windows Management Framework 4.0 + obrigatório)
    - Windows Server 2012 (estrutura de gerenciamento do Windows 4.0 + obrigatório)
    - Windows Server 2012 R2


- Depois de instalar o Gerenciador de Conexão híbridos, ocorre o seguinte: 

    - A Conexão de híbrido hospedado no Azure é configurado automaticamente para usar a cadeia de Conexão do aplicativo principal. 
    - O recurso local ativado automaticamente está configurado para usar a cadeia de Conexão diante local principal.

- O Gerenciador de Conexão híbrido deve usar uma cadeia de conexão válida no local para autorização. Os aplicativos do Azure Web ou Mobile deve usar uma cadeia de conexão do aplicativo válido para autorização.
- Você pode dimensionar híbrido conexões instalando outra instância do Gerenciador de Conexão híbrido em outro servidor. Configure o ouvinte local para usar o mesmo endereço como o primeiro ouvinte de local. Nessa situação, o tráfego é distribuído aleatoriamente (round robin) entre os ouvintes ativo no local. 


## <a name="ManageHybridConnection"></a>Gerenciar conexões de híbrido
Para gerenciar suas conexões híbridos, você pode:

- Usar o portal do Azure e vá para seu BizTalk Service. 
- Use [APIs REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copiar/recriar as cadeias de caracteres de Conexão híbrido

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Serviços BizTalk** e selecione seu BizTalk Service. 
3. Selecione a guia **Conexões híbrido** :  
![Guia de conexões híbrido][HybridConnectionTab]
4. Selecione a Conexão de híbrido. Na barra de tarefas, selecione **Gerenciar Conexão**:  
![Gerenciar as opções][HCManageConnection]

    **Gerenciar Conexão** lista as cadeias de caracteres de conexão do aplicativo e locais. Você pode copiar as cadeias de caracteres de Conexão ou gerar novamente a chave de acesso usado na cadeia de conexão. 

    **Se você selecionar recriar**, a chave de acesso compartilhado usada dentro da cadeia de Conexão é alterado. Faça o seguinte:
    - No portal do Azure clássico, selecione **Chaves de sincronização** no aplicativo do Azure.
    - Execute novamente a **Instalação local**. Quando você executa novamente a configuração de local ativado, o recurso de local é automaticamente configurado para usar a cadeia de conexão primária atualizado.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Política de grupo de uso para controlar os recursos de locais usados por uma Conexão de híbrido

1. Baixe os [modelos administrativos do Gerenciador de Conexão de híbrido](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extrai os arquivos.
3. No computador que modifica a política de grupo, faça o seguinte:  

    - Copiar o. Arquivos ADMX para a pasta *%WINROOT%\PolicyDefinitions* .
    - Copiar o. Arquivos ADML à pasta *%WINROOT%\PolicyDefinitions\en-us* .

Depois de copiada, você pode usar o Editor de política de grupo para alterar a política.




## <a name="next"></a>Próximo

[Conectar aos aplicativos Web Azure para um recurso de local](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Conectar ao local SQL Server no Azure Web Apps](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Visão geral de conexões de híbrido](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Consulte também

[API REST para gerenciar serviços BizTalk no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Serviços do BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md)  
[Criar um BizTalk Service usando Azure portal clássico](biztalk-provision-services.md)  
[Serviços BizTalk: Guias de painel, o Monitor e a escala](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
