<properties
    pageTitle="Segurança para Hubs de notificação"
    description="Este tópico explica segurança para hubs de notificação Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Segurança

##<a name="overview"></a>Visão geral

Este tópico descreve o modelo de segurança dos Hubs de notificação do Azure. Como Hubs de notificação são uma entidade de barramento de serviço, eles implementam o mesmo modelo de segurança que barramento de serviço. Para obter mais informações, consulte os tópicos de [Autenticação de barramento de serviço](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

##<a name="shared-access-signature-security-sas"></a>Segurança de assinatura de acesso compartilhado (SAS) 

Notificação Hubs implementa um esquema de segurança de nível de entidade chamado SAS (assinatura de acesso compartilhado). Esse esquema permite mensagens entidades declarar até 12 regras de autorização em sua descrição que conceder direitos na entidade.

Cada regra contém um nome, um valor de chave (secreta compartilhada) e um conjunto de direitos, conforme explicado na seção "Declarações de segurança". Ao criar um Hub de notificação, duas regras são criadas automaticamente: uma com direitos de ouvir (que usa o aplicativo de cliente) e outra com todos os direitos (que usa o back-end do aplicativo).

Ao executar o gerenciamento de registro de aplicativos do cliente, se as informações enviadas por meio de notificações não é confidenciais (por exemplo, atualizações do tempo), uma maneira comum para acessar um Hub de notificação para dar o valor de chave da regra de acesso somente ouvir ao aplicativo do cliente e para fornecer o valor da chave do acesso completo regra no back-end do aplicativo.

Não é recomendável que você inserir o valor da chave em aplicativos cliente da Windows Store. Uma maneira de evitar incorporação o valor de chave é ter o aplicativo cliente recuperá-lo do aplicativo back-end na inicialização.

É importante entender que a tecla com o acesso de ouvir permite que um aplicativo cliente registrar para qualquer marca. Se seu aplicativo deve restringir os registros a tags específicas aos clientes específico (por exemplo, quando marcas representam IDs de usuário), seu back-end do aplicativo deve executar os registros. Para obter mais informações, consulte Gerenciamento de registro. Observe que dessa maneira, o aplicativo cliente não terão acesso direto aos Hubs de notificação.

##<a name="security-claims"></a>Declarações de segurança

Semelhante a outras entidades, operações de Hub de notificação são permitidas para três declarações de segurança: ouvir, enviar e gerenciar.

| Declaração | Descrição | Operações permitidas |
|-------|-------------|--------------------|
| Ouvir | Criar/atualizar, leia e excluir registros único | Criar/Atualizar registro<br><br>Registro de leitura<br><br>Ler todos os registros para uma alça<br><br>Excluir registro |
| Enviar | Enviar mensagens para o hub de notificação | Enviar mensagem |
| Gerenciar | CRUDs em Hubs de notificação (incluindo atualizando PNS credenciais e chaves de segurança) e leia registros com base em marcas | Hubs de notificação de criar/atualizar/leitura/excluir<br><br>Ler registros por marca |


Hubs de notificação aceitam declarações concedidas por tokens de controle de acesso do Microsoft Azure e por tokens de assinatura gerados com chaves compartilhadas configuradas diretamente no Hub notificação.