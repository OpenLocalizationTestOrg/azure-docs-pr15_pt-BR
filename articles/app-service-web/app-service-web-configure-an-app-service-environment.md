<properties
    pageTitle="Como configurar um ambiente de serviço de aplicativo | Microsoft Azure"
    description="Configuração, gerenciamento e monitoramento de ambientes de serviço de aplicativo"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>


# <a name="configuring-an-app-service-environment"></a>Configurando um ambiente de serviço de aplicativo #

## <a name="overview"></a>Visão geral ##

Em um alto nível, um ambiente de serviço de aplicativo do Azure consiste em vários componentes principais:

- Recursos de computação que estejam em execução no ambiente de serviço de aplicativo de serviço hospedado
- Armazenamento
- Um banco de dados
- Uma rede Virtual Classic(V1) ou recurso Manager(V2) Azure (VNet) 
- Uma sub-rede com o serviço do ambiente de serviço de aplicativo hospedado em execução

### <a name="compute-resources"></a>Recursos de computação

Use os recursos de computação para o pool de recursos de quatro.  Cada ambiente de serviço de aplicativo (ASE) tem um conjunto de front-ends e três pools de trabalhador possíveis. Você não precisa usar todos os pools de trabalho de três - se desejar, você pode simplesmente usar um ou dois.

Os hosts pools de recursos (front-ends e trabalhadores) não estão diretamente acessíveis para locatários. Você não pode usar o protocolo de área de trabalho remota (RDP) para conectá-los, altere o provisionamento ou agir como um administrador neles.

Você pode definir o tamanho e a quantidade de pool de recursos. Em um ASE, você tem quatro opções de tamanho, que são rotuladas P1 através de P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte [preços de serviço de aplicativo](../app-service/app-service-value-prop-what-is.md).
Alterar o tamanho ou a quantidade é chamado de uma operação de escala.  Operação de apenas uma escala pode estar em andamento por vez.

**Front-ends**: front-ends são os pontos de extremidade HTTP/HTTPS para os aplicativos que são mantidos no seu ASE. Você não executar cargas de trabalho nos front-ends.

- Um ASE começa com dois P2s, que é suficiente para cargas de trabalho de desenvolvimento/teste e cargas de trabalho de produção raio. É altamente recomendável P3s para moderados cargas de trabalho de produção pesado.
- Para moderada cargas de trabalho de produção pesado, recomendamos que você tenha pelo menos quatro P3s para garantir que há suficientes front-ends executar quando ocorre de manutenção agendada. Atividades de manutenção agendada levará para baixo de front-um end por vez. Isso reduz geral capacidade de front-end disponível durante as atividades de manutenção.
- Instantaneamente, você não pode adicionar uma nova instância de front-end. Elas podem tomar entre 2 a 3 horas para provisionar.
- Para ajustar mais de escala, você deve monitorar a porcentagem de CPU, porcentagem de memória e métricas de solicitações ativas para o pool de front-end. Se as porcentagens de CPU ou memória acima de 70% durante a execução de P3s, adicione mais front-ends. Se o valor de solicitações ativas médias check-out a 15.000 a 20.000 solicitações por front-end, você também deve adicionar mais front-ends. O objetivo geral é manter 70% de CPU e memória porcentagens abaixo e solicitações ativas média check-out para abaixo de 15.000 solicitações por frente encerrar quando você estiver executando P3s.  

**Trabalhadores**: os operadores estiverem onde seus aplicativos realmente executam. Quando você dimensionar seus planos de serviço de aplicativo, que usa o trabalhadores do pool de trabalho associado.

- Você não pode adicionar instantaneamente trabalhadores. Ela pode levar de 2 a 3 horas para provisionar, independentemente de quantos estão sendo adicionados.
- Dimensionar o tamanho de um recurso de computação para qualquer pool levará 2 a 3 horas por domínio de atualização. Há 20 domínios de atualização um ASE. Se você escala o tamanho de computação de um pool de trabalho com 10 instâncias, pode levar entre 20 a 30 horas para ser concluída.
- Se você alterar o tamanho dos recursos de computação que são usados em um pool de trabalho, você fará com que inicia fria dos aplicativos que estejam em execução no pool trabalhador.

A maneira mais rápida para alterar o tamanho de recursos de computação de um pool de trabalho que não está sendo executado qualquer aplicativos é:

- Dimensione para baixo a contagem de instância 0. Levará cerca de 30 minutos para desalocar suas instâncias.
- Selecione o novo tamanho de computação e o número de instâncias. A partir daqui, levará entre 2 a 3 horas para ser concluída.

Se os seus aplicativos exigem um tamanho maior de recursos de computação, você não pode aproveitar as orientações anterior. Em vez de alterar o tamanho do pool de trabalho que hospeda esses aplicativos, você pode preencher outro pool de trabalho com trabalhadores do tamanho desejado e mova os aplicativos sobre ao pool.

- Crie instâncias adicionais do tamanho computação necessários em outro pool de trabalho. Isso levará de 2 a 3 horas para ser concluída.
- Reatribua seus planos de serviço de aplicativo que hospedam os aplicativos que precisam de um tamanho maior para o pool de trabalho configurado recentemente. Esta é uma operação rápida que deve ter menos de um minuto para ser concluída.  
- Dimensione para baixo do primeiro pool de trabalho se não precisar mais essas instâncias não utilizadas. Esta operação leva cerca de 30 minutos para ser concluída.

**AutoScaling**: uma das ferramentas que podem ajudar você a gerenciar seu consumo de recursos de computação é autoscaling. Você pode usar autoscaling para front-end ou pools de trabalho. Você pode fazer coisas como aumentar suas instâncias de qualquer tipo de pool de manhã e reduzi-lo durante a noite. Ou talvez você pode adicionar instâncias quando o número de trabalhadores que estão disponíveis em um pool de trabalhador cai abaixo de um determinado limite.

Se você quiser definir regras de escala automática ao redor de métricas de pool de recursos de computação, em seguida, tenha em mente o tempo que necessita de provisionamento. Para obter mais detalhes sobre autoscaling ambientes de serviço de aplicativo, consulte [como configurar escala automática em um ambiente de serviço de aplicativo][ASEAutoscale].

### <a name="storage"></a>Armazenamento

Cada ASE está configurado com 500 GB de armazenamento. Este espaço é usado em todos os aplicativos do ASE. Este espaço de armazenamento é uma parte do ASE e atualmente não pode ser trocado para usar seu espaço de armazenamento. Se você estiver fazendo ajustes à sua rede virtual roteamento ou segurança, você precisa ainda permitir acesso ao armazenamento do Azure – ou o ASE não funcionará.

### <a name="database"></a>Banco de dados

O banco de dados contém as informações que define o ambiente, bem como os detalhes sobre os aplicativos que estejam em execução dentro dela. Isso também é uma parte da assinatura do Azure mantidas. Não é algo que você tem uma capacidade direta para manipular. Se você estiver fazendo ajustes à sua rede virtual roteamento ou segurança, você precisa ainda permitir acesso ao SQL Azure – ou o ASE não funcionará.

### <a name="network"></a>Rede

O VNet que é usado com seu ASE pode ser um que você fez quando você criou o ASE ou um que você fez antecedência. Quando você cria a sub-rede durante a criação de ASE, ele força do ASE estejam no mesmo grupo de recursos como a rede virtual. Se precisar de grupo de recursos usado pelo seu ASE seja diferente da sua VNet, em seguida, você precisa criar sua ASE usando um modelo do Gerenciador de recursos.

Há algumas restrições sobre a rede virtual que é usado para um ASE:
 
- A rede virtual deve ser uma rede virtual regional.
- Deve haver uma sub-rede com 8 ou mais endereços onde o ASE é implantado.
- Após uma sub-rede é usada para hospedar um ASE, o intervalo de endereços da sub-rede não pode ser alterado. Por esse motivo, é recomendável que a sub-rede contém pelo menos 64 endereços para acomodar qualquer futuro crescimento ASE.
- Pode haver mais em sub-rede mas do ASE nada.

Ao contrário do serviço hospedado que contém o ASE, a [rede virtual] [ virtualnetwork] e sub-rede estão sob o controle de usuário.  Você pode administrar sua rede virtual por meio da interface de usuário de rede Virtual ou PowerShell.  Um ASE pode ser implantado em um clássico ou VNet do Gerenciador de recursos.  O portal e experiências de API são ligeiramente diferentes entre clássico e VNets Gerenciador de recursos, mas a experiência de ASE é a mesma.

O que é usado para hospedar um ASE VNet pode usar qualquer um dos endereços de IP de RFC1918 particular ou pode usar endereços IP públicos.  Se desejar usar um intervalo de IP que não é coberto pelo RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) e em seguida, você precisa criar seu VNet e sub-rede a ser usado por sua ASE com antecedência criação ASE.

Porque esse recurso coloca o serviço de aplicativo do Azure em sua rede virtual, significa que os aplicativos que são hospedados em seu ASE agora podem acessar recursos que são disponibilizados pelo rota expressa ou -to-site redes virtuais privadas (VPNs) diretamente. Os aplicativos em seu ambiente de serviço de aplicativo não exigem recursos de rede adicionais para acessar recursos disponíveis para a rede virtual que hospeda seu ambiente de serviço de aplicativo. Isso significa que você não precisa usar integração de VNET ou conexões de híbrido para acessar recursos em ou conectado à sua rede virtual. Você ainda pode usar ambos esses recursos embora para acessar recursos em redes que não estão conectados à sua rede virtual.

Por exemplo, você pode usar a integração de VNET a integração com uma rede virtual que está em sua assinatura, mas não está conectada à rede virtual que seu ASE está em. Você ainda também pode usar conexões de híbrido para acessar os recursos que estão em outras redes, assim como faria normalmente.  

Se você tiver sua rede virtual configurado com uma VPN ExpressRoute, você deve estar ciente de algumas das necessidades de roteamento que tem um ASE. Há algumas configurações de rota definidos pelo usuário (UDR) que são incompatíveis com um ASE. Para obter mais detalhes sobre como executar uma ASE em uma rede virtual com rota expressa, consulte [executando um ambiente de serviço de aplicativo em uma rede virtual com rota expressa][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Protegendo o tráfego de entrada

Há dois métodos principais para controlar o tráfego de entrada para seu ASE.  Você pode usar Groups(NSGs) de segurança de rede para controlar quais IP endereços podem acessar seu ASE conforme descrito aqui [como controlar o tráfego de entrada em um ambiente de serviço de aplicativo](app-service-app-service-environment-control-inbound-traffic.md) e você também pode configurar seu ASE com um Balancer(ILB) de carga interno.  Esses recursos também podem ser usados juntos se você quiser restringir o acesso usando NSGs para seu ASE ILB.

Quando você cria um ASE, ele criará um VIP em sua VNet.  Há dois tipos de VIP, internos e externos.  Quando você cria um ASE com um VIP externo seus aplicativos em seu ASE poderá ser acessados por meio de um endereço IP roteável na internet. Quando você seleciona interno seu ASE será configurado com um ILB e não será diretamente acessível pela internet.  Um ASE ILB ainda requer um VIP externo, mas ele é usado somente para acesso de gerenciamento e manutenção Azure.  

Durante a criação de ILB ASE você fornece o subdomínio usado do ASE ILB e será necessário gerenciar o seu próprio DNS para o subdomínio que você especificar.  Porque você definir o nome de subdomínio, você também precisa gerenciar o certificado usado para acesso HTTPS.  Após a criação de ASE você for solicitado para fornecer o certificado.  Para saber mais sobre como criar e usar um ASE ILB lidos [usando um balanceador de carga interno com um ambiente de serviço de aplicativo][ILBASE]. 


## <a name="portal"></a>Portal

Você pode gerenciar e monitorar o seu ambiente de serviço de aplicativo usando a interface do usuário no portal do Azure. Se você tiver um ASE, são provavelmente ver o símbolo de serviço de aplicativo na sua barra lateral. Este símbolo é usado para representar ambientes de serviço de aplicativo no portal do Azure:

![Símbolo de ambiente de serviço de aplicativo][1]

Para abrir a interface do usuário que lista todos os seus ambientes de serviço de aplicativo, você pode usar o ícone ou selecione a divisa (">" símbolo) na parte inferior da barra lateral para selecionar ambientes de serviço de aplicativo. Selecionando um dos ASEs listados, você deve abrir a interface do usuário que é usado para monitorar e gerenciá-lo.

![Interface do usuário para monitorar e gerenciar seu ambiente de serviço de aplicativo][2]

Esta primeira blade mostra algumas propriedades do seu ASE, juntamente com um gráfico de métrica por pool de recursos. Algumas das propriedades que são mostradas no bloco **Essentials** também são hiperlinks que abrirá a lâmina que está associada ele. Por exemplo, você pode selecionar o nome de **Rede Virtual** para abrir a interface de usuário associado com a rede virtual que seu ASE é executado em. **Planos de serviço de aplicativo** e **aplicativos** abrem blades que esses itens que estão no seu ASE na lista.  

### <a name="monitoring"></a>Monitoramento

Os gráficos permitem que você veja uma variedade de métricas de desempenho em cada pool de recursos. Para o pool de front-end, você pode monitorar a média CPU e memória. Para pools de trabalho, você pode monitorar a quantidade que é usada e a quantidade que está disponível.

Serviço de aplicativo vários planos podem fazer uso dos funcionários em um pool de trabalho. A carga de trabalho não é distribuída da mesma maneira como com os servidores de front-end, então o uso de CPU e memória não fornecem muito a respeito das informações úteis. É mais importante controlar quantas trabalhadores que você usou e estão disponíveis, especialmente se você estiver gerenciando este sistema para outras pessoas usarem.  

Você também pode usar todas as métricas que podem ser controladas nos gráficos configurar alertas. Configurar alertas aqui funciona da mesma maneira como em outro lugar no aplicativo de serviço. Você pode definir um alerta da parte de interface do usuário de **alertas** ou de fazer buscas detalhadas em qualquer métricas de interface do usuário e selecionando **Adicionar alerta**.

![Métricas de interface do usuário][3]

As métricas apenas discutidos são as métricas de ambiente de aplicativo de serviço. Também existem métricas que estão disponíveis no nível de plano de serviço de aplicativo. Isso é onde o monitoramento de CPU e memória faz muito sentido.

Em um ASE, todos os planos do serviço de aplicativo são dedicados planos de serviço de aplicativo. Isso significa que apenas aplicativos que estejam em execução nos hosts alocados para que o plano de serviço de aplicativo estão os aplicativos nesse plano de serviço de aplicativo. Para ver detalhes em seu plano de serviço de aplicativo, abra seu plano de serviço de aplicativo a partir de qualquer uma das listas a IU ASE ou **planos de serviço de aplicativo procurar** (que lista todos eles).   

### <a name="settings"></a>Configurações

Dentro de lâmina ASE, há uma seção de **configurações** que contém vários recursos importantes:

**Configurações de** > **Propriedades**: A lâmina de **configurações** é aberto automaticamente quando você exibir seu blade ASE. Na parte superior é **Propriedades**. Há um número de itens que são redundantes ao que você vê no **Essentials**aqui, mas o que é muito útil é o **Endereço IP Virtual**, bem como **Endereços de IP de saída**.

![Blade de configurações e propriedades][4]

**Configurações de** > **Endereços IP**: quando você cria um aplicativo de IP Secure Sockets Layer (SSL) no seu ASE, você precisa de um endereço de IP SSL. Para obter um, seu ASE necessidades endereços de IP SSL que ele possui que podem ser alocados. Quando um ASE é criado, ele tem um endereço de IP SSL para essa finalidade, mas você pode adicionar mais. Há um encargo para endereços adicionais IP SSL, conforme mostrado no [Serviço de aplicativo preços] [ AppServicePricing] (na seção em conexões SSL). O preço adicional é o preço de IP SSL.

**Configurações de** > **Front-Ends** / **Pools de trabalhador**: cada uma dessas lâminas de pool de recursos oferece a capacidade de ver informações apenas em pool de recursos, além de fornecer controles para dimensionar totalmente desse pool de recursos.  

A lâmina base para cada pool de recursos fornece um gráfico com métricas para esse pool de recursos. Assim como com os gráficos da lâmina ASE, você pode ir para o gráfico e configure alertas conforme desejado. Definir um alerta da lâmina ASE para um pool de recursos específicos faz a mesma coisa que executá-la do pool de recursos. Da lâmina de **configurações** do pool do operador, você tem acesso a todos os aplicativos ou planos de serviço de aplicativo que estejam em execução neste pool de trabalho.

![Configurações de pool de trabalho da interface do usuário][5]

### <a name="portal-scale-capabilities"></a>Recursos de escala de portal  

Há três operações de escala:

- Alterando o número de endereços IP do ASE que estão disponíveis para uso de IP SSL.
- Alterar o tamanho do recurso da computação que é usado em um pool de recursos.
- Alterando o número de recursos de computação que são usados em um pool de recursos, manualmente ou através de autoscaling.

No portal do, há três maneiras de controlar quantos servidores que você tem no seu pool de recursos:

- Uma operação de escala da lâmina ASE principal na parte superior. Você pode fazer alterações de configuração de escala de vários para os pools de front-end e trabalhador. Eles são aplicados como uma única operação.
- Uma operação de escala manual da lâmina de **escala** do pool do recurso individual, que é em **configurações**.
- Auto-dimensionamento, configurado da lâmina de **escala** de pool do recurso individual.

Para usar a operação de escala na lâmina ASE, arraste o controle deslizante para a quantidade desejada e salvar. Esta UI também suporta alterando o tamanho.  

![Escala de interface do usuário][6]

Para usar os recursos de manual ou escala automática em um pool de recursos específicos, vá para **configurações** > **Front-Ends** / **Trabalhador Pools** conforme apropriado. Abra o pool que você deseja alterar. Vá para **configurações** > **escala** ou **configurações** > **Dimensionar**. A lâmina **Escala Out** permite controlar a quantidade de instância. **Escala para cima** permite controlar o tamanho do recurso.  

![Configurações de escala de interface do usuário][7]

## <a name="fault-tolerance-considerations"></a>Considerações de tolerância

Você pode configurar um ambiente de serviço de aplicativo para usar recursos de computação total até 55. Esses 55 de recursos de computação, 50 só pode ser usado para cargas de trabalho de host. O motivo para isso é dupla. Há um mínimo de 2 recursos de computação front-end.  Isso deixa até 53 para dar suporte a alocação de pool de trabalho. Para fornecer tolerância, você precisa ter um recurso de computação adicionais que está alocado de acordo com as seguintes regras:

- Cada pool de trabalho precisa de pelo menos 1 recursos de computação adicionais que não está disponível para ser atribuído a uma carga de trabalho.
- Quando a quantidade de recursos de computação em um pool de trabalhador fica acima um determinado valor, em seguida, outro recurso de computação é necessário para tolerância. Isso não é o caso no pool de front-end.

Em qualquer pool de único trabalhador, os requisitos de tolerância que são para um determinado valor de X recursos atribuídos a um grupo de trabalho:

- Se X for entre 2 e 20, a quantidade de recursos de computação úteis que você pode usar para cargas de trabalho é X-1.
- Se X for entre 21 e 40, a quantidade de recursos de computação úteis que você pode usar para cargas de trabalho é X-2.
- Se X estiver entre 41 e 53, a quantidade de recursos de computação úteis que você pode usar para cargas de trabalho é X-3.

Os requisitos mínimos tem 2 servidores front-end e 2 trabalhadores.  Com as instruções acima, aqui estão alguns exemplos para esclarecer:  

- Se você tiver 30 trabalhadores em um único pool, 28 delas pode ser usado para cargas de trabalho de host.
- Se você tiver 2 trabalhadores em um único pool, 1 pode ser usado para cargas de trabalho de host.
- Se você tiver 20 trabalhadores em um único pool, 19 pode ser usado para cargas de trabalho de host.  
- Se você tiver 21 trabalhadores em um único pool, ainda, em seguida, 19 somente pode ser usado para cargas de trabalho de host.  

O aspecto de tolerância é importante, mas você precisa mantê-la em mente ao dimensionar acima certos limites. Se você quiser adicionar mais capacidade de 20 instâncias, vá para 22 ou superior porque 21 não adicionar qualquer mais capacidade. O mesmo é verdadeiro indo acima 40, onde o próximo número que adiciona capacidade é 42.  

## <a name="deleting-an-app-service-environment"></a>Excluindo um ambiente de serviço de aplicativo ##

Se você deseja excluir um ambiente de serviço de aplicativo, use simplesmente a ação **Excluir** na parte superior da lâmina ambiente de aplicativo de serviço. Quando você fizer isso, você será solicitado a inserir o nome do seu ambiente de serviço de aplicativo para confirmar que você realmente deseja fazer isso. Observe que quando você exclui um ambiente de serviço de aplicativo, você exclui todo o conteúdo dentro dele também.  

![Excluir um ambiente de serviço de aplicativo de interface do usuário][9]  

## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o aplicativo de serviço ambientes, consulte [como criar um ambiente de serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
