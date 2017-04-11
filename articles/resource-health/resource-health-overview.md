<properties
   pageTitle="Visão geral de integridade do Azure recursos | Microsoft Azure"
   description="Visão geral da integridade de recurso do Azure"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Visão geral de integridade do Azure recursos

Integridade do recurso Azure é um serviço que expõe a integridade dos recursos Azure individuais e fornece orientação viável para solucionar problemas. Em um ambiente de nuvem onde não é possível acessar diretamente servidores ou elementos de infraestrutura, o objetivo de integridade do recurso é reduzir o tempo gastam de clientes em solução de problemas, em particular, reduzindo o tempo gasto determinando se a raiz do problema estabelece dentro do aplicativo ou se ela é causada por um evento dentro de plataforma Windows Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>O que é considerado um recurso e como faz integridade do recurso decide se o recurso é eficaz, ou não? 
Um recurso é uma instância de criados pelo usuário de um tipo de recurso fornecido por um serviço, por exemplo: uma máquina virtual, um aplicativo Web ou um banco de dados do SQL. 

Integridade do recurso depende de sinais emitidos por recurso e/ou o serviço para determinar se um recurso é eficaz, ou não. É importante notar que atualmente recurso integridade somente as contas para a integridade de um recurso específico digite e não considera outros elementos que podem contribuir para a integridade geral. Por exemplo, ao relatar que o status de uma máquina virtual, apenas a parte de computação da infraestrutura é considerado, ou seja, problemas na rede não serão exibidos em integridade do recurso, a menos que haja uma falha de serviço declarado nesse caso, ele será ser reproduzido através do banner na parte superior da lâmina. Mais informações sobre a interrupção do serviço são oferecidas neste artigo. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Como a integridade do recurso é diferente do painel de integridade do serviço?

As informações fornecidas pela integridade do recurso são mais granulares que é fornecido pelo painel de integridade do serviço. Embora SHD se comunica eventos que afetam a disponibilidade de um serviço em uma região, integridade do recurso expõe informações relevantes para um recurso específico, por exemplo, ele irá expor eventos que afetam a disponibilidade de uma máquina virtual, um aplicativo web ou um banco de dados do SQL. Por exemplo, se um nó inesperadamente reinicialização, clientes cujas máquinas virtuais foram executados nesse nó poderão obter o motivo por que seus foi indisponível por um período de tempo.   

## <a name="how-to-access-resource-health"></a>Como acessar a integridade do recurso
Para os serviços disponíveis por meio de integridade do recurso, há 2 maneiras de acessar a integridade do recurso.

### <a name="azure-portal"></a>Portal do Azure
A lâmina de integridade do recurso no Portal do Azure, fornece informações detalhadas sobre a integridade do recurso bem como recomendado ações que variam dependendo a integridade atual do recurso. Este blade fornece a melhor experiência ao consultar a integridade dos recursos, como facilita o acesso a outros recursos dentro do portal. Conforme mencionado anteriormente, o conjunto de ações recomendadas na lâmina recurso integridade varia com base na integridade atual:

* Recursos de integridade: desde que não foi detectado nenhum problema que poderia afetar a integridade do recurso, as ações são voltadas para ajudar o processo de solução de problemas. Por exemplo, ela fornece acesso direto lâmina solução de problemas, que oferece uma orientação sobre como resolver a face de clientes problemas mais comuns.
* Recurso não íntegro: problemas causados pelo Azure, a lâmina exibirão ações Microsoft está demorando (ou levou) para recuperar o recurso. Para problemas causados pelo usuário ações iniciadas, o será blade uma lista de clientes de ações pode levar até solucionar o problema e recuperar o recurso.  

Depois de fazer logon no Portal do Azure, há duas maneiras de acessar a lâmina de integridade do recurso: 

###<a name="open-the-resource-blade"></a>Abra a lâmina de recurso
Abra a lâmina de recursos para um determinado recurso. Na lâmina configurações que abre ao lado da lâmina de recurso, clique em integridade do recurso para abrir a lâmina de integridade do recurso. 

![Blade de integridade do recurso](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Ajuda e blade de suporte
Abra a Ajuda e suporte lâmina clicando no ponto de interrogação no canto superior direito e então selecionando Ajuda + suporte. 

**Na barra de navegação superior de**

![Ajuda + suporte](./media/resource-health-overview/HelpAndSupport.png)

Clicar no bloco abre a lâmina de assinatura de integridade do recurso que irá listar todos os recursos em sua assinatura. Ao lado de cada recurso, não há um ícone indicando sua integridade. Clicar em cada recurso abrirá a lâmina de integridade do recurso.

**Bloco de integridade do recurso**

![Bloco de integridade do recurso](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>O que significa o meu status de integridade do recurso?
Há 4 status de integridade diferente que talvez você veja para o recurso.

### <a name="available"></a>Disponível
O serviço não detectou problemas na plataforma que poderia ser impacto sobre a disponibilidade do recurso. Isso é indicado por um ícone de marca de seleção verde. 

![Recurso está disponível](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível

Nesse caso o serviço detectou um problema contínuo na plataforma que está afetando a disponibilidade deste recurso, por exemplo, o nó onde a máquina virtual foi executando reiniciado inesperadamente. Isso é indicado por um ícone de aviso vermelho. Informações adicionais sobre o problema são fornecidas na seção intermediária da lâmina, incluindo: 

1.  Quais ações Microsoft está demorando para recuperar o recurso 
2.  Uma linha do tempo detalhada do problema, incluindo o tempo de resolução esperado
3.  Uma lista de ações para usuários recomendada 

![Recurso não está disponível](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Indisponível – cliente iniciado
O recurso não está disponível devido a uma solicitação de cliente como interromper um recurso ou solicitar uma reinicialização. Isso é indicado por um ícone de informacional azul. 

![Recurso não está disponível devido a uma ação iniciada do usuário](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Desconhecido
O serviço não recebeu informações sobre esse recurso por mais de 5 minutos. Isso é indicado por um ícone de ponto de interrogação cinza. 

É importante observar que isso não é uma indicação definitiva que há algo errado com um recurso, para que os clientes devem seguir essas recomendações:

* Se o recurso esteja funcionando como esperado, mas sua integridade é definida como desconhecido em integridade do recurso, há sem problemas, e você pode esperar o status do recurso para atualizar a íntegra após alguns minutos.
* Se houver problemas ao acessar o recurso e sua integridade são definidos como desconhecido em integridade do recurso, isso pode ser uma indicação antecipada pode haver um problema e investigações adicionais devem ser feitas até que a integridade é atualizada para eficaz, ou não íntegra

![Integridade do recurso é desconhecida](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Eventos que afetam o serviço
Se o recurso pode ser afetado por um evento de impacto de serviço em andamento, uma faixa será exibida na parte superior da lâmina de integridade do recurso. Clicar no banner abrirá a lâmina de eventos de auditoria, que exibirá informações adicionais sobre a interrupção.

![Integridade de recursos pode ser afetada por um SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>O que mais eu preciso saber sobre a integridade do recurso?

### <a name="signal-latency"></a>Latência de sinal
Os sinais que feed de integridade do recurso, pode estar até 15 min atrasada, que podem causar discrepâncias entre o status de integridade atual do recurso e sua disponibilidade real. É importante ter isto em mente como ele ajudará a eliminar tempo desnecessária para analisa possíveis problemas. 

### <a name="special-case-for-sql"></a>Caso especial para SQL 
Relatórios de integridade de recurso o status do banco de dados SQL, não o SQL server. Enquanto ir esta rota fornece uma imagem de integridade mais realista, ele requer que vários serviços e componentes ser levadas em consideração para determinar a integridade do banco de dados. O sinal atual depende de logon no banco de dados, o que significa que para bancos de dados que recebem logon regular (que inclui entre outras coisas, recebendo solicitações de execução de consulta) a integridade status será regularmente exibido. Se o banco de dados não foram acessado por um período de 10 minutos ou mais, ele será movido para o estado desconhecido. Isso não significa que o banco de dados está indisponível, apenas que nenhum sinal foi emitido porque nenhum logon foram executada. Conectando-se ao banco de dados e executar uma consulta irá emitir os sinais necessários para determinar e atualizar o status de integridade do banco de dados.

## <a name="feedback"></a>Comentários
Estamos sempre são abertos para comentários e sugestões! Envie suas [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, você pode envolver conosco por meio do [Twitter](https://twitter.com/azuresupport) ou os [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).
