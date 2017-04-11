<properties
  pageTitle="Carregar testes personalizados balanceador e monitorar o status de integridade | Microsoft Azure"
  description="Saiba como usar testes personalizados para balanceador de carga do Azure para monitorar instâncias atrás balanceador de carga"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>Compreender testes de Balanceador de carga

Azure balanceador de carga oferece a capacidade de monitorar a integridade de instâncias do servidor usando testes. Quando um teste Falha ao responder, balanceador de carga interrompe o envio de novas conexões na instância não íntegra. Conexões existentes não são afetadas e novas conexões são enviadas para instâncias íntegros.

Funções de serviço de nuvem (funções de trabalho e funções da web) usam um agente de convidado para monitoramento de teste. TCP ou HTTP testes personalizados devem ser configurados quando você usa máquinas virtuais atrás balanceador de carga.

## <a name="understand-probe-count-and-timeout"></a>Entender a contagem de teste e tempo limite

Comportamento de teste depende:

- O número de testes bem-sucedida que permitem uma instância a ser rotulado como ativo.
- O número de testes com falha que causa uma instância a ser rotulado como inativo.

O valor de tempo limite e frequência definido SuccessFailCount determinar se uma instância é confirmada para ser executando ou não. No portal do Azure, o tempo limite está definido como duas vezes o valor de frequência.

A configuração de teste de todas as instâncias de balanceamento de carga de um ponto de extremidade (ou seja, um conjunto balanceamento de carga) deve ser o mesmo. Isso significa que você não pode ter uma configuração de teste diferente para cada instância da função ou máquina virtual no mesmo serviço hospedado por uma combinação de ponto de extremidade específico. Por exemplo, cada instância deve ter tempos limite e portas locais idênticas.

>[AZURE.IMPORTANT] Um teste de Balanceador de carga usa o endereço IP 168.63.129.16. Este endereço IP público facilita a comunicação para recursos de plataforma interno para a trazer seu-proprietário-IP cenário Azure rede Virtual. O endereço IP público 168.63.129.16 virtual é usado em todas as regiões e não será alterado. Recomendamos que você permite este endereço IP em qualquer diretivas de firewall local. Ele não deve ser considerado um risco de segurança porque somente a plataforma Windows Azure interna possa ser fonte de uma mensagem desse endereço. Se você não fizer isso, haverá um comportamento inesperado em uma variedade de cenários como configurar o mesmo intervalo de endereços IP de 168.63.129.16 e tendo duplicado endereços IP.

## <a name="learn-about-the-types-of-probes"></a>Saiba mais sobre os tipos de testes

### <a name="guest-agent-probe"></a>Teste do agente de convidado

Este teste só está disponível para os serviços de nuvem do Azure. Balanceador de carga utiliza o agente de convidado na máquina virtual, ouve e responde com uma resposta HTTP 200 Okey somente quando a instância está no estado pronta (ou seja, não em outro estado como ocupado, reciclagem ou parar).

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para testes de integridade](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [começar a criar um balanceador de carga voltado para a Internet para serviços de nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>O que torna um teste do agente de convidado marcar uma instância como não íntegra?

Se o agente de convidado falhar ao responder com HTTP 200 Okey, balanceador de carga marca a instância respondendo e interrompe o envio de tráfego a essa instância. O balanceador de carga continua ping a instância. Se o agente de convidado responde com um 200 HTTP, balanceador de carga envia o tráfego a essa instância novamente.

Quando você usa uma função de web, o código de site normalmente é executado em w3wp.exe, que não é monitorado pelo Azure agente tecidos ou convidado. Isso significa que falhas no w3wp.exe (por exemplo, respostas HTTP 500) não serão relatadas para o agente de convidado e balanceador de carga não terão instância fora rotação.

### <a name="http-custom-probe"></a>Teste personalizado HTTP

O teste de Balanceador de carga de HTTP personalizado substitui o teste de agente de convidado padrão, que significa que você pode criar sua própria lógica personalizada para determinar a integridade da instância de função. O balanceador de carga testes seu ponto de extremidade cada 15 segundos, por padrão. A instância é considerada na rotação de Balanceador de carga se ele responde com um 200 HTTP dentro do período de tempo limite (31 segundos por padrão).

Isso pode ser útil se você quiser implementar sua própria lógica para remover instâncias de rotação de Balanceador de carga. Por exemplo, você pode decidir remover uma instância se ela estiver acima de 90% CPU e retorna um status de não-200. Se você tiver funções da web que usam w3wp.exe, também significa que você obtém automáticas monitoramento de seu site, porque falhas no seu código de site retornará um status de não-200 para o teste de Balanceador de carga.

>[AZURE.NOTE] O teste personalizado HTTP suporta caminhos relativos e protocolo HTTP apenas. Não há suporte para HTTPS.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>O que torna um teste personalizado HTTP marcar uma instância como não íntegra?

- O aplicativo de HTTP retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Esta é uma confirmação positiva que a instância do aplicativo deve ser seguida Cancelar serviço imediatamente.
- O servidor HTTP não responde sequer após o período de tempo limite. Dependendo do valor de tempo limite definido, isso significa esse teste várias solicitações ir sem resposta antes que o teste é marcado como não sendo executado (ou seja, antes de SuccessFailCount testes são enviadas).
- O servidor fecha a conexão por meio de uma redefinição TCP.

### <a name="tcp-custom-probe"></a>Teste personalizado TCP

Testes TCP iniciam uma conexão executando um handshake de três formas com a porta definido.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>O que torna um teste personalizado TCP marcar uma instância como não íntegra?

- O servidor TCP não responde sequer após o período de tempo limite. Quando o teste é marcado como não sendo executado depende do número de solicitações de teste com falha que foram configurados para ir não respondidas antes de marcar o teste como não sendo executado.
- O teste recebe um TCP redefinir da instância de função.

Para obter mais informações sobre como configurar um teste de integridade HTTP ou um teste TCP, consulte [começar a criar um balanceador de carga de voltado para a Internet no Gerenciador de recursos usando o PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Adicionar instâncias eficaz, volta para a rotação de Balanceador de carga

Testes TCP e HTTP são considerados íntegros e marcar a instância de função como eficaz, quando:

- O balanceador de carga obtém um teste positivo inicializa a primeira vez a máquina virtual.
- O número SuccessFailCount (descrito anteriormente) define o valor de testes bem-sucedida que são necessárias para marcar a instância de função como íntegra. Se uma instância da função foi removida, o número de testes bem-sucedida, sucessivas deve igual ou exceder o valor de SuccessFailCount para marcar a instância de função como execução.

>[AZURE.NOTE] Se a integridade de uma instância da função é flutuante, balanceador de carga mais aguarda antes de colocar a instância de função no estado de integridade. Isso é feito por meio de políticas para proteger o usuário e a infraestrutura.

## <a name="use-log-analytics-for-load-balancer"></a>Análise de log de uso de Balanceador de carga

Você pode usar [a análise de log de Balanceador de carga](load-balancer-monitor-log.md) para verificar o status de integridade de teste e a contagem de teste. Registro em log pode ser usado com Power BI ou ideias operacionais do Azure para fornecer estatísticas sobre status de integridade de Balanceador de carga.
