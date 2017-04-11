<properties
    pageTitle="Sincronização do Azure AD Connect: Gerenciador de serviços de sincronização UI | Microsoft Azure"
    description="Entenda a guia de operações no Gerenciador de serviço de sincronização do Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronização do Azure AD Connect: Gerenciador de serviços de sincronização

[Operações](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Designer de metaverso](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Pesquisa metaversa](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Gerenciador de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Guia operações mostra os resultados das operações mais recentes. Essa guia é fundamental para entender e solucionar problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Entender as informações visíveis na guia operações
A metade superior mostra todas as execuções em ordem crônicos. Por padrão, o log de operações mantém informações sobre últimos sete dias, mas essa configuração pode ser alterada com o [Agendador](active-directory-aadconnectsync-feature-scheduler.md). Você deseja procurar qualquer execução que não mostra um status de sucesso. Você pode alterar a classificação clicando nos cabeçalhos.

Coluna **Status** é as informações mais importantes e mostra o problema mais grave uma execução. Apresentamos aqui um resumo dos status mais comuns em ordem de prioridade para investigar (onde * indicar várias cadeias de caracteres de erro possíveis).

Status | Comentário
--- | ---
parado-* | A execução não pôde ser concluída. Por exemplo, se o sistema remoto está inoperante e não pode ser contatado.
limite parou de erros | Há mais de 5.000 erros. Executar automaticamente parou devido ao grande número de erros.
concluído -\*-erros | Executar concluída, mas há erros (menos de 5.000) que devem ser investigados.
concluído -\*-avisos | A execução concluída, mas alguns dados não está no estado esperado. Se você tiver erros, essa mensagem geralmente é apenas um sintoma. Até que você resolveu erros, você deve investigar não avisos.
sucesso | Não há problemas.

Quando você seleciona uma linha, na parte inferior atualiza para mostrar os detalhes do que são executadas. Para a esquerda da parte inferior, você pode ter uma lista dizendo **etapa #**. Esta lista só aparecerá se você tiver vários domínios na floresta onde cada domínio é representado por uma etapa. O nome de domínio pode ser encontrado sob o título **partição**. Em **Estatísticas de sincronização**, você pode encontrar mais informações sobre o número de alterações que foram processadas. Você pode clicar nos links para obter uma lista dos objetos alterados. Se você tiver objetos com erros, esses erros aparecem em **Erros de sincronização**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Solucionar erros no guia operações
![Gerenciador de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Quando você tiver erros, os dois o objeto no erro e o erro em si são links que fornece mais informações.

Comece clicando a cadeia de caracteres de erro (**sincronização regra-erro-função-disparadas** na imagem). Você primeiro é apresentadas com uma visão geral do objeto. Para ver o erro real, clique no botão **Rastreamento da pilha**. Este rastreamento fornece informações de nível de depuração para o erro.

**Dica:** Você pode com o botão direito na caixa de **informações de pilha de chamadas** , escolha **Selecionar tudo**e **Copiar**. Em seguida, você pode copiar pilha e examine o erro em seu favorito editor, como o bloco de notas.

- Se o erro for de **SyncRulesEngine**, em seguida, as informações de pilha de chamada primeiro tem uma lista de todos os atributos no objeto. Role para baixo até ver o título **InnerException = >**.  
![Gerenciador de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
Linha após mostra o erro. Na imagem acima, o erro é de um Fabrikam de regra de sincronização personalizado criado.

Se o erro em si não dá informações suficientes, é hora de examinar os dados em si. Você pode clicar no link com o identificador de objeto e [siga um objeto e seus dados por meio do sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
