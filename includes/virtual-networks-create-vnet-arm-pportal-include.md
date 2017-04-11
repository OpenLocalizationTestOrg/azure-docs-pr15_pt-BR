## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Como criar um VNet no portal do Azure

Para criar uma VNet baseado no cenário acima usando o portal de visualização Azure, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **novo** > **rede** > **rede Virtual**, clique em **Gerenciador de recursos** na lista **Selecionar um modelo de implantação** e clique em **criar**, conforme mostrado na figura abaixo.

    ![Criar VNet no portal do Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. Na lâmina **Criar rede virtual** , defina as configurações de VNet, conforme mostrado na figura abaixo.

    ![Criar blade de rede virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Clique em **grupo de recursos** e selecione um grupo de recursos para adicionar o VNet para ou clique em **Criar novo** para adicionar o VNet a um novo grupo de recursos. A figura a seguir mostra o recurso configurações de grupo para um novo grupo de recursos denominado **TestRG**. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](../articles/resource-group-overview.md#resource-groups).

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Se necessário, altere as configurações de **assinatura** e o **local** de sua VNet. 

6. Se não quiser ver o VNet como um bloco na **Startboard**, desabilite **Pin para Startboard**. 

7. Clique em **criar** e observe o bloco nomeado **rede Virtual criando** conforme mostrado na figura abaixo.

    ![Criar bloco de rede virtual](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Aguarde a VNet seja criado, na lâmina **rede Virtual** , clique em **todas as configurações** > **sub-redes** > **Adicionar** conforme mostrado abaixo.

    ![Adicionando sub-rede no portal do Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Especifique as configurações de sub-rede da sub-rede de *back-end* , conforme mostrado abaixo e clique em **Okey**. 

    ![Configurações de sub-rede](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Observe que a lista de sub-redes, conforme mostrado na figura abaixo.

    ![Lista de sub-redes no VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)
