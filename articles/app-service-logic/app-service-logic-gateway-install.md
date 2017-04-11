<properties
   pageTitle="Aplicativos de lógica instalar o gateway de dados locais | Microsoft Azure"
   description="Informações sobre como instalar o gateway de dados locais para uso em um aplicativo de lógica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Instalar o gateway de dados local para aplicativos de lógica

## <a name="installation-and-configuration"></a>Instalação e configuração

### <a name="prerequisites"></a>Pré-requisitos

Mínimo:

* 4,5 do .NET framework
* versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

Recomendado:

* CPU core 8
* 8 GB de memória
* versão de 64 bits do Windows 2012 R2 (ou posterior)

Considerações relacionadas:

* Você não pode instalar um gateway em um controlador de domínio.
* Você não deve instalar um gateway em um computador, um laptop, que pode ser desativado, asleep, ou não conectado à Internet, porque o gateway não é possível executar qualquer uma nessas circunstâncias. Além disso, o desempenho do gateway pode sofrer em uma rede sem fio.

### <a name="install-a-gateway"></a>Instalar um gateway

Você pode obter o [instalador para o gateway de dados locais aqui](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Especifique **o gateway de dados local** como o modo, entrar com seu trabalho ou escola conta e, em seguida, configurar um novo gateway ou migrar, restaurar ou assumir um gateway existente.

* Para configurar um gateway, digite um **nome** para ele e uma **chave de recuperação**e clique ou toque em **Configurar**.

    Especifique uma chave de recuperação que contém pelo menos oito caracteres e mantê-lo em um lugar seguro. Você precisará esta tecla se desejar migrar, restaurar ou assumir seu gateway.

* Para migrar, restaurar ou assumir um gateway existente, forneça a chave de recuperação que foi especificada quando o gateway foi criado.

### <a name="restart-the-gateway"></a>Reinicie o gateway

O gateway é executado como um serviço do Windows e, como com qualquer outro serviço do Windows, você pode iniciar e pará-lo de várias maneiras. Por exemplo, você pode abrir um prompt de comando com permissões elevadas na máquina onde o gateway está sendo executado e execute um desses comandos:

* Para interromper o serviço, execute este comando:

    `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurar um proxy ou firewall

Para obter informações sobre como fornecer informações de proxy para seu gateway, consulte [definir configurações de proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Você pode verificar se seu firewall ou proxy, pode estar bloqueando conexões executando o seguinte comando em um prompt de PowerShell. Isto irá testar a conectividade com o barramento de serviço do Azure. Nesse somente testa a conectividade de rede e não tem algo a ver com o serviço de nuvem do servidor ou o gateway. Ele ajuda a determinar se seu computador possa acessá-lo check-out na Internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Os resultados devem ser semelhantes neste exemplo. Se **TcpTestSucceeded** não for verdadeira, você pode estar bloqueado por um firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se você quiser ser completa, substitua os valores **ComputerName** e **porta** com aqueles listados em [Configurar portas](#configure-ports) posteriormente neste tópico.

O firewall também pode estar bloqueando as conexões que o barramento de serviço do Azure faz para os centros de dados do Azure. Se esse for o caso, você desejará branca (desbloquear) todos os endereços IP para a sua região para os centros de dados. Você pode obter uma lista de [endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurar as portas

O gateway cria uma conexão de saída para barramento de serviço do Azure. Ele se comunica com portas de saída: TCP 443 (padrão), 5671, 5672, 9350 via 9354. O gateway não exige portas de entrada.

Saiba mais sobre [as soluções híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMES DE DOMÍNIO | PORTAS DE SAÍDA | DESCRIÇÃO |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671-5672 | Enfileiramento avançado de protocolo (AMQP) |
| *. servicebus.windows.net | 443, 9350-9354 | Escutas no serviço de retransmissão de barramento sobre TCP (requer 443 para aquisição de token de controle de acesso) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 443 | Usado para testar a conectividade com a internet se o gateway está inacessível pelo serviço Power BI. |

Se você precisar endereços IP de lista branca em vez dos domínios, você pode baixar e usar a [lista de intervalos de IP do Microsoft Azure data center](https://www.microsoft.com/download/details.aspx?id=41653). Em alguns casos, as conexões de barramento de serviço do Azure serão feitas com o endereço IP em vez de nomes de domínio totalmente qualificado.

### <a name="sign-in-account"></a>Conta de entrada

Os usuários serão entrar com seja um trabalho ou escola conta. Esta é sua conta de organização. Se você optou por uma oferta do Office 365 e não fornece o seu email do trabalho real, ele pode parecer com jeff@contoso.onmicrosoft.com. Sua conta, dentro de um serviço de nuvem, é armazenada em um locatário do Azure Active Directory (AAD). Na maioria dos casos, UPN da sua conta AAD correspondem o endereço de email.

### <a name="windows-service-account"></a>Conta de serviço do Windows

O gateway de dados local está configurado para usar SERVICE\PBIEgwService NT para a credencial de logon de serviço do Windows. Por padrão, ela tem direita do Log em como um serviço. Isso é no contexto da máquina em que você está instalando o gateway.

Isso não é a conta usada para se conectar a fontes de dados locais ou a conta corporativa ou escolar com o qual você entrar em serviços em nuvem.

##<a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="general"></a>Geral

**Pergunta**: quais fontes de dados o gateway suporta?<br/>
**Resposta**: como este escrevendo, SQL Server.

**Pergunta**: eu preciso de um gateway para fontes de dados na nuvem, como o SQL Azure? <br/>
**Resposta**: não. Um gateway conecta-se a fontes de dados locais somente.

**Pergunta**: o que é o serviço do Windows real chamado?<br/>
**Resposta**: em serviços, o gateway se chama o serviço de Gateway do Power BI Enterprise.

**Pergunta**: existem quaisquer conexões de entrada para o gateway da nuvem? <br/>
**Resposta**: não. O gateway usa conexões de saída para barramento de serviço do Azure.

**Pergunta**: o que acontece se eu bloquear conexões de saída? O que eu preciso para abrir? <br/>
**Resposta**: consulte as portas e os hosts que usa o gateway.


**Pergunta**: o gateway precisa estar instalado na mesma máquina como fonte de dados? <br/>
**Resposta**: não. O gateway se conectará à fonte de dados usando as informações de conexão que foi fornecidas. Pense do gateway como um aplicativo cliente nesse sentido. Ele apenas precisa ser capaz de se conectar ao nome do servidor que foi fornecido.


**Pergunta**: qual é a latência para execução de consultas à fonte de dados do gateway? Qual é a melhor arquitetura? <br/>
**Resposta**: para reduzir a latência de rede, instalar o gateway mais próximo a fonte de dados possível. Se você pode instalar o gateway na fonte de dados reais, ele minimizará a latência introduzida. Considere também os centros de dados. Por exemplo, se seu serviço está usando o Centro de dados Oeste EUA e você tiver hospedado em um máquina virtual do Azure do SQL Server, você precisa ter a máquina virtual de Azure Oeste EUA também. Isso minimizará a latência e evitar encargos de egresso na máquina virtual do Azure.


**Pergunta**: há quaisquer requisitos de largura de banda de rede? <br/>
**Resposta**: é recomendável têm boa taxa de transferência para sua conexão de rede. Cada ambiente é diferente, e a quantidade de dados que está sendo enviados afetará os resultados. Usando a rota expressa pode ajudar a garantir um nível de produtividade entre locais e os centros de dados do Azure.

Você pode usar o aplicativo de teste de velocidade do Azure ferramenta de terceiros para ajudar a avaliar o que é a taxa de transferência.


**Pergunta**: pode executar o gateway serviço Windows com uma conta do Active Directory do Azure? <br/>
**Resposta**: não. O serviço do Windows deve ter uma conta do Windows válida. Por padrão, ele será executado com o SID de serviço, NT SERVICE\PBIEgwService.


**Pergunta**: como os resultados são enviados para a nuvem? <br/>
**Resposta**: isso é feito por meio do barramento de serviço do Azure. Para obter mais informações, consulte como funciona.


**Pergunta**: onde minhas credenciais são armazenados? <br/>
**Resposta**: as credenciais que você insere para uma fonte de dados são armazenadas criptografados no serviço de nuvem do gateway. As credenciais são descriptografar no gateway local.

### <a name="high-availabilitydisaster-recovery"></a>Alta disponibilidade/recuperação de dados

**Pergunta**: há planos para habilitar cenários de alta disponibilidade com o gateway? <br/>
**Resposta**: Este é o roteiro, mas nós ainda não tem uma linha do tempo.


**Pergunta**: quais opções estão disponíveis para recuperação de dados? <br/>
**Resposta**: você pode usar a chave de recuperação para restaurar ou mover um gateway. Quando você instala o gateway, especifique a chave de recuperação.


**Pergunta**: qual é o benefício da chave de recuperação? <br/>
**Resposta**: ele oferece uma maneira de migrar ou recuperar suas configurações de gateway após um desastre.

### <a name="troubleshooting"></a>Solução de problemas

**Pergunta**: onde estão os logs de gateway? <br/>
**Resposta**: Consulte ferramentas posteriormente neste tópico.


**Pergunta**: como ver quais consultas estão sendo enviadas para a fonte de dados local? <br/>
**Resposta**: você pode habilitar o rastreamento de consulta, que incluirá as consultas que está sendo enviadas. Lembre-se de alterá-lo novamente para o valor original quando terminado de solução de problemas. Deixar consulta rastreamento habilitado fará com que os logs sejam maiores.

Você também pode examinar ferramentas sua fonte de dados com consultas de rastreamento. Por exemplo, você pode usar eventos estendido ou criador de perfil do SQL para SQL Server e o Analysis Services.

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Quando um usuário interage com um elemento que está conectado a uma fonte de dados local:

1. O serviço de nuvem cria uma consulta, juntamente com as credenciais criptografadas da fonte de dados e envia a consulta para a fila do gateway processar.
1. O serviço analisa a consulta e envia a solicitação para o barramento de serviço do Azure.
1. O gateway de dados locais controla o barramento de serviço do Azure para solicitações pendentes.
1. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
1. O gateway envia a consulta à fonte de dados para execução.
1. Os resultados são enviados da fonte de dados para o gateway e então em serviço de nuvem. O serviço usa os resultados.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="update-to-the-latest-version"></a>Atualizar para a versão mais recente

Muitos dos problemas podem expor quando a versão do gateway está desatualizada.  É uma boa prática geral para garantir que você está na versão mais recente.  Se você ainda não atualizou o gateway por mês, ou mais, talvez você queira considere instalar a versão mais recente do gateway e veja se você pode reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Erro: Falha ao adicionar usuário ao grupo. (Usuários de Log de desempenho de PBIEgwService-2147463168)

Você pode receber este erro se você estiver tentando instalar o gateway em um controlador de domínio, que não é compatíveis. Você precisará implantar o gateway em um computador que não seja um controlador de domínio.

## <a name="tools"></a>Ferramentas

### <a name="collecting-logs-from-the-gateway-configurator"></a>Coletar logs de configurator gateway

Você pode coletar vários logs para o gateway. Sempre comece com os logs!

#### <a name="installer-logs"></a>Logs de instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Logs de configuração

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Logs de serviços de gateway do Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Logs de eventos

Os logs de Gateway de gerenciamento de dados e PowerBIGateway estão presentes em **Logs de serviços e aplicativos**.

### <a name="fiddler-trace"></a>Rastreamento de Fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitora o tráfego HTTP.  Você pode ver verso e daí do Power BI de serviço do computador cliente. Isso pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Próximas etapas
- [Criar uma conexão local para aplicativos de lógica](app-service-logic-gateway-connection.md)
- [Recursos de integração do Enterprise](app-service-logic-enterprise-integration-overview.md)
- [Conectores de aplicativos de lógica](../connectors/apis-list.md)