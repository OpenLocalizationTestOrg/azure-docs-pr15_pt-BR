<properties
    pageTitle="Sincronização de dados offline em aplicativos móveis do Azure | Microsoft Azure"
    description="Visão geral do recurso de sincronização de dados offline para aplicativos do Azure móvel e referência conceitual"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de dados offline em aplicativos móveis do Azure

## <a name="what-is-offline-data-sync"></a>O que é sincronização de dados offline?

Sincronização de dados offline é um cliente e servidor recurso SDK do Azure Mobile aplicativos que torna mais fácil para os desenvolvedores criem aplicativos que são funcionais sem uma conexão de rede.

Quando o aplicativo está no modo offline, os usuários ainda podem criar e modificar dados, que serão salvas em um armazenamento local. Quando o aplicativo estiver online novamente, ele pode sincronizar alterações locais com seu back-end do aplicativo do Azure Mobile. O recurso também inclui suporte para detectar conflitos quando o mesmo registro é alterado no cliente e back-end. Em seguida, podem ser tratados conflitos no servidor ou o cliente.

Sincronização offline tem vários benefícios:

* Melhorar a capacidade de resposta do aplicativo em cache de dados do servidor localmente no dispositivo
* Criar aplicativos robustos que permanecerem útil quando há problemas de rede
* Permitir que os usuários finais criar e modificar dados, mesmo quando não há nenhum acesso de rede, com o suporte de cenários com pouca ou nenhuma conectividade
* Sincronizar dados em vários dispositivos e detectar conflitos quando o mesmo registro é modificado por dois dispositivos
* Limite o uso de rede em redes de alta latência ou medidas

Os seguintes tutoriais mostram como adicionar sincronização offline aos seus clientes móveis usando aplicativos do Azure Mobile:

* [Android: Habilitar a sincronização offline]
* [iOS: habilitar a sincronização offline]
* [IOS Xamarin: habilitar a sincronização offline]
* [Xamarin Android: Habilitar a sincronização offline]
* [Xamarin.Forms: Sincronização offline de habilitar](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma Windows universal: Habilitar a sincronização offline]

## <a name="what-is-a-sync-table"></a>O que é uma tabela de sincronização?

Para acessar o ponto de extremidade "/ tabelas", o cliente móvel do Azure SDKs fornecer interfaces como `IMobileServiceTable` (cliente .NET SDK) ou `MSTable` (iOS cliente). Essas APIs conectem diretamente ao aplicativo do Azure Mobile back-end e falharão se o dispositivo do cliente não tiver uma conexão de rede.

Para dar suporte ao uso offline, seu aplicativo deve usar a *tabela de sincronização* APIs, tais como `IMobileServiceSyncTable` (cliente .NET SDK) ou `MSSyncTable` (iOS cliente). Todos os mesmos operações de CRUD (criar, ler, atualizar, excluir) funcionam em relação à tabela de sincronização APIs, exceto agora elas serão ler ou gravar um *armazenamento local*. Antes de quaisquer operações de tabela de sincronização podem ser realizadas, o armazenamento local deve ser inicializado.

## <a name="what-is-a-local-store"></a>O que é um armazenamento local?

Um armazenamento local é a camada de persistência de dados no dispositivo cliente. SDK do cliente de aplicativos do Azure Mobile fornece uma implementação de armazenamento local padrão. No Windows, Xamarin e Android, ele se baseia SQLite; IOS, ele se baseia nos dados Core.

Para usar a implementação baseada em SQLite no Windows Phone ou Windows Store 8.1, você precisa instalar uma extensão de SQLite. Para obter mais detalhes, consulte [Universal plataforma Windows: habilitar a sincronização offline]. Android e iOS fornecidos com uma versão do SQLite no sistema operacional dispositivo em si, portanto, não é necessário fazer referência a sua versão do SQLite.

Os desenvolvedores também podem implementar seu próprio armazenamento local. Por exemplo, se você quiser armazenar dados em um formato criptografado no cliente móvel, você pode definir um local de armazenamento usa SQLCipher para criptografia.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?

Um *contexto de sincronização* está associado um objeto de cliente móvel (tais como `IMobileServiceClient` ou `MSClient`) e rastreia as alterações feitas com tabelas de sincronização. O contexto de sincronização mantém uma *fila de operação* que mantém uma lista ordenada de operações CUD (criar, atualizar, excluir) que posteriormente será enviada para o servidor.

Um armazenamento local está associado com o contexto de sincronização usando um método de inicialização como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK do cliente .NET].

## <a name="how-sync-works"></a>Offline como funciona a sincronização

Ao usar tabelas de sincronização, seu código de cliente controla quando as alterações locais serão sincronizadas com um back-end do aplicativo do Azure Mobile. Nada é enviado para o back-end até que haja uma chamada para alterações de local de *envio* . Da mesma forma, o armazenamento local é preenchido com novos dados somente quando há uma chamada para *extrair* dados.

* **Envio**: envio é uma operação no contexto de sincronização e envia todas as alterações CUD desde o último envio. Observe que não é possível enviar apenas alterações de uma tabela individual, pois caso contrário operações podem ser enviadas fora de ordem. Envio executa uma série de chamadas REST para seu aplicativo do Azure Mobile back-end, que por sua vez, irá modificar seu banco de dados do servidor.

* **Retirar**: recepção é executada em uma base por tabela e pode ser personalizada com uma consulta para recuperar somente um subconjunto dos dados de servidor. SDK de cliente do Azure Mobile, em seguida, insira os dados resultantes o armazenamento local.

* **Envia implícito**: se uma recepção é executada em uma tabela que tenha locais atualizações pendentes, a recepção primeiro executará um envio no contexto de sincronização. Isso ajuda a minimizar conflitos entre as alterações que já estão na fila e novos dados do servidor.

* **Sincronização incremental**: o primeiro parâmetro para a operação de recepção é um *nome de consulta* que é usado somente no cliente. Se você usar um nome de consulta não nulas, o SDK do Azure Mobile executará uma *sincronização incremental*.
  Sempre que uma operação de recepção retorna um conjunto de resultados, mais recente `updatedAt` carimbo de hora desse conjunto de resultado é armazenado nas tabelas de sistema local SDK. Operações de recepção subsequente recuperará somente os registros após esse carimbo de hora.

  Para usar a sincronização incremental, o servidor deve retornar significativo `updatedAt` valores e também deve oferecer suporte a classificação por esse campo. No entanto, como o SDK adiciona seu próprio classificar no campo updatedAt, você não pode usar uma consulta de recepção que tem seu próprio `$orderBy$` cláusula.

  O nome da consulta pode ser qualquer cadeia de caracteres que você escolher, mas ele deve ser exclusivo para cada consulta lógica em seu aplicativo.
  Caso contrário, operações de recebimento diferente podem substituir o carimbo de hora de sincronização incremental mesmo e suas consultas podem retornar resultados incorretos.

  Se a consulta tem um parâmetro, uma maneira de criar um nome de consulta exclusivo é incorporar o valor do parâmetro.
  Por exemplo, se você estiver filtrando em ID de usuário, o nome da consulta pode ser da seguinte maneira (em c#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se você deseja deixar de usar a sincronização incremental, passe `null` como a ID de consulta. Nesse caso, todos os registros serão recuperados em cada chamada para `PullAsync`, que é potencialmente ineficaz.

* **Purging**: você pode limpar o conteúdo do armazenamento local usando `IMobileServiceSyncTable.PurgeAsync`.
  Isso pode ser necessário se você tiver dados obsoletos do banco de dados do cliente, ou se você quiser descartar todas as alterações pendentes.

  Uma limpeza limpará uma tabela do armazenamento local. Se houver operações pendentes sincronização com o banco de dados do servidor, a limpeza será lançar uma exceção, a menos que o parâmetro *force limpar* está definido.

  Como um exemplo de dados obsoletos no cliente, suponha que no exemplo "lista de tarefas pendentes", Device1 extrai somente os itens que não são concluídos. Em seguida, um todoitem "Comprar leite" está marcado como concluído no servidor por outro dispositivo. No entanto, Device1 ainda terá o todoitem "Comprar leite" no repositório local porque ele está recebendo somente os itens que não são marcadas como concluídos. Uma limpeza limpará este item obsoleto.

## <a name="next-steps"></a>Próximas etapas

* [iOS: habilitar a sincronização offline]
* [IOS Xamarin: habilitar a sincronização offline]
* [Xamarin Android: Habilitar a sincronização offline]
* [Plataforma Windows universal: Habilitar a sincronização offline]

<!-- Links -->
[Cliente .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Habilitar a sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: habilitar a sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[IOS Xamarin: habilitar a sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Habilitar a sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Plataforma Windows universal: Habilitar a sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
