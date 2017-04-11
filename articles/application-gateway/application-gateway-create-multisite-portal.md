<properties
   pageTitle="Configurar um gateway de aplicativo existente para hospedar vários sites no portal do Azure | Microsoft Azure"
   description="Esta página fornece instruções para configurar um gateway existente do aplicativo do Azure para hospedar vários aplicativos da web no mesmo gateway com o portal do Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurar um gateway de aplicativo existente para hospedar vários aplicativos da web

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Várias opções de hospedagem de site permite que você implantar mais de um aplicativo da web no mesmo gateway aplicativo. Ele depende de presença do cabeçalho de host na solicitação de HTTP recebida, para determinar quais ouvinte deve receber o tráfego. O ouvinte depois direciona o tráfego ao pool de back-end apropriado conforme configurado na definição de regras do gateway. Em aplicativos da web SSL habilitado, o gateway de aplicativo depende a extensão de nome de servidor indicação (SNI) para escolher o ouvinte correto para o tráfego da web. Um uso comum para várias opções de hospedagem de site é carregar solicitações de saldo para domínios da web diferente para pools de servidor de back-end diferente. Da mesma forma vários subdomínios do mesmo domínio raiz também poderiam ser hospedados no mesmo gateway aplicativo.

## <a name="scenario"></a>Cenário

No exemplo a seguir, o gateway de aplicativo está servindo tráfego para contoso.com e fabrikam.com com dois grupos de servidor back-end: contoso pool de servidor e pool de servidor de fabrikam. Configuração semelhante pode ser usadas para subdomínios de host como app.contoso.com e blog.contoso.com.

![cenário multissite][multisite]

## <a name="before-you-begin"></a>Antes de começar

Este cenário adiciona o suporte de vários local para um gateway de aplicativo existente. Para concluir esse cenário cenário um gateway de aplicativo existente precisa estar disponíveis para configurar. Visite a [criar um gateway de aplicativo usando o portal](./application-gateway-create-gateway-portal.md) para aprender a criar um gateway de aplicativos básicos no portal.

## <a name="requirements"></a>Requisitos

- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. FQDN também pode ser usado.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, esses valores diferenciam maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar). Para gateways de aplicativo habilitado de vários locais, nome do host e indicadores SNI também são adicionados.
- **Regra:** A regra associará o ouvinte, o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte.
- **Certificados:** Cada ouvinte requer um certificado exclusivo, neste exemplo 2 ouvintes são criadas para vários locais. Dois certificados. pfx e as senhas para que elas precisam ser criados.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

A seguir estão as etapas necessárias para atualizar o gateway do aplicativo:

1. Crie pools de back-end para usar para cada site.
2. Crie um novo ouvinte para cada gateway de aplicativo de site oferecerá suporte.
3. Crie regras para mapear cada ouvinte com o back-end apropriado.

## <a name="create-back-end-pools-for-each-site"></a>Criar pools de back-end para cada site

Um pool de back-end para cada site do gateway desse aplicativo será é necessário suporte, nesse caso 2 será criada, uma para contoso11.com e outra para fabrikam11.com.

### <a name="step-1"></a>Etapa 1

Navegar para um gateway de aplicativo existente no portal do Azure (https://portal.azure.com). Selecione **pools de back-end** e clique em **Adicionar**

![Adicionar pools de back-end][7]

### <a name="step-2"></a>Etapa 2

Preencha as informações para o pool de back-end **pool1**, adicionando os endereços ip ou FQDNs para os servidores de back-end e clique em **Okey**

![configurações de pool1 do pool de back-end][8]

### <a name="step-3"></a>Etapa 3

Na lâmina pools de back-end, clique em **Adicionar** para adicionar um pool de back-end adicionais **pool2**, adicionando os endereços ip ou FQDNS para os servidores de back-end e clique em **Okey**

![configurações de pool2 do pool de back-end][9]

### <a name="create-listeners-for-each-back-end"></a>Criar ouvintes para cada back-end

Gateway do aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site no mesmo endereço IP público e a porta. O ouvinte básico criado no portal não contém essa propriedade.

### <a name="step-1"></a>Etapa 1

Clique **ouvintes** no gateway aplicativo existente e clique em **vários locais** para adicionar o ouvinte primeiro.

![blade de visão geral de ouvintes][1]

### <a name="step-2"></a>Etapa 2

Preencha as informações para o ouvinte, neste exemplo encerramento de SSL é configurada, criar uma nova porta de front-end. Carregar o certificado. pfx a ser usado para o encerramento de SSL. A única diferença neste blade em comparação com a lâmina ouvinte básica padrão é o nome do host.

![blade de propriedades de ouvinte][2]

### <a name="step-3"></a>Etapa 3

Clique em **vários locais** e crie outro ouvinte conforme descrito na etapa anterior para o segundo local. Verifique se usar um certificado diferente para o segundo ouvinte. A única diferença neste blade em comparação com a lâmina ouvinte básica padrão é o nome do host. Preencha as informações para o ouvinte e clique em **Okey**.

![blade de propriedades de ouvinte][3]

> [AZURE.NOTE] Criação de ouvintes no portal do Azure para o gateway de aplicativo é uma tarefa demorada, pode levar algum tempo para criar os dois ouvintes nesse cenário. Quando concluir a apresentação de ouvintes no portal do, conforme mostrado na imagem a seguir.

![Visão geral de ouvinte][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Criar regras para mapear ouvintes para pools de back-end

### <a name="step-1"></a>Etapa 1

Navegar para um gateway de aplicativo existente no portal do Azure (https://portal.azure.com). Selecione **regras** e escolha o existente de regra padrão **rule1** e clique em **Editar**.

### <a name="step-2"></a>Etapa 2

Preencha a lâmina de regras, como visto na imagem a seguir. Escolher o ouvinte primeiro e o primeiro pool e clicando em **Salvar** quando terminar.

![Editar regra existente][6]

### <a name="step-3"></a>Etapa 3

Clique em **regra básica** para criar a regra 2a. Preencha o formulário com o ouvinte segundo e o segundo pool de back-end e clique em **Okey** para salvar.

![Adicionar blade regra básica][10]

Isso conclui Configurando um gateway de aplicativo existente com suporte a vários local por meio do portal Azure.

## <a name="next-steps"></a>Próximas etapas

Saiba como proteger seus sites com [Application Gateway - Firewall do aplicativo Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png