<properties 
   pageTitle="Mensagens de syslog na análise de Log | Microsoft Azure"
   description="Syslog é um protocolo de log de eventos que é comum para Linux.   Este artigo descreve como configurar o conjunto de mensagens de Syslog na análise de Log e detalhes dos registros criam no repositório OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="syslog-data-sources-in-log-analytics"></a>Fontes de dados de syslog na análise de Log

Syslog é um protocolo de log de eventos que é comum para Linux.  Aplicativos enviarão mensagens que podem ser armazenadas no computador local ou entregues em um coletor Syslog.  Quando o agente de OMS para Linux é instalado, ele configura o daemon do Syslog local para encaminhar mensagens para o agente.  O agente envia a mensagem para a análise de Log onde um registro correspondente é criado no repositório OMS.  

> [AZURE.NOTE]Análise de log é compatível com conjunto de mensagens enviadas por rsyslog ou ng syslog. O daemon do syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e Oracle Linux versão (sysklog) não há suporte para conjunto de evento syslog. Para coletar dados de syslog nesta versão dessas distribuições, o [daemon rsyslog](http://rsyslog.com) deve ser instalado e configurado para substituir sysklog.

![Conjunto de syslog](media/log-analytics-data-sources-syslog/overview.png)


## <a name="configuring-syslog"></a>Configurando Syslog
O agente de OMS para Linux coletará somente eventos com as instalações e severidades especificadas em sua configuração.  Você pode configurar Syslog por meio do portal OMS ou gerenciando arquivos de configuração em seus agentes Linux.


### <a name="configure-syslog-in-the-oms-portal"></a>Configurar Syslog no portal do OMS

Configure Syslog no [menu de dados nas configurações de análise de Log](log-analytics-data-sources.md#configuring-data-sources).  Essa configuração foi entregue para o arquivo de configuração em cada agente de Linux.

Você pode adicionar um novo recurso digitando em seu nome e clicando em **+**.  Para cada recurso, serão coletadas somente mensagens com as severidades selecionadas.  Verifique as gravidades para o recurso específico que você deseja coletar.  Você não pode fornecer qualquer critério adicional para filtrar mensagens.

![Configurar Syslog](media/log-analytics-data-sources-syslog/configure.png)


Por padrão, todas as alterações de configuração são enviadas automaticamente para todos os agentes.  Se você quiser configurar Syslog manualmente em cada agente Linux, desmarque a caixa *Aplicar abaixo de configuração para minha máquinas Linux*.


### <a name="configure-syslog-on-linux-agent"></a>Configurar Syslog no Linux agente

Quando o [agente OMS está instalado em um cliente Linux](log-analytics-linux-agents.md), ele instala um arquivo de configuração de syslog padrão que define o recurso e a gravidade das mensagens que são coletados.  Você pode modificar esse arquivo para alterar a configuração.  O arquivo de configuração é diferente dependendo o daemon do Syslog que instalou o cliente.

> [AZURE.NOTE] Se você editar a configuração de syslog, você deve reiniciar o daemon do syslog para que as alterações tenham efeito.

#### <a name="rsyslog"></a>rsyslog

O arquivo de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**.  Conteúdo padrão é mostrado abaixo.  Isso reúne mensagens de syslog enviadas do agente local para todos os recursos com um nível de aviso ou superior.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Você pode remover um recurso removendo sua seção do arquivo de configuração.  Você pode limitar as severidades que são coletadas para um determinado recurso modificando entrada desse recurso.  Por exemplo, para limitar a facilidade de usuário de mensagens com uma gravidade de erro ou superior você faria modificar linha do arquivo de configuração para o seguinte:

    user.error  @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog ng

Arquivo de configuração para rsyslog é local em **/etc/syslog-ng/syslog-ng.conf**.  Conteúdo padrão é mostrado abaixo.  Isso coleta syslog mensagens enviadas do agente local para todos os recursos e severidades todos os.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };
    
    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };
    
    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };
    
    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };
    
    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };
    
    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };
    
    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Você pode remover um recurso removendo sua seção do arquivo de configuração.  Você pode limitar as severidades que são coletadas para um determinado recurso removendo-os da sua lista.  Por exemplo, para limitar o recurso de usuário às mensagens apenas alertas e críticas, você poderia modificar essa seção do arquivo de configuração para o seguinte:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Alterar a porta de Syslog

O agente OMS ouve mensagens de Syslog no cliente local na porta 25224.  Você pode alterar essa porta adicionando a seção a seguir para o arquivo de configuração do agente OMS localizado em **/etc/opt/microsoft/omsagent/conf/omsagent.conf**.  Substitua 25224 na entrada de **porta** o número da porta que desejar.  Observe que você também precisará modificar o arquivo de configuração para o daemon do Syslog enviar mensagens para esta porta.

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>Coleta de dados

O agente OMS ouve mensagens de Syslog no cliente local na porta 25224. Arquivo de configuração para o daemon do Syslog encaminha mensagens de Syslog enviadas a partir do aplicativo para esta porta onde eles são coletados pela análise de Log.


## <a name="syslog-record-properties"></a>Propriedades do registro de syslog

Os registros de syslog têm um tipo de **Syslog** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Computador | Computador que o evento foram coletado do. |
| Recurso | Define a parte do sistema que gerou a mensagem. |
| HostIP | Endereço IP do sistema enviando a mensagem.  |
| Nome do host | Nome do sistema enviando a mensagem. |
| SeverityLevel | Nível de gravidade do evento. |
| SyslogMessage | Texto da mensagem. |
| ProcessID | ID do processo que gerou a mensagem. |
| EventTime | Data e hora em que o evento foi gerado.



## <a name="log-queries-with-syslog-records"></a>Consultas de log com registros de Syslog

A tabela a seguir fornece exemplos diferentes de consultas de log que recuperam registros de Syslog.

| Consulta | Descrição |
|:--|:--|
| Tipo = Syslog | Syslogs tudo. |
| Tipo = Syslog SeverityLevel = erro | Todos os registros de Syslog com gravidade do erro. |
| Tipo = Syslog & #124; Count () medida por computador | Registros de contagem de Syslog por computador. |
| Tipo = Syslog & #124; Count () medida pelo recurso | Registros de contagem de Syslog por recurso. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as pesquisas de log](log-analytics-log-searches.md) analisar os dados coletados de fontes de dados e soluções. 
- Use [Campos personalizados](log-analytics-custom-fields.md) para analisar dados de registros de syslog em campos individuais.
- [Configurar Linux agentes](log-analytics-linux-agents.md) para coletar outros tipos de dados. 
