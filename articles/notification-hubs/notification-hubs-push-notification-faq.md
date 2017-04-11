<properties
    pageTitle="Hubs de notificação Azure - perguntas frequentes (perguntas frequentes)"
    description="Perguntas frequentes sobre projetar ou implementar soluções em Hubs de notificação"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="notificação de envio, notificações por push, as notificações de envio do iOS, notificações por push android, envio do ios, android push"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notificações de envio com Hubs de notificação Azure - perguntas frequentes

##<a name="general"></a>Geral
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. o que é o modelo de preço de Hubs de notificação?
Hubs de notificação é oferecida em três níveis:

* **Livre** - obter coloca até 1 milhão por assinatura por mês.
* **Básica** - obter 10 milhões coloca por assinatura por mês como uma linha de base, com opções de crescimento de cota.
* **Padrão** - obter coloca 10 milhões por assinatura por mês como uma linha de base, com cota aumentar opções, além de telemetria rich capabilties.

Os detalhes mais recentes podem ser encontrados na página de [Preços de Hubs de notificação] . O preço é estabelecido no nível de assinatura e é baseado no número de desnecessárias de notificação de envio para que não importa quantas hubs namespaces ou uma notificação de que você criou na sua assinatura do Azure.

Nível de **livre** é oferecida para finalidade de desenvolvimento com nenhuma garantia de SLA. Embora esta camada possa ser um bom ponto de partida para aqueles que deseja explorar os recursos de notificações de envio por meio de Hubs de notificação do Azure, talvez não seja a melhor opção para médias e aplicativos de grande escala.

**Básico** & níveis**padrão** são oferecidas para uso de produção com os seguintes recursos principais habilitado *somente para o padrão de nível*:

- *Telemetria Rich* - oferta de notificação Hubs um número de recursos para exportar seus dados de telemetria, bem como o envio de informações de registro de notificação para exibição offline e análise.
- *Multilocação* - Ideal se você estiver criando um aplicativo móvel usando os Hubs de notificação para dar suporte a vários locatários. Isso permite definir credenciais de serviços de notificação de envio por Push (PNS) no nível de namespace do Hub de notificação para o aplicativo e, em seguida, você pode separar os locatários fornecendo hubs individuais sob este namespace comuns. Isso permite facilidade de manutenção enquanto mantendo as chaves SAS para enviar e receber notificações por push dos hubs notificação separados para cada locatário garantindo que sobreposição de locatário de cruz não.
- *Agendada Push* - permite que você agende as notificações por push, que serão subsequentemente enfileiradas e enviadas.
- A *importação em massa* - permite que você importe registros em massa.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. o que é o SLA de Hubs de notificação?
Para níveis **básicos** e Hubs de notificação **padrão** , garantimos que pelo menos 99,9% das vezes, aplicativos configurados adequadamente poderão enviar notificações por push ou realizar operações de gerenciamento de registro relacionadas com um Hub de notificação implantado em um nível compatível. Para saber mais sobre nosso SLA, visite a página de [SLA de Hubs de notificação] .

> [AZURE.NOTE] Não há nenhuma garantia de SLA para o trecho entre o serviço de notificação de plataforma e o dispositivo como Hubs de notificação dependem de provedores de plataforma externo para fornecer a notificação de envio para o dispositivo.

###<a name="3---which-customers-are-using-notification-hubs"></a>3. quais clientes estão usando Hubs de notificação?
Temos um grande número de clientes com Hubs de notificação alguns notáveis mostradas abaixo:

* Sochi 2014 – 100s de grupos de interesse, 3 + milhões de dispositivos, 150 milhões notificação enviada em 2 semanas. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Tempos de Seattle - [CaseStudy - tempos de Seattle]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Aplicativos do Bing – 10s de milhões de dispositivos, enviando notificações de 3 milhões/dia.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. como atualizar ou fazer downgrade meu Hubs de notificação para alterar meu nível de serviço?
Acesse o [Portal de clássico do Azure], clique barramento de serviço e clique em seu namespace depois seu hub de notificação. Na guia escala, você poderá alterar o nível de serviço de Hubs de notificação.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Design e desenvolvimento
###<a name="1---which-server-side-platforms-do-you-support"></a>1. quais plataformas de servidor você suporte?
Fornecemos SDK e [exemplos completos] para .NET, Java, PHP, Python, Node para que um back-end do aplicativo pode ser configurado para se comunicar com Hubs de notificação usando qualquer uma dessas plataformas. Notificação Hubs APIs são baseados em REST interfaces para que possa escolher diretamente falar em vez disso, se você não quiser adicionar uma dependência extra. Mais detalhes podem ser encontradas na página [NH - APIs REST] .

###<a name="2---which-client-platforms-do-you-support"></a>2. quais plataformas de cliente você suporte?
Oferecemos suporte para enviar as notificações por push para o [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universal do Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [China Android (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), plataformas de [Aplicativos do Chrome](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) . Para obter uma lista completa de tutoriais de Introdução lidar com as notificações por push envio nessas plataformas, visite nossa página [NH - obtendo tutoriais de Introdução] .

###<a name="3---do-you-support-smsemailweb-notifications"></a>3. você oferece suporte a notificações de Email/SMS/web?
Hubs de notificação é projetado principalmente para enviar notificações aos aplicativos móveis usando as plataformas listadas acima. Nós ainda não oferece a capacidade de enviar email ou alertas SMS; No entanto, plataformas de terceiros que fornecem esses recursos podem ser integradas junto com Hubs de notificação de enviar notificações por push nativo usando [Aplicativos do Azure Mobile].

Hubs de notificação também não fornecem um navegador push notificação entrega serviço-de-prontos. Os clientes podem optar por implementar isso usando o SignalR sobre as plataformas suportadas do lado do servidor. Se você está procurando para enviar notificações para aplicativos de navegador na caixa de proteção Chrome, consulte o [tutorial de aplicativos do Chrome].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. qual é a relação entre os aplicativos do Azure Mobile e Hubs de notificação do Azure e quando usar o que?
Se você tiver um back-end do aplicativo móvel existente e você deseja adicionar a capacidade de enviar notificações por push, em seguida, você pode usar Hubs de notificação do Azure. Se você quiser configurar seu back-end do aplicativo móvel do zero, em seguida, considere usar aplicativos do Azure Mobile. Um aplicativo móvel do Azure provisiona automaticamente um Hub de notificação para que você seja capaz de enviar notificações por push facilmente do aplicativo móvel back-end. Preços para aplicativos do Azure móvel incluem os encargos base de um Hub de notificação e você paga apenas quando você ultrapassar as verificações extras incluídas. Obter mais detalhes sobre os custos estão disponíveis na página de [Preços de serviços de aplicativo] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. quantos dispositivos posso suporte se eu enviar notificações de envio por meio de Hubs de notificação?
Consulte a página de [Preços de Hubs de notificação] para obter detalhes sobre o número de dispositivos com suporte.

Para determinados cenários, se precisar de suporte para mais de 10.000.000 dispositivos registrados, por favor, [Fale conosco](https://azure.microsoft.com/overview/contact-us/) diretamente e podemos ajudará você dimensionar sua solução.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. quantas notificações de envio podem eu enviar?
Dependendo a camada selecionada, Azure serão automaticamente redimensionados para cima com base no número de notificações fluindo por meio do sistema.

>[AZURE.NOTE] O custo de uso geral pode subir com base no número de notificações de envio sendo atendidos. Certifique-se de que você estejam cientes dos limites de nível existente em estrutura de tópicos na página de [Preços de Hubs de notificação] .

Nossos clientes existentes estão usando Hubs de notificação para enviar milhões de notificações por push diariamente. Você não precisa fazer nada especial para dimensionar seu envio notificações atinja enquanto você estiver usando Hubs de notificação do Azure.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. quanto tempo leva para enviadas notificações alcançar meu dispositivo de envio?
Azure Hubs de notificação é capaz de processar pelo menos a **notificação de envio de 1 milhão envia um minuto** em um normal use cenário onde a carga de entrada é bastante consistentes e não está spikey natureza. Essa taxa pode variar dependendo do número de marcas, natureza da entrada envia e outros fatores externos.

Durante o tempo de entrega estimada, o serviço é capaz de calcular os destinos por mensagens de plataforma e roteiro para os serviços de entrega de notificação de envio respectivos com base em expressões marcas/marca registrada. Daqui em diante, ele é responsabilidade dos serviços notificações Push (PNS) para enviar a notificação no dispositivo.

Um PNS não garante qualquer SLA para fornecer notificações; No entanto, normalmente uma grande maioria de notificações de envio são entregues para dispositivos de destino em poucos minutos (geralmente dentro dos limites de 10 minutos) desde o momento em que eles são enviados para nossa plataforma. Pode haver alguns destaques que podem levar mais tempo.

>[AZURE.NOTE] Azure Hubs de notificação tem uma política no lugar para descartar qualquer notificações de envio que não podem ser entregue para o PNS em 30 minutos. Esse atraso pode acontecer por vários motivos, mais comumente porque o PNS é a otimização de seu aplicativo.

###<a name="8---is-there-any-latency-guarantee"></a>8. há nenhuma garantia de latência?
Devido à natureza de notificações de envio (eles são entregues por um serviço de notificações Push externo, específicos de plataforma), não há nenhuma garantia de latência. Normalmente, a maioria das notificações por push entregue dentro de alguns minutos.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. quais são as considerações que preciso levar em consideração ao projetar uma solução com namespaces e Hubs de notificação?

####<a name="mobile-appenvironment"></a>Aplicativo/ambiente móvel

* Deve haver um Hub de notificação por aplicativo móvel, por ambiente.
* Em um cenário de vários locatário, cada locatário deve ter um hub separado.
* Você nunca deve compartilhar o mesmo Hub de notificação entre ambientes de produção e de teste como isso pode causar problemas durante a linha ao enviar notificações. Por exemplo, Apple oferece pontos de extremidade de área restrita e produção Push cada qual com credenciais separadas.
* Por padrão, você pode enviar notificações de teste para seus dispositivos registrados por meio do Portal do Azure ou o componente integrado Azure no Visual Studio. O limite é definido como 10 dispositivos que estão selecionados aleatoriamente do pool de registro.

>[AZURE.NOTE] Se o hub foi configurado originalmente com um certificado de área restrita do Apple e, em seguida, configurado para usar um certificado de produção Apple, os tokens de dispositivo antigo seriam invalidadas com o novo certificado e causar coloca falha. É melhor separar a produção e ambientes de teste e use hubs diferentes para ambientes diferentes.

####<a name="pns-credentials"></a>Credenciais PNS

Quando um aplicativo móvel estiver registrado no portal do desenvolvedor de uma plataforma (por exemplo, Apple ou Google etc), em seguida, você recebe um aplicativo tokens de segurança e identificador que um back-end do aplicativo precisa fornecer aos serviços de notificação de envio da plataforma para poder enviar notificações por push para os dispositivos. Esses tokens de segurança que podem ser na forma de certificados (por exemplo, para Apple iOS ou Windows Phone) ou chaves de segurança (Google Android, Windows etc) precisam ser configurados em Hubs de notificação. Isso é feito normalmente no nível do hub de notificação, mas também pode ser feito no nível de namespace em um cenário com vários locatário.

####<a name="namespaces"></a>Namespaces

Namespaces podem ser usados para agrupamento de implantação.  Ele também pode ser usado para representar todos os Hubs de notificação para todos os locatários do mesmo aplicativo no cenário vários locatário.

####<a name="geo-distribution"></a>Distribuição geográfica

Distribuição geográfica nem sempre é crítica em cenários de notificação de envio. É a ser observado que vários serviços de notificação por Push (por exemplo, APNS, GCM etc), que, por fim, entregar as notificações de envio para os dispositivos, não estiverem uniformemente distribuído.

Se você tiver um aplicativo que é usado em todo o mundo, você pode criar vários hubs em diferentes namespaces tirar proveito da disponibilidade do serviço de notificação Hubs em diferentes regiões Azure em todo o mundo.

>[AZURE.NOTE] Isso aumentará o custo de gerenciamento - especialmente ao redor de registros, para que isso realmente não é recomendado e deve ser executado somente se houver uma necessidade explícita.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10. devo fazer registros do aplicativo back-end ou diretamente por meio de cliente dispositivos?
Registros do aplicativo back-end são úteis quando você precisa fazer autenticação de cliente antes de criar o registro ou quando você possui marcas que devem ser criadas ou modificadas por back-end aplicativo com base em alguma lógica de aplicativo. Para obter detalhes, você pode ler mais nas páginas de [orientação de registro de back-end] e [back-end registro orientações - 2] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. o que é o modelo de segurança de entrega de notificação de envio?
Azure Hubs de notificação de usar uma [Assinatura de acesso compartilhado (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md)-com base em modelo de segurança. Você pode usar os tokens SAS no nível raiz do namespace ou do nível de notificação Hubs granular. Esses tokens SAS podem ser definidas com regras de autorização diferente por exemplo, enviar mensagem permissões, ouvir permissões de notificação etc. Mais detalhes estão disponíveis no [modelo de segurança de NH] documento.

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. como posso deve tratar carga confidencial nas notificações de envio?
Todas as notificações serão entregues em dispositivos de destino da plataforma Push notificação serviços (PNS). Quando um remetente envia uma notificação para Hubs de notificação do Azure estamos processar e passar a notificação para os respectivos PNS.

Todas as conexões do remetente para os Hubs de notificações do Azure e depois para o PNS usam HTTPS.

>[AZURE.NOTE] Azure Hubs de notificações não registra a carga da mensagem de nenhuma forma.

Para enviar cargas confidenciais Entretanto, recomendamos que um padrão de envio por Push seguro onde o remetente oferece uma notificação 'ping' com um identificador de mensagem no dispositivo sem a carga confidencial e quando o aplicativo no dispositivo recebe essa carga, ele é capaz de chamar uma API segura diretamente para buscar os detalhes da mensagem. Um guia sobre como implementar o padrão descrito acima está disponível na página [NH - tutorial seguro Push] .

##<a name="operations"></a>Operações
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. qual é a história de recuperação de desastres (DR)?
Fornecemos cobertura de recuperação de metadados no nosso end (nome do Hub de notificação, cadeia de conexão e outras informações críticas). Quando um cenário de DR é disparado, os dados de registros são o **segmento somente** da infraestrutura Hubs de notificação que serão perdida. Você precisará implementar uma solução para preencher dados em seu novo hub após a recuperação.

- *Etapa 1* - criar um Hub de notificação secundário em um data center diferente. Você pode criar isso dinamicamente no momento do evento DR ou você pode criar um get ir. Não faz muita diferença opção que você escolher como provisionar o Hub de notificação é um processo relativamente rápido na ordem de alguns segundos. Ter um desde o começo, você será protegem a partir do evento de DR afetar seus recursos de gerenciamento, portanto, é a opção altamente recomendável.

- *Etapa 2* - Hydrate notificação Hub secundário com os registros do Hub notificação principal. Não é recomendável tentar manter os registros em ambos os hubs e tentar mantê-las em sincronia instantâneos como registros estejam chegando em - normalmente que ainda não trabalhou bem devido a tendência inerente dos registros de expirar no lado PNS. Notificação Hubs limpá-los como podemos receber comentários PNS sobre registros expirados ou inválidos.  

Recomendação é usar um back-end do aplicativo que:

- Mantém um determinado conjunto de registros no fim para que ele possa fazer uma inserção em massa no hub notificação secundário no caso de DR

**OU**

- Obtém uma regular despejar dos registros do hub primário como um backup e uma massa insira em a NH secundário.

>[AZURE.NOTE] Funcionalidade de importação/exportação de registros disponível na camada padrão é descrita no documento de [Importação/exportação de registros] .

Se você não tiver um back-end, em seguida, quando o aplicativo for iniciado em qualquer um dos dispositivos de destino, eles executará um novo registro no Hub do notificação secundário, e eventualmente secundário Hub de notificação terá todos os dispositivos ativos registrados.

A desvantagem é que haverá um período de tempo quando dispositivos onde os aplicativos ainda não tiver aberto o não receberá notificações.

###<a name="2---is-there-any-audit-log-capability"></a>2. há qualquer recurso de log de auditoria?
Todas as operações de gerenciamento de Hubs de notificação acessem Logs de operação que são expostas no [Portal de clássico do Azure].

##<a name="monitoring--troubleshooting"></a>Monitoramento e solução de problemas
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. quais recursos de solução de problemas estão disponíveis?
Azure Hubs de notificação fornecem diversos recursos para executar a solução de problemas comuns, particularmente no cenário mais comum em torno de notificações ignorados. Consulte os detalhes em nosso [NH - solução de problemas] white paper.

###<a name="2---what-telemetry-features-are-available"></a>2. quais recursos de telemetria estão disponíveis?
Habilita a notificação Hubs Azure exibindo dados de telemetria no [Portal de clássico do Azure]. Detalhes das métricas disponíveis estão disponíveis na página [NH - métricas] .

>[AZURE.NOTE] Notificações bem-sucedida significam apenas que as notificações de envio tiveram foi entregue para o serviço de notificações Push externos (por exemplo, APNS para a Apple, GCM para Google etc). É até o PNS para fornecer a notificação para dispositivos de destino. Normalmente, o PNS não expõem métricas de entrega para terceiros.  

Fornecemos também a capacidade de exportar os dados de telemetria programaticamente (no nível **padrão** ). Consulte o [NH - amostra de métricas] para obter detalhes.

[Azure Portal clássico]: https://manage.windowsazure.com
[Hubs de notificação preços]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notificação Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - tempos de Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - APIs REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - tutoriais de Introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Tutorial de aplicativos do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Diretrizes de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Diretrizes de registro de back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança de NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - tutorial de envio seguro]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - solução de problemas]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - métricas]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - amostra de métricas]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Importação/exportação de registros]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[exemplos completos]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicativos móveis do Azure]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[Preço de serviços de aplicativo]: https://azure.microsoft.com/en-us/pricing/details/app-service/
