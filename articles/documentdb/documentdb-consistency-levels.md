<properties
    pageTitle="Níveis de consistência em DocumentDB | Microsoft Azure"
    description="DocumentDB tem quatro níveis de consistência para ajudar a consistência eventual saldo, disponibilidade e latência concessões."
    keywords="consistência eventual, documentdb, azure, Microsoft azure"
    services="documentdb"
    authors="syamkmsft"
    manager="jhubbard"
    editor="cgronlun"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="syamk"/>

# <a name="consistency-levels-in-documentdb"></a>Níveis de consistência em DocumentDB

DocumentDB Azure foi projetado desde o início com distribuição global em mente. Ele foi projetado para oferecer baixa latência previsíveis garantias, um SLA de disponibilidade de 99,99% e vários modelos de consistência reduzida bem definido. Atualmente, DocumentDB fornece quatro níveis de consistência: sessão forte, envelhecimento limitada e eventual. Além do **fortes** e a **consistência eventual** modelos será comumente oferecidas por outros bancos de dados NoSQL, DocumentDB também oferece dois modelos de consistência cuidadosamente codificado e operacionalizada – **limitada envelhecimento** e **sessão**e confirmou a sua utilidade contra casos de uso do mundo real. Coletivamente esses níveis de quatro consistência permitem fazer concessões bem razoável entre consistência, disponibilidade e latência. 

## <a name="scope-of-consistency"></a>Escopo de consistência

O detalhamento de consistência é delimitado para uma solicitação de usuário único. Uma solicitação de gravação pode correspondem a um inserir, substituir, upsert ou excluir transação (com ou sem a execução de um disparador associado anterior ou posterior). Ou uma solicitação de gravação pode corresponder à execução transações de um procedimento armazenado de JavaScript operar em vários documentos dentro de uma partição. Como com as gravações, uma transação de leitura/consulta também é delimitada para uma solicitação de usuário único. O usuário pode ser necessário à pagina sobre um grande conjunto de resultados, abrangendo várias partições, mas cada lido transação é com escopo de uma única página e servida em uma única partição.

## <a name="consistency-levels"></a>Níveis de consistência

Você pode configurar um nível de consistência padrão em sua conta do banco de dados que se aplica a todas as coleções (em todos os bancos de dados) em sua conta de banco de dados. Por padrão, todas as leituras e consultas emitidas para os recursos definidos pelo usuário usará o nível de consistência padrão especificado na conta do banco de dados. No entanto, você pode relaxar o nível de consistência de uma solicitação de leitura/consulta específica, especificando o cabeçalho de solicitação [[x-ms--nível de consistência]](https://msdn.microsoft.com/library/azure/mt632096.aspx) . Há quatro tipos de níveis de consistência compatíveis com o protocolo de replicação DocumentDB que fornecem uma troca limpar entre as garantias de consistência específico e desempenho, conforme descrito abaixo.

![DocumentDB oferece vários bem definidas modelos de consistência (reduzida) à sua escolha][1]

**Forte**: 

- Consistência forte oferece uma garantia de [linearizability](https://aphyr.com/posts/313-strong-consistency-models) com as leituras garantidas para retornar a versão mais recente de um documento. 
- Consistência forte garante que uma gravação só é visível após confirmada permanentemente pelo quorum de maioria das réplicas. Uma gravação ou sincronia confirmada permanentemente por primário e o quorum da secundários ou ela é anulada. Uma leitura sempre é confirmada pela maioria ler quorum, um cliente nunca pode ver uma gravação não confirmada ou parcial e sempre garante ler a gravação confirmada mais recentes. 
- Contas de DocumentDB que estão configuradas para usar consistência forte não é possível associar mais de uma região Azure com sua conta de DocumentDB. 
- O custo de uma operação de leitura (em termos de [unidades de solicitação](documentdb-request-units.md) consumidas) com consistência forte é maior do que a sessão e eventual, mas a mesma que envelhecimento limitado.
 

**Bounded envelhecimento**: 

- Limitada garantias de consistência envelhecimento que as leituras podem ficar atrás gravações por no máximo *K* versões ou prefixos de um documento ou *t* intervalo de tempo. 
- Consequentemente, quando escolhendo limitada envelhecimento, o "envelhecimento" pode ser configurado de duas maneiras: 
    - Número de versões *K* do documento pelo qual as leituras de latência atrás as gravações
    - Intervalo de tempo *t* 
- Limitada envelhecimento ofertas total ordem global, exceto dentro da janela"envelhecimento". Observe que a leitura monotônico garante existe dentro de uma região internas e externas "envelhecimento janela". 
- Envelhecimento limitado fornece uma maior garantia de consistência de sessão ou consistência eventual. Para aplicativos distribuídos globalmente, é recomendável que usar envelhecimento limitado para cenários em que você gostaria de ter consistência forte, mas também quiser 99,99% de disponibilidade e baixa latência. 
- Contas de DocumentDB que estão configuradas com consistência envelhecimento limitada podem associar qualquer número de regiões Azure a sua conta de DocumentDB. 
- O custo de uma operação de leitura (em termos de RUs consumidas) com envelhecimento limitado é maior do que a sessão e consistência eventual, mas a mesma consistência forte.

**Sessão**: 

- Ao contrário dos modelos de consistência global oferecidos por níveis de consistência envelhecimento fortes e limitada, consistência de sessão é delimitada para uma sessão de cliente. 
- Consistência de sessão é ideal para todos os cenários em que uma sessão de dispositivo ou usuário está envolvida desde que ela garante monotônico leituras, gravações monotônico e ler suas próprias gravações (RYW) garante. 
- Consistência de sessão fornece consistência previsível para uma sessão e máximo lido produtividade, oferecendo os mais baixos leituras e gravações de latência. 
- Contas de DocumentDB que estão configuradas com consistência de sessão podem associar qualquer número de regiões Azure a sua conta de DocumentDB. 
- O custo de uma operação de leitura (em termos de RUs consumidas) com o nível de consistência de sessão é menor que fortes e limitado envelhecimento, mas consistência eventual mais de
 

**Eventual**: 

- Consistência eventual garante que em ausência de quaisquer gravações adicionais, as réplicas dentro do grupo eventualmente encontrarão. 
- Consistência eventual é o formulário maior de consistência onde um cliente pode obter os valores que são mais antigos do que aqueles que ele tinha visto antes.
- Consistência eventual fornece a consistência de leitura maior, mas oferece a menor latência para leituras e gravações.
- Contas de DocumentDB que estão configuradas com consistência eventual podem associar qualquer número de regiões Azure a sua conta de DocumentDB. 
- O custo de uma operação de leitura (em termos de RUs consumidas) com a consistência eventual nível é menor de todos os níveis de consistência DocumentDB.


## <a name="consistency-guarantees"></a>Garantias de consistência

A tabela a seguir captura várias garantias de consistência correspondente para os níveis de quatro consistência.

| Garantia                                                         |    Forte                                       |    Envelhecimento limitado                                                                           |    Sessão                                       |    Eventual                                 |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
|    **Total do pedido global**                                |    Sim                                          |    Sim, fora "envelhecimento janela"                                                      |    Não, ordem parcial "sessão"                   |    Não                                            |
|    **Garantia de prefixo consistente**                       |    Sim                                          |    Sim                                                                                         |    Sim                                           |    Sim                                           |
|    **Leituras monotônico**                                   |    Sim                                          |    Sim, entre regiões fora da janela de envelhecimento e dentro de uma região o tempo todo.     |    Sim, para a sessão determinada                    |    Não                                            |
|    **Gravações monotônico**                                  |    Sim                                          |    Sim                                                                                         |    Sim                                           |    Sim                                           |
|    **Ler suas gravações**                                  |    Sim                                          |    Sim                                                                                         |    Sim (na região gravação)                      |    Não                                            |


## <a name="configuring-the-default-consistency-level"></a>Configurando o nível de consistência padrão

1.  No [portal do Azure](https://portal.azure.com/), em Jumpbar, clique em **DocumentDB (NoSQL)**.

2. Na lâmina **DocumentDB (NoSQL)** , selecione a conta de banco de dados para modificar.

3. Na lâmina conta, clique em **padrão consistência**.


4. Na lâmina **Consistência padrão** , selecione o novo nível de consistência e clique em **Salvar**.

    ![Captura de tela realçando o ícone configurações e a entrada de consistência padrão](./media/documentdb-consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Níveis de consistência para consultas

Por padrão, para recursos definidos pelo usuário, o nível de consistência para consultas é a mesma que o nível de consistência para leituras. Por padrão, o índice é atualizado sincronia em cada inserir, substituir ou excluir de um documento para o conjunto. Isso permite que as consultas aceitar o mesmo nível de consistência que as leituras de documento. Enquanto DocumentDB é otimizada de gravação e oferece suporte contínuo volumes de gravações do documento, manutenção de índice síncrono e atendendo a consultas consistentes, você pode configurar certos conjuntos para atualizar seu índice lentamente. Ainda mais a indexação lenta melhora o desempenho de gravação e é ideal para cenários de inclusão de massa quando uma carga de trabalho pesado principalmente leitura.  

Modo de indexação|  Leituras|  Consultas  
-------------|-------|---------
Consistente (padrão)|   Selecionar da envelhecimento forte, limitado, sessão, ou eventual|    Selecionar da envelhecimento forte, limitado, sessão, ou eventual|
Preguiçoso|   Selecionar da envelhecimento forte, limitado, sessão, ou eventual|    Eventual  

Como com solicitações de leitura, você pode reduzir o nível de consistência de uma solicitação de consulta específica, especificando o cabeçalho de solicitação de [x-ms-nível de consistência](https://msdn.microsoft.com/library/azure/mt632096.aspx) .

## <a name="next-steps"></a>Próximas etapas

Se você quiser fazer ler mais sobre níveis de consistência e compensações, recomendamos os seguintes recursos:

-   Doug Terry. Consistência de dados replicado explicado por meio de beisebol (vídeo).   
[https://www.YouTube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-   Doug Terry. Consistência de dados replicado explicado por meio de beisebol.   
[http://Research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.PDF](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-   Doug Terry. Garantias de sessão para fracamente dados replicados consistentes.   
[http://DL.ACM.org/Citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-   Pedro Abadi. Compensações de consistência no Design de sistemas de banco de dados de distribuído moderna: PONTA é apenas parte da história ".   
[http://Computer.org/CSDL/mags/co/2012/02/mco2012020037-ABS.HTML](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-   Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Stoica de íon. Probabilístico limitada envelhecimento (serviço de catálogo telefônico) para práticos Quorums parciais.   
[http://VLDB.org/pvldb/vol5/p776_peterbailis_vldb2012.PDF](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-   Vogels Werner. Eventual consistente - revisto.    
[http://allthingsdistributed.com/2008/12/eventually_consistent.HTML](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png
