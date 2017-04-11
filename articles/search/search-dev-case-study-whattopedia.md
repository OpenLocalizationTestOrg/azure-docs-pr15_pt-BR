<properties 
    pageTitle="Estudo de caso de desenvolvedor de pesquisa Azure: Como WhatToPedia criado um portal de infomedia no Microsoft Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
    description="Saiba como criar um informações portal e meta mecanismo de pesquisa usando a pesquisa do Azure, um serviço de pesquisa de nuvem hospedado para desenvolvedores." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Estudo de caso de desenvolvedor do Azure pesquisa

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>Como [WhatToPedia.com](http://whattopedia.com/) criados um portal de infomedia no Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">A ideia grande</font> 


Nossa ideia é criar um portal de informações que ajudam a compradores conectar com revendedores por meio de uma experiência altamente relevantes, a pesquisa com escopo, semelhante a como percorrer relação aos turistas correspondência de portais com hotéis, restaurantes e entretenimento no território não transformado. 

O portal que montamos fornecerá uma experiência de pesquisa muito alta qualidade sobre os dados de um revendedor em um determinado mercado, ajudar compradores Localizar lojas com base no local e outras comodidades o revendedor fornece. Podemos irá propagar o mecanismo de pesquisa com um conjunto de dados inicial, mas ainda mais valor será criado ao longo do tempo, com a Ajuda de assinantes de um revendedor que postam informações sobre seus negócios. Promoções, mercadoria novo, marcas populares, serviços de especialidade internos-– são exemplos dos dados que adiciona valor ao nosso site. Esses dados é auto relatados e integrados ao corpo de pesquisa, depois que o revendedor se inscreve como um assinante. Anúncios, além do modelo de assinatura, forneça o fluxo de receita para nosso novo negócio.

Pesquisa será o modelo de interação de usuário predominante, em uma plataforma de nuvem puro. Para fins de escala e baixo custo, quase tudo o que fazemos, da experiência do portal ao controle de origem, será por meio de um serviço online. Usando um mecanismo de pesquisa que fornece os recursos que precisamos prontos para uso, podemos criar um aplicativo de pesquisa rapidamente, sem precisar criar e gerenciar uma pesquisa engine sozinhos.

## <a name="what-we-built"></a>O que criamos

WhatToPedia é uma empresa de infomedia de inicialização. Criamos [WhatToPedia.com](http://whattopedia.com/) – - atualmente no mercado de teste em Norte da Europa com uma data de ativação de 2 de fevereiro de 2015. Nossa base de clientes é principalmente lojas físicas bloco que precisam de uma presença online acessível que seja fácil de gerenciar e manter.

Nossa tarefa é atrair compradores por meio de uma experiência de pesquisa online excelentes, aumentando os resultados com base em cidade ou ambiente, marcas, armazenar nomes ou armazenar tipos. Atrair compradores tem um efeito dominó, revendedores motivadora para se inscrever em nosso site de portal. Assinaturas são baseados em taxa, a uma taxa acessível.

 ![][7] 

Após a inscrição para uma assinatura, um revendedor assume seu perfil existente (criado inicialmente por nós de dados comprados), atualizá-lo com dados adicionais sobre promoções, marcas em destaque ou anúncios. Recursos internos, como idiomas falados, moedas aceitaram, isenta de imposto de compras, pode ser auto relatados para atrair melhor compradores que buscam esses comodidades.

## <a name="who-we-are"></a>Quem estamos

Meu nome é Thomas Segato (Microsoft Consulting) e trabalhei com Jesper Boelling, conduzir desenvolvedor na WhatToPedia, projetar a solução. 

WhatToPedia é uma inicialização, teste seu novo negócio portal na Suécia, onde a maioria das 60.000 revendedores são físicas bloco SMEs (pequeno e médio porte corporativo) de marketing. Porque sabemos que uma pessoa compras na Europa fala vários idiomas e executa várias moedas, podemos criar soluções que acomodar um comprador multilíngue. Nós necessários e encontrado, um mecanismo de pesquisa que ofereça suporte a nossas necessidades multilíngues na pesquisa do Azure.

Pesquisa Azure era uma virada de jogo para nosso projeto. Antes da disponibilidade de pesquisa do Azure, podemos gastos considerável energia trabalhando através do kinks da criação de nosso própria mecanismo de pesquisa. Tendo Azure pesquisa como um serviço online removido o maior desafio técnico e administrativo da nossa solução, devem ir para o mercado rapidamente e com uma experiência de pesquisa mais eficiente.  

## <a name="how-we-did-it"></a>Como fizemos

Nossa visão era criar uma infraestrutura completa com base em serviços de nuvem apenas. Microsoft foi escolhido como a plataforma estratégica porque ele foi o provedor que oferecidas o necessário serviços (para colaboração e desenvolvimento), dimensionar sob demanda, além de preço acessível.
 
### <a name="high-level-components"></a>Componentes de alto nível

Criamos uma empresa, não apenas um site. Suporte a esforço inteira necessária uma ampla variedade de ferramentas e aplicativos. Podemos adotados Visual Studio e Visual Studio Team Services para o desenvolvimento, serviço TFS (Team Foundation) Online para controle de origem e gerenciamento de scrum, Office 365 para comunicação e colaboração e claro Microsoft Azure para todas as operações relacionadas ao site e armazenamento. Com o Visual Studio, o IDE fornecido provisionamento direto no Azure, com integração ao TFS Online fornecendo um aumento de produtividade adicionais.

O diagrama a seguir ilustra os componentes de alto nível usados na infraestrutura de WhatToPedia.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>Como usamos Microsoft Azure

Examinando caixas verdes no diagrama anterior, você verá que a solução WhatToPedia baseia-se sobre esses serviços:

- [Pesquisa do Azure](https://azure.microsoft.com/services/search/)
- [Sites Azure usando MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs para tarefas agendadas](../app-service-web/websites-webjobs-resources.md)
- [Banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Armazenamento de blob do Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Entrega de Email de SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Coração da solução é dados e pesquisa. O fluxo de dados do provedor de revendedor para o cliente final é ilustrado abaixo:

  ![][9]

Armazenamento de dados principal é o revendedor e dados de estatísticas no Azure SQL Database. Isso consiste o conjunto de dados inicial, além de dados de um revendedor específicas adicionados ao longo do tempo. Estamos usando um WebJob do Azure para postar atualizações do banco de dados SQL para o corpo de pesquisa na pesquisa do Azure.

### <a name="presentation-layer"></a>Camada de apresentação

O portal é um site do Azure, implementado no MVC 4 e [Twitter inicialização](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Escolhemos MVC porque ele oferece uma abordagem muito mais limpa para HTML de desenvolvimento do ASP.NET baseada em formulários. Para evitar ter que criar aplicativos para vários dispositivos e manter várias plataformas móveis, Twitter inicialização foi escolhida para dar suporte a todos os dispositivos e plataformas.

### <a name="authentication-permissions-and-sensitive-data"></a>Autenticação, permissões e dados confidenciais

Compradores navegue no site de forma anônima. Assim, há nenhum requisito de logon para compradores, nem podemos fazer armazenar quaisquer dados de consumidor. 

Revendedores são uma história diferente. Aqui, podemos armazenar informações de perfil público, informações de cobrança e conteúdo de mídia que deseja expor no site. Cada revendedor que assina o site Obtenha o logon do usuário, usado para autenticar o usuário antes de fazer as atualizações para o perfil do assinante.  Podemos armazenar com segurança todos os dados do assinante em armazenamento Azure SQL Database e Azure BLOB.
Nós tiver optado por um modelo de autenticação com base em autenticação baseada em formulários do .NET. Escolhemos essa abordagem para sua simplicidade; não precisamos as funções, o suporte de interface do usuário e outros recursos estranhos que vêm com outras abordagens. 

Para garantir que revendedores vejam apenas os dados que pertence a elas, criamos um revendedor ID para cada revendedor subsequentemente usado em todas as operações leitura e gravação envolvendo dados específicos de um revendedor. Com essa abordagem, descobrimos que não precisamos conceder permissões de banco de dados para revendedores individuais. Todos os revendedores interagem com o sistema em uma função de banco de dados único, com a ID de um revendedor de nossa técnica de isolamento de dados.

Porque nossos negócios são tudo sobre os efeitos downstream (gerando mais negócios revendedores, criando incentivos anunciar e inscrever-se) nós pode desenhar a linha na manipulação de compras pela web. Assim, você não encontrará um carrinho de compras em nosso site, que simplifica nossos requisitos de segurança. 

Outra simplificação que podemos empregados era terceirizado nossas operações a pagar contas e cobrança. Por roteamento informações de pagamento de cliente diretamente para um terceiro ([SveaWebPay](http://www.sveawebpay.se/)), podemos reduzir os riscos associando armazenar e proteger dados confidenciais em nossos armazenamentos de dados. 

### <a name="search-engine"></a>Mecanismo de pesquisa

O núcleo da nossa solução é o mecanismo de pesquisa baseado no serviço de pesquisa do Azure. Inicialmente, criamos um mecanismo de pesquisa personalizada, mas durante esse processo, podemos percebidos a complexidade esforço era muito alto realmente e que solicitado conosco considerar outras alternativas. 

Recursos básicos que foram mais importantes conosco incluídos:

- Filtros
- Navegação com faceta
- Aumentando os resultados
- Paginação AJAX

Uma pesquisa na internet nos trouxe a vídeo a seguir, que inspirou nos Experimente o Azure pesquisa: [Detalhamento na Europa TechEd](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Depois de assistir ao vídeo, estamos prontos para criar um protótipo com base no que estamos viu. Como podemos já tinha um modelo de dados no MVC, a criação de protótipo foi simples porque os dados contidos termos pesquisáveis e podemos já tinha trabalhei os requisitos para como queremos faceta, classificar e filtrar os dados. 

Isso é como podemos criados protótipo.

**Configurar o serviço de pesquisa do Azure**

1. Fazer login no Portal de clássico do Azure e adicionado o serviço de pesquisa à nossa assinatura. Usamos a versão compartilhada (gratuita com nossa assinatura).
2. Crie um índice. Para protótipo, usamos o portal de interface do usuário para definir os campos de pesquisa e criar os perfis de pontuação. Nosso perfil pontuação baseia-se a dados de localização: país | Cidade | endereço (consulte: adicionar perfis de pontuação).
3. Copiar a URL do serviço e o administrador api-chave nossos arquivos de configuração. Esta chave é na página de serviço de pesquisa no portal e ele é usado para autenticar o serviço.
    
**Desenvolver um trabalho de indexador de pesquisa – Console do Windows**

1. Ler todos os revendedores de banco de dados.
2. Chamar a API do serviço de pesquisa do Azure para fazer o upload de revendedores um por vez (consulte: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Definir uma propriedade no banco de dados que revendedor é indexado para indexação incremental. Fizemos isso adicionando um campo de 'indexador' que armazena o status do índice de cada perfil (indexado ou não). 

Consulte o apêndice do trecho de código que cria o trabalho indexador.

**Desenvolver um Portal da Web de pesquisa – MVC**

1. Serviço de pesquisa do Azure para acessar todos os documentos de pesquisa de chamadas (consulte: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extrair após a resposta do serviço de pesquisa (usando json.net http://james.newtonking.com/json)
   - Resultados
   - Aspectos
   - Contagens de resultados
   - Desenvolva uma interface de usuário para exibir os resultados da pesquisa, aspectos e contagens (nós já tinha isso).

Este é o código que usamos para obter os resultados da pesquisa do Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Aumentando por local**

Provavelmente a exigência mais importante para verificar no protótipo incluídos adicionando uma palavra-chave de pesquisa local à consulta. É fundamental para nosso portal que se um usuário insere um nome de cidade na pesquisa de consulta, que os revendedores na cidade determinado seriam classificada superiores revendedores tendo a palavra-chave cidade na descrição. Para esse requisito, usamos um perfil de pontuação classificar o campo Cidade superior a outros campos.

**Suporte a vários idiomas**

Precisamos exibir resultados de pesquisa correta em idiomas corretos e forneça uma opção para encontrar os mesmos resultados em diferentes idiomas. Os dois lados para esse problema foram: 

- Pesquisar palavras em vários idiomas
- Exibir resultados da pesquisa no idioma correto

Podemos resolvido a parte da apresentação, adicionando um documento para cada idioma com texto localizado e uma propriedade com o idioma. Quando um usuário insere um termo de pesquisa, podemos usuário `$filter` escolheu expressões para filtrar no idioma do usuário.

Cada um dos documentos tem uma propriedade oculta chamada "cidades" integradas do tipo de conjunto. Essa propriedade armazena nomes de cidades em todos os idiomas, permitindo que o usuário pesquisar em vários idiomas.

###<a name="data-storage"></a>Armazenamento de dados

Todos os dados (perfil, assinatura e contabilidade) são armazenados no banco de dados do SQL. Todos os arquivos de mídia são armazenados no armazenamento de BLOB do Azure, incluindo imagens e vídeos fornecidos pelo revendedor. Usar o armazenamento BLOB separado isola os efeitos do upload de arquivos; arquivos nunca são combinados com o site, portanto, não precisamos recriar o site sempre podemos adicionar arquivos.

Uma vantagem importante de nosso design de armazenamento é que vários desenvolvedores podem compartilhar um armazenamento de desenvolvimento único. Um dos requisitos para o projeto WhatToPedia foi sejam capazes de criar um ambiente de desenvolvimento dentro de 15 minutos, incluindo vídeos, imagens e dados de revendedor. Obtendo os dados mais recentes do TFS Online, executando um script SQL e executar o trabalho de importação, um ambiente completo pode ser posicionado localização do mecanismo para cima sem hora todo. Esta prática também melhora o processo de teste.

###<a name="webjobs"></a>WebJobs

Usamos WebJobs do Azure para atualizar dados ao índice. Criando um trabalho de indexador de pesquisa, a parte de indexação foi muito fácil integrar nossa solução. A única alteração de código, fizemos era acomodar o trabalho indexador adicionar um `Indexed` campo ao nosso modelo de dados para indicar o estado de índice. Sempre que um novo perfil é adicionado ou atualizado, o `Indexed` campo é definido como false. O mesmo se aplica se o revendedor mudar sua dados de perfil por meio do portal.  

O trabalho procura por todas as linhas tendo `Indexed` definida como false. Quando ele encontra a linha, o documento é enviado para pesquisa do Azure e então a `Indexed` campo está definido como true. Não precisamos planejar adicionando versus atualizando dados porque o serviço de pesquisa do Azure realmente cuida disso. Se você adicionar um documento que já está presente, o serviço fará uma atualização automaticamente.

Todos os trabalhos de web foram desenvolvidos como aplicativos de console que podem ser carregados para sites Azure como arquivos ZIP, descompactados e então agendados.

O trabalho está agendado para ser executado a cada 5 minutos como uma tarefa agendada web. Podemos calculados que o serviço levou aproximadamente três minutos para carregar 3.000 documentos, que estava dentro de nossas necessidades. 

> [AZURE.NOTE] Não há um recurso de indexador de protótipo que recentemente foi introduzido na pesquisa do Azure. Este recurso chegou tarde demais para que possamos usá-lo no nosso primeiro lançamento, mas ele aparece para resolver o mesmo problema que usamos nosso trabalho indexador, que é automatizar as operações de carga de dados.


###<a name="backup-strategy"></a>Estratégia de backup

Criamos uma estratégia de backup várias camadas para recuperar de um intervalo de cenários, de uma falha grave, para baixo até a recuperação de uma transação individual. Os ativos proteger incluem três tipos de dados (site da web, dados de assinante e arquivos de mídia). 

Primeiro, mantendo o código de origem do site da web no TFS Online, sabemos que, se o site falhar, podemos pode recriá-lo por republicar do TFS. 

Dados do assinante no banco de dados do SQL Azure serão o ativo mais confidencial. Vamos voltar isso usando os internos recurso (consulte [Backup de banco de dados do SQL Azure e restauração](http://msdn.microsoft.com/library/azure/jj650016.aspx)). O agendamento de backup é o backup de banco de dados completo uma vez por semana, banco de dados diferencial backups uma vez por dia e backups de log de transação cada 5 minutos.  Considerando o tamanho dos dados, essa solução é mais do que adequado para nossos volumes de dados projetada e imediata.

Terceiro, podemos armazenar arquivos de imagem e vídeo no armazenamento de BLOB do Azure. Estamos ainda avaliando o plano de backup definitivo para esses dados, considerando Cloudberry Explorer para Azure como uma possível solução. Por agora, podemos usar um WebJob para copiar imagens e vídeos para outro local.

##<a name="what-we-learned"></a>O que aprendemos

Como podemos já tinha dados, foi fácil estabelecer prova de conceito. Em horas, tivemos um protótipo com aspectos contadores, paginação, classificados como perfis e resultados de pesquisa. Resultados da pesquisa foram tão precisos, que podemos decidiu remover alguns dos filtros apresentados ao cliente final. 

A maior surpresa para nós era quão rápido poderia aprendemos pesquisa do Azure e quanto obtivemos de volta. Literalmente, podemos estabelecida prova de conceito em algumas horas (consulte a observação abaixo), substituindo 500 linhas de código com 3 linhas de código no aplicativo front-end (mais de uma nova WebJob) e como obter resultados melhores. 

Anteriormente, nosso código implementado paginação, contagens e outros comportamentos que são padrão para pesquisar. Usando a pesquisa do Azure, os resultados que voltamos incluem as ocorrências de pesquisa, aspectos, paginação dados, contagens – todas as coisas que podemos necessários e foram precisar fornecer sozinhos. Ele também incluído aumentando e navegação com faceta interna, que não tivemos em nossa solução original.

O maior desafio durante a implementação foi que ela foi uma versão de visualização e localizar informações e experiências compartilhadas era difícil. Depois de nós conectado alguns pontos, descobrimos que usar o serviço de pesquisa do Azure era bem simple devido a seu formato de dados API REST e JSON. Chamamos a estrutura diretamente do aberto mais plug-ins fonte como JQuery JSON.Net e podemos poderia usar ferramentas como Fiddler para experimento fast e depuração. 

> [AZURE.NOTE] Além de ter os dados preparado para, ajudar que as pessoas a criação de protótipo já compreendidos como a pesquisa funciona de tecnologia, tornando-nos mais produtivo e mais apreciativo dos recursos internos. Se você precisar entender os construção de consulta de pesquisa, navegação com faceta, filtros, etc. você deve esperar protótipos para levar mais tempo. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Controlando aspectos na página de apresentação de pesquisa

Uma das nossas lições durante a prova de conceito era planejar aspectos cuidadosamente antecipadamente. Depois de carregar muitos dados na solução, podemos viu que o volume de aspectos era muito alto para apresentar aos usuários. 

Nós pode ser resolvido, restringindo o parâmetro de contagem de faceta. O parâmetro de contagem impõe um limite rígido no número de aspectos retornada para o usuário. Um link que inclui uma discussão do parâmetro contagem pode ser encontrado [aqui](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs para agendar tarefas

Pesquisa Azure não era a surpresa agradável somente para nós. Descobrimos que usando WebJobs para automatizar nossas operações de carga de dados para pesquisar Azure era muito superior à nossa abordagem anterior, que exigia usando uma dedicado máquina virtual executando o Agendador do Windows, com tarefas agendadas para atualizar o índice de pesquisa. WebJobs foi simples configurar e fácil depuração e claro muito mais barato que pagar por uma máquina virtual dedicada.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Azure BLOB Storage Explorer para atualizar as imagens

Encontramos que usando o [Gerenciador de armazenamento de BLOB do Azure](https://azurestorageexplorer.codeplex.com/) (disponível no codeplex) para ser muito útil para gerenciar atualizações de imagem e vídeo para o site. Podemos usá-lo como uma ferramenta de desenvolvedor para atualizar manualmente a imagens e vídeos que fazem parte de nosso site principal. Nós consideramos mais flexível que Implantando alterações para o portal e elimina uma iteração de teste completo sempre que precisamos atualizar uma imagem. 

##<a name="a-few-final-words"></a>Algumas palavras finais

Obrigado para o pessoal excelente WhatToPedia para permitir conosco compartilhar sua história!  

Esperamos que você encontrou este estudo de caso útil. Se você passar para usar a pesquisa do Azure, é recomendável alguns recursos para acelerar você ao longo:

- [Fórum do MSDN dedicada a pesquisa do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow também tem uma marca](http://stackoverflow.com/questions/tagged/azure-search)
- [Página de documentação no Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Azure documentação de pesquisa no MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Apêndice: WebJob de indexador de pesquisa

O código a seguir cria o indexador mencionado na seção sobre a criação de protótipo.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
