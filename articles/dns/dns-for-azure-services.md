<properties
  pageTitle="Usando o Azure DNS com outros serviços do Azure | Microsoft Azure"
  description="Noções básicas sobre como usar o DNS do Azure para resolver nome para outros serviços do Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>Usando o Azure DNS com outros serviços do Azure

DNS Azure é um hospedado gerenciamento e o nome do serviço de resolução DNS. Isso permite que você crie públicos nomes DNS para os outros aplicativos e serviços que você implantou no Azure. Criar um nome para um serviço Azure no seu domínio personalizado é tão simple quanto adicionar um registro do tipo correto para o serviço.

* Para endereços IP alocados dinamicamente, você deve criar um registro CNAME DNS que mapeia para o nome DNS criados Azure para seu serviço. Padrões DNS impedem a usando um registro CNAME para o apex de zona.
* Para endereços IP estática alocados, você pode criar um registro de DNS A usando qualquer nome, incluindo um nome de _domínio "descoberto"_ em apex a zona.

A tabela a seguir descreve os tipos de registro com suporte que podem ser usados para vários serviços Azure. Como você pode ver desta tabela, o DNS do Azure suporta apenas os registros DNS para recursos de rede com a Internet. Azure DNS não pode ser usado para resolução de nomes de endereços internas, privadas.

| Serviço do Azure | Interface de rede | Descrição |
|---------------|-------------------|-------------|
| Gateway de aplicativo | IP público front-end | Você pode criar um registro DNS A ou CNAME. |
| Balanceador de carga | IP público front-end | Você pode criar um registro DNS A ou CNAME. Balanceador de carga pode ter um endereço IP público IPv6 dinamicamente atribuído. Portanto, você deve criar um registro CNAME para um endereço IPv6. |
| Gerenciador de tráfego | Nome público | Você só pode criar um CNAME que mapeia para o nome de trafficmanager.net atribuído ao seu perfil do Gerenciador de tráfego. Para obter mais informações, consulte [Gerenciador de tráfego como funciona](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Serviço de nuvem | IP público | Para endereços IP estática alocados, você pode criar um registro DNS A. Para endereços IP alocados dinamicamente, você deve criar um registro CNAME que mapeia para o nome de _cloudapp.net_ . Essa regra se aplica ao VMs criadas no portal do clássico porque eles são implantados como um serviço na nuvem. Para obter mais informações, consulte [Configurar um nome de domínio personalizado nos serviços de nuvem](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Serviço de aplicativo | IP externo | Para endereços IP externos, você pode criar um registro DNS A. Caso contrário, você deve criar um registro CNAME que mapeia para o nome de azurewebsites.net. Para obter mais informações, consulte [mapear um nome de domínio personalizado para um aplicativo do Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Gerenciador de recursos de VMs | IP público | Gerenciador de recursos de VMs pode ter endereços IP públicos. Uma máquina virtual com um endereço IP público também pode estar atrás de um balanceador de carga. Você pode criar um registro DNS A ou CNAME para o endereço público. Esse nome personalizada pode ser usado para ignorar o VIP no balanceador de carga. |
| VMs clássicas | IP público | VMs clássicas criados usando o PowerShell ou CLI pode ser configurado com um endereço de virtual (reservado) dinâmico ou estático. Você pode criar um CNAME DNS ou um registro, respectivamente. |
