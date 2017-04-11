## <a name="scenario"></a>Cenário

Para ilustrar melhor como criar um VNet e sub-redes, este documento usará cenário abaixo.

![Cenário de VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Neste cenário, você criará uma VNet denominada **TestVNet** com um bloco CIDR reservado de **192.168.0.0./16**. Seu VNet irá conter as seguintes sub-redes: 

- **FrontEnd**, usando **192.168.1.0/24** como seu bloco CIDR.
- **Back-end**, usando **192.168.2.0/24** como seu bloco CIDR.

 