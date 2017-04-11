## <a name="download-and-understand-the-arm-template"></a>Baixar e entender o modelo ARM

Você pode baixar o modelo ARM existente para criar uma VNet e duas sub-redes do github, faça as alterações podem desejada e reutilizá-la. Para fazer isso, siga as etapas abaixo.

1. Navegue até [a página de modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Clique em **azuredeploy.json**e clique em **MATÉRIA**.
3. Salve o arquivo para uma uma pasta local no seu computador.
4. Se você estiver familiarizado com modelos ARM, pule para a etapa 7.
5. Abra o arquivo que você acabou de salvar e examinar o conteúdo em **parâmetros** na linha 5. Parâmetros de modelo ARM fornecem um espaço reservado para valores que podem ser preenchidos durante a implantação.

    | Parâmetro | Descrição |
    |---|---|
    | **local** | Azure região onde o VNet será criado |
    | **vnetName** | Nome para o novo VNet |
    | **addressPrefix** | Espaço de endereço para o VNet, no formato CIDR |
    | **subnet1Name** | Nome para a primeira VNet |
    | **subnet1Prefix** | Bloco CIDR para a primeira sub-rede |
    | **subnet2Name** | Nome para a segunda VNet |
    | **subnet2Prefix** | Bloco CIDR para a segunda sub-rede |

    >[AZURE.IMPORTANT] Modelos ARM mantidos no github podem alterar ao longo do tempo. Certifique-se de que você verifique o modelo antes de utilizá-lo.
    
6. Verifique o conteúdo em **recursos** e observe o seguinte:

    - **tipo**. Tipo de recurso sendo criado pelo modelo. Nesse caso, **Microsoft.Network/virtualNetworks**, que representam um VNet.
    - **nome**. Nome do recurso. Observe o uso de **[parameters('vnetName')]**, que significa que o nome será fornecidos como entrada pelo usuário ou um arquivo de parâmetro durante a implantação.
    - **Propriedades**. Lista de propriedades para o recurso. Este modelo usa as propriedades de espaço e sub-rede do endereço durante a criação de VNet.

7. Navegar de volta para [a página de modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Clique em **azuredeploy-paremeters.json**e clique em **MATÉRIA**.
9. Salve o arquivo para uma uma pasta local no seu computador.
10. Abra o arquivo que você acabou de salvar e edite os valores para os parâmetros. Use os valores abaixo para implantar o VNet descrito em nosso cenário.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Salve o arquivo.
  