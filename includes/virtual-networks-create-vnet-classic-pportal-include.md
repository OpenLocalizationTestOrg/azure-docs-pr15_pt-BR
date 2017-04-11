## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Como criar um VNet clássico no portal do Azure

Para criar uma VNet clássica baseado no cenário acima, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **novo** > **rede** > **rede Virtual**, observe que a lista **Selecionar um modelo de implantação** já mostra **clássico**e clique em **criar**, conforme mostrado na figura abaixo.

    ![Criar VNet no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Na lâmina **rede Virtual** , digite o **nome** da VNet e, em seguida, clique em **espaço de endereço**. Configurar suas configurações de espaço de endereço para o VNet e sua sub-rede primeiro e, em seguida, clique em **Okey**. A figura a seguir mostra as configurações de bloqueio CIDR para o nosso cenário.

    ![Blade de espaço de endereço](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Clique em **Grupo de recursos** e selecione um grupo de recursos para adicionar o VNet para ou clique em **Criar novo grupo de recursos** para adicionar o VNet a um novo grupo de recursos. A figura a seguir mostra o recurso configurações de grupo para um novo grupo de recursos denominado **TestRG**. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Criar blade de grupo de recursos](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Se necessário, altere as configurações de **assinatura** e o **local** de sua VNet. 

6. Se não quiser ver o VNet como um bloco na **Startboard**, desabilite **Pin para Startboard**. 

7. Clique em **criar** e observe o bloco nomeado **rede Virtual criando** conforme mostrado na figura abaixo.

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Aguarde a VNet seja criado e quando você vir o bloco abaixo, clique em para adicionar mais sub-redes.

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Você verá a **configuração** para sua VNet, conforme mostrado abaixo. 

    ![Criar VNet no portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Clique em **sub-redes** > **Adicionar**, digite um **nome** e especifique um **intervalo de endereços (bloco CIDR)** para a sua sub-rede e clique em **Okey**. A figura a seguir mostra as configurações para o nosso cenário atual.

    ![Criar VNet no portal do Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)