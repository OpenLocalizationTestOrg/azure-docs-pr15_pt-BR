<properties
   pageTitle="Notificações de serviços confiáveis | Microsoft Azure"
   description="Documentação conceitual para notificações de serviço tecidos confiável serviços"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Notificações de serviços confiáveis

Notificações de permitir que os clientes controlar as alterações que estão sendo feitas a um objeto que ele estão interessados. Notificações de suporte de dois tipos de objetos: *Gerenciador de estado confiável* e *Dicionário confiável*.

Motivos comuns para usando notificações são:

- Construção materializada exibições, como índices secundários ou agregado modos de exibição filtrados de estado da réplica. Um exemplo é um índice classificado de todas as chaves no dicionário confiável.
- Enviar dados de monitoramento, como o número de usuários adicionados na última hora.

Notificações são acionadas como parte da aplicação de operações. Devido a isso, as notificações devem ser tratadas tão rápido quanto eventos possíveis e síncronos não devem incluir qualquer operações caras.

## <a name="reliable-state-manager-notifications"></a>Notificações de Gerenciador de estado de confiável

Gerenciador de estado confiável fornece notificações para os seguintes eventos:

- Transação
    - Confirmação
- Gerenciador de estado
    - Recriar
    - Adição de um estado confiável
    - Remoção de um estado confiável

Gerenciador de estado confiável rastreia as transações inflight atual. A única alteração no estado de transação que faz com que uma notificação para ser acionado é uma transação sendo confirmada.

Gerenciador de estado confiável mantém um conjunto de estados confiáveis como dicionário confiável e fila confiável. Gerenciador de estado confiável dispara notificações quando essa coleção é alterada: um estado confiável for adicionado ou removido ou toda a coleção é reconstruída.
O conjunto de Gerenciador de estado confiável é recriado em três casos:

- Recuperação: Quando uma réplica for iniciado, ele recupera seu estado anterior do disco. No final da recuperação, ele usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados confiáveis recuperados.
- Cópia completa: antes de uma réplica pode ingressar o conjunto de configuração, ela tem a ser criado. Às vezes, isso exige uma cópia completa do estado do Gerenciador de estado confiável da réplica primária a ser aplicado à réplica secundária ociosa. Gerenciador de estado de confiável na réplica secundário usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados confiáveis que ele adquirida da réplica principal.
- Restaurar: Em cenários de recuperação de desastres, estado da réplica pode ser restaurado de um backup via **RestoreAsync**. Nesses casos, o Gerenciador de estado confiável na réplica primária usa **NotifyStateManagerChangedEventArgs** para disparar um evento que contém o conjunto de estados confiáveis que ele restaurado do backup.

Para registrar para notificações de transação e/ou notificações de Gerenciador de estado, você precisa registrar com os eventos **TransactionChanged** ou **StateManagerChanged** no Gerenciador de estado confiável. Um lugar comum para registrar com esses manipuladores de eventos é o construtor de seu serviço de estado. Quando você se registrar no construtor, você não perder qualquer notificação que é causada por uma alteração durante a vida útil do **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Manipulador de eventos **TransactionChanged** usa **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Ele contém a propriedade de ação (por exemplo, **NotifyTransactionChangedAction.Commit**) que especifica o tipo de alteração. Ele também contém a propriedade de transação que fornece uma referência à transação que foram alterados.

>[AZURE.NOTE] Hoje, **TransactionChanged** são gerados somente se a transação for confirmada. A ação, em seguida, é igual a **NotifyTransactionChangedAction.Commit**. Mas, no futuro, eventos podem ser gerados para outros tipos de alterações de estado de transação. É recomendável verificar a ação e processamento do evento somente se ele for um que você espera.

A seguir é um manipulador de eventos de **TransactionChanged** de exemplo.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Manipulador de eventos **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Você pode usar a propriedade de ação no **NotifyStateManagerChangedEventArgs** convertê **NotifyStateManagerChangedEventArgs** para a subclasse correta:

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** e **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

A seguir está um exemplo de manipulador de notificação de **StateManagerChanged** .

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificações de dicionário confiáveis

Dicionário confiável fornece notificações para os seguintes eventos:

- Recompilar: Chamado quando **ReliableDictionary** recuperou seu estado de um estado local recuperado ou copiado ou um backup.
- Limpar: Chamado quando o estado de **ReliableDictionary** foi desmarcado por meio do método **ClearAsync** .
- Adicione: Chamado quando um item foi adicionado à **ReliableDictionary**.
- Atualização: Chamado quando um item em **IReliableDictionary** foi atualizado.
- Remover: Chamado quando um item em **IReliableDictionary** foi excluído.

Para obter notificações de dicionário confiável, você precisa registrar o manipulador de eventos **DictionaryChanged** em **IReliableDictionary**. Um lugar comum para registrar com esses manipuladores de eventos está na **ReliableStateManager.StateManagerChanged** adicionar notificação.
Registrando quando **IReliableDictionary** é adicionado ao **IReliableStateManager** garante que você não perca as notificações.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** é o método de amostra que chama **OnStateManagerChangedHandler** exemplo anterior.

O código anterior define a interface de **IReliableNotificationAsyncCallback** , juntamente com **DictionaryChanged**. Porque **NotifyDictionaryRebuildEventArgs** contém uma interface de **IAsyncEnumerable** – que precisa ser enumerado assíncrona – reconstruir notificações são acionados por meio de **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] No código anterior, como parte do processamento a notificação de recriação, primeiro o estado agregado mantido está desmarcado. Como o conjunto confiável está sendo recriado com um novo estado, todas as notificações anterior são sem importância.

Manipulador de eventos **DictionaryChanged** usa **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyDictionaryChangedEventArgs** tem cinco subclasses. Use a propriedade de ação no **NotifyDictionaryChangedEventArgs** convertê **NotifyDictionaryChangedEventArgs** para a subclasse correta:

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendações

- *Execute* completas eventos de notificação tão rápido quanto possível.
- *Não* executar quaisquer operações caras (por exemplo, operações de i/o) como parte de eventos sincronizados.
- *Faça* check o tipo de ação antes de processar o evento. Novos tipos de ação podem ser adicionados no futuro.

Aqui estão algumas coisas a ter em mente:

- Notificações são acionadas como parte da execução de uma operação. Por exemplo, uma notificação de restauração é acionada como a última etapa de uma operação de restauração. Uma restauração não será concluída até que o evento de notificação é processado.
- Como as notificações são acionadas como parte das operações de aplicação, clientes ver somente notificações para operações de localmente confirmadas. E porque operações garante sejam apenas localmente confirmada (em outras palavras, conectado), eles podem ou não pode ser desfeitos no futuro.
- No caminho refazer, uma única notificação é acionada para cada operação aplicada. Isso significa que se transação T1 inclui Create(X), Delete(X) e Create(X), você receberá uma notificação para a criação de X, uma para a exclusão e outra para a criação novamente, nessa ordem.
- Para as transações que contêm várias operações, operações são aplicadas na ordem em que foram recebidos na réplica primária do usuário.
- Como parte do processamento andamento false, algumas operações podem ser desfeitas. Notificações são geradas para essas operações de desfazer, revertendo o estado da réplica a um ponto estável. Uma diferença importante das notificações de desfazer é que eventos que têm chaves duplicadas são agregados. Por exemplo, se está sendo desfeita transação T1, você verá uma notificação única para Delete(X).

## <a name="next-steps"></a>Próximas etapas

- [Conjuntos de confiáveis](service-fabric-work-with-reliable-collections.md)
- [Início rápido de serviços confiável](service-fabric-reliable-services-quick-start.md)
- [Serviços confiáveis de backup e restauração (recuperação)](service-fabric-reliable-services-backup-restore.md)
- [Referência do desenvolvedor para conjuntos de confiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
