<properties 
    pageTitle="Gerenciar pontos de extremidade streaming com o portal Azure | Microsoft Azure" 
    description="Este tópico mostra como gerenciar pontos de extremidade streaming com o portal do Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerenciar pontos de extremidade streaming com o portal do Azure

## <a name="overview"></a>Visão geral

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Serviços de mídia do Microsoft Azure, um **Ponto de extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente um aplicativo de player de cliente, ou para uma rede de entrega de conteúdo (CDN) para distribuição adicional. Serviços de mídia também fornece integração perfeita com o Azure CDN. O fluxo de saída de um serviço de StreamingEndpoint pode ser um fluxo ao vivo ou um vídeo sob demanda ativo na sua conta de serviços de mídia.

Além disso, você pode controlar a capacidade do serviço de Streaming ponto de extremidade de lidar com crescentes necessidades de largura de banda ajustando streaming unidades. É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. Unidades de streaming fornecem capacidade de egresso dedicado que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional, que inclui: [dinâmica embalagem](media-services-dynamic-packaging-overview.md), CDN integração e configuração avançada.

>[AZURE.NOTE]Você é cobrado somente quando o ponto de extremidade de Streaming estiver em estado de execução.

Este tópico fornece uma visão geral das funcionalidades de principal que são fornecidas pelo Streaming pontos de extremidade. O tópico também mostra como usar o portal do Azure para gerenciar pontos de extremidade de streaming. Para obter informações sobre como dimensionar o ponto de extremidade streaming, consulte [Este](media-services-portal-scale-streaming-endpoints.md) tópico.

## <a name="start-managing-streaming-endpoints"></a>Comece a gerenciar pontos de extremidade de streaming

Para iniciar o gerenciamento de pontos de extremidade de streaming de sua conta, faça o seguinte.

1. No [portal do Azure](https://portal.azure.com/), selecione sua conta de serviços de mídia do Azure.
2. Na janela **configurações** , selecione **pontos de extremidade de Streaming**.

    ![Ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Adicionar/excluir um ponto de extremidade de streaming

Para adicionar/excluir ponto de extremidade streaming usando o portal do Azure, faça o seguinte:

1. Para adicionar um ponto de extremidade streaming, clique no **+ ponto de extremidade** na parte superior da página. 
2. Para excluir um ponto de extremidade streaming, pressione o botão **Excluir** . 

    O padrão de streaming de ponto de extremidade não pode ser excluído.
2. Clique no botão **Iniciar** para iniciar o ponto de extremidade streaming.

    ![Ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Por padrão, você pode ter até dois pontos de extremidade de streaming. Se você precisar solicitar mais, consulte [cotas e limitações](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>Configurando o ponto de extremidade de Streaming

Ponto de extremidade de streaming permite que você configure as seguintes propriedades quando você tiver pelo menos 1 unidade de escala: 

- Controle de acesso
- Controle de cache
- Cross políticas de acesso do site

Para obter informações detalhadas sobre essas propriedades, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Você pode configurar o ponto de extremidade de streaming, fazendo o seguinte:

1. Selecione o ponto de extremidade streaming que você deseja configurar.
1. Clique em **configurações**.
  
Segue uma breve descrição dos campos.

![Ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Política de cache máximo: usado para configurar a vida útil do cache para ativos atendidos por meio de neste ponto de extremidade de streaming. Se nenhum valor estiver definida, o padrão é usado. Os valores padrão também podem ser definidos diretamente no armazenamento do Azure. Se Azure CDN estiver habilitada para o ponto de extremidade streaming, você não deve definir o valor de política de cache para menos de 600 segundos.  

2. Endereços IP permitidos: usado para especificar endereços IP que deveriam ter permissão de se conectar ao ponto de extremidade streaming publicado. Se nenhum endereço IP especificado, qualquer endereço IP seria capaz de se conectar. Endereços IP podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1'), um intervalo IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo IP usando o endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ' 10.0.0.1(255.255.255.0)').

3. Configuração para autenticação de cabeçalho de assinatura Akamai: usado para especificar como a solicitação de autenticação de cabeçalho de assinatura de servidores Akamai está configurada. Expiração é no UTC.



##<a id="enable_cdn"></a>Habilitar a integração de CDN do Azure

Você pode especificar para habilitar a integração do Azure CDN para um ponto de extremidade de Streaming (ele é desabilitado por padrão).

Para definir a integração do Azure CDN como verdadeiro:

- O ponto de extremidade streaming deve ter pelo menos uma unidade de streaming. Se posteriormente você quiser definir unidades de escala como 0, você deve primeiro desabilitar a integração de CDN. Quando você cria um novo streaming uma unidade de streaming de ponto de extremidade é definida por padrão automaticamente.

- O ponto de extremidade streaming deve ser em um estado parado. Quando a CDN obtém ativada, você pode iniciar o ponto de extremidade streaming. 

Pode levar até 90 mínimo para a integração do Azure CDN obter habilitadas.  Leva até duas horas para que as alterações ser ativo entre todos os CDN é exibida.

Integração de CDN está habilitada em todos os centros de dados Azure: Oeste EUA, Leste dos EUA, Europa do Norte, Europa Ocidental, Japão Oeste, Japão Leste, Sudeste da Ásia e da Ásia Oriental.

Quando ela estiver ativada, a configuração de **Controle de acesso** obtém desativada.

![Ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Integração de serviços de mídia Azure com o Azure CDN é implementada em **Azure CDN da Verizon**.  Se desejar usar **Azure CDN do Akamai** para serviços de mídia do Azure, você deve [Configurar o ponto de extremidade manualmente](../cdn/cdn-create-new-endpoint.md).  Para obter mais informações sobre os recursos de CDN Azure, consulte [Visão geral CDN](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Considerações adicionais

- Quando CDN estiver habilitada para um ponto de extremidade streaming, clientes não podem solicitar o conteúdo diretamente a partir de origem. Se você precisar a capacidade de teste seu conteúdo com ou sem CDN, você pode criar outro ponto de extremidade de streaming que não CDN habilitado.
- Seu nome de host do ponto de extremidade streaming permanece o mesmo após habilitar CDN. Você não precisa fazer alterações ao seu fluxo de trabalho de serviços de mídia depois CDN está habilitado. Por exemplo, se seu nome de host do ponto de extremidade streaming for strasbourg.streaming.mediaservices.windows.net, após habilitar CDN, o nome de host mesmo exato é usado.
- Para novos pontos de extremidade de streaming, você pode ativar CDN simplesmente criando um novo ponto de extremidade; para pontos de extremidade de streaming existentes, você precisa primeiro interrompa o ponto de extremidade e habilite a CDN.
 

Para mais informações, consulte [Serviços de mídia do Azure anunciando integração com o Azure CDN (rede de distribuição de conteúdo)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Próximas etapas

Examine os serviços de mídia caminhos de aprendizado.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
