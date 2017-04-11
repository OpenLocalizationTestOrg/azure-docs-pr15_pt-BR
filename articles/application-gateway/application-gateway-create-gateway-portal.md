<properties
   pageTitle="Criar um gateway de aplicativo usando o portal | Microsoft Azure"
   description="Aprenda a criar um Gateway de aplicativo usando o portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
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

# <a name="create-an-application-gateway-by-using-the-portal"></a>Criar um gateway de aplicativo usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gerenciador de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Azure Application Gateway é um balanceador de carga de camada-7. Ele oferece failover, roteamento de desempenho solicitações HTTP entre diferentes servidores, independentemente de estarem na nuvem ou local. Gateway do aplicativo fornece vários recursos de controlador de entrega de aplicativo (ADC), incluindo balanceamento de carga de HTTP afinidade de sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, sondagens de integridade personalizados, suporte para vários locais e várias outras. Para localizar uma lista completa de recursos com suporte, visite [Visão geral de Gateway do aplicativo](application-gateway-introduction.md)

## <a name="scenario"></a>Cenário

Neste cenário, você saiba como criar um gateway de aplicativo usando o portal do Azure.

Este cenário irá:

- Crie um gateway de aplicativo médio com duas instâncias.
- Crie uma rede virtual chamada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
- Crie uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configure um certificado para descarregamento SSL.

![Exemplo de cenário][scenario]

>[AZURE.IMPORTANT] Testes adicionais de configuração do gateway aplicativo, incluindo integridade personalizado, endereços de pool de back-end e regras adicionais são configurados depois que o gateway de aplicativo está configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que você deixe bastante espaço de endereço para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, gateways de aplicativo somente adicionais são capazes de ser adicionado à sub-rede.

## <a name="create-the-application-gateway"></a>Criar o Gateway de aplicativo

### <a name="step-1"></a>Etapa 1

Navegue até o portal Azure, clique em **novo** > **rede** > **Application Gateway**

![Criação de Gateway do aplicativo][1]

### <a name="step-2"></a>Etapa 2

Em seguida, preencha as informações básicas sobre o gateway do aplicativo. Ao concluir, clique em **Okey**

As informações necessárias para as configurações básicas são:

- **Nome** - o nome para o gateway de aplicativo.
- **Nível** - este é o nível do gateway aplicativo. Dois níveis estão disponíveis, **WAF** e **padrão**. WAF permite que o recurso de firewall do aplicativo web.
- **Tamanho SKU** - esta configuração é o tamanho do gateway aplicativo, opções disponíveis são (**pequeno**, **Médio**e **grande**). *Pequeno não está disponível quando o nível de WAF é escolhido*
- **Contagem de instância** - o número de instâncias, esse valor deve ser um número entre 2 e 10.
- **Grupo de recursos** - grupo de recursos para manter o gateway do aplicativo, pode ser um grupo de recursos existente ou um novo.
- **Local** - a região para o gateway de aplicativo, ele é o mesmo local no grupo de recursos. *O local é importante como a rede virtual e IP público deve estar no mesmo local que o gateway*.

![configurações básicas de mostrando de lâmina][2]

>[AZURE.NOTE] Uma contagem de instância de 1 pode ser escolhida para fins de teste. É importante saber que qualquer contagem de instância em duas instâncias não é coberto pelo SLA e, portanto, não recomendável. Gateways pequenos devem ser usadas para teste de desenvolvimento e não para fins de produção.

### <a name="step-3"></a>Etapa 3

Depois de definidas as configurações básicas, a próxima etapa é definir a rede virtual a ser usado. A rede virtual hospeda o aplicativo que o gateway de aplicativo balanceamento de carga em.

Clique em **Escolher uma rede virtual** para configurar a rede virtual.

![Blade mostrando as configurações de gateway do aplicativo][3]

### <a name="step-4"></a>Etapa 4

Na lâmina *Escolher uma rede Virtual* , clique em **Criar novo**

Enquanto não explicado neste cenário, uma rede Virtual existente pôde ser selecionada neste momento.  Se uma rede virtual existente for usada, é importante saber que a rede virtual precisa de um vazio ou uma sub-rede somente gateway de recursos do aplicativo a ser usado.

![Escolha blade de rede virtual][4]

### <a name="step-5"></a>Etapa 5

Preencha as informações de rede na lâmina **Criar rede Virtual** , conforme descrito na descrição do [cenário](#scenario) anterior.

![Criar blade de rede virtual com as informações inseridas][5]

### <a name="step-6"></a>Etapa 6

Depois que a rede virtual é criada, a próxima etapa é definir o front-end IP para o gateway de aplicativo. Neste ponto, a opção é entre um público ou um endereço IP privado para o front-end. A opção depende se o aplicativo é internet opostas ou interno apenas. Neste cenário supõe usando um endereço IP público. Para escolher um endereço IP privado, o botão **particular** pode ser clicado. Um endereço IP automaticamente atribuído é escolhido ou você pode clicar na caixa de seleção de **Escolher um endereço IP particular específico** para inserir uma manualmente.

### <a name="step-7"></a>Etapa 7

Clique em **Escolher um endereço IP público**. Se um endereço IP público existente estiver disponível ela pode ser escolhida neste ponto, neste cenário você criar um novo endereço IP público. Clique em **Criar novo**

![Escolha pública lâmina de endereço IP][6]

### <a name="step-8"></a>Etapa 8

Em seguida, dê um nome amigável para o endereço IP público e clique **Okey**

![Criar blade de endereço IP público][7]

### <a name="step-9"></a>Etapa 9

A última configuração configurar ao criar um gateway de aplicativo é a configuração de ouvinte.  Se for usado **http** , nada é deixado para configurar e **Okey** pode ser clicado. Para usar **https** será necessária configuração adicional.

Para usar **https**, é necessário um certificado. A chave privada do certificado é necessária para uma exportação. pfx do certificado e a senha precisar ser fornecido.

### <a name="step-10"></a>Etapa 10

Clique em **HTTPS**, clique no ícone de **pasta** ao lado da caixa de texto **PFX carregar certificado** .
Navegue até o arquivo. pfx de certificado em seu sistema de arquivos. Quando ela estiver selecionada, dê um nome amigável para o certificado e digite a senha para o arquivo. pfx.

Após concluir clique **Okey** para examinar as configurações para o Gateway de aplicativo.

![Seção de configuração de ouvinte na lâmina de configurações][9]

### <a name="step-11"></a>Etapa 11

Revise a página Resumo e clique em **Okey**.  Agora o gateway de aplicativo enfileirado e criado.

### <a name="step-12"></a>Etapa 12

Depois que o gateway do aplicativo tiver sido criado, navegue até ele no portal para continuar a configuração do gateway aplicativo.

![Modo de exibição de recursos do aplicativo Gateway][10]

Estas etapas criam um gateway de aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento é bem-sucedido.

## <a name="next-steps"></a>Próximas etapas

Este cenário cria um gateway de aplicativo padrão. São as próximas etapas para configurar o gateway de aplicativo adicionando membros de pool, modificar configurações e ajustando regras no gateway para que ele funcione corretamente.

Saiba como criar testes de integridade personalizado visitando [criar um teste de integridade personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar SSL transferindo e levar a descriptografia SSL dispendiosa longe dos seus servidores web visitando [Configurar SSL descarregar](application-gateway-ssl-portal.md)

Saiba como proteger seus aplicativos com o [Firewall do aplicativo Web](application-gateway-webapplicationfirewall-overview.md) um recurso do gateway de aplicativo.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
