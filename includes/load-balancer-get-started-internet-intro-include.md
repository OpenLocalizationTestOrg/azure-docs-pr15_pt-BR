Um balanceador de carga Azure é um balanceador de carga de camada 4 (TCP, UDP). O balanceador de carga oferece alta disponibilidade, distribuindo o tráfego de entrada entre instâncias de serviço Íntegro nos serviços de nuvem ou máquinas virtuais em um conjunto de Balanceador de carga. Azure balanceador de carga também pode apresentar esses serviços em várias portas, vários endereços IP ou ambos.

Você pode configurar um balanceador de carga para:

* Carregar saldo tráfego da Internet para máquinas virtuais (VMs). Nos referimos um balanceador de carga neste cenário como um [balanceador de carga voltados para a Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Tráfego de equilíbrio de carga entre VMs em uma rede virtual (VNet), entre VMs nos serviços de nuvem ou entre computadores no local e VMs em uma rede virtual entre locais. Nos referimos um balanceador de carga neste cenário como um [balanceador de carga interno (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Encaminhar o tráfego externo para uma instância específica do VM.
