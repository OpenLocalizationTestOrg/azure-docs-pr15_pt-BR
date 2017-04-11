## <a name="scenario"></a>Cenário

Para ilustrar melhor como criar UDRs, este documento usará cenário abaixo.

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-create-udr-scenario-include/figure1.png)

Neste cenário você criará um UDR para a *sub-rede de final de frente* e outro UDR da *sub-rede de final de Back* , conforme descrito abaixo: 

- **UDR-FrontEnd**. O front-end UDR será aplicado à sub-rede *FrontEnd* e conter uma rota:  
    - **RouteToBackend**. Essa rota enviará todo o tráfego para a sub-rede de back-end à máquina virtual **FW1** .
- **UDR-back-end**. Back-end UDR será aplicado à sub-rede *back-end* e conter uma rota: 
    - **RouteToFrontend**. Essa rota enviará todo o tráfego para a sub-rede de front-end à máquina virtual **FW1** .

A combinação dessas rotas garantirá que todo o tráfego destinado a partir de uma sub-rede para outra será roteado para a máquina virtual do **FW1** , que está sendo usada como um dispositivo virtual. Você também precisa ativar o encaminhamento de IP para essa máquina virtual, para garantir que ele possa receber tráfego destinado a outras VMs.
