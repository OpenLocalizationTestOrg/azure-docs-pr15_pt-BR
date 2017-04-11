Com o Gerenciador de recursos do Azure, você define parâmetros para valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada parâmetros que contém todos os valores de parâmetro.
Você deve definir um parâmetro para os valores que variam com base no projeto que você está implantando ou baseado no ambiente do qual que você está implantando. Não defina parâmetros para valores que sempre permanecerá o mesmo. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantar. 

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores de um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Podemos descreverá cada parâmetro no modelo.

### <a name="logicappname"></a>logicAppName

O nome do aplicativo lógica para criar.

    "logicAppName": {
        "type": "string"
    }