<properties
   pageTitle="Conjuntos de confiáveis | Microsoft Azure"
   description="Serviços de estado de serviço tecidos fornecem coleções confiáveis que permitem escrever aplicativos em nuvem altamente disponível, escaláveis e baixa latência."
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
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introdução a conjuntos de confiável nos serviços de estado de estrutura de serviço do Azure

Conjuntos de confiáveis permitem que você escrever aplicativos em nuvem altamente disponível, escaláveis e baixa latência, como se estivesse escrevendo aplicativos único computador. As classes no namespace **Microsoft.ServiceFabric.Data.Collections** fornecem um conjunto de conjuntos de-de-prontos automaticamente disponibilizar seu estado altamente. Os desenvolvedores precisam programa somente para as APIs conjunto confiável e deixar que conjuntos confiável gerenciar o estado replicado e local.

A principal diferença entre conjuntos de confiável e outras tecnologias de alta disponibilidade (como relacionada, o serviço de tabela do Microsoft Azure e serviço de fila do Azure) é que o estado é mantido localmente na instância de serviço enquanto também feitos altamente disponível. Isso significa que:

- Todas as leituras são locais, o que resulta em baixa latência e alta produtividade lê.
- Todas as gravações provocam o número mínimo de rede IOs, que resulta em baixa latência e gravações de alta produtividade.

![Imagem da evolução dos conjuntos.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Conjuntos de confiáveis podem ser considerados como a evolução natural das classes **Collections** : um novo conjunto de coleções que foram projetados para os aplicativos de nuvem e vários computadores sem aumentar a complexidade para o desenvolvedor. Assim, coleções confiável são:

- Replicados: Alterações de estado são replicadas para alta disponibilidade.
- Persistentes: Os dados são mantidos em disco para durabilidade contra interrupções de grande escala (por exemplo, uma queda de energia de data center).
- Assíncrono: APIs são assíncronas para garantir que threads não são bloqueados quando gerar IO.
- Transações: APIs utilizam a abstração de transações para que você possa gerenciar vários conjuntos de confiável dentro de um serviço facilmente.

Coleções confiáveis fornecem consistência forte garante prontos para uso para facilitar o raciocínio sobre o estado do aplicativo.
Consistência forte é alcançada garantindo transação confirmações terminar somente após a transação inteira foi logon um quorum de maioria das réplicas, incluindo o principal.
Para obter mais fraca consistência, aplicativos podem confirmar volta ao cliente/solicitante antes da confirmação assíncrona retorna.

As APIs de coleções confiável são uma evolução dos conjuntos de simultâneas APIs (encontrado no namespace **System.Collections.Concurrent** ):

- Assíncrono: Retorna uma tarefa desde que, ao contrário dos conjuntos de simultâneos, as operações são replicadas e persistentes.
- Não parâmetros de saída: usa `ConditionalValue<T>` para retornar um bool e um valor em vez de parâmetros de saída. `ConditionalValue<T>`é como `Nullable<T>` , mas não exige T para ser uma estrutura.
- Transações: Usa um objeto de transação para permitir que o usuário agrupar as ações em vários conjuntos de confiável em uma transação.

Hoje, **Microsoft.ServiceFabric.Data.Collections** contém dois conjuntos de:

- [Dicionário confiável](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa uma coleção de replicado, transações e assíncrona de pares chave/valor. Semelhante ao **ConcurrentDictionary**, a chave e o valor podem ser de qualquer tipo.
- [Fila confiável](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa uma replicado, transações e assíncrona estrito primeiro a entrar, fila (FIFO) fila. Semelhante ao **ConcurrentQueue**, o valor pode ser de qualquer tipo.

## <a name="isolation-levels"></a>Níveis de isolamento
Nível de isolamento define o grau ao qual a transação deve ser isolada modificações feitas por outras transações.
Há dois níveis de isolamento com suporte nos conjuntos confiável:

- **Leitura repetida**: Especifica que instruções não podem ler os dados que foram modificados porém ainda não confirmados por outras transações e que nenhuma outra transação pode modificar dados que tenham sido lidos pela transação atual até que a transação atual seja concluída. Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Instantâneo**: Especifica que dados lidos por qualquer instrução em uma transação será a versão transacionalmente consistente dos dados existentes no início da transação.
A transação pode reconhecer somente as modificações de dados que foram confirmadas antes do início da transação.
Modificações de dados feitas por outras transações após o início da transação atual não são visíveis para declarações de execução na transação atual.
O efeito é como se as instruções em uma transação obtêm um instantâneo dos dados confirmados como eram no início da transação.
Instantâneos são consistentes entre conjuntos de confiável.
Para obter mais detalhes, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Conjuntos de confiáveis escolher automaticamente o nível de isolamento a ser usado para uma operação de leitura determinada dependendo a operação e a função da réplica no momento da criação da transação.
A seguir é a tabela que descreve os padrões de nível de isolamento para operações de dicionário confiável e fila.

| Operação \ função      | Principal          | Secundário        |
| --------------------- | :--------------- | :--------------- |
| Leitura de entidade única    | Leitura repetida  | Instantâneo         |
| Enumeração \ contagem   | Instantâneo         | Instantâneo         |

>[AZURE.NOTE] Exemplos comuns para operações de entidade único são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

O dicionário confiável e fila confiável suportem ler suas gravações.
Em outras palavras, qualquer gravação dentro de uma transação estarão visível para um procedimento lido que pertence à mesma transação.

## <a name="locking"></a>Bloqueio
Em conjuntos de confiável, todas as transações estão em duas fases: uma transação não libera os bloqueios adquiriu até que a transação termina com anular ou uma confirmação.

Dicionário confiável usa bloqueio para todas as operações de entidade única em nível de linha.
Fila confiável negócios desativar concorrência estrito propriedade fila transações.
Fila confiável usa bloqueios de nível de operação permitindo uma transação com `TryPeekAsync` e/ou `TryDequeueAsync` e uma transação com `EnqueueAsync` por vez.
Observe que para preservar fila, se um `TryPeekAsync` ou `TryDequeueAsync` nunca observará que a fila confiável está vazia, eles também bloqueará `EnqueueAsync`.

Escreva operações sempre obtêm os bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores.
Qualquer operação de leitura feita usando isolamento de instantâneo é gratuito de bloqueio.
Por padrão, qualquer operação de leitura repetida leva bloqueios compartilhado.
No entanto, para qualquer operação de leitura que ofereça suporte a leitura repetida, o usuário pode pedir um bloqueio de atualização em vez do bloqueio compartilhado.
Um bloqueio de atualização é um bloqueio assimétrico usado para impedir que uma forma comum de bloqueio que ocorre quando várias transações bloqueiam recursos possíveis atualizações posteriormente.

A matriz de compatibilidade de bloqueio pode ser encontrada abaixo:

| Solicitar \ concedidas | Nenhum         | Compartilhado       | Atualização      | Exclusivo    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Compartilhado            | Nenhum conflito  | Nenhum conflito  | Conflito    | Conflito     |
| Atualização            | Nenhum conflito  | Nenhum conflito  | Conflito    | Conflito     |
| Exclusivo         | Nenhum conflito  | Conflito     | Conflito    | Conflito     |

Observe que o argumento de tempo limite nas APIs coleções confiável é usado para detecção de bloqueio.
Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1.
É possível para que elas sejam bloqueio, pois ambos acabam tendo o bloqueio compartilhado.
Nesse caso, uma ou ambas as operações irão expirar.

Observe que o cenário de bloqueio acima é um ótimo exemplo de como um bloqueio de atualização impedir travamentos.

## <a name="persistence-model"></a>Modelo de persistência
O Gerenciador de estado confiável e conjuntos de confiável siga um modelo de persistência que é chamado de Log e ponto de verificação.
Este é um modelo onde cada alteração de estado é registrada no disco e aplicada apenas na memória.
O estado completo em si é mantido apenas ocasionalmente (também conhecido como Ponto de verificação).
A vantagem é que deltas são transformados em acrescentar apenas gravações sequenciais em disco para melhorar o desempenho.

Para entender melhor o modelo de Log e ponto de verificação, vamos primeiro examinar o cenário de disco infinito.
O Gerenciador de estado confiável logs cada operação antes que ele é replicado.
Isso permite que o conjunto de confiável aplicar somente a operação na memória.
Já que os logs são persistentes, mesmo quando a réplica falha e precisa ser reiniciado, o Gerenciador de estado confiável tem informações suficientes em seus logs para repetir todas as operações que réplica perdeu.
Como o disco está infinito, registros de log nunca precisam ser removidos e coleção confiável precisa gerenciar apenas o estado de memória.

Agora vamos examinar o cenário de disco finita.
Como os registros de log acumulam, o Gerenciador de estado confiável serão executados sem espaço de disco.
Antes que acontece, o Gerenciador de estado confiável precisa truncar seu log para liberar espaço para os registros mais recentes.
Ele irá solicitar as coleções confiável a verificação seu estado na memória em disco.
É responsabilidade dos coleções confiável para manter seu estado até esse ponto.
Quando as coleções de arquivos confiáveis concluir seus pontos de verificação, o Gerenciador de estado confiável pode truncar o log para liberar espaço em disco.
Dessa forma, quando a réplica precisa ser reiniciado, conjuntos de confiável recuperará seu estado marcado, e o Gerenciador de estado confiável será recuperar e reproduzir todas as alterações de estado que ocorreram desde o ponto de verificação.

>[AZURE.NOTE] Adicionar outro valor de ponto de verificação é que ele melhora o desempenho de recuperação em casos comuns.
Isso ocorre porque os pontos de verificação contêm somente as versões mais recentes.

## <a name="recommendations"></a>Recomendações

- Não modifique um objeto do tipo personalizado retornado por operações de leitura (por exemplo, `TryPeekAsync` ou `TryGetValueAsync`). Conjuntos de confiáveis, como conjuntos de simultâneas, retornam uma referência para os objetos e não uma cópia.
- Fazer cópia profunda objeto retornado de um tipo personalizado antes de modificá-lo. Como tipos internos e estruturas são passar por valor, não é preciso fazer uma cópia profunda neles.
- Não use `TimeSpan.MaxValue` para tempos limite. Interagindo com dados deve ser usado para detectar travamentos.
- Não use uma transação depois que ele foi comprometido, anulado ou descartado.
- Não use uma enumeração fora o escopo da transação que foi criada.
- Não crie uma transação dentro de outra transação `using` instrução porque ele pode causar travamentos.
- Garantir que seu `IComparable<TKey>` implementação está correta. O sistema demora dependência sobre isso para mesclar os pontos de verificação.
- Use bloqueio de atualização ao ler um item com uma intenção para atualizá-lo para impedir que uma determinada classe de travamentos.
- Considere o uso de backup e restaurar a funcionalidade para fazer a recuperação de dados.
- Evitar misturar operações de entidade única e operações de vários entidades (por exemplo `GetCountAsync`, `CreateEnumerableAsync`) na mesma transação devido aos níveis de isolamento diferentes.

Aqui estão algumas coisas a ter em mente:

- O tempo limite padrão é 4 segundos para todas as APIs conjunto confiável. A maioria dos usuários não deve substituí-la.
- O token de cancelamento padrão é `CancellationToken.None` em todas as APIs de coleções confiável.
- O parâmetro de tipo de chave (*TKey*) para um dicionário confiável corretamente deve implementar `GetHashCode()` e `Equals()`. Chaves devem ser imutáveis.
- Para obter alta disponibilidade para os conjuntos de confiável, cada serviço deve ter pelo menos um destino e mínima réplica Definir tamanho de 3.
- Operações de leitura no secundário podem ler versões que não estão quorum confirmada.
Isso significa que uma versão de dados que são lidos a partir de um único secundário pode ser progrediu falso.
Claro, leituras de primário são sempre estáveis: pode nunca ser false progrediu.

## <a name="next-steps"></a>Próximas etapas

- [Início rápido de serviços confiável](service-fabric-reliable-services-quick-start.md)
- [Trabalhando com conjuntos de confiáveis](service-fabric-work-with-reliable-collections.md)
- [Notificações de serviços confiáveis](service-fabric-reliable-services-notifications.md)
- [Serviços confiáveis de backup e restauração (recuperação)](service-fabric-reliable-services-backup-restore.md)
- [Configuração do Gerenciador de estado confiável](service-fabric-reliable-services-configuration.md)
- [Introdução aos serviços Web API do serviço tecidos](service-fabric-reliable-services-communication-webapi.md)
- [Uso dos serviços confiável modelo de programação avançado](service-fabric-reliable-services-advanced-usage.md)
- [Referência do desenvolvedor para conjuntos de confiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
