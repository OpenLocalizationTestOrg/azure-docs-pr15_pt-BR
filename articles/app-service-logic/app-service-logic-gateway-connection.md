<properties
   pageTitle="Lógica de aplicativos conexão de gateway de dados locais | Microsoft Azure"
   description="Informações sobre como criar uma conexão para o gateway de dados local de um aplicativo de lógica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Conectar-se para o gateway de dados local para os aplicativos de lógica

Conectores de aplicativos de lógica compatíveis permitem que você configure sua conexão para acessar os dados no local por meio do gateway de dados local.  As etapas a seguir apresentará como instalar e configurar o gateway de dados locais para trabalhar com um aplicativo de lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Deve estar usando um trabalho ou da escola endereço de email no Azure para associar o gateway de dados locais à sua conta (conta do Azure Active Directory com base)
    * Se você estiver usando uma Account da Microsoft (por exemplo, @outlook.com, @live.com) você pode usar sua conta do Azure para criar um trabalho ou escola endereço de email seguindo [as etapas aqui](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Não há uma limitação no momento que a instalação do gateway será concluída somente quando usando uma conta que tenha sido registrada com o Power BI no local.  Enquanto isso, registre qualquer conta com "Power BI livre" para concluir a instalação com êxito.

* Deve ter o local dados gateway [instalado em um computador local](app-service-logic-gateway-install.md).
* Gateway não deve ter sido solicitado por outro gateway de dados do Azure local ([reivindicar acontece com a criação da etapa 2 abaixo](#2-create-an-azure-on-premises-data-gateway-resource)) - uma instalação somente pode ser associada ao recurso de um gateway.

## <a name="installing-and-configuring-the-connection"></a>Instalando e configurando a conexão

### <a name="1-install-the-on-premises-data-gateway"></a>1. instalar o gateway de dados local

Informações sobre como instalar o gateway de dados local podem ser encontradas [neste artigo](app-service-logic-gateway-install.md).  O gateway deve ser instalado em um computador local antes de continuar com o restante das etapas.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Crie um recurso de gateway de dados do Azure local

Depois de instalado, você deve associar sua assinatura do Azure com o gateway de dados local.

1. Fazer login no Azure usando o mesmo trabalho ou o endereço de email da escola que foi usado durante a instalação do gateway
1. Clique em botão do **novo** recurso
1. Procure e selecione o **gateway de dados local**
1. Preencha as informações para associar o gateway a sua conta, incluindo selecionando o **Nome da instalação**

    ![Conexão de Gateway de dados local][1]
1. Clique no botão **criar** para criar o recurso

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. criar uma conexão de aplicativo de lógica no designer

Agora que sua assinatura do Azure está associada a uma instância do gateway de dados local, você pode criar uma conexão a ele de dentro de um aplicativo de lógica.

1. Abra um aplicativo de lógica e escolha um conector que suporta conectividade de local (até o momento, SQL Server)
1. Selecione a caixa de seleção para **Conectar via gateway de dados local**

    ![Criação de Gateway do Designer de aplicativo de lógica][2]
1. Selecione o **Gateway** conectar e concluir outras informações de conexão necessárias
1. Clique em **criar** para criar a conexão

A conexão agora deve ser configurada com êxito para uso em seu aplicativo de lógica.  

## <a name="next-steps"></a>Próximas etapas
- [Exemplos comuns e cenários para aplicativos de lógica](app-service-logic-examples-and-scenarios.md)
- [Recursos de integração do Enterprise](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG