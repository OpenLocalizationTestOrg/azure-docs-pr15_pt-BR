
<properties
    pageTitle="Gerenciar recursos com a CLI Azure | Microsoft Azure"
    description="Usar a Interface de linha de comando (CLI) do Azure para gerenciar grupos e recursos Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Usar a CLI do Azure para gerenciar recursos do Azure e grupos de recursos


> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


A Interface de linha do Azure (Azure comando) é uma das várias ferramentas que você pode usar para implantar e gerenciar recursos com o Gerenciador de recursos. Este artigo apresenta as maneiras mais comuns para gerenciar grupos de recursos e recursos Azure usando a CLI Azure no modo de Gerenciador de recursos. Para obter informações sobre como usar a CLI para implantar recursos, consulte [recursos de implantar com modelos do Gerenciador de recursos e CLI do Azure](resource-group-template-deploy-cli.md). Para informações detalhadas sobre recursos Azure e Gerenciador de recursos, visite a [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Para gerenciar recursos Azure com a CLI do Azure, você precisa [instalar o CLI do Azure](xplat-cli-install.md)e [Faça logon no Azure](xplat-cli-connect.md) usando o `azure login` comando. Certificar-se de que a ILC está no modo do Gerenciador de recursos (executar `azure config mode arm`). Se você já fez essas coisas, você está pronto para ir.



## <a name="get-resource-groups-and-resources"></a>Obtenha recursos e grupos de recursos

### <a name="resource-groups"></a>Grupos de recursos

Para obter uma lista de todos os grupos de recursos de sua assinatura e seus locais, execute este comando.

    azure group list
    

### <a name="resources"></a>Recursos
 Para listar todos os recursos em um grupo, como uma com nome *testRG*, use o comando a seguir.

    azure resource list testRG

Para exibir um recurso individual dentro do grupo, como uma máquina virtual denominada *MyUbuntuVM*, use um comando semelhante ao seguinte.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Observe que o parâmetro **Microsoft.Compute/virtualMachines** . Este parâmetro indica o tipo de recurso que você está solicitando informações em.
    
>[AZURE.NOTE]Ao usar os comandos de **recurso azure** diferente o comando **lista** , você deve especificar a versão da API do recurso com o parâmetro **-o** . Se você não tiver certeza sobre a versão da API, consulte o arquivo de modelo e localize o campo apiVersion para o recurso. Para obter mais informações sobre as versões de API no Gerenciador de recursos, consulte [Gerenciador de recursos de provedores, regiões, as versões de API e esquemas](resource-manager-supported-services.md).

Ao exibir detalhes em um recurso, geralmente é útil usar o `--json` parâmetro. Este parâmetro torna a saída mais legível, porque alguns valores são estruturas aninhadas ou coleções. O exemplo a seguir demonstra retornando os resultados do comando **Mostrar** como um documento JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Se desejar, salvar os dados JSON em arquivo usando o &gt; caractere para direcionar a saída para um arquivo. Por exemplo:
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Marcas

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Gerenciar recursos


Para adicionar um recurso como uma conta de armazenamento para um grupo de recursos, execute um comando semelhante a:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
Além de especificar a versão da API do recurso com o parâmetro **-o** , use o parâmetro **-p** para passar uma cadeia de caracteres formatada em JSON com todas as propriedades necessárias ou adicionais.
    
    
Para excluir um recurso existente como um recurso de máquina virtual, use um comando semelhante ao seguinte.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Para mover os recursos existentes para outra assinatura ou grupo de recursos, use o comando **Mover recurso azure** . O exemplo a seguir mostra como mover um Cache relacionada a um novo grupo de recursos. No parâmetro **-i** , fornece uma lista separada por vírgulas da identificação do recurso mover.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Controlar o acesso aos recursos

Você pode usar a CLI do Azure para criar e gerenciar políticas para controlar o acesso aos recursos do Azure. Para informações detalhadas sobre definições de política e atribuir políticas a recursos, consulte [usar diretiva para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

Por exemplo, definir a seguinte diretiva para negar todas as solicitações onde local não é Oeste EUA ou Centro Norte dos EUA e salvá-la a policy.json de arquivo de definição de política:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Em seguida, execute o comando **criar de definição de política** :

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Este comando mostra a saída semelhante à seguinte.

    + Criando a definição de política MyPolicy dados: PolicyName: MyPolicy dados: PolicyDefinitionId: /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    dados: PolicyType: dados personalizados: DisplayName: indefinido dados: Descrição: indefinido dados: PolicyRule: campo = local, no = [westus, northcentralus], efeito = negar

 Para atribuir uma política no escopo desejado, use o **PolicyDefinitionId** retornado do comando anterior. No exemplo a seguir, este escopo é a assinatura, mas você pode definir o escopo para recursos individuais ou grupos de recursos:

    atribuição de política Azure criar MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/###-###-###-###-### /

Você pode obter, alterar ou remover definições de políticas, usando os comandos de **definição de política Mostrar**, **definição de política conjunto**e **definição de política excluir** .

Da mesma forma, você pode obter, alterar ou remover atribuições de política usando os comandos de **atribuição de política Mostrar**, **atribuição de política definida**e **atribuição de política excluir** .


## <a name="export-a-resource-group-as-a-template"></a>Exportar um grupo de recursos como um modelo

Para um grupo existente do recurso, você pode exibir o modelo do Gerenciador de recursos do grupo de recursos. Exportar o modelo oferece dois benefícios:

1. Você pode facilmente automatizar implantações futuras da solução porque todas a infraestrutura está definida no modelo.

2. Você pode se familiarizar com sintaxe de modelo examinando o JSON que representa a sua solução.

Usando a CLI do Azure, você pode exportar um modelo que representa o estado atual do seu grupo de recursos, ou baixar o modelo que foi usado para uma determinada implantação.

* **Exportar o modelo para um grupo de recursos** - isso é útil quando você fez alterações em um grupo de recursos e precisa recuperar a representação JSON de seu estado atual. No entanto, o modelo gerado contém apenas um número mínimo de parâmetros e nenhuma variáveis. A maioria dos valores no modelo está embutida. Antes de implantar o modelo gerado, você talvez queira converter mais dos valores de parâmetros e você pode personalizar a implantação para ambientes diferentes.

    Para exportar o modelo para um grupo de recursos para um diretório local, execute o `azure group export` comando conforme mostrado no exemplo a seguir. (Substitua um diretório local apropriado para seu ambiente de sistema operacional).

        azure group export testRG ~/azure/templates/

* **Baixar o modelo para uma determinada implantação** – isso é útil quando você precisa exibir o modelo real que foi usado para implantar recursos. O modelo inclui todos os parâmetros e variáveis definidas para a implantação original. No entanto, se alguém da sua organização tiver feito alterações ao grupo de recursos fora a definição do modelo, esse modelo não representa o estado atual do grupo de recursos.

    Para baixar o modelo usado para uma determinada implantação para um diretório local, execute o `azure group deployment template download` comando. Por exemplo:

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Exportação de modelo está no modo de visualização e nem todos os tipos de recurso suportam exportar um modelo. Ao tentar exportar um modelo, você poderá ver o erro afirmando que alguns recursos não foram exportados. Se necessário, defina manualmente esses recursos no seu modelo após fazer o download.



## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes de operações de implantação e solucionar problemas de erros de implantação com a CLI do Azure, consulte [Exibir operações de implantação com CLI do Azure](resource-manager-troubleshoot-deployments-cli.md).
* Se você quiser usar a CLI para configurar um aplicativo ou script para acessar recursos, consulte [Usar CLI do Azure para criar um serviço principal para acessar recursos](resource-group-authenticate-service-principal-cli.md).


