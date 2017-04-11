<properties
    pageTitle="Como migrar aplicativos de lógica para esquema versão 2015-08-01-visualização | Serviço de aplicativo do Microsoft Azure"
    description="Você pode migrar facilmente seus aplicativos de lógica para a versão mais recente do esquema. Siga estas etapas."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Como migrar aplicativos de lógica ao esquema versão 2015-08-01-preview

Para mover seus aplicativos de lógica existente para o novo esquema, faça o seguinte:  
1. Abra seu aplicativo de lógica no portal do Azure  
2. Clique em esquema de atualização:

 ![Ícone de API][step1]   
A página de esquema de atualização exibe e fornece um link para um documento que fornecem detalhes sobre os aperfeiçoamentos no novo esquema: ![API do ícone][step2]

>[AZURE.NOTE] Quando você seleciona o **Esquema de atualização**, podemos automaticamente executar as etapas de migração e fornecer a saída de código para você. Você pode usar isso para atualizar a definição, no entanto, certifique-se de que você siga boas práticas de codificação como aqueles descrito na seção de **práticas recomendadas** abaixo.

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Práticas recomendadas ao migrar seus aplicativos de lógica para a versão mais recente do esquema:  

- Copie o script migrado para um novo aplicativo de lógica - não substituir o antigo um até concluir seus testes e confirmado o aplicativo migrado funciona conforme esperado.
- Teste sua lógica aplicativo **antes de** colocar em produção
- Após a conclusão da migração, comece atualizando seus aplicativos de lógica para usar as [APIs gerenciadas](./apis-list.md) sempre que possível. Por exemplo, você pode começar a usar o Dropbox v2, whereever você estiver usando o DropBox v1.


## <a name="whats-next"></a>Qual é a próxima
-  [Saiba como migrar manualmente os aplicativos de lógica](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






