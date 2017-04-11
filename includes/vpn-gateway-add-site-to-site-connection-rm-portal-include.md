1. Localize seu gateway de rede virtual e clique em **todas as configurações** para abrir a lâmina de **configurações** .

2. Na lâmina **configurações** , clique em **conexões**e clique em **Adicionar** na parte superior da lâmina para abrir a lâmina **Adicionar conexão** .

    ![Criar conexão to-Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. Na lâmina **Adicionar conexão** , o **nome de** sua conexão. 

4. Para **tipo de Conexão**, selecione **Site-to-site(IPSec)**.

5. Para **o gateway de rede Virtual**, o valor é fixo porque você se conecta este gateway.

6. Para o **gateway de rede Local**, clique em **Escolher um gateway de rede local** e selecione o gateway de rede local que você deseja usar. 

7. Para **Chave compartilhada**, aqui o valor deve corresponder ao valor que você está usando para seu dispositivo VPN local. Se seu dispositivo VPN em sua rede local não fornece uma chave compartilhada, você pode fazer um e entrada aqui e em seu dispositivo local. O importante é que ambos correspondem.

8. Os valores restantes para **assinatura**, **Grupo de recursos**e **local** são corrigidos.

9. Clique em **Okey** para criar sua conexão. Você verá *Criando Conexão* flash na tela.

10. Quando a conexão for concluída, você verá aparecem na lâmina **conexões** para seu Gateway.

    ![Criar conexão to-Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

