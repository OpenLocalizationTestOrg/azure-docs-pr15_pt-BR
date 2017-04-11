<properties
    pageTitle="Trabalhando com conjuntos de confiáveis | Microsoft Azure"
    description="Aprenda as práticas recomendadas para trabalhar com conjuntos de confiável."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Trabalhando com conjuntos de confiáveis

Serviço tecidos oferece um modelo de programação disponível para desenvolvedores .NET via conjuntos confiável. Especificamente, serviço tecidos fornece dicionário confiável e classes fila confiável. Quando você usa essas classes, seu estado é particionado (para escalabilidade), replicado (por disponibilidade) e transação dentro de uma partição (para semântica ACID). Vamos examinar um uso típico de um objeto de dicionário confiável e ver quais sua realmente fazendo.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Todas as operações em objetos de dicionário confiável (exceto para ClearAsync que não é possível refazer), exigem um objeto ITransaction. Esse objeto tem associada a ele qualquer e todas as alterações que você está tentando tornar para qualquer dicionário confiável e/ou confiável fila objetos em uma única partição. Adquirir uma ITransaction objeto chamando a partição do método de CreateTransaction do VisualStateManager.
 
No código acima, o objeto ITransaction é passado ao método de AddAsync do dicionário confiável. Internamente, métodos de dicionário que aceita uma chave levar um bloqueio de leitor/gravador associado com a chave. Se o método modifica o valor da chave, o método usa um bloqueio de gravação na chave e se só lê o método de valor da chave, em seguida, um bloqueio de leitura é executado na chave. Como AddAsync modifica o valor da chave com o valor de novo e passado, bloqueio de gravação da chave é interpretado. Então, se segmentos de 2 (ou mais) tentarem adicionar valores com a mesma chave ao mesmo tempo, um thread irá adquirir o bloqueio de gravação e os outros threads bloqueará. Por padrão, o bloco de métodos para até 4 segundos para adquirir o bloqueio; Após 4 segundos, os métodos lançam uma TimeoutException. Sobrecargas de método existirem permitindo que você passe um valor de tempo limite explícitas se você preferir.
 
Geralmente, você pode escrever seu código para reagir a uma TimeoutException captura-lo e repetindo a operação inteira (conforme mostrado no código acima). Em meu código simple, basta estou ligando Task.Delay passando 100 milissegundos de cada vez. Mas, na realidade, talvez seja melhor usar algum tipo de atraso de retirada exponencial em vez disso.
 
Depois que o bloqueio seja adquirido, AddAsync adiciona as referências de objeto de chave e valor a um dicionário temporário interno associado ao objeto ITransaction. Isso é feito para fornecer semântica de leitura your-proprietário-gravações. Ou seja, depois de você chamar AddAsync, uma chamada posterior para TryGetValueAsync (usando o mesmo objeto ITransaction) retornará o valor mesmo se você ainda não confirmada a transação. Em seguida, AddAsync serializa sua chave e valor objetos para matrizes de bytes e acrescenta essas matrizes de bytes para um arquivo de log no nó local. Por fim, AddAsync envia as matrizes de bytes para todas as réplicas secundárias para que eles têm as mesmas informações de chave/valor. Embora as informações de chave/valor tem sido escritas para um arquivo de log, as informações não são consideradas parte do dicionário, até que a transação que eles são associados com foi comprometida. 

No código acima, a chamada para CommitAsync confirma todas as operações da transação. Especificamente, ele acrescenta informações de confirmação para o arquivo de log no nó local e também envia o registro de confirmação para todas as réplicas secundárias. Depois que um quorum (maioria) das réplicas respondeu, todas as alterações de dados são consideradas permanentes e qualquer bloqueios associados teclas que foram manipuladas através do objeto ITransaction são lançados para que outras threads/transações pode manipular as mesmas teclas e seus valores.

Se CommitAsync não for chamado (normalmente devido a uma exceção sendo lançada), o objeto ITransaction obtém descartado. Quando descartar um objeto de ITransaction não confirmado, serviço tecidos acrescenta informações de anular ao arquivo de log do nó local e nada precisa ser enviada a qualquer uma das réplicas secundárias. E, em seguida, qualquer bloqueios associados teclas que foram manipuladas por meio da transação são lançados.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Armadilhas comuns e evitá-los
Agora que você sabe como as coleções de arquivos confiáveis funcionam internamente, vamos dar uma olhada em algumas uso indevido comuns deles. Ver o código abaixo:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Ao trabalhar com um dicionário regular do .NET, você pode adicionar um valor de chave/ao dicionário e, em seguida, altere o valor de uma propriedade (como LastLogin). No entanto, este código não funcionarão corretamente com um dicionário confiável. Lembre-se da discussão anterior, a chamada para AddAsync serializa os objetos de chave/valor para matrizes de bytes e salva as matrizes para um arquivo local e também envia a réplicas secundário. Se posteriormente você alterar uma propriedade, isso altera o valor da propriedade na memória ele não afeta o arquivo local ou os dados enviados para as réplicas. Se o processo falhar, o que está na memória é lançada ausente. Quando um novo processo for iniciado ou se outra réplica ficar primária, o valor da propriedade antigo é o que está disponível. 

Não consigo enfatizar o suficiente como é fácil fazer o tipo de erro mostrado acima. E, você só aprenderá sobre o erro se/quando que o processo for desligado. A forma correta de escrever o código é simplesmente reverter as duas linhas:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Aqui está outro exemplo mostrando um erro comum:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

Novamente, com dicionários de .NET regulares, o código acima funciona bem e é um padrão comum: o desenvolvedor usa uma chave para pesquisar um valor. Se o valor existir, o desenvolvedor altera um valor de propriedade. No entanto, com coleções confiáveis, este código exibe o mesmo problema como já discutido: __você não deve modificar um objeto depois que você atribuiu a um conjunto confiável.__
 
A forma correta de atualizar um valor em um conjunto confiável, é obter uma referência ao valor existente e considere objeto referido por esta referência imutável. Em seguida, crie um novo objeto que é uma cópia exata do objeto original. Agora, você pode modificar o estado desse novo objeto e gravar o novo objeto para a coleção de forma que ele obtém serializado para matrizes de bytes, acrescentado ao arquivo local e enviadas para as réplicas. Depois de confirmar as alterações, os objetos na memória, o arquivo local e todas as réplicas tenham o mesmo estado exato. Tudo o que é bom!

O código a seguir mostra a maneira correta de atualizar um valor em um conjunto confiável:

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Definir tipos de dados imutáveis para evitar erros de programador

Ideal seria Esperamos que o compilador para relatar erros quando você acidentalmente produzir código que sofre mutações estado de um objeto que você deveriam considerar imutável. Mas, o compilador c# não tem a capacidade de fazer isso. Portanto, para evitar possíveis bugs programador, é altamente recomendável que você definir os tipos que você usa com coleções confiáveis para ser tipos imutáveis. Especificamente, isso significa que você manter a opção de tipos de valor de núcleo (como números [Int32, UInt64, etc.], DateTime, Guid, período de tempo e assim por diante). E, é claro, você também pode usar cadeia de caracteres. É melhor evitar propriedades do conjunto de como serialização e desserialização pode frequentemente pode prejudicar o desempenho. No entanto, se você quiser usar propriedades de conjunto, recomendamos o uso de. Biblioteca de coleções imutáveis da rede (System.Collections.Immutable). Essa biblioteca está disponível para download de http://nuget.org. Recomendamos também fechamento suas classes e fazendo campos somente leitura sempre que possível.

O tipo de UserInfo abaixo demonstra como definir um tipo de imutável aproveitar as vantagens de recomendações mencionado anteriormente.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

O tipo de ItemId também é um tipo de imutável, como mostrado aqui:

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>Controle de versão de esquema (atualizações)

Internamente, conjuntos de confiável serializar os objetos usando. DataContractSerializer da rede. Os objetos serializados são mantidos no disco local da réplica principal e também são transmitidos para as réplicas secundárias. Como o seu serviço amadurece, é provável que você desejará alterar o tipo de dados (esquema) requer o seu serviço. Você deve abordar o controle de versão dos seus dados com atenção. Primeiramente, você sempre deve ser capaz de desserializar dados antigos. Especificamente, isso significa que seu código de desserialização deve ser indefinidamente compatível: 333 de versão do seu código de serviço deve ser capaz de operar em dados colocados em um conjunto confiável por versão 1 do seu código de serviço 5 anos.

Além disso, o código do serviço é atualizado um domínio de atualização por vez. Portanto, durante uma atualização, você tem duas versões diferentes do seu código de serviço em execução simultaneamente. Você deve evitar a nova versão do seu código de serviço usar o novo esquema conforme versões antigas do seu código de serviço talvez não seja possível lidar com o novo esquema. Quando possível, você deve projetar cada versão do seu serviço para serem compatíveis para frente por 1 versão. Especificamente, isso significa que V1 seu código do serviço deve ser capaz de simplesmente ignorar quaisquer elementos de esquema não tratar explicitamente. No entanto, ele deve ser capaz de salvar todos os dados não conhece explicitamente e simplesmente gravação-os de volta ao atualizar uma chave do dicionário ou valor. 

>[AZURE.WARNING] Enquanto você pode modificar o esquema de uma chave, você deve garantir que o código de hash da sua chave e algoritmos igual são estáveis. Se você alterar como qualquer um desses algoritmos operam, não será capaz de procurar a chave dentro do dicionário confiável nunca novamente.
  
Como alternativa, você pode executar o que normalmente é conhecido como uma atualização de fase 2. Com uma atualização de fase 2, atualizar seu serviço V1 para V2: V2 contém o código que sabe como lidar com a nova alteração de esquema mas este código não é executado. Quando o código de V2 lê V1 dados, ele opera nele e grava dados V1. Em seguida, após a atualização é concluída em atualizar todos os domínios, você pode alguma forma sinalizar para as instâncias de V2 em execução que a atualização foi concluída. (Uma forma de sinal isso é implementar uma atualização de configuração; é isso que isto faz uma atualização de fase 2). Agora, as instâncias de V2 podem ler dados V1, convertê-la em dados V2, operar nele e grave-os como dados V2. Quando outras instâncias ler dados V2, eles não precisa convertê-lo, eles apenas operam nele e gravar dados V2.

## <a name="next-steps"></a>Próximas etapas
Para saber sobre a criação de contratos de encaminhamento de dados compatíveis, consulte [Os contratos de dados posteriores](https://msdn.microsoft.com/library/ms731083.aspx).

Para saber as práticas recomendadas de contratos de dados de controle de versão, consulte [Controle de versão de contrato de dados](https://msdn.microsoft.com/library/ms731138.aspx). 

Para saber como implementar contratos de tolerância a dados de versão, consulte [Tolerância a versão retornos de serialização](https://msdn.microsoft.com/library/ms733734.aspx). 

Para saber como fornecer uma estrutura de dados que pode interagir em várias versões, consulte [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
