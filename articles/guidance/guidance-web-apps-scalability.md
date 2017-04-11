<properties
   pageTitle="Aplicativo web Scalable | Arquitetura de referência Azure | Microsoft Azure"
   description="Melhorando escalabilidade em um aplicativo web em execução no Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Melhorando escalabilidade em um aplicativo web 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo mostra uma arquitetura recomendada para melhorar o desempenho em um aplicativo web em execução no Microsoft Azure e escalabilidade. A arquitetura baseia-se nos [arquitetura de referência Azure: aplicativo web básico][basic-web-app]. As recomendações e considerações de artigo se aplicam a essa arquitetura também.

>[AZURE.NOTE] Azure tem dois diferentes modelos de implantação: Gerenciador de recursos e clássico. Este artigo usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

![[0]][0]

A arquitetura tem os seguintes componentes:

- **Grupo de recursos**. Um [grupo de recursos] [ resource-group] é um contêiner lógico para recursos Azure. 

- ** [Web app] [ app-service-web-app] ** e ** [aplicativo API][app-service-api-app]**. Um aplicativo moderno típico pode incluir um site e uma ou mais web RESTful APIs. Uma web API pode ser consumida por clientes de navegador por meio de AJAX, por aplicativos cliente nativo ou por aplicativos de servidor. Para considerações na web Projetando APIs, consulte [orientação de design de API][api-guidance].    

- **WebJob**. Usar o [Azure WebJobs] [ webjobs] para executar tarefas de longa execução em segundo plano. WebJobs podem ser executados em um cronograma, continously, ou em resposta a um gatilho, como colocar uma mensagem em uma fila. Um WebJob é executado como um processo de plano de fundo no contexto de um aplicativo de serviço de aplicativo. 

- **Fila**. Na arquitetura mostrada aqui, as filas de aplicativo tarefas em segundo plano colocando uma mensagem em um [Armazenamento de fila do Azure] [ queue-storage] fila. A mensagem aciona uma função na WebJob. 

    Como alternativa, você pode usar filas de barramento de serviço. Para uma comparação, consulte [filas do Azure e barramento de serviço - comparados e comparados][queues-compared].

- **Cache**. Armazenar dados semiestruturados estáticos no [Azure relacionada Cache][azure-redis].  

- **CDN**. Usar o [Azure Content Delivery Network] [ azure-cdn] (CDN) em cache disponível publicamente o conteúdo, menor latência e entrega mais rápida de conteúdo.

- **Armazenamento de dados.** Usar [o banco de dados do Azure SQL] [ sql-db] para dados relacionais. Para dados não-relacionais, considere uma loja NoSQL, como o armazenamento de tabela do Azure ou [DocumentDB][documentdb].

- **Pesquisa do azure**. Use [A pesquisa do Azure] [ azure-search] para adicionar funcionalidade de pesquisa, incluindo sugestões de pesquisa, pesquisa confusa e pesquisa de idiomas específicos. Pesquisa Azure geralmente é usada em conjunto com outro armazenamento de dados, especialmente se o armazenamento de dados principal requer consistência estrita. Nesta abordagem, você teria os dados autoritativos no armazenamento de dados e coloca o índice de pesquisa em pesquisa do Azure. Pesquisa Azure também pode ser usada para consolidar um índice de pesquisa único de várias fontes de dados.  

- **Email/SMS**. Se seu aplicativo precisar enviar emails ou mensagens SMS, use um serviço de terceiros como SendGrid ou Twilio, em vez de criar essa funcionalidade diretamente no aplicativo.

## <a name="recommendations"></a>Recomendações

### <a name="app-service-apps"></a>Aplicativos de serviço de aplicativo 

Recomendamos criar o aplicativo web e a API web como aplicativos de serviço de aplicativo separados. Este design permite que você executá-los em planos de serviço de aplicativo separados, que por sua vez lhe permite redimensioná-las de maneira independente. Se não precisar esse nível de escalabilidade no primeiro, você pode implantar os aplicativos para o mesmo plano e transfira-os para planos separados posteriormente, se necessário. (Para os planos Basic, Standard e Premium, você será cobrado para as instâncias de máquina virtual no plano, não por aplicativo. Consulte [preços de serviço de aplicativo][app-service-pricing])

Se você pretende usar os recursos de *APIs fácil* dos aplicativos do aplicativo de serviço móvel ou *Tabelas fácil* , crie um aplicativo de serviço de aplicativo separado para essa finalidade.  Esses recursos dependem de uma estrutura de aplicativo específico para habilitá-las.

### <a name="webjobs"></a>WebJobs

Se o WebJob faz uso de recursos, considere a possibilidade de implantá-lo em um aplicativo de serviço de aplicativo vazio dentro de um plano de serviço de aplicativo separado, para fornecer instâncias dedicadas para o WebJob. Consulte [diretrizes de trabalhos de plano de fundo][webjobs-guidance].  

### <a name="cache"></a>Cache

Você pode melhorar o desempenho e escalabilidade por meio [Do Azure relacionada Cache] [ azure-redis] em cache alguns dados. Considere o uso de Cache relacionada para:

- Dados de transação semiestruturados estático.

- Estado de sessão.

- Saída HTML. Isso pode ser útil em aplicativos que processam saída HTML complexa. 

Para obter mais orientações sobre a criação de uma estratégia de cache, consulte [cache orientações][caching-guidance].

### <a name="cdn"></a>CDN 

Usar o [Azure CDN] [ azure-cdn] em cache o conteúdo estático. O principal benefício de uma CDN é reduzir a latência para usuários, porque o conteúdo é armazenado em um *servidor de borda* parecido com aquele geograficamente o usuário. CDN também pode reduzir a carga no aplicativo, porque esse tráfego não está sendo tratado pelo aplicativo.

- Se seu aplicativo consiste principalmente páginas estáticas, considere usar CDN para o aplicativo inteiro em cache. Consulte [usar o Azure CDN no serviço de aplicativo do Azure][cdn-app-service].

- Caso contrário, colocar conteúdo estático, como imagens, CSS e HTML arquivos, para o armazenamento do Azure e uso CDN em cache esses arquivos. Consulte [integrar uma conta de armazenamento com CDN][cdn-storage-account].

> [AZURE.NOTE] Azure CDN não pode servir conteúdo que requer autenticação.

Para obter mais orientações, consulte [diretrizes de rede de entrega de conteúdo (CDN)][cdn-guidance]. 

### <a name="storage"></a>Armazenamento

Aplicativos modernos geralmente processam grandes quantidades de dados. Para dimensionar para a nuvem, é importante escolher o tipo certo de armazenamento. Aqui estão algumas recomendações de linha de base.  Para obter mais orientações, consulte [Avaliando os recursos de armazenamento de dados para soluções de persistência Polyglot][polyglot-storage].

O que você deseja armazenar | Exemplo | Armazenamento recomendado
--- | --- | ---
Arquivos | Imagens, documentos, PDFs | Armazenamento de blob do Microsoft Azure
Pares chave/valor | Dados de perfil de usuário pesquisados pelo ID de usuário | Armazenamento de tabela do Microsoft Azure
Mensagens curtas a intenção de disparar processamento posterior | Solicitações de pedido | Armazenamento de fila Azure, fila de barramento de serviço ou tópico de barramento de serviço
Dados não-relacionais, com um esquema flexível, que exigem consultando básica | Catálogo de produtos | Banco de dados de documento, como DocumentDB do Azure, MongoDB ou Apache CouchDB
Dados relacionais, exigindo suporte de consulta mais sofisticado, esquema estrita e/ou consistência forte | Inventário de produto | Banco de dados do SQL Azure

## <a name="scalability-considerations"></a>Considerações de escalabilidade

### <a name="app-service-app"></a>Aplicativo de serviço de aplicativo

Se sua solução inclui vários aplicativos de serviço de aplicativo, considere a possibilidade de implantá-los para separar os planos de serviço de aplicativo. Essa abordagem permite que você redimensioná-las de maneira independente, porque eles são executados em instâncias separadas. Para obter mais informações sobre dimensionamento, consulte as [Considerações de escalabilidade] [ basic-web-app-scalability] seção a [arquitetura do aplicativo web básico][basic-web-app].

Da mesma forma, considere colocar um WebJob em seu próprio plano, para que tarefas em segundo plano não executada nas mesmas instâncias que lidar com solicitações HTTP.  

### <a name="sql-database"></a>Banco de dados SQL

Aumentar a escalabilidade de um banco de dados do SQL com *fragmentação* o banco de dados &mdash; partição ou seja, o banco de dados horizontalmente. Fragmentação permite dimensionar horizontalmente, o banco de dados usando [Ferramentas de banco de dados elástica][sql-elastic]. Benefícios potenciais da fragmentação incluem:

- Taxa de transferência de transação melhor.

- Consultas podem ser executados com mais rapidez sobre um subconjunto dos dados. 

### <a name="azure-search"></a>Pesquisa do Azure

Pesquisa Azure remove a sobrecarga de executar pesquisas de dados complexos do armazenamento de dados principal, e ele pode dimensionar para tratar carga. Consulte [níveis de recursos de escala para cargas de trabalho de indexação na pesquisa do Azure e consulta][azure-search-scaling].

## <a name="security-considerations"></a>Considerações de segurança

### <a name="cross-origin-resource-sharing-cors"></a>Recurso de entre origens compartilhamento (CORS)

Se você criar um site e web API como aplicativos separados, o site não pode fazer chamadas de AJAX de cliente à API a menos que você ative CORS. 

> [AZURE.NOTE] Segurança navegador impede que a uma página da web façam solicitações AJAX para outro domínio. Essa restrição denomina-se a política de mesma origem e impede que um site mal-intencionado lendo sentitive dados de outro site. CORS é um padrão W3C que permite que um servidor relaxar a política de mesma origem e permitir algumas solicitações entre origens durante a rejeição outras pessoas.

Serviços de aplicativo possui suporte interno para CORS, sem precisar gravar qualquer código de aplicativo. Consulte [Consume um aplicativo de API do JavaScript usando CORS][cors]. Adicione o site à lista de origens de permitidos para a API. 

### <a name="sql-database-encryption"></a>Criptografia de banco de dados SQL

Usar a [Criptografia de dados transparente] [ sql-encryption] se você precisar criptografar dados inativos no banco de dados. Esse recurso executa em tempo real criptografia e descriptografia de um banco de dados inteiro (incluindo backups e arquivos de log de transação), sem a necessidade de mudanças no aplicativo. Criptografia adicionar alguns latência, portanto, é uma boa prática para separar os dados que devem ser seguro para seu próprio banco de dados e habilitar a criptografia somente para esse banco de dados.  

## <a name="next-steps"></a>Próximas etapas

- Para maior disponibilidade, implantar o aplicativo em mais de uma região e use o [Gerenciador de tráfego do Azure] [ tm] para failover. Para obter mais informações, consulte [arquitetura de referência Azure: aplicativo Web com alta disponibilidade][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Aplicativo da Web no Azure com escalabilidade aprimorada"