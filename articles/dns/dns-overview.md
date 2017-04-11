<properties
   pageTitle="Visão geral do DNS Azure | Microsoft Azure"
   description="Visão geral do Microsoft Azure os serviços de hospedagem de DNS do Azure. Hospede seu domínio no Microsoft Azure."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="azure-dns-overview"></a>Visão geral do DNS Azure


O sistema de nome de domínio ou DNS, é responsável por converter (ou resolvendo) um nome de site ou serviço para seu endereço IP. Azure DNS é um serviço de hospedagem para os domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança como outros serviços Azure.


Domínios DNS no DNS Azure são hospedados em rede global do Azure dos servidores de nomes DNS. Usamos difusão de rede para que cada consulta DNS for respondida pelo servidor DNS disponível mais próximo. Isso fornece alta disponibilidade e desempenho rápido para seu domínio.

O serviço DNS Azure baseia-se no Azure Resource Manager (ARM). Como tal, ele benefícios dos recursos ARM como bloqueio de recurso, logs de auditoria e controle de acesso baseado em função. Seus domínios e registros podem ser gerenciados através do portal do Azure, cmdlets do PowerShell do Azure e CLI Azure entre plataformas. Aplicativos que exigem gerenciamento de DNS automático podem integrar com o serviço através da API REST e SDKs.

DNS Azure não suportamos comprando de nomes de domínio. Se você quiser comprar domínios, você precisará usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobrará uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no Azure DNS para o gerenciamento de registros de DNS. Consulte o [representante um domínio ao Azure DNS](dns-domain-delegation.md) para obter detalhes.


## <a name="next-steps"></a>Próximas etapas

[Criar uma zona DNS](dns-getstarted-create-dnszone-portal.md)





