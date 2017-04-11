<properties
pageTitle="Como atualizar um serviço de nuvem | Microsoft Azure"
description="Saiba como atualizar os serviços de nuvem no Azure. Saiba como uma atualização em um serviço de nuvem prossegue para assegurar a disponibilidade."
services="cloud-services"
documentationCenter=""
authors="Thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>Como atualizar um serviço na nuvem

## <a name="overview"></a>Visão geral
10.000 pés, atualizar um serviço de nuvem, incluindo suas funções e o sistema operacional, convidado é um processo de três etapas. Primeiro, os binários e arquivos de configuração para o novo serviço de nuvem ou a versão do sistema operacional devem ser carregados. Em seguida, o Azure reserva recursos de computação e rede para o serviço de nuvem com base em requisitos da nova versão de serviço de nuvem. Por fim, o Azure executa uma atualização sem interrupção para atualizar de forma incremental o locatário para a nova versão ou o sistema operacional, convidado preservando sua disponibilidade. Este artigo discute os detalhes desta última etapa – a atualização sem interrupção.

## <a name="update-an-azure-service"></a>Atualizar um serviço Azure

Azure organiza suas instâncias de função em agrupamentos lógicos chamados de domínios de atualização (UD). Domínios de atualização (UD) são conjuntos lógicos de instâncias de função que são atualizados como um grupo.  As Azure atualizações uma nuvem service um UD por vez, que permite instâncias de outros UDs continue servindo tráfego.

O número de domínios de atualização padrão é 5. Você pode especificar um número diferente de domínios de atualização, incluindo o atributo upgradeDomainCount no arquivo de definição do serviço (.csdef). Para obter mais informações sobre o atributo upgradeDomainCount, consulte [Esquema de WebRole](https://msdn.microsoft.com/library/azure/gg557553.aspx) ou [Esquema de WorkerRole](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Quando você executa uma atualização in-loco de uma ou mais funções seu serviço, o Azure atualiza conjuntos de instâncias de função de acordo com o domínio de atualização à qual pertencem. Atualizações do Azure todas as instâncias em um determinado domínio atualização – interrompendo-los, atualizando-los, colocá-los de volta on-line – então move para o próximo domínio. Interrompendo somente as instâncias em execução no domínio atualização atual, Azure garante que ocorre uma atualização com o mínimo possível impacto no serviço em execução. Para obter mais informações, consulte [como a atualização prossegue](#howanupgradeproceeds) posteriormente neste artigo.

> [AZURE.NOTE] Enquanto os termos de **atualização** e a **atualização** com significado ligeiramente diferentes no contexto do Azure, ele podem ser usados alternadamente para os processos e as descrições dos recursos neste documento.

Seu serviço deve definir pelo menos duas instâncias de uma função para essa função sejam atualizadas in-loco sem tempo de inatividade. Se o serviço consiste em apenas uma instância de uma função, seu serviço estará disponível até que a atualização in-loco tenha terminado.

Este tópico aborda as seguintes informações sobre atualizações do Azure:

-   [Permitido alterações no serviço durante uma atualização](#AllowedChanges)
-   [Como uma atualização prossegue](#howanupgradeproceeds)
-   [Reversão de uma atualização](#RollbackofanUpdate)
-   [Iniciando várias operações mutação implantações em andamento](#multiplemutatingoperations)
-   [Distribuição de funções em domínios de atualização](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Permitido alterações no serviço durante uma atualização
A tabela a seguir mostra as alterações permitidas para um serviço durante uma atualização:

|Alterações permitidas de hospedagem, serviços e funções|Atualização in-loco|Em estágios (troca de VIP)|Excluir e implantar novamente|
|---|---|---|---|
|Versão do sistema operacional|Sim|Sim|Sim
|Nível de confiança do .NET|Sim|Sim|Sim|
|Tamanho de máquina virtual<sup>1</sup>|Sim<sup>2</sup>|Sim|Sim|
|Configurações de armazenamento local|Aumentar apenas<sup>2</sup>|Sim|Sim|
|Adicionar ou remover funções em um serviço|Sim|Sim|Sim|
|Número de instâncias de uma função específica|Sim|Sim|Sim|
|Tipo de número ou de pontos de extremidade de um serviço|Sim<sup>2</sup>|Não|Sim|
|Nomes e valores de configurações|Sim|Sim|Sim|
|Valores (mas não nomes) definições de configuração|Sim|Sim|Sim|
|Adicionar novos certificados|Sim|Sim|Sim|
|Alterar certificados existentes|Sim|Sim|Sim|
|Implantar o novo código|Sim|Sim|Sim|
<sup>1</sup> Alterar tamanho limitado ao subconjunto de tamanhos disponíveis para o serviço de nuvem.

<sup>2</sup> Requer o Azure SDK 1.5 ou versões posteriores.

> [AZURE.WARNING] Alterar o tamanho da máquina virtual será destroy dados locais.


Os itens a seguir não são suportados durante uma atualização:

-   Alterar o nome de uma função. Remova e, em seguida, adicione a função com o novo nome.
-   A alteração da contagem de atualização do domínio.
-   Diminuir o tamanho dos recursos locais.

Se você estiver fazendo outras atualizações a definição do serviço, como reduzir o tamanho do recurso local, você deve executar uma atualização de troca de VIP em vez disso. Para obter mais informações, consulte [Implantação de trocar](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>Como uma atualização prossegue
Você pode decidir se você deseja atualizar todas as funções no seu serviço ou uma única função no serviço. Nos dois casos, todas as instâncias de cada função que está sendo atualizado e pertence o primeiro domínio de atualização são interrompidas, atualizadas e colocadas online. Depois que eles estiverem online novamente, as instâncias no segundo domínio atualização são interrompidas, atualizadas e colocadas online. Um serviço de nuvem pode ter no máximo uma atualização ativa de cada vez. A atualização é sempre executada em relação a versão mais recente do serviço de nuvem.

O diagrama a seguir ilustra como a atualização ocorre se você estiver atualizando todas as funções no serviço:

![Serviço de atualização] (media/cloud-services-update-azure-service/IC345879.png "Serviço de atualização")

Este diagrama próxima ilustra como a atualização ocorre se você estiver atualizando apenas uma única função:

![Função de atualização] (media/cloud-services-update-azure-service/IC345880.png "Função de atualização")  

> [AZURE.NOTE] Ao atualizar um serviço de uma única instância para várias instâncias seu serviço será levado para baixo enquanto a atualização é executada devido aos serviços do Azure atualizações de maneira. A disponibilidade de serviço de guaranteeing do contrato de nível de serviço se aplica somente aos serviços que são implantados com mais de uma instância. A lista a seguir descreve como os dados em cada unidade são afetados por cada cenário de atualização de serviço Azure:
>
>Reinicialização de máquina virtual:
>
-   C: preservados
-   D: preservados
-   E: preservados
>
>Reinicialização portal:
>
-   C: preservados
-   D: preservados
-   E: destruído
>
>Nova portal imagem:
>
-   C: preservados
-   D: destruídos
-   E: destruído

>Atualização in-loco:
>
-   C: preservados
-   D: preservados
-   E: destruído
>
>Migração de nó:
>
-   C: destruído
-   D: destruídos
-   E: destruído

>Observe que, na lista acima, a unidade e: representa a unidade de raiz da função e não deve ser embutida. Em vez disso, use a variável de ambiente % RoleRoot % para representar a unidade.

>Para minimizar o tempo de inatividade durante a atualização de um serviço de ocorrência única, implantar um novo serviço de várias instâncias para o servidor de teste e execute uma troca de VIP.

Durante uma atualização automática, o controlador de tecidos Azure periodicamente avalia a integridade do serviço de nuvem para determinar quando for seguro Oriente a próxima UD. Essa avaliação de integridade é executada em uma base por função e considera apenas instâncias na versão mais recente (ou seja, instâncias de UDs que já tenham sido movimentadas). Ele verifica se um número mínimo de instâncias de função, para cada função, ter alcançado um estado terminal satisfatório.

### <a name="role-instance-start-timeout"></a>Tempo limite de início de instância de função
Controlador de tecidos esperará 30 minutos para cada instância de função alcançar um estado iniciado. Se a duração do tempo limite expirar, o controlador de tecidos continuará indo até a próxima instância de função.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Reversão de uma atualização
Azure fornece flexibilidade no gerenciamento de serviços durante uma atualização, permitindo que você iniciar operações adicionais em um serviço, depois que a solicitação de atualização inicial for aceito pelo controlador de tecidos do Azure. Uma reversão só pode ser executada quando uma atualização (Alterar configuração) ou atualização está no estado **em andamento** na implantação. Uma atualização ou a atualização é considerada em andamento desde que haja pelo menos uma instância do serviço que ainda não foi atualizado para a nova versão. Para testar se uma reversão é permitida, verifique o valor do sinalizador RollbackAllowed, retornado por operações [Obter implantação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Obter propriedades de serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx) , é definido como true.

> [AZURE.NOTE] Somente faz sentido para chamar reversão em uma atualização **in-loco** ou atualizar porque VIP trocar atualizações envolvem substituir uma instância em execução inteira do seu serviço por outra.

Reversão de uma atualização em andamento tem os seguintes efeitos na implantação:

-   Quaisquer instâncias de função que ainda não tinham foram atualizadas ou atualizadas para a nova versão não são atualizadas ou atualizadas, porque essas instâncias já estão executando a versão de destino do serviço.
-   Quaisquer instâncias de função que já tinham foram atualizadas ou atualizadas para a nova versão do pacote de serviço (\*.cspkg) a configuração do serviço ou arquivo (\*.cscfg) arquivo (ou os dois arquivos) são revertidos para a versão anterior à atualização desses arquivos.

Esta funcionalidade é fornecida pelos seguintes recursos:

-   A operação [Reversão atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) , que pode ser chamada em uma atualização de configuração (acionada pela [Configuração de implantação de alteração](https://msdn.microsoft.com/library/azure/ee460809.aspx)de chamada) ou uma atualização (disparadas por chamada [Atualizar implantação](https://msdn.microsoft.com/library/azure/ee460793.aspx)), desde que haja pelo menos uma instância no serviço que ainda não foi atualizado para a nova versão.
-   O elemento de bloqueado e o elemento de RollbackAllowed, que são retornados como parte do corpo da resposta das operações de [Obter implantação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Obter propriedades de serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  O elemento de bloqueado permite detectar quando uma operação de mutação pode ser chamada em uma determinada implantação.
    2.  O elemento RollbackAllowed permite detectar quando a operação de [Reversão atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) pode ser chamada em uma determinada implantação.

    Para realizar uma reversão, você não precisa verificar o bloqueado e os elementos de RollbackAllowed. Ele é suficiente para confirmar se RollbackAllowed está definido como true. Esses elementos são retornados somente se esses métodos são chamados usando o cabeçalho de solicitação definido como "x-ms-version: 2011-10-01" ou uma versão posterior. Para obter mais informações sobre cabeçalhos de controle de versão, consulte [o controle de versão do serviço de gerenciamento](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Há algumas situações onde uma reversão de uma atualização ou atualização não é suportada, estas são da seguinte maneira:

-   Redução de recursos locais - se a atualização aumenta os recursos locais para uma função a plataforma Windows Azure não permite revertendo. 
-   Limitações de cota - se a atualização foi uma escala para baixo de operação, você pode não ter cota de computação suficientes para concluir a operação de reversão. Cada assinatura Azure tem uma cota associada a ele que especifica o número máximo de cores que pode ser consumida por todos os serviços hospedados que pertencem a essa assinatura. Se executar uma reversão de uma atualização de determinado seria colocar sua assinatura sobre cota depois que uma reversão não será habilitada.
-   Condição de corrida - se a atualização inicial foi concluída, uma reversão não é possível.

Um exemplo de quando a reversão de uma atualização pode ser útil é se você estiver usando a operação de [Implantação de atualização](https://msdn.microsoft.com/library/azure/ee460793.aspx) no modo manual para controlar a taxa em que uma atualização in-loco importante para seu Azure hospedado serviço é implementada.

Durante a distribuição da atualização você chamar [Implantação de atualização](https://msdn.microsoft.com/library/azure/ee460793.aspx) no modo manual e começa a orientá-atualização de domínios. Se em algum momento, como você monitorar a atualização, você observe que algumas instâncias de função na primeira domínios atualização que você examinar tornaram não responde, você pode chamar a operação de [Reversão atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) na implantação, que deixará inalterada as instâncias que tinham ainda não foram atualizadas e reversão que tinham foi atualizado para o pacote de serviço anterior e a configuração.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciando várias operações mutação implantações em andamento
Em alguns casos, convém iniciar várias operações de mutação simultâneas implantações em andamento. Por exemplo, você pode executar uma atualização de serviço e, enquanto que a atualização está sendo implementada em seu serviço, você deseja fazer algumas alterações, por exemplo, para implementar a atualização novamente, aplique uma atualização diferente, ou até mesmo excluir a implantação. Um caso em que isso pode ser necessário é se uma atualização do serviço contém código com bugs que faz com que uma instância de função atualizado repetidamente falhar. Nesse caso, o controlador de tecidos Azure não poderão progredir na aplicação que atualizar porque um número insuficiente de instâncias no domínio atualizado é íntegro. Esse estado é conhecido como um *preso implantação*. Você pode soltá implantação Revertendo a atualização ou aplicando uma atualização clara sobre topo da falha de um.

Depois que a solicitação inicial para atualizar ou atualizar o serviço foi recebida pelo controlador de tecidos do Azure, você pode iniciar operações mutação subsequentes. Isto é, você não tem de esperar a operação inicial seja concluída antes de iniciar a operação de mutação outra.

Iniciando uma operação de atualização segunda enquanto a primeira atualização estiver em andamento executará semelhante a operação de reversão. Se a segunda atualização está no modo automático, o primeiro domínio de atualização será atualizado imediatamente, possivelmente levam a instâncias de vários domínios de atualização sendo offline no mesmo ponto no tempo.

As operações de mutação são da seguinte maneira: [Alterar a configuração de implantação](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Implantação de atualização](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Status de implantação de atualização](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Excluir implantação](https://msdn.microsoft.com/library/azure/ee460815.aspx)e [Reversão atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Duas operações, [Obtenha implantação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Obter propriedades de serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx), retornam o sinalizador de bloqueado que pode ser examinado para determinar se uma operação de mutação pode ser chamada em uma determinada implantação.

Para chamar a versão desses métodos que retorna o sinalizador bloqueado, você deve definir o cabeçalho de solicitação para "x-ms-version: 2011-10-01" ou uma versão posterior. Para obter mais informações sobre cabeçalhos de controle de versão, consulte [o controle de versão do serviço de gerenciamento](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição de funções em domínios de atualização
Azure distribui instâncias de uma função uniformemente entre um número definido de domínios de atualização, que pode ser configurado como parte do arquivo de definição (.csdef) do serviço. O número máximo de domínios atualização é 20 e o padrão é 5. Para obter mais informações sobre como modificar o arquivo de definição de serviço, consulte o [Esquema de definição de serviço do Azure (.csdef arquivo)](cloud-services-model-and-package.md#csdef).

Por exemplo, se sua função tiver dez instâncias, por padrão cada domínio de atualização contém duas instâncias. Se sua função tiver 14 instâncias, em seguida, quatro dos domínios atualização contêm três instâncias e um domínio quinto contém dois.

Atualização domínios são identificados com um índice baseado em zero: o primeiro domínio de atualização tem uma identificação de 0, e o segundo domínio de atualização tem uma identificação de 1 e assim por diante.

O diagrama a seguir ilustra como um serviço que contém duas funções são distribuídos quando o serviço define dois domínios de atualização. O serviço está em execução oito instâncias da função da web e nove da função de trabalho.

![Distribuição de domínios de atualização] (media/cloud-services-update-azure-service/IC345533.png "Distribuição de domínios de atualização")

> [AZURE.NOTE] Observe que o Azure controla como instâncias são alocadas em domínios de atualização. Não é possível especificar quais instâncias estão alocadas para qual domínio.

## <a name="next-steps"></a>Próximas etapas
[Como gerenciar serviços de nuvem](cloud-services-how-to-manage.md)  
[Como monitorar os serviços de nuvem](cloud-services-how-to-monitor.md)  
[Como configurar os serviços de nuvem](cloud-services-how-to-configure.md)  
