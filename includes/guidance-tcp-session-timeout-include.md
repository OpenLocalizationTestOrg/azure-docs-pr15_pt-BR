##<a name="tcp-settings-for-azure-vms"></a>Configurações de TCP para VMs do Azure

Azure VMs se comunicar com a Internet pública usando [NAT] [ nat] (NAT). Dispositivos NAT atribuir um endereço IP público e porta para uma máquina virtual do Azure, permitindo que máquina virtual para estabelecer um soquete para comunicação com outros dispositivos. Se pacotes parar fluindo por meio desse soquete após uma hora específica, o dispositivo NAT interrompe o mapeamento e soquete é gratuito ser usado em outras VMs.

Este é um comportamento NAT comuns, que pode causar problemas de comunicação em aplicativos de TCP com base que espera um soquete a ser mantida além de um período de tempo limite. Há duas configurações de tempo limite ocioso considerar, para sessões em um estado *estabelecido conexão* :

- **entrada** por meio do [balanceador de carga do Azure][azure-lb-timeout]. Esse tempo limite padrão é 4 minutos e pode ser ajustado para cima para 30 minutos.
- **saída** usando [SNAT] [ snat] (fonte NAT). Esse tempo limite está definido como 4 minutos e não pode ser ajustada.

Para garantir conexões não são perdidos fora do limite de tempo limite, você deverá garantir seu aplicativo mantém a sessão de funcionamento, ou você pode configurar o sistema operacional subjacente para fazer isso. As configurações a serem usados são diferentes para sistemas Linux e Windows, conforme mostrado abaixo.

Para [Linux][linux], você deve alterar as variáveis de núcleo abaixo.
NET.IPv4.tcp_keepalive_time = net.ipv4.tcp_keepalive_intvl 120 = 30 net.ipv4.tcp_keepalive_probes = 8
 
Para [Windows][windows], você deve alterar os valores de registro abaixo.
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


As configurações acima garantem um pacote de funcionamento manter é enviado após 2 minutos (120 segundos) de tempo ocioso e então enviado a cada 30 segundos. E se 8 desses pacotes falhar, a sessão será interrompida.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md