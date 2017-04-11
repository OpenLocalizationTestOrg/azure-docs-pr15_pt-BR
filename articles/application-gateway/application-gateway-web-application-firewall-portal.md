<properties
   pageTitle="Criar um gateway de aplicativo com o firewall do aplicativo web usando o portal | Microsoft Azure"
   description="Aprenda a criar um Gateway de aplicativo com o firewall do aplicativo web usando o portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Criar um gateway de aplicativo com o firewall do aplicativo web usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-web-application-firewall-powershell.md)

O firewall do aplicativo da web (WAF) no Azure Application Gateway protege aplicativos web contra ataques comuns baseado na web, como a inclusão de SQL, ataques de script entre sites e sequestros de sessão. Aplicativo Web protege contra muitas das OWASP superiores 10 comuns da web vulnerabilidades.

Azure Application Gateway é um balanceador de carga de camada-7. Ele oferece failover, roteamento de desempenho solicitações HTTP entre diferentes servidores, independentemente de estarem na nuvem ou local.
Aplicativo fornece vários recursos de controlador de entrega de aplicativo (ADC), incluindo balanceamento de carga de HTTP afinidade de sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, sondagens de integridade personalizados, suporte para vários locais e várias outras.
Para localizar uma lista completa de recursos com suporte, visite [Visão geral de Gateway do aplicativo](application-gateway-introduction.md)

## <a name="scenarios"></a>Cenários

Neste artigo, há dois cenários:

No primeiro cenário, você saiba como [Adicionar firewall do aplicativo da web para um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway).

O segundo cenário, você vai aprender a [criar um gateway de aplicativo com o firewall do aplicativo web](#create-an-application-gateway-with-web-application-firewall)

![Exemplo de cenário][scenario]

>[AZURE.NOTE] Testes adicionais de configuração do gateway aplicativo, incluindo integridade personalizado, endereços de pool de back-end e regras adicionais são configurados depois que o gateway de aplicativo está configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que você deixe bastante espaço de endereço para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, gateways de aplicativo somente adicionais são capazes de ser adicionado à sub-rede.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar o firewall do aplicativo da web a um gateway de aplicativo existente

Este cenário atualiza um gateway de aplicativo existente para dar suporte ao firewall do aplicativo da web no modo de prevenção.

### <a name="step-1"></a>Etapa 1

Navegue até o portal Azure, selecione um Gateway de aplicativo existente.

![Criação de Gateway do aplicativo][1]

### <a name="step-2"></a>Etapa 2

Clique em **configuração** e atualizar as configurações de gateway do aplicativo. Ao concluir clique em **Salvar**

As configurações para atualizar um gateway de aplicativo existente para dar suporte ao firewall de aplicativo web são:

- **Nível** - o nível selecionado deve ser **WAF** para oferecer suporte a firewall do aplicativo web
- **Tamanho SKU** - esta configuração é o tamanho do gateway aplicativo com firewall do aplicativo da web, opções disponíveis são (**médios** e **grandes**).
- **Status do firewall** - esta configuração desabilita ou habilita o firewall do aplicativo web.
- **Modo de firewall** - esta configuração é como o firewall do aplicativo web lida com tráfego mal-intencionado. Modo de **detecção** somente logs de eventos, onde o modo de **prevenção** registra os eventos e interrompe o tráfego mal-intencionado.

![configurações básicas de mostrando de lâmina][2]

>[AZURE.NOTE] Para exibir logs de firewall do aplicativo da web, diagnóstico deve ser habilitado e ApplicationGatewayFirewallLog selecionado. Uma contagem de instância de 1 pode ser escolhida para fins de teste. É importante saber que qualquer contagem de instância em duas instâncias não é coberto pelo SLA e, portanto, não recomendável. Pequenos gateways não estão disponíveis ao usar o firewall do aplicativo web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Criar um gateway de aplicativo com o firewall do aplicativo web

Este cenário irá:

- Crie um gateway de aplicativo de firewall de aplicativo web médio com duas instâncias.
- Crie uma rede virtual chamada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
- Crie uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configure um certificado para descarregamento SSL.

### <a name="step-1"></a>Etapa 1

Navegue até o portal Azure, clique em **novo** > **rede** > **Application Gateway**

![Criação de Gateway do aplicativo][1-1]

### <a name="step-2"></a>Etapa 2

Em seguida, preencha as informações básicas sobre o gateway do aplicativo. Certifique-se de escolher **WAF** como a camada. Ao concluir, clique em **Okey**

As informações necessárias para as configurações básicas são:

- **Nome** - o nome para o gateway de aplicativo.
- **Nível** - o nível do gateway aplicativo, opções disponíveis são (**padrão** e **WAF**). Firewall de aplicativo Web só está disponível na camada de WAF.
- **Tamanho SKU** - esta configuração é o tamanho do gateway aplicativo, opções disponíveis são (**médios** e **grandes**).
- **Contagem de instância** - o número de instâncias, esse valor deve ser um número entre **2** e **10**.
- **Grupo de recursos** - grupo de recursos para manter o gateway do aplicativo, pode ser um grupo de recursos existente ou um novo.
- **Local** - a região para o gateway de aplicativo, ele é o mesmo local no grupo de recursos. *O local é importante como a rede virtual e IP público deve estar no mesmo local que o gateway*.

![configurações básicas de mostrando de lâmina][2-2]

>[AZURE.NOTE] Uma contagem de instância de 1 pode ser escolhida para fins de teste. É importante saber que qualquer contagem de instância em duas instâncias não é coberto pelo SLA e, portanto, não recomendável. Pequenos gateways não são suportados para cenários de firewall do aplicativo web.

### <a name="step-3"></a>Etapa 3

Depois de definidas as configurações básicas, a próxima etapa é definir a rede virtual a ser usado. A rede virtual hospeda o aplicativo que o gateway de aplicativo balanceamento de carga em.

Clique em **Escolher uma rede virtual** para configurar a rede virtual.

![Blade mostrando as configurações de gateway do aplicativo][3]

### <a name="step-4"></a>Etapa 4

Na lâmina **Escolher uma rede Virtual** , clique em **Criar novo**

Enquanto não explicado neste cenário, uma rede Virtual existente pôde ser selecionada neste momento.  Se uma rede virtual existente for usada, é importante saber que a rede virtual precisa de um vazio ou uma sub-rede somente gateway de recursos do aplicativo a ser usado.

![Escolha blade de rede virtual][4]

### <a name="step-5"></a>Etapa 5

Preencha as informações de rede na lâmina **Criar rede Virtual** , conforme descrito na descrição do [cenário](#scenario) anterior.

![Criar blade de rede virtual com as informações inseridas][5]

### <a name="step-6"></a>Etapa 6

Depois que a rede virtual é criada, a próxima etapa é definir o front-end IP para o gateway de aplicativo. Neste ponto, a opção é entre um público ou um endereço IP privado para o front-end. A opção depende se o aplicativo é internet opostas ou interno apenas. Neste cenário supõe usando um endereço IP público. Para escolher um endereço IP privado, o botão **particular** pode ser clicado. Um endereço IP automaticamente atribuído é escolhido ou você pode clicar na caixa de seleção de **Escolher um endereço IP particular específico** para inserir uma manualmente.

Clique em **Escolher um endereço IP público**. Se um endereço IP público existente estiver disponível ela pode ser escolhida neste ponto, neste cenário você criar um novo endereço IP público. Clique em **Criar novo**

![Escolha pública lâmina de endereço IP][6]

### <a name="step-7"></a>Etapa 7

Em seguida, dê um nome amigável para o endereço IP público e clique **Okey**

![Criar blade de endereço IP público][7]

### <a name="step-8"></a>Etapa 8

Em seguida, você pode configurar a configuração de ouvinte.  Se for usado **http** , nada é deixado para configurar e **Okey** pode ser clicado. Para usar **https** será necessária configuração adicional.

Para usar **https**, é necessário um certificado. A chave privada do certificado é necessária para que um. pfx exportar do certificado precisa ser fornecido e a senha para o arquivo.

Clique em **HTTPS**, clique no ícone de **pasta** ao lado da caixa de texto **PFX carregar certificado** .
Navegue até o arquivo. pfx de certificado em seu sistema de arquivos. Quando ela estiver selecionada, dê um nome amigável para o certificado e digite a senha para o arquivo. pfx.

Após concluir clique **Okey** para examinar as configurações para o Gateway de aplicativo.

![Seção de configuração de ouvinte na lâmina de configurações][8]

### <a name="step-9"></a>Etapa 9

Defina as configurações específicas de **WAF** .

- **Status do firewall** - essa configuração ativa ou desativa WAF.
- **Modo de firewall** - essa configuração determina que o WAF ações leva no tráfego mal-intencionado. Se **a detecção** for escolhido, o tráfego só é registrado.  Se **prevenção** for escolhido, o tráfego está conectado e interrompido com um não autorizado 403.

![configurações de firewall do aplicativo Web][9]

### <a name="step-10"></a>Etapa 10

Revise a página Resumo e clique em **Okey**.  Agora o gateway de aplicativo enfileirado e criado.

### <a name="step-12"></a>Etapa 12

Depois que o gateway do aplicativo tiver sido criado, navegue até ele no portal para continuar a configuração do gateway aplicativo.

![Modo de exibição de recursos do aplicativo Gateway][10]

Estas etapas criam um gateway de aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento é bem-sucedido

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar o log de diagnóstico, para registrar os eventos que são detectados ou impedidos com Firewall do aplicativo Web visitando o [Diagnóstico de Gateway do aplicativo](application-gateway-diagnostics.md)

Saiba como criar testes de integridade personalizado visitando [criar um teste de integridade personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar SSL transferindo e levar a descriptografia SSL dispendiosa desativar os servidores web visitando [Configurar SSL descarregar](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png