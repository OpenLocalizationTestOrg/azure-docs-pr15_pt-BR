<properties
    pageTitle="Configurações personalizadas para ambientes de serviço de aplicativo"
    description="Configurações personalizadas para ambientes de serviço de aplicativo"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Configurações personalizadas para ambientes de serviço de aplicativo

## <a name="overview"></a>Visão geral ##
Como os ambientes de serviço de aplicativo são isolado para um único cliente, há certas configurações que podem ser aplicadas exclusivamente aos ambientes de serviço do aplicativo. Este artigo documentos as várias personalizações específicas que estão disponíveis para ambientes de serviço de aplicativo.

Se você não tiver um ambiente de serviço de aplicativo, consulte [como criar um ambiente de serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

Você pode armazenar personalizações de ambiente de aplicativo de serviço usando uma matriz no novo atributo **clusterSettings** . Esse atributo é encontrado no dicionário "Propriedades" de *hostingEnvironments* Gerenciador de recursos do Azure entidade.

Trecho de modelo abreviado Gerenciador de recursos a seguir mostra o atributo **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

O atributo **clusterSettings** pode ser incluído em um modelo de Gerenciador de recursos para atualizar o ambiente de serviço de aplicativo.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Use o Gerenciador de recursos do Azure para atualizar um ambiente de serviço de aplicativo
Como alternativa, você pode atualizar o ambiente de serviço de aplicativo usando o [Gerenciador de recursos do Azure](https://resources.azure.com).  

1. No Gerenciador de recursos, vá para o nó para o ambiente de serviço de aplicativo (**assinaturas** > **resourceGroups** > **provedores** > **Micrososft.Web** > **hostingEnvironments**). Clique no ambiente de serviço de aplicativo específico que você deseja atualizar.

2. No painel direito, clique em **Leitura/gravação** na barra de ferramentas superior para permitir interativa de edição no Gerenciador de recursos.  

3. Clique azul **Editar** botão para tornar o modelo do Gerenciador de recursos editável.

4. Role até a parte inferior do painel direito. O atributo **clusterSettings** está na parte inferior, onde você pode inserir ou atualizar seu valor.

5. Digite (ou copie e cole) a matriz de valores de configuração que você deseja no atributo **clusterSettings** .  

6. Clique em verde **colocar** botão que tem localizado na parte superior do painel direito para confirmar a alteração no ambiente de serviço de aplicativo.

No entanto, você envia a alteração, leva cerca de 30 minutos multiplicados pelo número de front-ends no ambiente de serviço de aplicativo para que a alteração seja efetivada.
Por exemplo, se um ambiente de serviço de aplicativo tem quatro front-ends, levará aproximadamente duas horas para a atualização de configuração concluir. Enquanto a alteração de configuração está sendo implementada, nenhuma outra operações de dimensionamento ou operações de alteração de configuração podem ocorrer no ambiente de serviço de aplicativo.

## <a name="disable-tls-10"></a>Desabilitar o TLS 1.0 ##
Uma pergunta recorrente de clientes, especialmente os clientes que estão lidando com conformidade PCI auditorias, como desabilitar explicitamente TLS 1.0 para seus aplicativos.

TLS 1.0 pode ser desabilitado através da seguinte entrada de **clusterSettings** :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Alterar ordem de pacote de codificação TLS ##
Outra pergunta de clientes é se eles podem modificar a lista de codificações negociada pelo seu servidor e isso pode ser feito modificando as **clusterSettings** , conforme mostrado abaixo. A lista de conjuntos de codificação disponíveis pode ser recuperada [deste artigo MSDN] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Se valores incorretos estão definidos para o conjunto de codificação que SChannel não consegue entender, toda a comunicação TLS ao seu servidor pode parar de funcionar. Nesse caso, você precisará remover a entrada de *FrontEndSSLCipherSuiteOrder* da **clusterSettings** e enviar o modelo atualizado do Gerenciador de recursos para reverter para as configurações de pacote de codificação padrão.  Use esta funcionalidade com cuidado.

## <a name="get-started"></a>Introdução
O modelo de sites do Gerenciador de recursos de início rápido do Azure inclui um modelo com a definição de base para a [criação de um ambiente de serviço de aplicativo](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
