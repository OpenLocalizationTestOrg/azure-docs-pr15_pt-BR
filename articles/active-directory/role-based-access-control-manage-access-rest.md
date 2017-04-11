<properties
    pageTitle="Gerenciando o controle de acesso baseado em função com a API REST"
    description="Gerenciando o controle de acesso baseado em função com a API REST"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>Gerenciando o controle de acesso baseado em função com a API REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

Controle de acesso baseado em função (RBAC) no Portal do Azure e API do Gerenciador de recursos do Azure ajuda você a gerenciar o acesso à sua assinatura e recursos em um nível refinado. Com esse recurso, você pode conceder acesso para usuários, grupos ou objetos de serviço do Active Directory atribuindo algumas funções-los em um escopo específico.

## <a name="list-all-role-assignments"></a>Lista todas as atribuições de função

Lista todas as atribuições de função no escopo especificado e subscopes.

Para atribuições de função de lista, você deve ter acesso ao `Microsoft.Authorization/roleAssignments/read` operação no escopo. Todas as funções internas têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método **obter** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo para o qual você deseja listar as atribuições de função. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{versão da api}* 2015-07-01.

3. Substitua *{filtro}* com a condição que você deseja aplicar para filtrar a lista de atribuição de função:

  - Lista as atribuições de função para somente o escopo especificado, não incluindo as atribuições de funções em subscopes:`atScope()`    
  - Lista as atribuições de função para um usuário específico, o grupo ou o aplicativo:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - Lista de atribuições de função para um usuário específico, inclusive aquelas herdadas de grupos |`assignedTo('{objectId of user}')`

### <a name="response"></a>Resposta

Código de status: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Obter informações sobre uma atribuição de função

Obtém informações sobre uma atribuição de função única especificada pelo identificador de atribuição de função.

Para obter informações sobre uma atribuição de função, você deve ter acesso ao `Microsoft.Authorization/roleAssignments/read` operação. Todas as funções internas têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método **obter** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo para o qual você deseja listar as atribuições de função. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de atribuição de função}* com o identificador GUID da atribuição de função.

3. Substitua *{versão da api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de status: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

Crie uma atribuição de função no escopo especificado para o capital especificado conceder a função especificada.

Para criar uma atribuição de função, você deve ter acesso aos `Microsoft.Authorization/roleAssignments/write` operação. Das funções internas, somente o *proprietário* e o *Administrador de acesso de usuários* têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método de **colocar** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo no qual você deseja criar as atribuições de função. Quando você cria uma atribuição de função em um escopo pai, todos os escopos filho herdam a mesma atribuição de função. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1   
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de atribuição de função}* com um novo GUID, que se torna o identificador GUID da nova atribuição de função.

3. Substitua *{versão da api}* 2015-07-01.

Para o corpo da solicitação, forneça os valores no seguinte formato:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nome do elemento     | Necessário | Tipo   | Descrição |
|------------------|----------|--------|-------------|
| roleDefinitionId | Sim      | Cadeia de caracteres | O identificador da função. O formato do identificador é:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | Sim      | Cadeia de caracteres | ID do objeto do Azure AD principal (usuário, grupo ou entidade de serviço) ao qual a função é atribuída. |

### <a name="response"></a>Resposta

Código de status: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Excluir uma atribuição de função

Exclua uma atribuição de função no escopo especificado.

Para excluir uma atribuição de função, você deve ter acesso ao `Microsoft.Authorization/roleAssignments/delete` operação. Das funções internas, somente o *proprietário* e o *Administrador de acesso de usuários* têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método **DELETE** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo no qual você deseja criar as atribuições de função. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de atribuição de função}* com a id de atribuição de função GUID.

3. Substitua *{versão da api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de status: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Lista todas as funções

Lista todas as funções disponíveis para atribuição no escopo especificado.

Funções de lista, você deve ter acesso ao `Microsoft.Authorization/roleDefinitions/read` operação no escopo. Todas as funções internas têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método **obter** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo para o qual você deseja listar as funções. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{versão da api}* 2015-07-01.

3. Substitua *{filtro}* com a condição que você deseja aplicar para filtrar a lista de funções:

  - Lista funções disponíveis para atribuição no escopo especificado e quaisquer de seus escopos filho:`atScopeAndBelow()`
  - Pesquisa para uma função usando o nome de exibição exato: `roleName%20eq%20'{role-display-name}'`. Use o formulário de codificada de URL do nome para exibição exato da função. Por exemplo,`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Resposta

Código de status: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Obter informações sobre uma função

Obtém informações sobre uma única função especificada pelo identificador de definição de função. Para obter informações sobre uma única função usando seu nome de exibição, consulte a [lista de todas as funções](role-based-access-control-manage-access-rest.md#list-all-roles).

Para obter informações sobre uma função, você deve ter acesso ao `Microsoft.Authorization/roleDefinitions/read` operação. Todas as funções internas têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método **obter** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo para o qual você deseja listar as atribuições de função. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* com o identificador GUID da definição de função.

3. Substitua *{versão da api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de status: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Crie uma função personalizada
Crie uma função personalizada.

Para criar uma função personalizada, você deve ter acesso aos `Microsoft.Authorization/roleDefinitions/write` operação em todos os `AssignableScopes`. Das funções internas, somente o *proprietário* e o *Administrador de acesso de usuários* têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método de **colocar** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* a primeira *AssignableScope* da função personalizada. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis.

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* com um novo GUID, que se torna o identificador GUID da nova função personalizada.

3. Substitua *{versão da api}* 2015-07-01.

Para o corpo da solicitação, forneça os valores no seguinte formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome do elemento | Necessário | Tipo | Descrição |
|--------------|----------|------|-------------|
| nome         | Sim | Cadeia de caracteres   | Identificador GUID da função personalizada.    |
| properties.roleName               | Sim | Cadeia de caracteres   | Exibe o nome da função personalizada. Caracteres de tamanho máximo 128.                        |
| Properties.Description            | Não  | Cadeia de caracteres   | Descrição da função personalizada. Caracteres de tamanho máximo 1024.                                               |
| Properties.Type                   | Sim | Cadeia de caracteres   | Defina como "CustomRole".                                         |
| Properties.Permissions.Actions    | Sim | String] | Uma matriz de cadeias de caracteres de ação especificando as operações concedidas pela função personalizada.             |
| properties.permissions.notActions | Não  | String] | Uma matriz de cadeias de caracteres de ação especificando as operações excluir das operações concedidas pela função personalizada. |
| properties.assignableScopes       | Sim | String] | Uma matriz de escopos no qual a função personalizada pode ser usada.   |

### <a name="response"></a>Resposta

Código de status: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Modificar uma função personalizada.

Para modificar uma função personalizada, você deve ter acesso ao `Microsoft.Authorization/roleDefinitions/write` operação em todos os `AssignableScopes`. Das funções internas, somente o *proprietário* e o *Administrador de acesso de usuários* têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método de **colocar** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* a primeira *AssignableScope* da função personalizada. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* com o identificador GUID da função personalizada.

3. Substitua *{versão da api}* 2015-07-01.

Para o corpo da solicitação, forneça os valores no seguinte formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome do elemento | Necessário | Tipo | Descrição |
|--------------|----------|------|-------------|
| nome         | Sim      | Cadeia de caracteres | Identificador GUID da função personalizada. |
| properties.roleName | Sim | Cadeia de caracteres | Exibe o nome da função personalizada atualizada. |
| Properties.Description | Não | Cadeia de caracteres | Descrição da função personalizada atualizada. |
| Properties.Type | Sim | Cadeia de caracteres | Defina como "CustomRole". |
| Properties.Permissions.Actions | Sim | String] | Uma matriz de cadeias de caracteres de ação especificando as operações ao qual a função personalizada atualizada concede acesso. |
| properties.permissions.notActions | Não | String] | Uma matriz de cadeias de caracteres de ação especificando as operações excluir das operações que concede a função personalizada atualizada. |
| properties.assignableScopes | Sim | String] | Uma matriz de escopos no qual a função personalizada atualizada pode ser usada. |

### <a name="response"></a>Resposta

Código de status: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Exclua uma função personalizada.

Para excluir uma função personalizada, você deve ter acesso ao `Microsoft.Authorization/roleDefinitions/delete` operação em todos os `AssignableScopes`. Das funções internas, somente o *proprietário* e o *Administrador de acesso de usuários* têm acesso a essa operação. Para obter mais informações sobre atribuições de função e gerenciando acesso para recursos Azure, consulte [Controle de acesso de Azure Role-Based](role-based-access-control-configure.md).

### <a name="request"></a>Solicitação

Use o método **DELETE** com o seguinte URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, verifique as seguintes substituições para personalizar sua solicitação:

1. Substitua *{escopo}* com o escopo no qual você deseja excluir a definição de função. Os exemplos a seguir mostram como especificar o escopo para diferentes níveis:

  - Assinatura: /subscriptions/ {id da assinatura}  
  - Grupo de recursos: /subscriptions/ {id da assinatura} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* com a id de definição de função GUID da função personalizada.

3. Substitua *{versão da api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de status: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
