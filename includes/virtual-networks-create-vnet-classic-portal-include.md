## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Como criar um VNet no portal do Azure

Para criar uma VNet baseado no cenário acima, siga as etapas abaixo.

1. Em um navegador, navegue até http://manage.windowsazure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **novo** > **Serviços de rede** > **Rede VIRTUAL** > **Criar personalizado** conforme mostrado na figura abaixo.

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Na página **Detalhes de rede Virtual** , digite o **nome** da VNet, selecione seu **local**e, em seguida, clique na seta no canto inferior direito da página para Avançar para a etapa 2. A figura a seguir mostra as configurações para o nosso cenário.

    ![Página de detalhes de uma rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Na página **DNS Servers e a conectividade VPN** , especifique o nome e endereço IP para até 9 servidores DNS usar. Se você não especificar um servidor DNS, seu VNet usará a resolução de resolução nomeação internos fornecida pelo Azure. Para o nosso cenário, podemos não configurará servidores DNS.
5. Se você quiser fornecer acesso VPN de ponto-a-site para sua VNet, habilite a caixa de seleção **Configurar uma VPN de ponto-a-site** . Se você não definir uma VPN de ponto-a-site, você pode adicioná-lo ao seu VNet a qualquer momento após a criação. Para o nosso cenário, podemos não configurará uma VPN de ponto-a-site.
6. Se você quiser fornecer-to-site VPN conectividade entre seu VNet e outro VNet ou sua rede local, ative a caixa de seleção **Configurar uma VPN to-site** e especifique se você deseja usar **rota expressa** ou anotação e o nome da rede para se conectar ao. Se você não definir uma VPN to-site, você pode adicioná-lo ao seu VNet a qualquer momento após a criação. Para o nosso cenário, podemos não configurará uma VPN to-site.
7. Clique na seta no canto inferior direita da página para ir para a etapa 3. A figura a seguir mostra as configurações para o nosso cenário.

    ![Página de conectividade VPN e servidores DNS](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Na página **Virtual espaços de endereço de rede** , em **IP inicial**, clique em *10.0.0.0* para alterar o espaço de endereço de VNet e digite o espaço de endereço inicial que você deseja usar. Para o nosso cenário, digite *192.168.0.0*. 
9. Em **CIDR (contagem de endereço)** , selecione o número de bits para a máscara de sub-rede. Para o nosso cenário, selecione *16 (65536)*.
10. Em **sub-redes**, clique em *sub-rede-1* e renomeie a sub-rede, se necessário. Para o nosso cenário, renomeá-lo para *FrontEnd*.

    >[AZURE.NOTE] Se você clicar fora da caixa de texto nome para uma sub-rede não será capaz de editar o nome, se a sub-rede novamente. Para corrigir isso, você precisa remover a sub-rede clicando no botão X à sua direita, em seguida, adicione uma nova sub-rede conforme descrito na etapa 13 abaixo.

11. Em **IP inicial** para a primeira sub-rede, especifique o endereço IP inicial para a sub-rede. Para o nosso cenário, digite *192.168.1.0*.
12. Em **CIDR (contagem de endereço)** , selecione o número de bits para a máscara de sub-rede para a primeira sub-rede. Para o nosso cenário, selecione *24 (256)*.
13. Clique em **Adicionar sub-rede** para adicionar uma nova sub-rede, se necessário. Para o nosso cenário, adicione uma sub-rede e repita as etapas de 10 a 12 para configurar o VNet conforme mostrado na figura abaixo.

    ![Página de espaços de endereço de rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Clique no botão de marca de seleção no canto inferior direita da página para criar a VNet. Após alguns segundos seu VNet será mostrado na lista de VNets disponível, conforme mostrado na figura abaixo.

    ![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)