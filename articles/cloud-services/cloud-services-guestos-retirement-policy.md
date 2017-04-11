<properties 
   pageTitle="Guia de suporte e política de aposentadoria para o sistema operacional de convidado do Azure | Microsoft Azure" 
   description="Fornece informações sobre o que a Microsoft oferecerá suporte como relação ao SO convidado Azure usada pelos serviços de nuvem." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Política de suporte e desativação de SO convidado Azure
As informações nesta página se relaciona com o sistema operacional do Azure convidado ([SO convidado](cloud-services-guestos-update-matrix.md)) para trabalhador de serviços de nuvem e funções da web (PaaS). Ele não se aplica às máquinas virtuais (IaaS). 

A Microsoft tem uma [política para o sistema operacional convidado de suporte](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)de publicado. A página que você está lendo agora descreve como a política é implementada.

A política é 

1. A Microsoft oferecerá suporte **pelo menos as duas famílias mais recentes do sistema operacional convidado**. Quando uma família é desativada, os clientes têm 12 meses a partir da data de aposentadoria oficial para atualizar para uma família de SO convidado com suporte mais recente.
2. A Microsoft oferecerá suporte a **pelo menos duas versões mais recentes das famílias de SO convidado com suporte**. 
3. A Microsoft oferecerá suporte no **mínimo as duas versões mais recentes do SDK do Azure**. Quando uma versão do SDK é desativada, os clientes terão 12 meses a partir da data de aposentadoria oficial atualizar para uma versão mais recente. 

Às vezes mais de duas versões ou famílias podem ser suportados. Informações de suporte de SO convidado oficiais aparecerão na [matriz de compatibilidade de SDK e versões de sistemas operacionais de convidado do Azure](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Quando uma família de SO convidado ou a versão está desativado 


Uma nova SO convidado **família** é introduzido após o lançamento de uma nova versão oficial do sistema operacional Windows Server. Sempre que uma nova família de SO convidado é introduzida, Microsoft irá retirar a família SO convidado mais antigo. 

Novas SO convidado **versões** são introduzidas sobre cada mês para incorporar as últimas atualizações do MSRC. Devido as atualizações mensais regulares, uma versão de SO convidado é normalmente desabilitados 60 dias após o lançamento. Isso mantém pelo menos duas versões de SO convidado para cada família disponível para uso. 

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante uma desativação família SO convidado 


Assim que a desativação será anunciada, os clientes têm um período de 12 meses "transição" antes da família mais antiga oficialmente é removida do serviço. Esse tempo de transição pode ser estendido a critério da Microsoft. As atualizações serão lançadas nas [versões de sistemas operacionais de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

Um processo de aposentadoria gradual começará 6 meses para o período de transição. Durante esse tempo:

1. Microsoft irá notificar os clientes sobre a aposentadoria. 
2. A versão mais recente do SDK do Azure não suporta a família de SO convidado desativada.
3. Novas implantações e mostram reimplantações de serviços de nuvem não serão permitidas na família retirada

Microsoft continuará apresentar a nova versão de SO convidado incorporando as últimas atualizações do MSRC até o último dia do período de transição, conhecido como a "data de expiração". Nesse momento, os serviços de nuvem ainda em execução será ser sem suporte em SLA Azure. A Microsoft tem a critério forçar atualização, excluir ou interromper esses serviços após essa data.



### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante uma desativação da versão do sistema operacional de convidado 
Se os clientes definem seus SO convidado para atualizar automaticamente, eles nunca precisam se preocupar sobre como lidar com versões de SO convidado. Eles sempre utilizará a versão mais recente do sistema operacional de convidado.

Versões de sistemas operacionais de convidado são lançadas a cada mês. Devido a taxa de lançamentos regulares, cada versão tem um tempo de vida fixo.

Em 60 dias para o período de vida uma versão é "*desativado*". "Desativado" significa que a versão é removida do Azure portal clássico. Ele também pode não ser definido CSCFG arquivo de configuração. Implantações existentes permanecem em execução, mas novas implantações e atualizações de código e a configuração para implantações existentes não serão permitidas. 

Posteriormente, o sistema operacional convidado versão "*expira*" e quaisquer instalações ainda executando essa versão é forçar atualizado e conjunto para atualizar automaticamente o sistema operacional convidado no futuro. Expiração é feita em lotes para que o período de tempo de desabilitação de expiração pode variar. 

Esses períodos podem ser feitos mais critério da Microsoft para facilitar transições de cliente. As alterações serão comunicadas nas [versões de sistemas operacionais de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Notificações durante aposentadoria 

* **Família aposentadoria** <br>Microsoft usará postagens de blog e notificação de portal clássica Azure. Os clientes que ainda estiver usando uma família de SO convidado descartada serão notificados por meio de comunicação direta (email, mensagens de portal, chamada telefônica) para administradores de serviço atribuído. Todas as alterações serão lançadas para esta página e o RSS feed listados no início desta página. 


* **Versão aposentadoria** <br>Todas as alterações serão lançadas para esta página e o RSS feed listados no início desta página, incluindo a versão, desabilitado e datas de vencimento. Os administradores de serviços receberá e-mails se tiverem implantações em execução em uma família ou uma versão de SO convidado desativado. O intervalo desses e-mails pode variar. Geralmente elas são pelo menos um mês antes da desativação, embora esse intervalo não é um SLA oficial. 


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Como posso atenuar os impactos da migração?**

Você deve usar mais recente família SO convidado para projetar seus serviços de nuvem. 

1. Comece a planejar sua migração para uma família mais recente no início. 
2. Configure implantações de teste temporárias para testar seu serviço de nuvem em execução no nova família. 
3. Definir sua versão de SO convidado como **automático** (osVersion = * no arquivo [.cscfg](cloud-services-model-and-package.md#cscfg) ) para que a migração para novas versões de SO convidado ocorre automaticamente.

**E se meu aplicativo web requer maior integração com o sistema operacional?**

Se a arquitetura de seu aplicativo web requer mais profunda dependência no sistema operacional subjacente, use plataforma suporte para recursos como [tarefas de inicialização](cloud-services-startup-tasks.md) ou outros mecanismos de extensibilidade que podem existir no futuro. Como alternativa, você também pode usar [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infraestrutura como um serviço), onde você é responsável por manter o sistema operacional subjacente.
 
## <a name="next-steps"></a>Próximas etapas
Examine as últimas [lançamentos de SO convidado](cloud-services-guestos-update-matrix.md).
