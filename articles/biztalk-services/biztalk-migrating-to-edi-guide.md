<properties   
    pageTitle="Migrando soluções EDI do BizTalk Server guia técnico do BizTalk Services | Microsoft Azure"
    description="Migrar EDI para MABS; Serviços de BizTalk do Microsoft Azure"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrando soluções EDI do BizTalk Server aos Serviços BizTalk: guia técnico

Autor: Tim Wieman e Nitin Mehrotra

Revisores: Karthik Bharthy

Escrito usando: lançamento do Microsoft Azure BizTalk Services – fevereiro de 2014.

## <a name="introduction"></a>Introdução

Dados EDI (intercâmbio eletrônico) é um dos principais meios pelos quais dados do exchange de empresas eletronicamente, também chamado como transações para empresas ou B2B. BizTalk Server teve suporte a EDI uma década, desde a versão inicial BizTalk Server. Com os Serviços BizTalk, Microsoft continua o suporte para soluções EDI na plataforma do Microsoft Azure. Transações B2B principalmente são externas à organização e, portanto, é mais fácil implementar se ele foi implementado em uma plataforma de nuvem. Microsoft Azure fornece esse recurso por meio dos serviços do BizTalk.

Enquanto alguns clientes examinar Serviços BizTalk como uma plataforma de "greenfield" para novas soluções EDI, muitos clientes têm soluções EDI do BizTalk Server atuais que eles talvez queira migrar para o Azure. Porque BizTalk serviços EDI foi projetada com base nas mesmas entidades de chave como arquitetura de EDI do BizTalk Server (negociação parceiros, entidades, contratos), é possível migrar artefatos EDI do BizTalk Server para Serviços BizTalk.

Este documento discute algumas das diferenças envolvidas migrando artefatos EDI do BizTalk Server aos Serviços BizTalk. Este documento presume um conhecimento de trabalho de processamento de EDI do BizTalk Server e acordos de parceiro de negociação. Para obter mais informações sobre EDI do BizTalk Server, consulte [Negociação parceiro Management Using BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Qual versão do artefatos EDI do BizTalk Server pode ser migrada para Serviços BizTalk?

O módulo de EDI do BizTalk Server foi aprimorado significativamente para BizTalk Server 2010, quando ela foi remodelada para incluir parceiros, perfis e contratos. Serviços BizTalk usa o mesmo modelo para organizar os parceiros comerciais e as divisões de negócios nesses parceiros comerciais. Como resultado, a migração artefatos EDI do BizTalk Server 2010 e versões posteriores para Serviços BizTalk, é um processo muito mais direto. Para migrar artefatos EDI associados com versões anteriores ao BizTalk Server 2010, você deve primeiro atualizar para o BizTalk Server 2010 e depois migrar seu artefatos EDI aos Serviços BizTalk.

## <a name="scenariosmessage-flow"></a>Fluxo de cenários/mensagem

Como com o BizTalk Server, EDI processamento nos serviços do BizTalk é construído em torno de uma solução de gerenciamento de parceiro de negociação (TPM). A solução TPM tem os seguintes componentes principais:

- Parceiros comerciais, que representam organização em uma transação B2B.
- Perfis, que representam divisões dentro de um parceiro comercial.
- Negociação contratos de parceiro (ou contratos), que representam o contrato de negócios entre dois parceiros/perfis.

A ilustração a seguir mostra o semelhanças, bem como diferenças, entre uma solução EDI do BizTalk Server e solução EDI do BizTalk serviços:

![][EDImessageflow]

As principais diferenças e semelhanças entre um fluxo de solução EDI no BizTalk Server e os serviços do BizTalk são:

- Assim como BizTalk Server usa um pipeline de EDIReceive para receber uma mensagem EDI e um pipeline de EDISend para enviar uma mensagem EDI, Serviços BizTalk usa uma ponte EDI receber para receber e a ponte EDI enviar para enviar mensagens de EDI. No BizTalk Server, os canais estão associadas a um contrato usando enviar ou recebem portas. Nos serviços do BizTalk, o contrato próprio indica enviar ou receber ponte.
- No BizTalk Server, após o pipeline de EDIReceive processa a mensagem EDI, a mensagem será despejada um banco de dados do SQL Server. O pipeline de EdiSend, em seguida, seleciona a mensagem do banco de dados do SQL Server, processa e, em seguida, envia-lo para o parceiro comercial.

    Nos serviços do BizTalk, depois de receber o EDI ponte processa a mensagem EDI, ele roteia a mensagem para um processo externo. O processo externo poderia estar em execução no Microsoft Azure ou local. O processo externo deve encaminhar a mensagem para a ponte de envio EDI; a ponte de envio puxe naturalmente a mensagem. Após processar a mensagem, a ponte de envio EDI encaminha a mensagem para o parceiro comercial.

Serviços do BizTalk fornece uma experiência de configuração de fácil de usar para criar e implantar um contrato B2B entre parceiros comerciais sem configurar qualquer computação do Microsoft Azure instâncias (funções da Web ou de trabalho), qualquer bancos de dados do Microsoft Azure SQL ou quaisquer contas de armazenamento do Microsoft Azure rapidamente. Cenários mais complexos exigem amarrando em fluxos de trabalho ou outro processamento de serviço "ao redor das bordas" de um contrato de parceiro negociação, ou seja, antes ou depois de processamento de ponte de negociação EDI de contrato de parceiro. Em detalhes, as seguintes sequências de eventos ocorrerem durante uma mensagem de EDI processamento nos serviços do BizTalk.

1. Uma mensagem de EDI é recebida do parceiro, Fabrikam comercial.  Para receber mensagens EDI de parceiros comerciais, Serviços BizTalk suporta protocolos de transporte como FTP, SFTP, AS2 e HTTP/S.

2. O processamento de lado receber de contrato de parceiro negociação desmonta a mensagem EDI formato XML.  Você pode encaminhar a mensagem EDI desmontada (no formato XML) para pontos de extremidade do serviço barramento como um ponto de extremidade do serviço barramento retransmissão, tópico de barramento de serviço, fila de barramento de serviço ou uma ponte de Serviços BizTalk.

3. As mensagens XML desmontadas podem ser recebidas depois do ponto de extremidade para ainda mais o processamento personalizado.  Esses pontos de extremidade podem ser processados por um componente de local ou uma instância de computação do Microsoft Azure para processar ainda mais a mensagem em um serviço de fluxo de trabalho do Windows (WF) ou Windows Communication Foundation (WCF), por exemplo.

4. O "processamento do lado do envio" do parceiro negociação contrato reúne a mensagem XML em formato EDI e envia para parceiro comercial, Contoso.  Para enviar mensagens EDI para parceiros comerciais, Serviços BizTalk suporta os mesmos protocolos como aqueles usados para receber mensagens EDI.

Este documento ainda mais fornece orientação conceitual em migrar alguns dos diferentes artefatos EDI do BizTalk Server aos Serviços BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Enviar/receber portas para parceiros comerciais

No BizTalk Server configurar locais de receber e portas de recebimento para receber mensagens EDI/XML de parceiros comerciais e você configurar portas de enviar para enviar mensagens EDI/XML para parceiro comercial. Em seguida, obstruir essas portas a um contrato de parceiro negociação usando o console de administração do BizTalk Server. Nos serviços do BizTalk, os locais onde você recebe mensagens de parceiros comerciais e onde você enviar mensagens para parceiros comerciais são configuradas como parte do negociação parceiro contrato de si mesmo (como parte das configurações de transporte) no Portal de Serviços BizTalk.  Assim você não realmente tem o conceito de "enviar portas" e "receber locais", por si, nos serviços do BizTalk. Para obter mais informações, consulte [Criando contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Canais (pontes)

No EDI do BizTalk Server, canais são entidades de processamento de mensagem que também podem incluir lógica personalizada para recursos de processamento específico, conforme necessário pelo aplicativo. Para os serviços do BizTalk, o equivalente seria uma ponte EDI. No entanto nos serviços do BizTalk, por agora, as pontes EDI são "fechadas".  Isto é, você não pode adicionar suas próprias atividades personalizadas a uma ponte de EDI. Qualquer processamento personalizado deve ser feito fora a ponte EDI em seu aplicativo, antes ou depois a mensagem insere a ponte configurada como parte do contrato de parceiro de negociação. Pontes EAI tem a opção de fazer processamento personalizado. Se você quiser processamento personalizado, você pode usar pontes EAI antes ou depois que a mensagem é processada pelo ponte EDI. Para obter mais informações, consulte [como incluir código personalizado em pontes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Você pode inserir um fluxo de publicar/assinar com código personalizado e/ou usando o barramento de serviço tópicos e filas de mensagens antes do contrato de parceiro negociação recebe a mensagem ou após o contrato processa a mensagem e a encaminha para um ponto de extremidade do barramento de serviço.

Ver o **Fluxo de cenários/mensagem** neste tópico para o padrão de fluxo de mensagem.

## <a name="agreements"></a>Contratos

Se você estiver familiarizado com o BizTalk Server 2010 esportivos parceiro contratos usados para processamento de EDI, Serviços BizTalk negociação acordos de parceiro ser bastante familiares. A maioria das configurações de contrato é as mesmas e usa a mesma terminologia. Em alguns casos, as configurações de contrato são muito mais simples em comparação com as mesmas configurações no BizTalk Server. Serviços do Microsoft Azure BizTalk suporta X12 e EDIFACT AS2 transportam.

Serviços do Microsoft Azure BizTalk também fornece uma ferramenta de **Migração de dados de TPM** para migrar negociação parceiros e os contratos do módulo de parceiro de negociação do BizTalk Server para o Portal de serviços do BizTalk. A ferramenta de migração de dados de TPM está disponível como parte de um pacote de ferramentas, que pode ser baixado do [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). O pacote também inclui um arquivo Leiame que fornece instruções sobre como usar a ferramenta e informações básicas de solução de problemas para a ferramenta.

## <a name="schemas"></a>Esquemas

Serviços do BizTalk fornece esquemas EDI que podem ser usados em soluções dos serviços do BizTalk.  Além disso, esquemas de EDI do BizTalk Server também podem ser usados com os Serviços BizTalk porque o nó raiz do esquema EDI é mesmo nos BizTalk Server, bem como serviços do BizTalk. Portanto, você poderá levar seus esquemas EDI do BizTalk Server diretamente e usá-las nas soluções EDI que você desenvolver usando os serviços do BizTalk. Você também pode baixar os esquemas do [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapas (transformações)

Mapas no BizTalk Server são chamados de transformações nos serviços do BizTalk. Migrando mapas do BizTalk Server para serviços de BizTalk podem ser uma das tarefas mais complexas para atingir (dependendo da complexidade de mapa). A ferramenta de mapeamento usada para serviços de BizTalk é diferente do BizTalk mapper. Embora o mapeador principalmente tenha a mesma aparência, o formato de mapa subjacente é diferente. Os functoids (chamados de **Operações de mapa** nos serviços do BizTalk) disponíveis para os usuários também são diferentes.  Na verdade, você não pode usar diretamente um mapa de BizTalk nos serviços do BizTalk. Além disso, nem todos os functoids disponíveis no BizTalk Server estão disponíveis como operações de mapa nos serviços do BizTalk.

### <a name="new-transform-operations"></a>Novas operações de transformação

Enquanto a lista mapa de operações de transformação disponíveis pareça bem diferente de mapeador BizTalk Server, BizTalk Services transforma ter novas maneiras de realizar as mesmas tarefas. Por exemplo, BizTalk Services transforma ter **Operações de lista** disponíveis. Isso não estava disponível no mapeador BizTalk.  As **Operações de lista** permitem que você crie e operar em uma "lista", onde uma lista é um conjunto de itens (também conhecido como "linhas") e onde cada item pode ter vários membros (também conhecido como "colunas").  Você pode classificar a lista, selecione itens com base em uma condição, etc.

Outro exemplo de nova funcionalidade no BizTalk Services transforma são as **Operações de Loop**.  É difícil criar loops aninhados no mapeador BizTalk Server.  Portanto, as operações de mapa de Loop são adicionadas para os serviços de BizTalk as transformações.

Ainda outro exemplo é a operação de mapa de expressão **If-Then-Else** .  Fazer uma operação if-then-else foi possível no mapeador BizTalk, mas ele necessário functoids vários para realizar uma tarefa visivelmente simple.

### <a name="migrating-biztalk-server-maps"></a>Migrando BizTalk Server mapeia

Serviços do Microsoft Azure BizTalk fornece uma ferramenta para migrar BizTalk Server mapeia para Serviços BizTalk transformações. O **BTMMigrationTool** está disponível como parte do pacote de **Ferramentas** fornecido com o [SDK dos serviços do BizTalk baixar](http://go.microsoft.com/fwlink/p/?LinkId=235057). Para obter mais informações sobre a ferramenta, consulte [converter um mapa de BizTalk para Serviços BizTalk transformação](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Você também pode examinar uma amostra por Sandro Pereira, MVP do BizTalk, sobre como [migrar mapas de BizTalk Server para transformações de Serviços BizTalk](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orquestrações

Se você precisar migrar coordenação BizTalk Server processamento para Microsoft Azure, as orquestrações precisaria ser reescrito porque Microsoft Azure não dá suporte a execução orquestrações BizTalk Server.  Você pode regravar a funcionalidade de coordenação em um serviço do Windows Workflow Foundation 4.0 (WF4).  Isso seria uma reformulação completa como não há atualmente nenhuma migração de orquestrações BizTalk Server para WF4. Aqui estão alguns recursos de fluxo de trabalho do Windows:

- [*Como integrar um serviço de fluxo de trabalho do WCF com tópicos e filas do barramento de serviço*](https://msdn.microsoft.com/library/azure/hh709041.aspx) por Paolo Salvatori. 

- [ *Criando aplicativos com o Windows Workflow Foundation e Azure* sessão](http://go.microsoft.com/fwlink/p/?LinkId=237314) da conferência Build 2011.

- [*Central de desenvolvedores do Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) no MSDN.

- [*Documentação do Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) no MSDN.

## <a name="other-considerations"></a>Outras considerações

A seguir estão algumas considerações que você deve fazer ao usar os Serviços BizTalk.

### <a name="fallback-agreements"></a>Contratos de fallback

Processamento de EDI do BizTalk Server tem o conceito de "Contratos de Fallback".  Serviços de BizTalk faz **não** possuem um conceito de contrato de Fallback até o momento.  Consulte BizTalk documentação tópicos [A função de contratos no processamento de EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) e [Configurando Global ou Fallback contrato propriedades](https://msdn.microsoft.com/library/bb245981.aspx) para obter informações sobre como os contratos de Fallback são usados no BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>O roteamento para vários destinos

Pontes de Serviços BizTalk, em seu estado atual não suporta roteamento de mensagens para vários destinos usando um publicar-assinar o modelo. Em vez disso, você pode encaminhar mensagens de uma ponte de Serviços BizTalk para um tópico de barramento de serviço, que podem ter várias assinaturas a receber a mensagem mais de um ponto de extremidade.

## <a name="conclusion"></a>Conclusão

Serviços do Microsoft Azure BizTalk é atualizado em regulares etapas para adicionar mais recursos e funcionalidades. Com cada atualização, esperamos para maior funcionalidade para facilitar a criação de soluções de ponta a ponta usando os serviços do BizTalk e outras tecnologias Azure de suporte.

## <a name="see-also"></a>Consulte também

[Desenvolvimento de aplicativos empresariais com o Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
