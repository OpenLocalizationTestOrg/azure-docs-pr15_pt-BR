1. No portal do, de **todos os recursos**, clique em **+ Add**. Na **tudo** blade caixa Pesquisar, digite o **gateway de rede Local**, clique em para pesquisar. Isso retornará uma lista. Clique em **gateway de rede Local** para abrir a lâmina, clique em **criar** para abrir a lâmina de **gateway de rede local de criar** .

    ![criar o gateway de rede local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. No **criar blade de gateway de rede local**, especifique um **nome** para o objeto de gateway de rede local.
 
3. Especifica um **endereço IP** público válido para o dispositivo VPN ou o gateway de rede virtual à qual você deseja se conectar.<br>Se esta rede local representa no local, este é o endereço IP público do dispositivo VPN que você deseja se conectar. Ele não pode ser protegido por NAT e deve ser acessíveis pelo Azure.<br>Se dessa rede local representa VNet outro, você vai especificar o endereço IP público atribuído para o gateway de rede virtual para esse VNet.<br>

4. **Espaço de endereço** refere-se aos intervalos de endereços para a rede que representa dessa rede local. Você pode adicionar vários intervalos de espaço de endereço. Certifique-se de que os intervalos que você especificar aqui não se sobreponham com intervalos de outras redes que você deseja se conectar.
 
5. Para **assinatura**, verifique se que a assinatura correta está mostrando.

6. Para o **Grupo de recursos**, selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos, ou selecione um que você já criou.

7. Para **local**, selecione o local em que esse objeto será criado em. Talvez você queira selecionar que sua VNet reside no mesmo local, mas não é necessário fazer isso.

8. Clique em **criar** para criar o gateway de rede local.
