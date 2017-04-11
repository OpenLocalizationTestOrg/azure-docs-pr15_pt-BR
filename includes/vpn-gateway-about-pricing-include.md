Você paga por duas coisas: a cada hora calcular custos para o gateway de rede virtual e transferir os dados de saída do gateway rede virtual. Informações sobre preços podem ser encontradas na página de [preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Custos de computação de gateway de rede virtual**<br>O gateway de cada rede virtual tem uma por hora Calcular custo. O preço é baseado no gateway SKU que você especificar quando você cria um gateway de rede virtual. O custo é para o gateway em si e além a transferência de dados que flui através do gateway.

**Custos de transferência de dados**<br>Os custos de transferência de dados são calculados com base no tráfego de saída do gateway de rede virtual de origem.

- Se você estiver enviando o tráfego para o seu dispositivo VPN locais, ele será cobrado com a taxa de transferência de dados de egresso de Internet.
- Se você estiver enviando o tráfego entre redes virtuais em diferentes regiões, os preços baseia-se a região.
- Se você estiver enviando o tráfego somente entre redes virtuais que estão na mesma região, não há nenhum custos de dados. Tráfego entre VNets na mesma região é gratuito.