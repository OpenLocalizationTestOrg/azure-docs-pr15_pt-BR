<properties
   pageTitle="Habilitando diretiva de SSL e SSL de ponta a ponta no aplicativo Gateway | Microsoft Azure"
   description="Esta página fornece uma visão geral do Gateway aplicativo suporte para SSL de ponta a ponta."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Habilitando diretiva de SSL e SSL de ponta a ponta no aplicativo Gateway

## <a name="overview"></a>Visão geral

Gateway de aplicativo compatível com o encerramento de SSL no gateway, depois de tráfego que normalmente fluem não criptografado para os servidores de back-end. Isso permite que os servidores web para ser unburdened de sobrecarga de criptografia/descriptografia dispendioso. Entretanto para alguns clientes a comunicação criptografado para os servidores de back-end não é uma opção aceitável. Isso pode ser devido a requisitos de segurança/conformidade ou o aplicativo pode aceitar apenas conexão segura. Para tais aplicativos, o gateway de aplicativo agora dá suporte a criptografia de SSL de ponta a ponta.

Ponta a ponta SSL permite com segurança transmitir dados confidenciais para o back-end criptografado ainda aproveitando os benefícios dos recursos de balanceamento de carga de camada 7 gateway qual aplicativo fornece, como afinidade de cookie, baseado em URL roteamento, suporte para roteamento baseado em sites ou capacidade de inserir X-encaminhadas-* cabeçalhos.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicativo encerra as sessões SSL de usuário no gateway e descriptografa o tráfego de usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriado para rotear o tráfego para. Gateway do aplicativo, em seguida, inicia uma nova conexão SSL para o servidor back-end e criptografa novamente os dados usando o certificado de chave pública do servidor back-end antes de transmitir a solicitação para o back-end. Encerrar para encerrar SSL está habilitado definindo definição de protocolo na BackendHTTPSetting para Https, que é aplicado a um pool de back-end. Cada servidor back-end do pool de back-end com SSL de ponta a ponta habilitado deve ser configurado com um certificado para permitir a comunicação segura.

![cenário de ponta a ponta ssl][1]

Neste exemplo, solicitações usando TLS1.2 são roteadas para servidores de back-end em Pool1 usando SSL de ponta a ponta.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Encerrar para encerrar SSL e lista de exceções de certificados

Gateway do aplicativo somente se comunica com instâncias de back-end conhecidos que têm na lista branca seus certificados com o gateway do aplicativo. Para habilitar a lista de exceções de certificados, você deve carregar a chave pública de certificados de servidor de back-end para o gateway de aplicativo (não o certificado raiz). Somente conexões com back-ends conhecidos e lista branca, em seguida, são permitidos. Os resultados de back-ends restantes em um erro de gateway. Certificados autoassinados são para fins de teste somente e não recomendados para cargas de trabalho de produção. Esses certificados também devem estar na lista branca com o gateway do aplicativo, conforme descrito nas etapas anteriores, antes que possam ser usadas.

## <a name="application-gateway-ssl-policy"></a>Política de SSL de Gateway do aplicativo

Gateway de aplicativo suporta configurável SSL negociação políticas de usuário, que permite aos clientes mais controle sobre conexões SSL o gateway de aplicativo.

1. SSL 2.0 e 3.0 desabilitada por padrão para todos os Gateways de aplicativos. Eles não são configuráveis.
2. Fornece de definição de política SSL opção para desabilitar qualquer um dos seguintes 3 protocolos - TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Se nenhuma diretiva SSL for definida todos os três (TLSv1\_0, TLSv1\_1, TLSv1_2) estão habilitados.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre encerrar para encerrar SSL e política SSL, vá para [Habilitar SSL de ponta a ponta no gateway do aplicativo](application-gateway-end-to-end-ssl-powershell.md) criar um gateway de aplicativo com capacidade de enviar tráfego para back-ends no formato criptografado.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png