<properties
   pageTitle="Interagir com relatórios usando a API do JavaScript | Microsoft Azure"
   description="Power BI inserida, interagir com relatórios de uso da API do JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir com relatórios do Power BI usando a API do JavaScript

A API do JavaScript do Power BI permite incorporar facilmente relatórios do Power BI em seus aplicativos. Com a API, seus aplicativos programaticamente podem interagir com elementos de relatório diferente, como páginas e filtros. Este interatividade torna relatórios do Power BI mais integrada parte do seu aplicativo.

Você pode inserir um relatório do Power BI em seu aplicativo usando um iframe que está hospedado como parte do aplicativo. O iframe atua como um limite entre seu aplicativo e o relatório, como você pode ver na imagem a seguir. 

![Power BI incorporado iframe sem API do Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

O iframe facilita o processo de incorporação muito, mas sem a API do JavaScript o relatório e o seu aplicativo não podem interagir uns com os outros. Essa falta de interação pode tornar acha que o relatório não é realmente parte do aplicativo. O relatório e o aplicativo realmente precisam comunicar trocadas, como a imagem a seguir.

![Power BI incorporado iframe com a API do Javascript](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

A API do JavaScript do Power BI permite que você escrever código que com segurança pode passar pelo limite iframe. Isso permite que seu aplicativo programaticamente execute uma ação em um relatório e para escutar eventos de ações que os usuários fazem dentro do relatório.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>O que você pode fazer com a API do JavaScript do Power BI?
Com a API do JavaScript que você pode gerenciar relatórios, navegar para páginas em um relatório, filtrar um relatório e lidar com a incorporação de eventos. O diagrama a seguir mostra a estrutura da API.

![Diagrama de API do JavaScript de BI de energia](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Gerenciar relatórios
A API do Javascript permite que você gerencie o comportamento no nível do relatório e de página:

- Inserir um relatório de BI do Power específico com segurança em seu aplicativo - Experimente o [aplicativo de demonstração de inserção](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Token de acesso do conjunto
- Configurar o relatório
  - Ativar e desativar o painel filtro e o painel de navegação de página - tente [atualizar o aplicativo de demonstração de configurações](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Definir padrões para páginas e filtros - tente a [Definir demonstração de padrões](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Entrar e sair do modo de tela inteira

[Saiba mais sobre a inserção de um relatório](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Navegar para páginas em um relatório
A API do JavaScript enbales descobrir todas as páginas em um relatório e definir a página atual. Experimente o [aplicativo de demonstração de navegação](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Saiba mais sobre navegação de página](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrar um relatório
A API do JavaScript fornece básicos e avançados de filtragem de recursos para relatórios incorporados e páginas do relatório. Experimente o [aplicativo de demonstração de filtragem](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)e revise algumas código introdutório aqui.  


#### <a name="basic-filters"></a>Filtros básicos
Um filtro básico é colocado em um coluna ou um nível hierárquico e contém uma lista de valores para incluir ou excluir.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Filtros avançados
Filtros avançados usam o operador lógico e ou ou e aceitar uma ou duas condições, cada uma com suas próprias operador e o valor. Condições com suporte são:

- Nenhum
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- Contém
- DoesNotContain
- StartsWith
- DoesNotStartWith
- É
- IsNot
- ÉCÉL
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Saiba mais sobre filtragem](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Manipulação de eventos
Além de enviar informações para o iframe, seu aplicativo também pode receber informações sobre os seguintes eventos que vêm do iframe:

- Incorporar
  - carregado
  - Erro
- Relatórios
  - pageChanged
  - dataSelected (em breve)

[Saiba mais sobre a manipulação de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a API do JavaScript do Power BI, confira os links a seguir:

- [Wiki de API do JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Referência do modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Amostras
  - [Angular](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Demonstração ao vivo](https://microsoft.github.io/PowerBI-JavaScript/demo/)
