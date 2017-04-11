### <a name="app-service-plan"></a>Plano de serviço de aplicativo

Cria o plano de serviço para o aplicativo web de hospedagem. Você fornece o nome do plano através do parâmetro **hostingPlanName** . O local do plano é o mesmo local usado para o grupo de recursos. O tamanho de camada e trabalhador preços são especificadas nos parâmetros de **sku** e **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

