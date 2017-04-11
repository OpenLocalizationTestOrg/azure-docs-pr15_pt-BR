<properties 
    pageTitle="Nova versão de esquema 2016-06-01 | Microsoft Azure" 
    description="Aprenda como escrever a definição de JSON para a versão mais recente dos aplicativos de lógica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Nova versão de esquema 2016-06-01

A API e o novo esquema versão para aplicativos de lógica possui um número de aprimoramentos que melhorar a confiabilidade e facilidade de uso dos aplicativos de lógica. Existem 3 principais diferenças:

1. Adição de escopos, que são as ações que contêm um conjunto de ações.
1. Condições e loops são ações de primeira classe
1. Execução ordenação mais detalhado via `runAfter` propriedade (que substitui `dependsOn`)

Para obter informações sobre como atualizar seus aplicativos de lógica do esquema de 2015-08-01-preview para o esquema de 2016-06-01, [Confira a seção atualização abaixo.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. escopos de

Uma das principais alterações neste esquema é a adição de escopos e a capacidade de aninhar ações entre si.  Isso é útil quando agrupar um conjunto de ações, ou quando precisar aninhar ações entre si (por exemplo uma condição pode conter outra condição).  Mais detalhes sobre sintaxe de escopo podem ser encontradas [aqui](app-service-logic-loops-and-scopes.md), mas um exemplo simples de escopo pode ser encontradas abaixo:


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. condições e loops alterações

Nas versões anteriores do esquema, condições e loops foram parâmetros associados a uma única ação.  Essa limitação tem foi ativada neste esquema e agora condições e loops aparecem como um tipo de ação.  Mais informações podem ser encontradas [neste artigo](app-service-logic-loops-and-scopes.md)e um exemplo simples de uma ação de condição é mostrado abaixo:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. propriedade RunAfter

O novo `runAfter` propriedade está substituindo `dependsOn` para ajudar a permitir mais precisão em execução ordenação.  `dependsOn`era sinônimo "a ação executou e foi bem-sucedida," no entanto, muitas vezes você precisa executar uma ação se a ação anterior for bem-sucedida, falha ou ignorada.  `runAfter`permite que flexibilidade.  Ele é um objeto que especifica todos os nomes de ação, que ele será executado após e define uma matriz de status que são aceitáveis para acionar de.  Por exemplo, se você quiser executar após etapas bem-sucedida A e B foi bem-sucedida ou falha, você poderia construir o seguinte `runAfter` propriedade:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Atualização para o esquema de 2016-06-01

Atualização para o novo esquema de 2016-06-01 leva apenas algumas etapas.  Para obter detalhes sobre as alterações do esquema podem ser encontradas [neste artigo](app-service-logic-schema-2016-04-01.md).  O processo de atualização inclui executando o script de atualização, salvar como um novo aplicativo de lógica e potencialmente sobrescrever antigo aplicativo lógica se necessário.

1. Abra seu aplicativo de lógica atual.
1. Clique no botão de **Esquema de atualização** na barra de ferramentas
   
    ![][1]
   
    A definição atualizada será retornada.  Você pode copiar e colar isso em uma definição de recurso, se for necessário, mas podemos **altamente recomendável** que você use o botão **Salvar como** para garantir que a conexão de todas as referências são válidas no aplicativo atualizado lógica.
1. Clique no botão **Salvar como** na barra de ferramentas da lâmina atualização.
1. Preencha o status de aplicativo nome e lógica e clique em **criar** para implantar o seu aplicativo de lógica de atualização.
1. Verificar que seu aplicativo atualizado lógica está funcionando conforme esperado.

    >[AZURE.NOTE] Se você estiver usando um disparador manual ou solicitação, a URL de retorno de chamada terá sido alterado em seu novo aplicativo de lógica.  Use a nova URL para verificar se ele funciona ponta a ponta, e você pode clonar em seu aplicativo de lógica existente para preservar URLs anteriores.

1. *Opcional* Use o botão de **clonar** na barra de ferramentas (adjacente para o ícone de **Esquema de atualização** na imagem acima) para substituir o seu aplicativo de lógica anterior com a nova versão de esquema.  Isso é necessário apenas se você quiser manter a mesma identificação do recurso ou solicitar disparador URL de seu aplicativo de lógica.

### <a name="upgrade-tool-notes"></a>Ferramenta de atualização anotações

#### <a name="condition-mapping"></a>Mapeamento de condição

A ferramenta fará um melhor esforço para agrupar as ações de ramificação true e false juntos em um escopo na definição atualizado.  Especificamente o designer padrão de `@equals(actions('a').status, 'Skipped')` deve aparecer como uma `else` ação.  Entretanto, se a ferramenta detectar padrões não reconhece-potencialmente irão Crie condições separadas para o verdadeiro e falsa ramificação.  Ações podem ser mapeadas novamente postar atualização, se necessário.

#### <a name="foreach-with-condition"></a>ForEach condição
  
O padrão de um loop foreach com uma condição por item anterior pode ser replicado no novo esquema com a ação de filtro.  Isso deve ocorrer automaticamente na atualização.  A condição se torne uma ação de filtro antes do loop foreach (para retornar somente uma matriz de itens que correspondem a condição) e essa matriz é passada para a ação de foreach.  Você pode exibir um exemplo [neste artigo](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Marcas de recurso

Marcas de recurso serão removidas na atualização e você precisará configurá-las novamente para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="manual-trigger-renamed-to-request-trigger"></a>Manual disparadores renomeado para solicitação disparadores

O tipo `manual` foi obsoleta e renomeado para `request` com o tipo de `http`.  Isso é mais consistente com o tipo de padrão que o disparador é usado para criar.

### <a name="new-filter-action"></a>Nova ação 'filtro'

Se você estiver trabalhando com uma matriz grande e a necessidade de filtragem para baixo até um conjunto menor de itens, você pode usar o novo tipo de 'filtro'.  Ele aceita uma matriz e uma condição e irá avaliar a condição para cada item e retornar uma matriz de itens que atendem à condição.

### <a name="foreach-and-until-action-restrictions"></a>ForEach e até restrições de ação

Foreach e até loop estiver restrito a uma única ação.

### <a name="trackedproperties-on-actions"></a>TrackedProperties em ações

Ações agora podem ter uma propriedade adicional (irmãos para `runAfter` e `type`) chamado `trackedProperties`.  É um objeto que especifica a determinadas entradas de ação ou saídas devem constar a diagnóstico do Azure telemetria que é emitida como parte de um fluxo de trabalho.  Por exemplo:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
- [Usar a definição de fluxo de trabalho do aplicativo de lógica](app-service-logic-author-definitions.md)
- [Criar um modelo de implantação do aplicativo de lógica](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png
