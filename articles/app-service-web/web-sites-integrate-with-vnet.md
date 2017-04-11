<properties 
    pageTitle="Integrar um aplicativo com uma rede Virtual Azure" 
    description="Mostra como conectar um aplicativo no serviço de aplicativo do Azure a uma rede de virtual Azure nova ou existente" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo com uma rede Virtual Azure #

Este documento descreve o recurso de integração de rede virtual do serviço de aplicativo do Azure e mostra como configurá-lo com os aplicativos em um [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Se você estiver familiarizado com redes virtuais Azure (VNETs), este é um recurso que permite que você coloque muitos dos seus recursos Azure em uma rede routeable não-internet que você controle o acesso ao.  Essas redes, em seguida, podem ser conectadas às suas redes local ativado usando uma variedade de tecnologias VPN.  Para saber mais sobre redes virtuais Azure começar com as informações aqui: [Visão geral da rede Virtual Azure][VNETOverview].  

O serviço de aplicativo do Azure tem duas formas.  

1. Os sistemas de vários locatários que oferecem suporte a gama completa de planos de preço
1. O recurso de premium do ambiente de serviço de aplicativo (ASE) que implanta na sua VNET.  

Este documento percorre integração de VNET e não o ambiente de serviço de aplicativo.  Se você quiser saber mais sobre o recurso de ASE e inicie com as informações aqui: [Introdução do ambiente de serviço de aplicativo][ASEintro].

Integração de VNET fornece o acesso de aplicativo web aos recursos da sua rede virtual, mas não conceder acesso privado a seu aplicativo web da rede virtual.  Acesso a sites particular só está disponível com um ASE configurado com um balanceador de carga interno (ILB).  Para obter detalhes sobre como usar um ASE ILB, comece com o artigo aqui: [Criando e usando um ASE ILB][ILBASE]. 

Um cenário comum onde você usaria VNET integração está ativando o acesso de seu aplicativo web a um banco de dados ou web services em execução em uma máquina virtual em sua rede virtual Azure.  Com a integração de VNET não é necessário expor um ponto de extremidade público para aplicativos em sua máquina virtual, mas usam os endereços de roteáveis particulares não-internet em vez disso.  

O recurso de integração de VNET:

- requer um padrão ou Premium preços plano 
- funcionará com Classic(V1) ou recurso Manager(V2) VNET 
- suporta TCP e UDP
- funciona com aplicativos Web, móveis e API
- Habilita um aplicativo para se conectar à apenas 1 VNET por vez
- Habilita até 5 VNETs a ser integrado em um plano de serviço de aplicativo 
- permite que o mesmo VNET a ser usado por vários aplicativos em um plano de serviço de aplicativo
- suporta um SLA 99,9% devido a uma dependência no Gateway VNET

Há algumas coisas que VNET integração não suporta incluindo:

- montagem de uma unidade
- Integração do AD 
- NetBios
- acesso a sites particulares

### <a name="getting-started"></a>Guia de Introdução ###
Aqui estão algumas coisas a ter em mente antes de conectar seu aplicativo web a uma rede virtual:

- Integração de VNET só funciona com aplicativos em um **padrão** ou **Premium** preços plano.  Se você habilitar o recurso e, em seguida, dimensionar seu plano de serviço de aplicativo de um plano de preços sem suporte seus aplicativos perderá suas conexões para as VNETs que estão usando.  
- Se sua rede virtual de destino já existir, ele deve ter ponto-a-site VPN habilitado com um gateway de roteamento dinâmico antes que ele pode ser conectado a um aplicativo.  Você não pode habilitar ponto-a-site rede Virtual privada (VPN) se seu gateway estiver configurado com roteamento estático.
- O VNET deve estar na mesma assinatura do seu Plan(ASP) de serviço do aplicativo.  
- Os aplicativos que se integram um VNET usará o DNS especificado para que VNET.
- Por padrão somente seus aplicativos integração vai rotear o tráfego para o seu VNET com base em rotas definidos no seu VNET.  


## <a name="enabling-vnet-integration"></a>Habilitar a integração de VNET ##

Este documento é voltado principalmente usando o Portal do Azure para integração de VNET.  Para habilitar a integração de VNET com seu aplicativo usando o PowerShell, siga as instruções aqui: [conectar seu aplicativo à sua rede virtual usando o PowerShell][IntPowershell].

Você tem a opção para conectar seu aplicativo a uma rede virtual nova ou existente.  Se você criar uma nova rede como um componente de integração, em seguida, além de criar apenas o VNET, um gateway de roteamento dinâmico será pré-configurado para você e aponte para VPN Site será ativado.  

>[AZURE.NOTE] Configurar uma nova integração de rede virtual pode demorar vários minutos.  

Para habilitar a integração de VNET abrir seu aplicativo configurações e selecione rede.  A interface do usuário que será aberta oferece três opções de rede.  Este guia só ficará à integração de VNET apesar híbrido conexões e ambientes de serviço de aplicativo é discutido mais adiante neste documento.  

Se seu aplicativo é não no correta preços plano a IU Felizmente permitirá que você dimensionar seu plano para um plano de preços superior de sua escolha.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Habilitar a integração de VNET com um VNET preexistente###
A interface de usuário de integração de VNET permite que você selecione uma lista de suas VNETs.  O VNETs clássico indicará que eles sejam tais com a palavra "Clássico" entre parênteses ao lado do nome VNET.  A lista é classificada que VNETs o Gerenciador de recursos são listados primeiro.  Na imagem abaixo, você pode ver que apenas um VNET pode ser selecionado.  Há vários motivos que um VNET será ser acinzentada incluindo:

- o VNET está em outra assinatura que sua conta tenha acesso ao
- o VNET não tem um ponto ao Site habilitado
- o VNET não tem um gateway de roteamento dinâmico


![][2]

Para habilitar integração basta clicar na VNET que você deseja integrar.  Após selecionar o VNET, o aplicativo será reiniciado automaticamente para que as alterações tenham efeito.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Habilitar o ponto de Site em um VNET clássico #####
Se seu VNET não tem um gateway nem tem ponto ao Site que você precisa configurar que o primeiro.  Para fazer isso para um VNET clássico, acesse o [Portal do Azure] [ AzurePortal] e abra a lista de Networks(classic) Virtual.  Do clique aqui na rede que você deseja integrar e clique na caixa grande em Essentials chamado conexões VPN.  A partir daqui, você pode criar seu ponto para VPN de site e até mesmo tê-lo a criar um gateway.  Após acessar através do ponto site com experiência de criação de gateway será cerca de 30 minutos antes que ele esteja pronto.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Habilitando aponte para o Site no VNET gerente de recursos #####

Para configurar um VNET Gerenciador de recursos com um gateway e aponte para o Site que você precisa usar o PowerShell como documentadas aqui, [Configurar uma conexão ponto-a-Site a uma rede virtual usando o PowerShell][V2VNETP2S].  A interface do usuário para executar esse recurso ainda não está disponível. 

### <a name="creating-a-pre-configured-vnet"></a>Criando um VNET pré-configurado ###
Se você quiser criar um novo VNET que está configurado com um gateway e ponto-a-Site, o serviço de aplicativo de rede da interface do usuário tem a capacidade de fazer isso, mas apenas para um gerente de recursos VNET.  Se você desejar criar um VNET clássico com um gateway e ponto-a-Site, em seguida, você precisa fazer isso manualmente por meio da interface de usuário de rede. 

Para criar um VNET Gerenciador de recursos por meio da UI de integração de VNET, simplesmente selecione **Criar nova rede Virtual** e forneça a:

- Nome de rede virtual
- Bloco de endereço de rede virtual
- Nome de sub-rede
- Bloco de endereço de sub-rede
- Bloco de endereço do gateway
- Bloco de endereço do ponto-a-Site

Se você quiser este VNET para conectar-se a qualquer uma das outra rede você deve evitar espaço de endereço IP que se sobrepõe as redes de separação.  

>[AZURE.NOTE] Criação de VNET Gerenciador de recursos com um gateway leva cerca de 30 minutos e atualmente não estará integrada a VNET com seu aplicativo.  Depois que sua VNET é criado com o gateway que você precisa voltar para seu aplicativo de interface de usuário de integração de VNET e selecione seu novo VNET.

![][3]

VNETs Azure normalmente são criadas em endereços de rede privada.  Por padrão a integração de VNET recurso encaminhará qualquer tráfego destinado esses intervalos de endereços IP para o seu VNET.  Os intervalos de endereços IP particulares são:

- 10.0.0.0/8 - esta é a mesma que 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - esta é a mesma que 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - esta é a mesma que 192.168.0.0 - 192.168.255.255
 
O espaço de endereço VNET precisa ser especificado na notação CIDR.  Se você estiver familiarizado com notação CIDR, é um método para especificar os blocos de endereço usando um endereço IP e um número inteiro que representa a máscara de rede. Como uma referência rápida, considere que 10.1.0.0/24 seria 256 endereços e 10.1.0.0/25 seria 128 endereços.  Um endereço IPv4 com um /32 seria apenas 1 endereço.  

Se você definir as informações do servidor DNS aqui, em seguida, que será definido para o seu VNET.  Após a criação de VNET, você pode editar essas informações com as experiências de usuário VNET.

Quando você cria uma VNET clássica usando a interface de usuário de integração de VNET, ele criará um VNET no mesmo grupo de recursos como seu aplicativo. 

## <a name="how-the-system-works"></a>Como o sistema funciona ##
Nos bastidores esse recurso cria sobre tecnologia de ponto-a-Site VPN para conectar seu aplicativo para seu VNET.   Aplicativos em um serviço de aplicativo do Azure tem uma arquitetura de sistema de vários locatários que impede um aplicativo diretamente em um VNET de provisionamento, como é feito com máquinas virtuais.  Criando na tecnologia de ponto-a-site podemos limitar o acesso de rede apenas na máquina virtual do aplicativo de hospedagem.  Acesso à rede fica mais restrito nesses hosts de aplicativo para que seus aplicativos só podem acessar as redes que você configurá-los para acessar.  

![][4]
 
Se você ainda não configurou um servidor DNS com sua rede virtual, você precisará usar endereços IP.  Ao usar endereços IP, lembre-se de que o principal benefício desse recurso é que ele permite que você use os endereços particulares dentro de sua rede privada.  Se você definir seu aplicativo para usar endereços de IP pública para uma das suas VMs, então você não estiver usando o recurso de integração de VNET e comunicando pela internet.


##<a name="managing-the-vnet-integrations"></a>Gerenciando as integrações VNET##

A capacidade de se conectar e desconectar um VNET está em um nível de aplicativo.  Operações que podem afetar a integração de VNET entre vários aplicativos estão em um nível ASP.  Da interface do usuário do que é mostrado no nível do aplicativo, você pode obter detalhes sobre sua VNET.  A maioria das mesmas informações também é mostrada no nível do ASP.  

![][5]

Na página Status do recurso de rede, você pode ver se o seu aplicativo está conectado à sua VNET.  Se seu gateway VNET for pressionada para qualquer motivo então isso mostra não como conectado.  

As informações que agora você tem disponíveis para você no aplicativo que nível interface de usuário de integração de VNET é a mesma que as informações de detalhe obtido o ASP.  Aqui estão os itens:

- Nome VNET - esse link abre a rede da interface do usuário
- Local - isso reflete a localização da sua VNET.  É possível fazer a integração com um VNET em outro local.
- Status de certificado - há certificados usados para proteger a conexão VPN entre o aplicativo e o VNET.  Isso reflete um teste para garantir que eles estejam em sincronia.
- Status do gateway - seus gateways deveria para baixo por qualquer motivo, em seguida, seu aplicativo não é possível acessar recursos na VNET.  
- Espaço de endereço VNET - este é o espaço de endereço IP para seu VNET.  
- Aponte para o espaço de endereço do Site, este é o ponto de espaço de endereço IP do site para sua VNET.  Seu aplicativo mostrará comunicação como proveniente de um dos IPs neste espaço de endereço.  
- Site para o espaço de endereço do site, você pode usar VPNs Site to Site conectar seu VNET aos recursos no local ou a outros VNETs.  Você deve ter que configurado os intervalos de IP definidos com que a conexão VPN será mostrado aqui.
- Servidores DNS - se você tiver servidores DNS configurados com seu VNET e em seguida, eles são listados aqui.
- IPs roteados para o VNET - lá são uma lista de endereços IP que sua VNET tenha definido para o roteamento.  Esses endereços serão mostrado aqui.  

A única operação que você pode realizar no modo de exibição de aplicativo de integração VNET é desconecte seu aplicativo o VNET que ele está conectado à.  Para fazer isso simplesmente clique em Desconectar na parte superior.  Esta ação não altera sua VNET.  A configuração VNET e de TI, incluindo os gateways permanece inalterada.  Se quiser excluir sua VNET, em seguida, é necessário primeiro excluir os recursos nele incluindo gateways.  

O modo de exibição de plano de serviço de aplicativo tem um número de operações adicionais.  Ele é também acessado diferente que do aplicativo.  Para acessar a interface de usuário de rede ASP simplesmente abrir seu ASP UI e role para baixo.  Não há um elemento de interface do usuário chamado Status de recurso de rede.  Dará alguns detalhes secundárias em torno de sua integração de VNET.  Clicar nesta UI abre a interface de usuário de Status de recurso de rede.  Se você clicar em "Clique aqui para gerenciar" você abrirá UI que lista as integrações VNET neste ASP.

![][6]

O local do ASP é bom lembrar quando olhando para os locais as VNETs integração de com.  Quando o VNET está em outro local, você está muito mais provável ver problemas de latência.  

Os VNETs integrados ao é um lembrete em quantos VNETs seus aplicativos estão integrados com este ASP e quantas você pode ter.  

Para ver detalhes adicionados em cada VNET, basta clicar na VNET que você está interessado.  Além de detalhes que foram observado que você também verá uma lista de aplicativos neste ASP que estejam usando essa VNET.  

Relacionadas com ações, há duas ações principais.  O primeiro é a capacidade de adicionar rotas que direcionam o tráfego deixando seu aplicativo em seu VNET.  A segunda ação é a capacidade de sincronizar certificados e informações de rede.

![][7]

**Roteamento** Conforme mencionado anteriormente as rotas definidos no seu VNET são o que é usado para direcionar o tráfego para o seu VNET de seu aplicativo.  Há alguns usos embora onde os clientes desejam enviar tráfego de saída adicional de um aplicativo para o VNET e para que elas esse recurso é fornecido.  O que acontece com o tráfego depois que é até como o cliente configura sua VNET.  

**Certificados** O Status de certificado reflete uma verificação sendo realizada pelo serviço de aplicativo para validar os certificados que estamos usando para a conexão VPN estão ainda boas.  Quando VNET integração habilitada, em seguida, se esta for a primeira integração para esse VNET de qualquer aplicativos neste ASP, há um exchange necessário de certificados para garantir a segurança da conexão.  Juntamente com os certificados recebemos a configuração de DNS, rotas e outras coisas semelhantes que descrevem a rede.
Se esses certificados ou informações de rede for alterado, em seguida, você precisará clique "Rede de sincronização".  **Observação**: quando você clica em "Rede de sincronização" e em seguida, você fará com que uma breve interrupção na conectividade entre seu aplicativo e seu VNET.  Enquanto seu aplicativo não será reiniciado a perda de conectividade pode causar a seu site não funcione corretamente.  

##<a name="accessing-on-premise-resources"></a>Acessando recursos de local##

Um dos benefícios do recurso de integração de VNET é que, se seu VNET está conectado à sua rede local ativado com uma VPN de Site para Site e seus aplicativos podem ter acesso aos recursos no local de seu aplicativo.  Para este trabalho embora talvez seja necessário atualizar o gateway VPN no local com as rotas para seu ponto de IP do Site variar.  Quando a VPN de Site para Site estiver configurada primeiro os scripts usados para configurá-lo devem configurar rotas incluindo seu ponto para VPN Site.  Se você adicionar o ponto a VPN de Site após sua criar sua VPN de Site para Site e em seguida, você precisará atualizar as rotas manualmente.  Para obter detalhes sobre como fazer isso irá variar por gateway e não são descritos aqui.  

>[AZURE.NOTE] Enquanto o recurso de integração de VNET funcionarão com uma VPN de Site para Site para acessar recursos local ele atualmente não funcionará com uma VPN ExpressRoute para fazer o mesmo.  Isso é verdadeiro ao integrar com um clássico ou o Gerenciador de recursos VNET.  Se você precisar acessar recursos por meio de uma VPN ExpressRoute você pode usar um ASE que pode ser executado no seu VNET. 

##<a name="pricing-details"></a>Detalhes de preços##
Existem algumas nuances que você deve estar atento ao usar o recurso de integração de VNET de preços.  Existem 3 encargos relacionados ao uso desse recurso:

- ASP requisitos de nível de preço
- Custos de transferência de dados
- Custos de Gateway VPN.

Para os aplicativos sejam capazes de usar esse recurso, eles precisam estar em um padrão ou um plano de serviço de aplicativo Premium.  Você pode ver mais detalhes sobre esses custos aqui: [Preços de serviço de aplicativo][ASPricing]. 

Devido a maneira ponto de VPNs Site são tratados, você sempre tenha um custo para dados de saída por meio de sua conexão de integração de VNET mesmo se o VNET estiver no mesmo data center.  Para ver o que esses encargos são dê uma olhada aqui: [Detalhes de preços transferir dados][DataPricing].  

O último item é o custo dos gateways VNET.  Se não precisar gateways algo como VPNs Site ao Site que você está pagando para gateways para suporte ao recurso de integração de VNET.  Há detalhes sobre esses custos aqui: [Preços de Gateway VPN][VNETPricing].  

##<a name="troubleshooting"></a>Solução de problemas##

Enquanto o recurso é fácil de configurar, isso não significa que sua experiência será problema gratuito.  Se você encontrar problemas ao acessar sua empresa desejada lá estão alguns utilitários que você pode usar para testar a conectividade do console do aplicativo.  Há duas experiências de console, que você pode usar.  Uma é do console Kudu e a outra é o console que você pode entrar no Portal do Azure.  Para acessar o console de Kudu de seu aplicativo vá para Ferramentas -> Kudu.  Esta é a mesma que indo para [nome do site]. scm.azurewebsites.net.  Depois que abre simplesmente vá até a guia de console de depuração.  Para acessar o portal console hospedada do Azure e de seu aplicativo vá para Ferramentas -> Console.  


####<a name="tools"></a>Ferramentas####

As ferramentas ping, nslookup e tracert não funcionarão por meio do console devido a restrições de segurança.  Para preencher a lá anular foram duas ferramentas separadas adicionadas.  Para testar a funcionalidade DNS adicionamos uma ferramenta chamada nameresolver.exe.  A sintaxe é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar nameresolver para verificar o nome de host que depende do seu aplicativo.  Assim que você pode testar se você tiver nada mal configurado com seu DNS ou talvez não têm acesso ao seu servidor DNS.

A ferramenta próxima permite testar a conectividade TCP para uma combinação de host e porta.  Esta ferramenta é chamada tcpping.exe e a sintaxe é:

    tcpping.exe hostname [optional: port]

Esta ferramenta informará se você pode entrar em um host específico e porta, mas não executará a mesma tarefa que você obtém com o ICMP base utilitário ping.  O utilitário de ping ICMP informará se seu host é para cima.  Com tcpping você descubra se você pode acessar uma porta específica em um host.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Depuração acessem VNET hospedado recursos####

Há várias coisas que podem impedir a contatar um host específico e a porta do seu aplicativo.  Na maioria das vezes é um dos três coisas:

- **Há um firewall da forma**  Se você tiver um firewall da maneira que você atingirá o tempo limite TCP.  Nesse caso é 21 segundos.  Use a ferramenta de tcpping para testar a conectividade.  Tempos limite TCP pode ser devido a muitas coisas além firewalls mas iniciar lá.  
- **O DNS é não acessível**  O tempo limite DNS é 3 segundos por servidor DNS.  Se você tiver 2 servidores DNS que é 6 segundos.  Use nameresolver para ver se o DNS está funcionando.  Lembre-se de que você não pode usar o nslookup como que não use o DNS seu VNET está configurado com.
- **Intervalo de IP P2S inválido** O ponto de intervalo IP do site precisa estar nos intervalos de IP particulares RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) se o intervalo usa IPs fora que então coisas não funcionarão.  

Se esses itens não atender seu problema, procure primeiro para as coisas simples, como:  

- O Gateway mostra como sendo para cima no Portal?
- Certificados que são mostrados como sendo sincronizados?
- Qualquer pessoa alterar a configuração de rede sem precisar fazer uma "rede de sincronização" na páginas ASP afetado? 

Se o gateway for pressionada colocá-lo fazer backup.  Se seus certificados estão fora de sincronizado, em seguida, vá para o modo de exibição ASP de integração VNET e acertar "Rede de sincronização".  Se você suspeitar de que houve uma alteração feita na sua configuração de VNET e não era sincronização faria com suas páginas de ASP, em seguida, vá para o modo de exibição ASP de integração VNET e acertar Just "Rede de sincronização" como um lembrete, isso fará com que uma breve interrupção com sua conexão de VNET e seus aplicativos.  

Se tudo isso serve você precisa investigar um pouco:

- Existem quaisquer outros aplicativos usando a integração de VNET alcançar recursos na mesma VNET? 
- Você pode ir para o console de aplicativo e usar tcpping alcançar quaisquer outros recursos no seu VNET?  

Se uma das opções acima for verdadeira, em seguida, a integração com seu VNET está corretamente configurada e o problema é para outro local.  Isso é onde ele obtém a ser mais do que um desafio porque não há nenhuma maneira simple de ver por que você não puder alcançar uma host: porta.  Algumas das causas incluem:

- você tiver um firewall para cima em seu host impedindo o acesso à porta de aplicativo do seu ponto para intervalo de IP do site.  Interseção sub-redes geralmente requer acesso público.
- o host de destino está inoperante
- seu aplicativo está inoperante
- você tinha o IP ou hostname errado
- seu aplicativo é listening em uma porta diferente esperado.  Você pode verificar isso indo até que hospedam e usando "netstat - aon" no prompt de cmd.  Isso mostrará a você qual processo ID é listening na qual porta.  
- seus grupos de segurança de rede estão configurados de modo que eles impedem o acesso ao seu aplicativo host e a porta do seu ponto para intervalo de IP do site

Lembre-se de que você não sabe qual IP no seu ponto para intervalo de IP do Site que seu aplicativo usará então você precisa permitir o acesso de todo o intervalo.  

Etapas de depuração adicionais incluem:

- Faça logon em outra máquina virtual no seu VNET e tentar atingir seu host: porta do recurso de lá.  Existem alguns utilitários de ping TCP que você pode usar para essa finalidade ou pode até mesmo usar telnet se precisa ser.  O objetivo aqui é apenas determinar se conectividade existe deste outro máquina virtual. 
- Exibir um aplicativo em outro acesso de máquina virtual e teste nesse host e porta do console de seu aplicativo  
####<a name="on-premise-resources"></a>Recursos de local####
Se seu não pode acessar recursos no local e em seguida, a primeira coisa que você deve verificar é se você pode acessar um recurso em seu VNET.  Se o que está funcionando as próximas etapas são muito fácil.  De uma máquina virtual no seu VNET você precisa tentar acessar o aplicativo de local em.  Você pode usar o telnet ou um utilitário de ping TCP.  Se sua máquina virtual não puder atingir seu recurso local ativado, primeiro verifique se sua conexão de VPN to Site está trabalhando.  Se estiver funcionando, marque as mesmas coisas observadas anteriormente, além de ativar a configuração de gateway local e status.  

Agora se seu VNET hospedado máquina virtual pode acessar seu sistema local ativado mas seu aplicativo não é o motivo será provavelmente um dos seguintes:
- suas rotas não estão configuradas com seu ponto para intervalos IP do site no seu gateway local ativado
- seus grupos de segurança de rede estão bloqueando o acesso para seu ponto de intervalo IP de Site
- seus firewalls local ativado estão bloqueando o tráfego do seu ponto para intervalo de IP do Site
- Você tem uma Route(UDR) de definidas pelo usuário no seu VNET que impede que o seu ponto para o tráfego de Site com base contatem na rede local

## <a name="hybrid-connections-and-app-service-environments"></a>Conexões de híbrido e ambientes de serviço de aplicativo##
Existem 3 recursos que habilitam o acesso aos recursos VNET hospedado.  Eles são:

- Integração de VNET
- Conexões de híbrido
- Ambientes de serviço de aplicativo

Conexões híbrido requer que você instale um agente de retransmissão chamado a Manager(HCM) de Conexão híbrido na sua rede.  HCM precisa ser capaz de se conectar ao Azure e também para seu aplicativo.  Essa solução é especialmente excelente de uma rede remota como na rede local ou até mesmo de outro nuvem hospedado rede porque ela não exige um ponto de extremidade acessíveis do internet.  HCM só é executado no Windows e você pode ter até 5 instâncias em execução para oferecer alta disponibilidade.  Conexões de híbrido só oferece suporte a TCP apesar e cada ponto de extremidade HC deve corresponder a uma combinação de host: porta específica.  

O recurso de ambiente de serviço de aplicativo permite executar uma instância do serviço de aplicativo do Azure em seu VNET.  Isso permite que os recursos de acesso de aplicativos no seu VNET sem qualquer etapas adicionais.  Alguns dos outros benefícios de um ambiente de serviço de aplicativo é que você pode usar 8 trabalhadores de núcleo dedicado com 14 GB de RAM.  Outro benefício é que você pode dimensionar o sistema para atender às suas necessidades.  Diferentemente os ambientes de vários locatários onde o ASP é limitado em tamanho, em um ASE você controla quantos recursos que você deseja dar ao sistema.  Relacionadas com o foco de rede deste documento porém, uma das coisas que você obtém com uma ASE que você não com integração de VNET é que ele pode trabalhar com uma VPN ExpressRoute.  

Embora haja que alguns usam sobreposição maiusculas, nenhum desses recursos pode substituir qualquer um dos outros.  Saber qual recurso usar está vinculado às suas necessidades e como você deseja usá-lo.  Por exemplo:

- Se você for um desenvolvedor e quiser simplesmente executa um site no Azure e acessar o banco de dados na estação de trabalho em sua mesa, em seguida, o mais fácil usar é híbrido conexões.  
- Se você é uma grande organização que deseja colocar um grande número de propriedades da web em público nuvem e gerenciá-los em sua própria rede depois que você deseja acessar com o ambiente de serviço de aplicativo.  
- Se você tiver um número de serviço de aplicativo hospedado aplicativos e simplesmente deseja acessar recursos no seu VNET e integração de VNET é a maneira de ir.  

Além os casos de uso há alguns simplicidade relacionados aspectos.  Se seu VNET já está conectado à sua rede local ativado, em seguida, usando a integração de VNET ou um ambiente de serviço de aplicativo é uma maneira fácil de consumir recursos de local.  Por outro lado, se sua VNET não estiver conectado à sua rede local ativado é muito mais sobrecarga para configurar uma VPN de site para o site com seu VNET comparado com a instalação HCM.  

Além das diferenças funcionais existem preços também diferenças.  O recurso de ambiente de aplicativo de serviço é um Premium oferta de serviço mas oferece mais rede possibilidades de configuração além de outros recursos excelentes.  Integração de VNET pode ser usada com padrão ou páginas ASP Premium e é ideal para consumir com segurança recursos em sua VNET do locatário de vários serviços de aplicativo.  Conexões de híbrido atualmente depende de um BizTalk conta que tem preços níveis que comece livre e, em seguida, decida cada vez mais cara com base na quantidade que você precisa.  Quando se trata de trabalhando em muitas redes apesar, não há nenhum outro recurso como conexões híbrido que pode habilitar a acessar recursos em mais de 100 redes separadas.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
