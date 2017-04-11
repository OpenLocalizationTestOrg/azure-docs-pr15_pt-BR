## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Como criar uma VNet usando um arquivo de configuração de rede no portal do Azure

Azure usa um arquivo xml para definir todas as VNets disponíveis para uma assinatura. Você pode baixar este arquivo e editá-lo para modificar ou excluir VNets existentes e criar novos. Neste documento, você irá aprender a baixar este arquivo, conhecido como arquivo de configuração (ou netcgf) de rede e editá-lo para criar um novo VNet. Verifique o [esquema de configuração de rede virtual Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para saber mais sobre o arquivo de configuração de rede.

Para criar um VNet usando um arquivo de netcfg por meio do portal Azure, siga as etapas abaixo.

1. Em um navegador, navegue até http://manage.windowsazure.com e, se necessário, entre com sua conta do Azure.
2. Role para baixo na lista de serviços e clique em **redes** , conforme mostrado abaixo.

    ![Redes virtuais Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Na parte inferior da página, clique no botão **Exportar** , conforme mostrado abaixo.

    ![Botão Exportar](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Na página **Exportar configuração de rede** , selecione a assinatura que você deseja exportar a configuração de rede virtual do e clique no botão de marca de seleção no canto inferior esquerdo da página.
5. Siga as instruções do seu navegador para salvar o arquivo de **NetworkConfig.xml** . Verifique se você observe onde você estiver salvando o arquivo.
6. Abra o arquivo que você salvou na etapa 5 acima usando qualquer aplicativo de editor de texto ou XML e procure o **<VirtualNetworkSites>** elemento. Se você tiver quaisquer redes já criadas, cada rede será exibido como seu próprio **<VirtualNetworkSite>** elemento.
7. Para criar a rede virtual descrita nesse cenário, adicione o seguinte XML apenas na **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Salve o arquivo de configuração de rede.
9.  No portal do Azure, no canto inferior esquerdo da página, clique em **novo**, e em seguida, clique **Serviços de rede**, clique **Rede VIRTUAL**e clique em **Configuração de importação** , conforme mostrado na figura abaixo.

    ![Configuração de importação](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Na página **importar o arquivo de configuração de rede** , clique em **Procurar para o arquivo...**, e em seguida, navegue até a pasta que você salvou o arquivo na etapa 8 acima, selecione o arquivo e clique em **Abrir**. A página da web deve ser semelhante a figura a seguir. No canto inferior direito da página, clique no botão de seta para mover para a próxima etapa.

    ![Página de arquivo de configuração de rede de importação](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Na página de **criação de sua rede** , observe a entrada para sua nova VNet, conforme mostrado na figura abaixo.

    ![Criar sua página de rede](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Clique no botão de marca de seleção no canto inferior direita da página para criar a VNet. Após alguns segundos seu VNet será mostrado na lista de VNets disponível, conforme mostrado na figura abaixo.

    ![Nova rede virtual](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)