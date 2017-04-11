<properties
   pageTitle="Configurar um gateway de aplicativo para descarregamento SSL usando o portal | Microsoft Azure"
   description="Esta página fornece instruções para criar um gateway de aplicativo com SSL descarregar usando o portal"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o portal

> [AZURE.SELECTOR]
-[Portal do Azure](application-gateway-ssl-portal.md)
-[PowerShell do Gerenciador de recursos do Azure](application-gateway-ssl-arm.md)
-[PowerShell clássico do Azure](application-gateway-ssl.md)

Azure Application Gateway pode ser configurado para finalizar a sessão de Secure Sockets Layer (SSL) no gateway para evitar dispendiosas tarefas de descriptografia SSL para que ocorram em web farm. Descarregamento SSL também simplifica a configuração de servidor front-end e o gerenciamento do aplicativo da web.

## <a name="scenario"></a>Cenário

O cenário a seguir percorre Configurando o SSL descarregar em um gateway de aplicativo existente. O cenário supõe que você já tiver seguido as etapas para [criar um Gateway de aplicativos](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Para configurar descarregamento SSL com um gateway de aplicativo, é necessário um certificado. Este certificado é carregado no gateway do aplicativo e usado para criptografar e descriptografar o tráfego enviado via SSL. O certificado deve estar no formato de troca de informações pessoais (pfx). Este formato de arquivo permite a chave privada a ser exportado que é requerido pelo gateway de aplicativo para executar a criptografia e descriptografia de tráfego.

## <a name="add-an-https-listener"></a>Adicionar um ouvinte HTTPS

O ouvinte HTTPS procura tráfego com base em sua configuração e ajuda a direcionar o tráfego para os pools de back-end.

### <a name="step-1"></a>Etapa 1

Navegue até o portal do Azure e selecione um gateway de aplicativo existente

### <a name="step-2"></a>Etapa 2

Clique ouvintes e clique no botão Adicionar para adicionar um ouvinte.

![blade de visão geral de gateway do aplicativo][1]

### <a name="step-3"></a>Etapa 3

Preencha as informações necessárias para o ouvinte e carregar o certificado. pfx, quando concluído clique Okey.

**Nome** - esse valor é um nome amigável do ouvinte.

**Configuração de IP de Frontend** - esse valor é a configuração de IP de frontend que é usada para o ouvinte.

**Frontend porta (nome /)** - um nome amigável para a porta usada no front-end do aplicativo gateway e a porta real usada.

**Protocolo** - uma opção para determinar se o https ou http será usado para o front-end.

**Certificado (nome/senha)** - descarregamento de SSL se é usado, é necessário para esta configuração um certificado. pfx e um nome amigável e a senha são necessários.

![Adicionar blade ouvinte][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Criar uma regra e associá-la para o ouvinte

O ouvinte agora foi criado. É hora para criar uma regra para lidar com o tráfego do ouvinte.

### <a name="step-1"></a>Etapa 1

Clique as **regras** do gateway aplicativo e clique em Adicionar.

![blade de regras de gateway do aplicativo][3]

### <a name="step-2"></a>Etapa 2

Na lâmina **Adicionar regra básica** , digite o nome amigável para a regra e escolha o ouvinte criado na etapa anterior. Escolha o pool de back-end apropriado e configuração http e clique em **Okey**

![janela de configurações de HTTPS][4]

Agora, as configurações são salvas para o gateway de aplicativo. O processo de salvamento para essas configurações podem demorar alguns instantes até que eles estão disponíveis para exibição por meio do portal ou através do PowerShell. Depois de salvar o gateway de aplicativo controla a criptografia e descriptografia de tráfego. Todo o tráfego entre o gateway do aplicativo e os servidores de web de back-end será tratado sobre http. Qualquer comunicação volta para o cliente se iniciada por https será retornada para o cliente criptografado.

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar um teste de integridade personalizado com o Azure Application Gateway, consulte [criar um teste de integridade personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png