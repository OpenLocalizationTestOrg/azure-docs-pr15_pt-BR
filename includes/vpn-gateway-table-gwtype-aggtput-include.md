|    | **Taxa de transferência do Gateway VPN (1)** | **Gateway VPN máx IPsec túneis (2)** | **Transferência de rota expressa Gateway** | **Gateway VPN e rota expressa coexistência**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU básico (3)(5)**              |  100 Mbps | 10                         |  500 Mbps                           | Não   |
| **SKU padrão (4)(5)**           |  100 Mbps | 10                         | 1000 Mbps                           | Sim  |
| **Alto desempenho SKU (4)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Sim  |

- (1) taxa de transferência a VPN é uma estimativa com base nas medidas entre VNets na mesma região Azure. Não é uma taxa de transferência garantia para conexões entre locais através da Internet. É a medida de transferência máxima de possíveis.
- (2) o número de túneis consulte RouteBased VPNs. Uma VPN PolicyBased só podem suportar um túnel VPN to-Site.
- (3) BGP não há suporte para a SKU básico.
- (4) PolicyBased VPNs não são suportados para este SKU. Eles são suportados para somente o SKU básico.
- (5) conexões de Gateway de VPN S2S ativa não são suportadas para este SKU. Ativa é suportada no SKU HighPerformance somente.