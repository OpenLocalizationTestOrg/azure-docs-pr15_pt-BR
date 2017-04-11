<properties
   pageTitle="Criar um gateway de aplicativo usando a CLI Azure no Gerenciador de recursos | Microsoft Azure"
   description="Aprenda a criar um Gateway de aplicativo usando a CLI Azure no Gerenciador de recursos"
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

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Criar um gateway de aplicativo usando a CLI do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gerenciador de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Azure Application Gateway é um balanceador de carga de camada-7. Ele oferece failover, roteamento de desempenho solicitações HTTP entre diferentes servidores, independentemente de estarem na nuvem ou local. Gateway de aplicativo tem os seguintes recursos de entrega de aplicativo: HTTP carregar testes de integridade personalizados de balanceamento, afinidade de sessão baseada em cookies e descarregamento Secure Sockets Layer (SSL) e suporte para vários locais.

## <a name="prerequisite-install-the-azure-cli"></a>Pré-requisito: Instalar o Azure CLI

Para executar as etapas neste artigo, você precisa [instalar a Interface de linha do Azure para Mac, Linux e Windows Azure CLI ()](../xplat-cli-install.md) e você precisa [fazer logon no Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Se você não tiver uma conta do Azure, você precisa de um. Sobe entrada para uma [avaliação gratuita aqui](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Neste cenário, você saiba como criar um gateway de aplicativo usando o portal do Azure.

Este cenário irá:

- Crie um gateway de aplicativo médio com duas instâncias.
- Crie uma rede virtual chamada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
- Crie uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configure um certificado para descarregamento SSL.

![Exemplo de cenário][scenario]

>[AZURE.NOTE] Testes adicionais de configuração do gateway aplicativo, incluindo integridade personalizado, endereços de pool de back-end e regras adicionais são configurados depois que o gateway de aplicativo está configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que você deixe bastante espaço de endereço para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, gateways de aplicativo somente adicionais são capazes de ser adicionado à sub-rede.

## <a name="log-in-to-azure"></a>Faça logon no Azure

Abra o **Prompt de comando do Microsoft Azure**e faça logon. 

    azure login

Depois de digitar o exemplo anterior, um código é fornecido. Navegue até https://aka.ms/devicelogin em um navegador para continuar o processo de login.

![logon de dispositivo mostrando cmd][1]

No navegador, insira o código recebido. Você é redirecionado para uma página de entrada.

![navegador para inserir código][2]

Depois que o código foi inserido você estiver conectado, feche o navegador para continuar com o cenário.

![conectado com êxito][3]

## <a name="switch-to-resource-manager-mode"></a>Alternar para o modo do Gerenciador de recursos

    azure config mode arm

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicativo, um grupo de recursos é criado para conter o gateway do aplicativo. A seguir mostra o comando.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Após o grupo de recursos é criado, uma rede virtual é criada para o gateway de aplicativo.  No exemplo a seguir, o espaço de endereço foi como 10.0.0.0/16, conforme definido nas notas do cenário anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Criar uma sub-rede

Após a rede virtual é criada, uma sub-rede é adicionada para o gateway de aplicativo.  Se você planeja usar o gateway de aplicativo com um aplicativo web hospedado na mesma rede virtual como o gateway de aplicativo, certifique-se de deixar espaço suficiente para outra sub-rede.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Depois que a rede virtual e sub-rede são criados, os pré-requisitos para o gateway de aplicativo estão concluídos. Além disso, um certificado de arquivo. pfx exportado anteriormente e a senha para o certificado são necessários para a seguinte etapa: endereços IP usados para back-end são os endereços IP para o servidor back-end. Esses valores podem ser IPs particular na rede virtual, ips pública ou nomes de domínio totalmente qualificado para os servidores de back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] Para obter uma lista de parâmetros que podem ser fornecidas durante a criação, execute o seguinte comando: **gateway de aplicativo de rede azure criar – Ajuda**.

Este exemplo cria um gateway de aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Ele também configura descarregamento SSL. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento é bem-sucedido.
Se você já tiver seu aplicativo web definido com o pool de back-end nas etapas anteriores, uma vez criado, balanceamento de carga começa.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar testes de integridade personalizado visitando [criar um teste de integridade personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar SSL transferindo e levar a descriptografia SSL dispendiosa desativar os servidores web visitando [Configurar SSL descarregar](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png