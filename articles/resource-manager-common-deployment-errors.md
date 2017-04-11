<properties
   pageTitle="Solucionar erros comuns de implantação do Azure | Microsoft Azure"
   description="Descreve como resolver erros comuns quando você implanta recursos para Azure usando o Gerenciador de recursos do Azure."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="Erro de implantação, implantação do azure, implantar para o azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solucionar erros comuns de implantação do Azure com o Gerenciador de recursos do Azure

Este tópico descreve como você pode resolver alguns comum erros de implantação Azure você pode encontrar. Se precisar de mais informações sobre o que deu errado com sua implantação, consulte primeiro [operações de implantação do modo de exibição](resource-manager-troubleshoot-deployments-portal.md) e, em seguida, volte para este artigo para obter ajuda com resolver o erro. As seções neste tópico lista o código de erro exibido.

## <a name="invalid-template"></a>Modelo inválido

Este erro pode resultar entre vários tipos de erros. 

### <a name="syntax-error"></a>Erro de sintaxe

Se você receber uma mensagem de erro que indica a validação de modelo falhou, você pode ter um problema de sintaxe no seu modelo. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Este erro é fácil fazer porque expressões de modelo podem ser complexas. Por exemplo, a atribuição de nome a seguir para uma conta de armazenamento contém um conjunto de chaves, três funções, três conjuntos de parênteses, um conjunto de aspas simples e uma propriedade:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Se você não fornecer a sintaxe correspondente, o modelo produz um valor que é diferente de sua intenção.

Quando você receber esse tipo de erro, examine cuidadosamente a sintaxe da expressão. Considere usar um editor de JSON como o [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou [Código do Visual Studio](resource-manager-vs-code.md), que pode avisá-lo sobre erros de sintaxe. 

### <a name="incorrect-segment-lengths"></a>Comprimentos de segmento incorreto

Outro erro de modelo inválido ocorre quando o nome do recurso não está no formato correto.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Um recurso de nível raiz deve ter um menos segmento no nome que no tipo de recurso. Cada segmento é diferenciado por uma barra. No exemplo a seguir, o tipo tem dois segmentos e o nome tem um segmento, portanto, é um **nome válido**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Mas o exemplo a seguir **não é um nome válido** porque ela tem o mesmo número de segmentos que o tipo.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Para recursos de filho, o tipo e o nome tem o mesmo número de segmentos. Este número de segmentos faz sentido porque o nome completo e o tipo para o filho inclui o nome de pai e tipo. Portanto, o nome completo ainda tem um menos segmento que o tipo de total. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Obter os segmentos direita tome cuidado com tipos de Gerenciador de recursos que são aplicados entre provedores de recursos. Por exemplo, aplicando um bloqueio de recursos a um site da web requer um tipo com quatro segmentos. Portanto, o nome é três segmentos:

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Copiar índice não é esperado

Você encontrar esse erro **InvalidTemplate** quando o elemento de **cópia** tiver aplicado a uma parte do modelo que não dá suporte a esse elemento. Só é possível aplicar o elemento de cópia para um tipo de recurso. Você não pode aplicar cópia para uma propriedade dentro de um tipo de recurso. Por exemplo, você aplicar cópia a uma máquina virtual, mas não é possível aplicá-lo para os discos do sistema operacional para uma máquina virtual. Em alguns casos, você pode converter um recurso de filho a um recurso de pai para criar um loop de cópia. Para obter mais informações sobre como usar cópia, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md).

### <a name="parameter-is-not-valid"></a>Parâmetro não é válido

Se o modelo especifica valores permitidos para um parâmetro, e você fornecer um valor que não seja um desses valores, você receberá uma mensagem semelhante ao seguinte erro:

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Duplo verificar os valores permitidos no modelo e forneça uma durante a implantação.

## <a name="not-found-or-resource-not-found"></a>Não encontrado (ou recurso não encontrado)

Ao seu modelo inclui o nome de um recurso que não pode ser resolvido, você recebe um erro semelhante a:

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Se você estiver tentando implantar o recurso ausente no modelo, verifique se você precisar adicionar uma dependência. Gerenciador de recursos otimiza implantação criando recursos em paralelo, quando possível. Se um recurso deve ser implantado após outro recurso, você precisa usar o elemento de **dependsOn** no seu modelo para criar uma dependência no outro recurso. Por exemplo, ao implantar um aplicativo web, o plano de serviço de aplicativo deve existir. Se você não especificou que o aplicativo web depende o plano de serviço de aplicativo, o Gerenciador de recursos cria os dois recursos ao mesmo tempo. Você recebe um erro informando que o recurso de plano de serviço de aplicativo não pode ser encontrado, porque ele ainda não existe durante a tentativa de definir uma propriedade do aplicativo web. Evitar esse erro definindo a dependência no web app.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
Você também ver esse erro quando o recurso existe em um grupo de recurso diferente daquela sendo implantado. Nesse caso, use a [função de identificação de recurso](resource-group-template-functions.md#resourceid) para obter o nome totalmente qualificado do recurso.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Se você tentar usar as funções de [referência](resource-group-template-functions.md#referenc) ou [listKeys](resource-group-template-functions.md#listkeys) com um recurso que não pode ser resolvido, você receberá o seguinte erro:

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Procure por uma expressão que inclui a função de **referência** . Verifique se os valores de parâmetro estão corretos.

## <a name="storage-account-already-exists-or-already-taken"></a>Já existe uma conta de armazenamento (ou já em uso)

Para contas de armazenamento, você deve fornecer um nome para o recurso que seja exclusivo entre Azure. Se você não fornecer um nome exclusivo, você receberá uma mensagem de erro como:

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Você pode criar um nome exclusivo concatenando a convenção de nomenclatura com o resultado da função [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Se você implantar uma conta de armazenamento com o mesmo nome de uma conta existente do armazenamento na sua assinatura, mas forneça um local diferente, você receberá um erro indicando que a conta de armazenamento já existe em um local diferente. Exclua a conta de armazenamento existente ou fornecer no mesmo local que a conta de armazenamento existente.

## <a name="account-name-invalid"></a>Nome de conta inválido

Você vê o erro **AccountNameInvalid** quando tentar dê um nome que inclui uma conta de armazenamento proibidas caracteres. Nomes de conta de armazenamento devem estar entre 3 e 24 caracteres e use números e apenas letras minúsculas.

## <a name="no-registered-provider-found"></a>Nenhum provedor registrado encontrado

Ao implantar o recurso, você pode receber o seguinte código de erro e a mensagem:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Você receber esse erro para um dos três razões:

1. Local não tem suportado para o tipo de recurso
2. Versão da API não tem suportado para o tipo de recurso
3. O provedor de recursos não foi registrado para sua assinatura

A mensagem de erro deve fornecer sugestões para as versões de API e locais com suporte. Você pode alterar o seu modelo para um dos valores sugeridos. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou a interface de linha que você está usando, mas não todos. Se você não tiver usado um provedor de determinado recurso antes, você talvez precise registrar esse provedor. Você pode descobrir mais sobre os provedores de recursos através do PowerShell ou CLI do Azure.

### <a name="powershell"></a>PowerShell

Para ver o status do registro, use o **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Para registrar um provedor, use **Register-AzureRmResourceProvider** e fornecer o nome do provedor do recurso que você deseja registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Para obter os locais com suporte para um determinado tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Para obter as versões de API com suporte para um determinado tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>CLI Azure

Para ver se o provedor está registrado, use o `azure provider list` comando.

    azure provider list

Para registrar um provedor de recursos, use o `azure provider register` comando e especifique o *namespace* para registrar.

    azure provider register Microsoft.Cdn

Para ver as versões de API e locais com suporte para um provedor de recursos, use:

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Operação não permitida

Você pode ter problemas quando implantação excede uma cota, o que poderia ser por grupo de recursos, assinaturas, contas e outros escopos. Por exemplo, sua assinatura pode ser configurada para limitar o número de cores para uma região. Se você tentar implantar uma máquina virtual com mais cores que o valor permitido, você recebe um erro informando que a cota foi excedida.
Para obter informações de cota concluída, consulte [assinatura do Azure e limites de serviço, cotas e restrições](azure-subscription-service-limits.md).

Para examinar as cotas da sua assinatura de cores, você pode usar o `azure vm list-usage` comando na CLI Azure. O exemplo a seguir ilustra que a cota de núcleo de uma conta de avaliação gratuita é 4:

    azure vm list-usage

Que retorna:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Se você implantar um modelo que cria mais de quatro cores na região Oeste EUA, você receberá um erro de implantação que se parece com:

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

Ou no PowerShell, você pode usar o cmdlet **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Que retorna:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Nesses casos, você deve acesse o portal e arquivo um problema de suporte para aumentar sua cota para a região em que você deseja implantar.

> [AZURE.NOTE] Lembre-se de que, para grupos de recursos, a cota é de cada região individual, não para a assinatura inteira. Se você precisar implantar 30 cores Oeste EUA, você terá que pedir 30 cores do Gerenciador de recursos Oeste EUA. Se você precisar implantar 30 cores em qualquer uma das regiões aos quais você tem acesso, você deve pedir 30 cores do Gerenciador de recursos em todas as regiões.

## <a name="invalid-content-link"></a>Link de conteúdo inválido

Quando você recebe a mensagem de erro:

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Provavelmente você tentou vincular a um modelo aninhado que não está disponível. Verifique o URI fornecido para o modelo aninhado. Se o modelo existe em uma conta de armazenamento, verifique se que o URI é acessível. Talvez você precise passar um token SAS. Para obter mais informações, consulte [usando modelos vinculados com o Gerenciador de recursos do Azure](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Falha de autorização

Você pode receber um erro durante a implantação porque a conta ou serviço principal tentar implantar os recursos não tem acesso para executar essas ações. Active Directory do Azure permite que você ou seu administrador para controlar quais identidades pode acessar quais recursos com um alto grau de precisão. Por exemplo, se sua conta for atribuída à função Leitor, você não é capazes de criar recursos. Nesse caso, você vê uma mensagem de erro indicando que a autorização falhou.

Para obter mais informações sobre controle de acesso baseado em função, consulte [Controle de acesso de Azure Role-Based](./active-directory/role-based-access-control-configure.md).

Além de controle de acesso baseado em função, suas ações de implantação podem ser limitadas por políticas com assinaturas. Por meio de políticas, o administrador pode impor convenções em todos os recursos implantados na assinatura. Por exemplo, um administrador pode exigir que um valor de marca específica for fornecido para um tipo de recurso. Se você não atender os requisitos da política, você receberá um erro durante a implantação. Para obter mais informações sobre políticas, consulte a [Política de uso para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Máquinas virtuais de solução de problemas

| Erro | Artigos |
| -------- | ----------- |
| Erros de extensão de script personalizado | [Falhas de extensão de máquina virtual do Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />ou<br />[Falhas de extensão Linux VM](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Imagem de SO erros de provisionamento | [Novos erros de máquina virtual do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />ou<br />[Novos erros de Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Falhas de alocação | [Falhas de alocação de máquina virtual do Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />ou<br />[Falhas de alocação de Linux VM](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Secure Shell (SSH) erros ao tentar se conectar | [Conexões Shell seguras para Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Erros de conexão com o aplicativo em execução na máquina virtual | [Aplicativo em execução na máquina virtual do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />ou<br />[Aplicativo em execução em uma VM Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Erros de conexão de área de trabalho remota | [Conexões de área de trabalho remota para máquina virtual do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Erros de Conexão resolvidos por reimplantar | [Reimplantar Máquina Virtual para novo nó Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Erros de serviço de nuvem | [Problemas de implantação de serviço de nuvem](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Outros serviços de solução de problemas

A tabela a seguir não é uma lista completa de tópicos de solução de problemas para o Azure. Em vez disso, ele se concentra nos problemas relacionados a implantação ou a configuração de recursos. Se precisar de ajuda para solucionar problemas de tempo de execução com um recurso, consulte a documentação para esse serviço Azure.

| Serviço | Artigo |
| -------- | -------- |
| Automação | [Dicas de solução de problemas para erros comuns em automação do Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Pilha Azure | [Solução de problemas do Microsoft Azure pilha](./azure-stack/azure-stack-troubleshooting.md) |
| Pilha Azure | [Web Apps e pilha Azure](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Fábrica de dados | [Solucionar problemas de fábrica de dados](./data-factory/data-factory-troubleshoot.md) |
| Estrutura de serviço | [Solucionar problemas comuns quando você implantar os serviços do Azure serviço tecidos](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperação de site | [Monitorar e solucionar problemas de proteção para máquinas virtuais e servidores físicos](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Armazenamento | [Monitorar, diagnosticar e solucionar problemas de armazenamento do Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Solucionar problemas de implantação de dispositivo StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Banco de dados SQL | [Solucionar problemas de conexão ao banco de dados do SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Depósito de dados do SQL | [Solução de problemas de depósito de dados do SQL Azure](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Compreender quando uma implantação está pronta

Gerenciador de recursos de Azure relatórios sucesso em uma implantação quando retornam todos os provedores de implantação com êxito. No entanto, essa mensagem não significa necessariamente que seu grupo de recursos é "ativa e pronto para seus usuários." Por exemplo, uma implantação talvez seja necessário baixar atualizações, aguarde recursos não modelo ou instalar scripts complexos. Gerenciador de recursos não sabe quando essas tarefas concluem porque eles não são atividades que um provedor rastreia. Nesses casos, pode ser algum tempo antes de seus recursos estão prontos para uso do mundo real. Como resultado, você deve esperar que o status da implantação é bem sucedida algum tempo antes de sua implantação pode ser usada.

Você pode impedir que o Azure relatar o sucesso da implantação, no entanto, criando um script personalizado para seu modelo personalizado. O script sabe como monitorar a implantação inteira para preparação de todo o sistema e retorna com êxito somente quando os usuários podem interagir com a implantação inteira. Se você quiser garantir que sua extensão é a última para executar, use a propriedade **dependsOn** no seu modelo.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre ações de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](resource-group-audit.md).
- Para saber mais sobre ações para determinar os erros durante a implantação, consulte [operações de implantação do modo de exibição](resource-manager-troubleshoot-deployments-portal.md).
