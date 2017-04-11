## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implantar o modelo ARM usando o clique para implantar

Você pode reutilizar o carregamento de modelos ARM predefinido para um repositório de github mantido pela Microsoft e abrir para a comunidade. Esses modelos podem ser implantados direto fora github, ou baixados e modificados para atender às suas necessidades. Para implantar um modelo que cria um VNet com duas sub-redes, siga as etapas abaixo.

1. Em um navegador, navegue até [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Role para baixo na lista de modelos e clique **101-vnet-dois-sub-redes**. Verifique o arquivo de **README.md** , conforme mostrado abaixo.

    ![Arquivo de READEME.md no github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Clique em **implantar para o Azure**. Se necessário, insira suas credenciais de login Azure. 
4. Na lâmina **parâmetros** , insira os valores que você deseja usar para criar seu novo VNet e clique em **Okey**. A figura a seguir mostra os valores para o nosso cenário.

    ![Parâmetros de modelo ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Clique em **grupo de recursos** e selecione um grupo de recursos para adicionar o VNet para ou clique em **Criar novo** para adicionar o VNet a um novo grupo de recursos. A figura a seguir mostra o recurso configurações de grupo para um novo grupo de recursos denominado **TestRG**.

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Se necessário, altere as configurações de **assinatura** e o **local** de sua VNet.
6. Se não quiser ver o VNet como um bloco na **Startboard**, desabilite **Pin para Startboard**.
5. Clique em **termos de Leagl**, leia os termos e clique em **comprar** para concordar. 
6. Clique em **criar** para criar o VNet.

    ![Enviar bloco de implantação no portal de visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Depois que a implantação for concluída, clique em **TestVNet** > **todas as configurações** > **sub-redes** para ver as propriedades de sub-rede, conforme mostrado abaixo.

    ![Criar VNet no portal de visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)