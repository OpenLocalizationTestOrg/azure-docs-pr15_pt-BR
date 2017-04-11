<properties
   pageTitle="Criar um teste personalizado para um gateway de aplicativo usando o portal | Microsoft Azure"
   description="Saiba como criar um teste personalizado para o Gateway de aplicativo usando o portal"
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

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar um teste personalizado para o Gateway de aplicativo usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clássico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Cenário

O cenário a seguir percorre criando um teste de integridade personalizados em um gateway de aplicativo existente.
O cenário supõe que você já tiver seguido as etapas para [criar um Gateway de aplicativos](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Criar o teste

Testes são configurados em um processo de duas etapas por meio do portal. A primeira etapa é criar o teste, em seguida você adicionar o teste para as configurações de http de back-end do gateway aplicativo.

### <a name="step-1"></a>Etapa 1

Navegue até http://portal.azure.com e selecione um gateway de aplicativo existente.

![Visão geral de Gateway do aplicativo][1]

### <a name="step-2"></a>Etapa 2

Clique em **testes** e clique no botão **Adicionar** para adicionar um novo teste.

![Adicionar blade de teste com informações preenchidas][2]

### <a name="step-3"></a>Etapa 3

Preencha as informações necessárias para o teste e ao concluir, clique em **Okey**.

- **Nome** - este é um nome amigável para o teste acessível no portal.
- **Host** - este é o nome de host que é usado para o teste. Aplicável somente quando vários locais está configurado no aplicativo Gateway, caso contrário use '127.0.0.1'. Isso é diferente do nome do host de máquina virtual.
- **Caminho** - o resto da url completo para o teste personalizado. Um caminho válido começa com '/'
- **Intervalo (s)** - com que frequência o teste é executado para verificar integridade. Não é recomendável definir inferior a 30 segundos.
- **Tempo limite (s)** - a quantidade de tempo que o teste aguarda antes de expirar. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada de http pode ser feita para garantir que a página de integridade de back-end está disponível.
- **Limite não íntegra** - número de tentativas sejam considerados sem integridade. Um limite de 0 significa que se uma falha de verificação de integridade back-end será determinado não íntegra imediatamente.

> [AZURE.IMPORTANT] o nome de host não é o nome do servidor. Este é o nome do host virtual executando no servidor do aplicativo. O teste é enviado à http://(host name):(port from httpsetting)/urlPath

![configurações de teste][3]

## <a name="add-probe-to-the-gateway"></a>Adicionar o teste para o gateway

Agora que o teste tiver sido criado, é hora para adicioná-lo para o gateway. Configurações de teste são definidas as configurações de http de back-end do gateway aplicativo.

### <a name="step-1"></a>Etapa 1

Clique nas **configurações de HTTP** do gateway aplicativo e, em seguida, clique nas configurações de http de back-end atual na janela para ativar a lâmina de configuração.

![janela de configurações de HTTPS][4]

### <a name="step-2"></a>Etapa 2

Na lâmina **appGatewayBackEndHttp** configurações, clique **teste personalizado de uso** e escolha o teste criado na seção [criar o teste](#createprobe) .
Ao concluir, clique em **OK** e as configurações serão aplicadas.

![blade de configurações de appgatewaybackend][5]

O teste de padrão verifica o acesso padrão ao aplicativo web. Agora que um teste personalizado tiver sido criado, o gateway de aplicativo usa o caminho personalizado definido para monitorar a saúde de back-end selecionado. O gateway de aplicativo com base nos critérios que foi definida, verifica o arquivo especificado no teste. Se a chamada para host: porta / caminho não retornar uma resposta de status de Http 200 Okey, o servidor é tirado de rotação, depois que o limite não íntegro é alcançado. Sondagem continua na instância não íntegra para determinar quando ele se torne Íntegro novamente. Depois que a instância é adicionada de volta ao pool de servidor eficaz, o tráfego começa fluindo-lo novamente e sondagem na instância continua no intervalo especificado do usuário como normal.


## <a name="next-steps"></a>Próximas etapas

Para saber como configurar SSL transferindo com o Azure Application Gateway consulte [Configurar SSL descarregar](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png