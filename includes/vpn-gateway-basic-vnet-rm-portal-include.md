Para criar um VNet no modelo de implantação do Gerenciador de recursos usando o portal do Azure, siga as etapas abaixo. As capturas de tela são fornecidas como exemplos. Certifique-se de que substitua os valores por suas próprias. Para obter mais informações sobre como trabalhar com redes virtuais, consulte [Visão geral de rede Virtual](../articles/virtual-network/virtual-networks-overview.md).

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.

2. Clique em **novo**. No campo de **pesquisa de mercado** , digite "Rede Virtual". Localize **Uma rede Virtual** na lista fornecida e clique para abrir a lâmina de **Rede Virtual** .

    ![Localize uma rede Virtual blade de recurso] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Localizar blade de recurso de rede virtual")

3. Na parte inferior da lâmina rede Virtual, na lista **Selecionar um modelo de implantação** , selecione **Gerenciador de recursos**e clique em **criar**.


    ![Selecione Gerenciador de recursos] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Selecione Gerenciador de recursos")

4. Na lâmina **Criar rede virtual** , defina as configurações de VNet. Quando você preenche os campos, o ponto de exclamação vermelho se tornará uma marca de seleção verde quando os caracteres inseridos no campo são válidos.

    ![Validação de campo] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Validação de campo")

5. **Criar rede virtual** lâmina parecido com o exemplo a seguir. Pode haver valores que são preenchimento automático. Em caso afirmativo, substitua os valores por suas próprias.

    ![Criar blade de rede virtual] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Criar blade de rede virtual")

6. **Nome**: digite o nome para sua rede Virtual.

7. **Espaço de endereço**: digite o espaço de endereço. Se você tiver vários espaços de endereço para adicionar, adicione seu primeiro espaço de endereço. Você pode adicionar espaços de endereço adicional posteriormente, depois de criar o VNet.
 
8. **Nome de sub-rede**: Adicione o nome de sub-rede e o intervalo de endereços de sub-rede. Você pode adicionar sub-redes adicionais posteriormente, depois de criar o VNet.

10. **Assinatura**: Verifique se a assinatura listada está correto. Você pode alterar assinaturas usando na lista suspensa.

11. **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo digitando um nome para o novo grupo de recursos. Se você estiver criando um novo grupo, nomeie o grupo de recursos acordo com os valores de configuração planejada. Para obter mais informações sobre os grupos de recursos, visite [Visão geral do Gerenciador de recursos do Azure](resource-group-overview.md#resource-groups).

12. **Local**: selecione o local para seu VNet. O local determina onde os recursos que você implanta este VNet residirão.

13. Selecione **Fixar a painel** se você quiser ser capaz de localizar seu VNet facilmente no painel e clique em **criar**.
    
    ![Fixar em dashboard] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "Fixar em dashboard")

14. Após clicar em **criar**, você verá um bloco no painel que reflita o andamento do seu VNet. O bloco é alterado conforme o VNet está sendo criada.

    ![Criando uma rede virtual lado a lado] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Criando uma rede virtual lado a lado")