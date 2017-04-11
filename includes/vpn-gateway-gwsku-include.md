Quando você cria um gateway de rede virtual, você precisa especificar o gateway SKU que você deseja usar. Quando você seleciona um gateway maior SKU, mais CPUs e largura de banda estão alocados para o gateway e como resultado, o gateway pode oferecer suporte a mais alta taxa de transferência de rede à rede virtual.

Gateway VPN pode usar SKUs do seguintes:

- Básico
- Padrão
- HighPerformance

Ao selecionar uma SKU, considere o seguinte:

- Se você quiser usar um tipo de PolicyBased VPN, você deve usar o SKU básico. VPNs PolicyBased (anteriormente chamado de roteamento estático) não são suportadas em qualquer outro SKU.
- BGP não é suportado no SKU básico.
- Coexistência de Gateway VPN rota expressa configurações não são suportadas no SKU básico.
- Conexões de Gateway de VPN S2S ativa podem ser configurados no HighPerformance SKU apenas.
