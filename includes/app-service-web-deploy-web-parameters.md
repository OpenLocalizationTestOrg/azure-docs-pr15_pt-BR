Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada parâmetros que contém todos os valores de parâmetro.
Você deve definir um parâmetro para os valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que sempre permanecerá o mesmo. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados. 

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores de um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Podemos descreverá cada parâmetro no modelo.

### <a name="sitename"></a>nome do site

O nome do aplicativo web que você deseja criar.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

O nome do serviço de aplicativo planeja usar para hospedar o aplicativo web.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU

A camada de preços para o plano de hospedagem.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

O modelo define os valores que são permitidos para esse parâmetro e atribui um valor padrão (S1) se nenhum valor for especificado.

### <a name="workersize"></a>workerSize

O tamanho da instância do plano de hospedagem (pequeno, médio ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
O modelo define os valores que são permitidos para este parâmetro (0, 1 ou 2) e atribui um valor padrão (0) se nenhum valor for especificado. Os valores correspondem aos pequenos, médios e grandes.
