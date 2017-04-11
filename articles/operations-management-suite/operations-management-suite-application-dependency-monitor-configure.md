<properties
   pageTitle="Configurando o Monitor de dependência de aplicativo (ADM) no pacote de gerenciamento de operações (OMS) | Microsoft Azure"
   description="Monitor de dependência de aplicativo (ADM) é uma solução de pacote de gerenciamento de operações (OMS) que detecta componentes de aplicativo em sistemas Windows e Linux automaticamente e mapeia a comunicação entre os serviços.  Este artigo fornece detalhes para implantação ADM em seu ambiente e usá-lo em uma variedade de cenários."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Configurando a solução de Monitor de dependência de aplicativos no pacote de gerenciamento de operações (OMS)
![Ícone de gerenciamento de alerta](media/operations-management-suite-application-dependency-monitor/icon.png) Monitor de dependência de aplicativo (ADM) detecta componentes de aplicativo em sistemas Windows e Linux automaticamente e mapeia a comunicação entre os serviços. Ele permite que você exiba seus servidores conforme você pensar – como sistemas interconectados que oferecem serviços essenciais.  Monitor de dependência de aplicativos mostra conexões entre servidores, processos, e portas em qualquer arquitetura TCP conectados com nenhuma configuração necessária diferente de instalação de um agente.

Este artigo descreve os detalhes de configuração de agentes de Monitor de dependência de aplicativos e integração.  Para obter informações sobre como usar o ADM, consulte [solução de usando o Monitor de dependência de aplicativo no pacote de gerenciamento de operações (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Monitor de dependência de aplicativo está no modo de visualização particular.  Você pode solicitar acesso a visualização particular ADM em [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante a visualização particular, todas as contas OMS têm acesso ilimitado ao ADM.  Nós ADM são gratuitos, mas os dados de análise de Log para os tipos de AdmComputer_CL e AdmProcess_CL irá ser limitados como qualquer outra solução.
>
>Depois de ADM insere demonstração pública, ele estará disponível somente para clientes pagos e gratuitos de visão e análise no OMS preços plano de.  Contas de nível gratuito serão limitadas ao 5 nós ADM.  Se você está participando de visualização particular e não está inscrito no OMS preços plano de quando ADM entra demonstração pública, ADM será desabilitado nesse momento. 



## <a name="connected-sources"></a>Fontes conectadas
A tabela a seguir descreve as fontes conectadas que são suportadas da solução ADM.

| Fonte conectada | Com suporte | Descrição |
|:--|:--|:--|
| [Agentes Windows](../log-analytics/log-analytics-windows-agents.md) | Sim | ADM analisa e coleta de dados de computadores de agente do Windows.  <br><br>Observe que, além do agente OMS, agentes Windows requerem o Microsoft Agent de dependência.  Consulte os [sistemas operacionais suportados](#supported-operating-systems) para uma lista completa de versões do sistema operacional. |
| [Agentes Linux](../log-analytics/log-analytics-linux-agents.md) | Sim | ADM analisa e coleta dados de computadores do agente de Linux.  <br><br>Observe que, além do agente OMS, agentes Linux requerem o Microsoft Agent de dependência.  Consulte os [sistemas operacionais suportados](#supported-operating-systems) para uma lista completa de versões do sistema operacional. |
| [Grupo de gerenciamento do SCOM](../log-analytics/log-analytics-om-agents.md) | Sim | ADM analisa e coleta de dados do Windows e Linux agentes em um grupo de gerenciamento do SCOM conectado. <br><br>Uma conexão direta do computador agente SCOM OMS é necessária. Dados são enviados diretamente do encaminhadas do grupo de gerenciamento do repositório OMS.|
| [Conta de armazenamento do Azure](../log-analytics/log-analytics-azure-storage.md) | Não | ADM coleta dados de computadores do agente, então não há nenhum dado dele para coletar de armazenamento do Azure. |

Observe que ADM só oferece suporte a plataformas de 64 bits.

No Windows, o agente de monitoramento Microsoft (MMA) é usado pelo SCOM e OMS para coletar e enviar dados de monitoramento.  (Esse agente é chamado o agente do SCOM, agente de OMS, MMA ou agente direto, dependendo do contexto.)  SCOM e OMS fornecem diferentes fora as versões de caixa de MMA, mas estas versões podem cada relatório para SCOM, OMS ou ambos.  No Linux, o agente de OMS para Linux reúne e envia monitoramento de dados OMS.  Você pode usar ADM em servidores com agentes direto OMS ou em servidores que estão anexados aos OMS por meio de grupos de gerenciamento do SCOM.  Para esta documentação, faremos referência todos esses agentes – no Linux ou Windows, se conectado a um MG SCOM ou diretamente OMS – como "OMS agente de", a menos que o nome de específicas de implantação do agente é necessário para o contexto.

O agente ADM não transmitir todos os dados em si e ele não exige qualquer alteração às portas ou firewalls.  Dados do ADM sempre são transmitidos pelo agente OMS para OMS, diretamente ou por meio do Gateway OMS.

![Agentes ADM](media/operations-management-suite-application-dependency-monitor/agents.png)

Se você for um cliente SCOM com um grupo de gerenciamento conectado ao OMS:

- Se seus agentes SCOM podem acessar a internet para se conectar ao OMS, nenhuma configuração adicional será necessária.  
- Se seus agentes SCOM não conseguem acessar o OMS pela internet, você precisará configurar o Gateway OMS para trabalhar com o SCOM.
  
Se você estiver usando o agente direto OMS, você precisa configurar o próprio agente de OMS se conecte a OMS ou ao seu Gateway OMS.  O Gateway OMS pode ser baixado do [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Evitando dados duplicados

Se você for um cliente SCOM, você não deve configurar seus agentes SCOM para se comunicar diretamente para OMS ou dado será relatado duas vezes.  ADM, isso resultará em computadores que aparecem duas vezes na lista de máquina.

Configuração do OMS deve ocorrer somente em um dos seguintes locais:

- O painel de pacote de gerenciamento de operações de Console SCOM para computadores gerenciados
- Configuração de ideias operacionais Azure nas propriedades MMA

Usando ambas as configurações com o mesmo espaço de trabalho em cada causará duplicação de dados. Usar ambos com espaços de trabalho diferentes pode resultar em configuração conflitante (aquele com solução ADM habilitado e outro sem) que pode impedir que dados fluindo para ADM completamente.  

Observe que mesmo se a própria máquina não está especificada na configuração de OMS do Console SCOM, se um grupo de instância como "Grupo de instâncias do Windows Server" estiver ativo, ele pode ainda resultar na configuração de OMS recebimento do computador por meio de SCOM.



## <a name="management-packs"></a>Pacotes de gerenciamento
Quando ADM é ativada em um espaço de trabalho do OMS, um 300KB pacote de gerenciamento é enviada para todos os Microsoft monitoramento agentes desse espaço de trabalho.  Se você estiver usando agentes SCOM em um [grupo de gerenciamento conectado](../log-analytics/log-analytics-om-agents.md), o pacote de gerenciamento de ADM será implantado do SCOM.  Se os agentes estiverem conectados diretamente, o MP serão entregues pelo OMS.

O MP é denominado Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Ele é escrito para *%Programfiles%\Microsoft monitoramento Agent\Agent\Health Service State\Management Packs\*.  A fonte de dados usada pelo pacote de gerenciamento é *% Program files%\Microsoft monitoramento Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuração
Além do Windows e Linux computadores têm um agente instalado e conectado ao OMS, o instalador do agente de dependência deve ser baixado da solução ADM e, em seguida, instalado como administrador ou raiz em cada servidor gerenciado.  Quando o agente ADM estiver instalado em um servidor de relatório para OMS, mapas de dependência ADM aparecerão em 10 minutos.  Se você tiver problemas, envie um email [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Migrando do BlueStripe FactFinder
Monitor de dependência de aplicativos fornecerá BlueStripe tecnologia em OMS em fases. FactFinder ainda tem suporte para os clientes existentes, mas não está mais disponível para compra individual.  Esta versão de visualização do agente de dependência só pode se comunicar com OMS.  Se você for um cliente FactFinder atual, identifique um conjunto de servidores de teste para ADM que não são gerenciados pelo FactFinder. 

### <a name="download-the-dependency-agent"></a>Baixar o agente de dependência
Além do agente de gerenciamento da Microsoft (MMA) e OMS Linux agente que fornecem a conexão entre o computador e OMS, todos os computadores analisados pelo Monitor de dependência de aplicativo devem ter o agente de dependência instalado.  No Linux, o agente de OMS para Linux deve ser instalado antes do agente de dependência. 

![Bloco do aplicativo Monitor de dependência](media/operations-management-suite-application-dependency-monitor/tile.png)

Para baixar o agente de dependência, clique em **Configurar solução** no bloco **Monitor de dependência de aplicativos** para abrir a lâmina **Agente de dependência** .  A lâmina agente de dependência tem links para o Windows e os agentes Linux. Clique no link apropriado para baixar cada agente. Consulte as seções a seguir para obter detalhes sobre como instalar o agente em sistemas diferentes.

### <a name="install-the-dependency-agent"></a>Instalar o agente de dependência

#### <a name="microsoft-windows"></a>Microsoft Windows
São necessários privilégios de administrador para instalar ou desinstalar o agente.

O agente de dependência é instalado em computadores com Windows com ADM-agente-Windows.exe. Se você executar este executável sem opções, ele iniciará um assistente que você pode seguir para executar a instalação interativamente.  

Use as seguintes etapas para instalar o agente de dependência em cada computador Windows.

1.  Certifique-se de que o agente OMS está instalado seguindo as instruções em conectar computadores diretamente para OMS.
2.  Baixe o agente do Windows e executá-lo com o comando a seguir.<br>*ADM-agente-Windows.exe*
3.  Siga o Assistente para instalar o agente.
4.  Se o agente de dependência falhar ao iniciar, verifique os logs para obter informações de erro detalhadas. Agentes do Windows, o diretório de log é *C:\Program Files\BlueStripe\Collector\logs*. 

O agente de dependência para Windows pode ser desinstalado por um administrador com o painel de controle.


#### <a name="linux"></a>Linux
Acesso à raiz é necessário instalar e configurar o agente.

O agente de dependência é instalado em computadores Linux com ADM-agente-Linux64.bin, um script de shell com um binário extração automática. Você pode executar o arquivo com MOS ou adicionar permissões de execução para o próprio arquivo.
 
Use as seguintes etapas para instalar o agente de dependência em cada computador Linux.

1.  Certifique-se de que o agente OMS está instalado seguindo as instruções em [coletar e gerenciar dados de computadores Linux.  Isso deve ser instalado antes do agente de dependência Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Instale o agente de dependência de Linux como raiz usando o seguinte comando.<br>*MOS ADM-agente-Linux64.bin*.
3.  Se o agente de dependência falhar ao iniciar, verifique os logs para obter informações de erro detalhadas. No Linux agentes, o diretório de log é */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Desinstalar o agente de dependência no Linux
Para desinstalar completamente o agente de dependência de Linux, você deve remover o próprio agente e o proxy qual é instalado automaticamente com o agente.  Você pode desinstalar ambos com o seguinte comando único.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Instalando a partir de uma linha de comando
A seção anterior fornece orientação sobre a instalação do agente de Monitor de dependência usando opções padrão.  As seções a seguir fornecem orientação para instalar o agente de uma linha de comando usando opções personalizadas.

#### <a name="windows"></a>Windows
Use as opções da tabela abaixo para executar a instalação de uma linha de comando. Para ver uma lista da instalação sinalizadores execute o instalador com o /? Sinalizador da seguinte maneira.

    ADM-Agent-Windows.exe /?

| Sinalizador | Descrição |
|:--|:--|
| /S | Execute uma instalação silenciosa sem solicitações de usuário. |

Arquivos para o Windows dependência Agent são colocados em *C:\Program Files\BlueStripe\Collector* por padrão.


#### <a name="linux"></a>Linux
Use as opções da tabela abaixo para executar a instalação. Para ver uma lista da instalação sinalizadores executar a instalação do programa com a - Ajuda sinalizador da seguinte maneira.

    ADM-Agent-Linux64.bin -help

| Descrição do sinalizador
|:--|:--|
| -s | Execute uma instalação silenciosa sem solicitações de usuário. |
| -verificar | Verifica se o sistema operacional e permissões, mas não instala o agente. |

Arquivos para o agente de dependência são colocados nos seguintes diretórios.

| Arquivos | Local |
|:--|:--|
| Arquivos principais | /usr/lib/bluestripe-Collector |
| Arquivos de log | /var/opt/Microsoft/Dependency-Agent/log |
| Arquivos de configuração | /etc/opt/Microsoft/Dependency-Agent/config |
| Executáveis de serviço | /sbin/bluestripe-Collector<br>/sbin/bluestripe-Collector-Manager |
| Arquivos de armazenamento binário | /var/opt/Microsoft/Dependency-Agent/Storage |



## <a name="troubleshooting"></a>Solução de problemas
Se você encontrar problemas com o Monitor de dependência de aplicativo, você pode coletar informações de solução de problemas de vários componentes usando as seguintes informações.

### <a name="windows-agents"></a>Agentes Windows

#### <a name="microsoft-dependency-agent"></a>Dependência do Microsoft Agent
Para gerar dados solução de problemas do agente de dependência, abra um Prompt de comando como administrador e execute o script de CollectBluestripeData.vbs usando o comando a seguir.  Você pode adicionar-sinalizador de ajuda para Mostrar opções adicionais.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

O pacote de dados de suporte são salvas no diretório % USERPROFILE % para o usuário atual.  Você pode usar-arquivo <filename> opção de salvá-lo em um local diferente.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Pacote de gerenciamento do Microsoft dependência agente para MMA
O pacote de gerenciamento do agente de dependência é executado dentro do agente de gerenciamento da Microsoft.  Ele recebe dados do agente de dependência e encaminha para o serviço de nuvem do ADM.
  
Verifique se o pacote de gerenciamento é baixado, executando as etapas a seguir.

1.  Procure por um arquivo denominado Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp em C:\Program Files\Microsoft monitoramento Agent\Agent\Health Service State\Management Packs.  
2.  Se o arquivo não estiver presente e o agente estiver conectado a um grupo de gerenciamento do SCOM, verifique se que ele foi importado para SCOM marcando pacotes de gerenciamento do espaço de trabalho de administração do Console de operações.

O MP ADM grava eventos no log de eventos do Windows do Gerenciador de operações.  O log pode ser [pesquisado em OMS](../log-analytics/log-analytics-log-searches.md) via a solução de log do sistema, onde você pode configurar quais arquivos de log para carregar.  Se os eventos de depuração estão ativados, eles são gravados no log de eventos do aplicativo, com a fonte de evento *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Agente de monitoramento da Microsoft
Para coletar rastreamentos de diagnósticos, abra um Prompt de comando como administrador e execute os seguintes comandos: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Rastreamentos são gravados c:\Windows\Logs\OpsMgrTrace.  Você pode parar o rastreamento com StopTracing.cmd.


### <a name="linux-agents"></a>Agentes Linux

#### <a name="microsoft-dependency-agent"></a>Dependência do Microsoft Agent
Para gerar dados de solução de problemas do agente de dependência, faça logon com uma conta que tem privilégios sudo ou raiz e execute o seguinte comando.  Você pode adicionar-sinalizador de ajuda para Mostrar opções adicionais.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

O pacote de dados de suporte é salvo em /var/opt/microsoft/dependency-agent/log (se raiz) em diretório de instalação do agente ou para a pasta base do usuário executando o script (se não raiz).  Você pode usar-arquivo <filename> opção de salvá-lo em um local diferente.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Dependência do Microsoft Agent Fluentd plug-in para Linux
O agente de dependência Fluentd plug-in é executado dentro do agente de Linux OMS.  Ele recebe dados do agente de dependência e encaminha para o serviço de nuvem do ADM.  

Logs são gravados em dois arquivos a seguir.

- /var/opt/Microsoft/omsagent/log/omsagent.log
- /var/log/messages.

#### <a name="oms-agent-for-linux"></a>Agente OMS para Linux
Um recurso de solução de problemas de conexão servidores Linux com OMS pode ser encontrado aqui: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Os logs para o agente de OMS para Linux estão localizados no */var/opt/microsoft/omsagent/log/*.  

Os logs de omsconfig (configuração do agente) estão localizados no */var/opt/microsoft/omsconfig/log/*.
 
O log para os componentes OMI e SCX que fornecem dados de métricas de desempenho estão localizadas em */var/opt/omi/log/* e */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Coleta de dados
Você pode esperar cada agente para transmitir aproximadamente 25MB por dia, dependendo de como complexa dependências do seu sistema estão.  Dados de dependência ADM são enviados por cada agente a cada 15 segundos.  

O agente de ADM geralmente consome 0,1% de memória do sistema e 0,1% da CPU do sistema.


## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
As seções a seguir listam os sistemas operacionais compatíveis para o agente de dependência.   arquiteturas de 32 bits não são suportadas para qualquer sistema operacional.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Área de trabalho do Windows
- Observação: Windows 10 ainda não é suportado
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com núcleo RHEL)
- Somente padrão e versões de núcleo SMP Linux são suportados.
- Versões de núcleo não padrão, como PAE e Xen, não há suporte para nenhuma distribuição Linux. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de lançamento de "2.6.16.21-0.8-xen".
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportados
- Não há suporte para núcleo CentOS Plus.
- Oracle Unbreakable núcleo (UEK) é coberta de outra seção abaixo.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versão do sistema operacional | Versão de núcleo |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versão do sistema operacional | Versão de núcleo |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versão do sistema operacional | Versão de núcleo |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5,9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux com núcleo Unbreakable (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versão do sistema operacional | Versão de núcleo
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versão do sistema operacional | Versão de núcleo
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5,9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versão do sistema operacional | Versão de núcleo
|:--|:--|
| 11 | 2.6.27 |
| SP1 11 | 2.6.32 |
| SP2 11 | 3.0.13 |
| SP3 11 | 3.0.76 |
| SP4 11 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versão do sistema operacional | Versão de núcleo
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e desempenho por meio de seu uso do serviço Monitor de dependência de aplicativos. A Microsoft usa esses dados para fornecer e melhorar a qualidade, a segurança e a integridade do serviço Monitor de dependência de aplicativos. Dados incluem informações sobre a configuração de seu software como o sistema operacional e versão e endereço IP, nome DNS e nome de estação de trabalho para fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para obter mais informações sobre o uso e coleta de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Próximas etapas
- Saiba como [usar o Monitor de dependência de aplicativo](operations-management-suite-application-dependency-monitor.md) uma vez ele foi implantado e configurado.
