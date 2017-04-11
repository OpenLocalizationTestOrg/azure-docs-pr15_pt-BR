<properties
    pageTitle="Visão geral de conexões de híbrido | Microsoft Azure"
    description="Saiba mais sobre conexões híbridos, segurança, portas TCP e configurações suportadas. MABS, WABS."
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
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Visão geral de conexões de híbrido
Introdução às conexões de híbrido, mostra as configurações suportadas e lista as portas TCP necessárias.


## <a name="what-is-a-hybrid-connection"></a>O que é uma conexão de híbrido

Híbrido conexões são um recurso do Azure BizTalk Services. Conexões de híbrido oferecem uma maneira fácil e conveniente para conectar-se o recurso de aplicativos Web do serviço de aplicativo do Azure (antigo sites) e o recurso de aplicativos Mobile no serviço de aplicativo do Azure (anteriormente serviços de celular) aos recursos de locais por trás do firewall.

![Conexões de híbrido][HCImage]

Os benefícios de conexões híbrido incluem:

- Aplicativos Web e aplicativos Mobile podem acessar dados locais e serviços existentes com segurança.
- Vários aplicativos da Web ou Mobile pode compartilhar uma Conexão de híbrido para acessar um recurso local.
- Mínimo portas TCP são necessárias para acessar a rede.
- Aplicativos que usam conexões híbrido acessam somente o recurso de locais específicos que é publicado por meio da Conexão híbrido.
- Pode se conectar a qualquer recurso local que usa uma porta TCP estática, como o SQL Server, MySQL, APIs de Web HTTP e a maioria dos serviços da Web personalizado.

    > [AZURE.NOTE] Serviços baseados em TCP que usam portas dinâmicas (como modo passivo FTP ou modo passivo estendido) não são suportados no momento. LDAP também não é suportada. LDAP usa uma porta TCP estática, mas ele também poderia usar UDP. Como resultado, ela não é suportada.

- Pode ser usado com todas as estruturas suportadas pelo Web Apps (.NET, PHP, Java, Python, node) e os aplicativos de Mobile (Node, .NET).
- Web Apps e aplicativos Mobile podem acessar recursos no local exatamente da mesma forma como se a Web ou o aplicativo Mobile está localizado em sua rede local. Por exemplo, o mesma conexão sequência usada local também pode ser usado no Azure.


Conexões de híbrido também fornecem administradores de empresa com controle e visibilidade sobre os recursos corporativos acessados por aplicativos híbridos, incluindo:

- Configurações de política de grupo, os administradores podem permitir conexões de híbrido na rede e também designar recursos que podem ser acessados por aplicativos híbridos.
- Logs de eventos e auditoria na rede corporativa fornecem visibilidade os recursos acessados por conexões híbrido.


## <a name="example-scenarios"></a>Cenários de exemplo

Conexões de híbrido suportam as seguintes combinações de estrutura e o aplicativo:

- Acesso do .NET framework para SQL Server
- Acesso do .NET framework para serviços HTTP/HTTPS com WebClient
- Acesso PHP ao SQL Server, MySQL
- Acesso de Java ao SQL Server, MySQL e Oracle
- Acesso de Java aos serviços HTTP/HTTPS

Ao usar conexões híbrido para acessar o local SQL Server, considere o seguinte:

- Express chamado instâncias do SQL deverá ser configuradas para usar portas estáticas. Por padrão, SQL Express instâncias nomeadas usar portas dinâmicas.
- Instâncias padrão do SQL Express usam uma porta estática, mas TCP deve ser habilitado. Por padrão, TCP não está habilitado.
- Ao usar cluster ou grupos de disponibilidade, o `MultiSubnetFailover=true` modo não é suportado atualmente.
- O `ApplicationIntent=ReadOnly` não é suportado atualmente.
- Autenticação do SQL podem ser necessária como o método de autorização de ponta a ponta com suporte no aplicativo do Azure e o SQL server local.


## <a name="security-and-ports"></a>Portas e segurança

Conexões de híbrido usam autorização de assinatura de acesso compartilhado (SAS) para proteger as conexões entre os aplicativos do Azure e o Gerenciador de Conexão de híbrido local e a Conexão híbrido. Chaves de conexão separadas são criadas para o aplicativo e o Gerenciador de Conexão de híbrido local. Essas chaves de conexão podem ser acumulados e revogados independentemente.

Conexões de híbrido oferecem para distribuição integral e segura das chaves para os aplicativos e o Gerenciador de Conexão de híbrido local.

Consulte [criar e gerenciar conexões de híbrido](integration-hybrid-connection-create-manage.md).

*Autorização de aplicativo é separada da Conexão híbrido*. Qualquer método de autorização apropriada pode ser usado. O método de autorização depende os métodos de autorização de ponta a ponta com suporte em nuvem Azure e os componentes de local. Por exemplo, seu aplicativo do Azure acessa um SQL Server local. Neste cenário, autorização de SQL pode ser o método de autorização que é suportado ponta a ponta.

#### <a name="tcp-ports"></a>Portas TCP
Conexões de híbrido requerem conectividade de TCP ou HTTP somente saída da sua rede privada. Você não precisa abrir quaisquer portas do firewall ou altere a configuração de perímetro de rede para permitir que qualquer conectividade de entrada na sua rede.

As seguintes portas TCP são usadas pelo híbrido conexões:

Porta | Por que você precisa
--- | ---
9350 - 9354 | Essas portas são usadas para transmissão de dados. O Gerenciador de retransmissão barramento de serviço testes porta 9350 para determinar se a conectividade TCP está disponível. Se estiver disponível, ele pressupõe que porta 9352 também está disponível. Tráfego de dados apresenta porta 9352. <br/><br/>Permita conexões de saída para essas portas.
5671 | Quando a porta 9352 é usada para tráfego de dados, porta 5671 é usada como o canal de controle. <br/><br/>Permita conexões de saída para esta porta.
80, 443 | Essas portas são usadas para algumas solicitações de dados para o Azure. Além disso, se as portas 9352 e 5671 não são úteis, *então* as portas 80 e 443 estão fallback portas usadas para transmissão de dados e o canal de controle.<br/><br/>Permita conexões de saída para essas portas. <br/><br/>**Observação** Não é recomendável usar esses das portas de fallback no lugar de outras portas TCP. O HTTP/WebSocket é usado como o protocolo em vez de TCP nativo para canais de dados. Ele pode resultar em desempenho inferior.



## <a name="next-steps"></a>Próximas etapas

[Criar e gerenciar conexões de híbrido](integration-hybrid-connection-create-manage.md)<br/>
[Conectar aos aplicativos Web Azure para um recurso de local](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Conectar ao local SQL Server de um aplicativo web do Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Consulte também

[API REST para gerenciar serviços BizTalk no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[Serviços BizTalk: edições gráfico](biztalk-editions-feature-chart.md)<br/>
[Criar um BizTalk Service usando portal do Azure](biztalk-provision-services.md)<br/>
[Serviços BizTalk: Guias de painel, o Monitor e a escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
