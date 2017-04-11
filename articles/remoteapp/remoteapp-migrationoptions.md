
<properties 
    pageTitle="Opções para a migração fora do Azure RemoteApp | Microsoft Azure" 
    description="Saiba mais sobre as opções para migrar fora do Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opções para a migração fora do Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se você interrompeu usando Azure RemoteApp devido a [anúncio de aposentadoria](https://go.microsoft.com/fwlink/?linkid=821148) ou porque você terminar sua avaliação, você precisa migrar de RemoteApp do Azure para outro serviço de aplicativo. Há duas abordagens diferentes para a migração: um gerenciados automaticamente (muitas vezes chamado de infraestrutura como um serviço [IaaS]) implantação ou uma totalmente gerenciada (normalmente chamada plataforma como um serviço) ou Software como um serviço [PaaS/SaaS] oferta. 

Autoatendimento IaaS é uma implantação tipo faça você mesmo que é gerenciada, operada e pertencente a você, diretamente implantado em máquinas virtuais (VMs) ou sistemas físicos. Na outra extremidade, uma totalmente gerenciado PaaS/oferta de SaaS é mais como RemoteApp Azure - um parceiro fornece uma camada de serviço na parte superior de uma solução de comunicação remota que manipula operacionais e atendendo, enquanto você, como cliente, faça alguns gerenciamento de imagem e o aplicativo.

Leia mais para obter mais informações, incluindo exemplos de diferentes opções de hospedagem.    

## <a name="self-managed-iaas-solutions"></a>Soluções de (IaaS) gerenciadas automaticamente

### <a name="rds-on-iaas"></a>**RDS em IaaS** 
Você pode implantar uma implantação de serviços de área de trabalho remota (no Windows Server) baseada em sessão nativa usando RemoteApp ou desktops local ou em um ambiente hospedado (como em VMs Azure). RDS em implantações de IaaS são melhores para clientes já familiarizados com e que tenham experiência técnica existente com implantações de RDS. 

> [AZURE.NOTE]
> Você precisa de licenciamento por Volume com Software Assurance (SA) para licenças de acesso de cliente RDS para usar essa opção de implantação.

Implantar RDS no Azure VMs é muito mais fácil quando você usa implantação e patches modelos (ler uma [Visão geral](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) e, em seguida, [vá obtê-los](https://aka.ms/rdautomation)). Você pode obter os mesmos recursos de dimensionamento Elástico com recursos de modelo de implantação de clássico Azure (não recursos de modelo de recursos do Azure) dentro de RemoteApp Azure usando o [script de dimensionamento automático](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), embora existam em mais configurações e personalizações. Quando você implanta RDS em VMs do Azure, suporte é fornecido por meio de [Suporte do Azure](https://azure.microsoft.com/support/plans/), os profissionais de suporte mesmo suporte você com o Azure RemoteApp. Você pode obter estimativas de custo com base no seu uso existente contatando o [Suporte do Azure](https://azure.microsoft.com/support/plans/), ou você pode executar cálculos usando uma breve sejam lançadas Calculadora de custo.  Além disso, com VMs série N (atualmente em particular preview) você pode adicionar vGPU - ouvir mais sobre a adição de vGPU e sobre como aproveitar [as melhorias de RDS no Windows Server 2016](https://myignite.microsoft.com/videos/2794) em nossa sessão Ignite.   

Temos guias de implantação de passo a passo para o [Windows Server 2012 R2](http://aka.ms/rdsonazure) e [Windows Server 2016](http://aka.ms/rdsonazure2016) para ajudá-lo com sua implantação. Confira o [blog de área de trabalho remota](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) para as notícias mais recentes.
 
### <a name="citrix-on-iaas"></a>**Citrix em IaaS** 
Um Citrix nativo implantação do XenApp baseada em sessão ou XenDesktop pode ser implantado no local ou em um ambiente hospedado (como em VMs Azure). 

Consulte o guia de implantação passo a passo, [Citrix XA 7.6 no Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), para obter mais informações. Leia mais sobre [Citrix no Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), incluindo uma calculadora de preços. Você também pode encontrar um [contato Citrix](http://citrix.com/English/contact/index.asp) para discutir suas opções com.

## <a name="fully-managed-paassaas-offerings"></a>Totalmente gerenciadas ofertas de PaaS/SaaS)

### <a name="citrix-cloud"></a>**Nuvem de Citrix** 
[Solução de nuvem existente Citrix](https://www.citrix.com/products/citrix-cloud/), idêntico arquitetura ao Citrix XenApp Express. Citrix está oferecendo uma [promoção de desconto de 50%](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) para os clientes existentes do Azure RemoteApp. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (no programa Technical preview)**
[Registre-se no seu programa Technical preview](http://now.citrix.com/remoteapp)e assistir sua [sessão de Ignite](https://myignite.microsoft.com/videos/2792) (começando em minuto 20:30). XenApp Express é arquitetura idêntica à nuvem Citrix exceto inclui gerenciamento simplificado da interface do usuário e outros recursos e recursos que são semelhantes aos RemoteApp do Azure. 

Saiba mais sobre [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programa de provedor de serviço de Citrix** 
O programa de provedor de serviço de Citrix torna mais fácil para provedores de serviços oferecer a simplicidade da nuvem virtual computação SMBs, oferecendo-los os serviços que querem em um modelo de fácil e flexível. Provedores de serviços de Citrix desenvolver seus negócios Microsoft SPLA e expandir seus investimentos na plataforma RDS com qualquer dispositivo, acesso em qualquer lugar, o mais amplo suporte do aplicativo, uma experiência avançada, segurança adicional e escalabilidade. Por sua vez, provedores de serviços de Citrix atrair mais assinantes, melhore a satisfação do cliente e reduzir seus custos operacionais. [Saiba mais](http://www.citrix.com/products/service-providers.html) ou [Encontre um parceiro](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Microsoft hospedado no provedor de serviços** 
Parceiros de hospedagem normalmente oferecem um totalmente gerenciado hospedado área de trabalho do Windows e serviço de aplicativo, que pode incluir a gerenciar o Azure, sistemas operacionais, aplicativos e recursos usando o parceiro de suporte técnico do licenciamento contratos com a Microsoft e outros fornecedores de software juntamente com sendo um contrato de licença de provedor de serviço para permitir revenda da licença de acesso ao assinante (SAL). As informações a seguir fornecem detalhes e informações de contato para alguns dos hosters especializados em ajudando clientes com sua migração de RemoteApp do Azure. Confira [a lista atual de provedores de serviço de hospedado](http://aka.ms/rdsonazurecertified) que concluiu o RDS em IaaS avaliação e caminho de aprendizagem.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) especializada em ISVs para fazer a transição para a nuvem e ISV' olhando para otimizar suas configurações atuais de nuvem. ASPEX oferece uma grande variedade de serviços gerenciados, devops e serviços de consultoria.  

Local principal: Antuérpia, Bélgica

Região de operação: Europa Ocidental

Status do parceiro: [Prata](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Provedor de serviços de nuvem da Microsoft: Sim

Oferecer soluções de RemoteApp e área de trabalho baseadas em sessão: Sim, ambas

Soluções de migração do Azure RemoteApp: Sim, [Saiba mais](https://www.aspex.be/en/azure-remote-apps)

**Contato:**

- Telefone: +3232202198
- Email:[info@aspex.be](mailto:info@aspex.be)
- Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nome da plataforma: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) é uma plataforma de automação para empresas de TI simplificar, otimizar e dimensionar a migração e entrega de remotas, aplicativos remotos e infraestrutura na nuvem Microsoft Azure. 

A plataforma MyCloudIT reduz o tempo de implantação, 95%, o Azure custo por 30% e move infraestrutura inteira do seu cliente para a nuvem em questão de alguns pressionamentos de tecla. Parceiros agora podem gerenciar os clientes de um painel de controle global, os usuários finais de serviço em todo o mundo como nunca antes e aumentar as receitas sem adicionar sobrecarga adicional ou amplo treinamento Azure.  

Local principal: Dallas, TX, EUA

Região de operação: em todo o mundo

Status do parceiro: [ouro](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Provedor de serviços de nuvem da Microsoft: Sim

Oferecer soluções de RemoteApp e área de trabalho baseadas em sessão: Sim, ambas

Soluções de migração do Azure RemoteApp: Sim, [Saiba mais](https://mycloudit.com/remote-app-microsoft/)

**Contato:**
- José Garoutte, VP de desenvolvimento de negócios

   Telefone: 972-218-0741

   Email:[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) especializada no fornecimento de soluções de área de trabalho hospedadas, oferecer completa da área de trabalho e aplicativos ISV experiências criadas na tecnologia Microsoft base a um cliente global do Azure e seus próprios dos data centers.

Local principal: Londres, Reino Unido; Cingapura; Houston, TX

Região de operação: em todo o mundo

Status do parceiro: ouro

Provedor de serviços de nuvem da Microsoft: Sim

Oferecer soluções de RemoteApp e área de trabalho baseadas em sessão: Sim, ambas

Soluções de migração do Azure RemoteApp: Sim, [Saiba mais](http://www.acuutech.com/ara-migration/)

**Contato:**

- Reino Unido:

  5/6 York casa, Langston estrada,

  Loughton, Essex IG10 3TQ
  
  Telefone: + 44 (0) 20 8502 2155
 
- Cingapura:

  Rua Cecil 100, #09-02 
  
  O globo, Cingapura 069532
  
  Telefone: + 65 6709 4933
 
- América do Norte: 

  3601 S. Sandman St.
  
  Pacote 200, Houston, TX 77098
  
  Telefone: +1 713 691 0800

## <a name="need-more-help"></a>Precisa de mais ajuda?
Ainda precisa de ajuda escolhendo ou tiver outras dúvidas? Use um dos seguintes métodos para obter ajuda. 

1.  E-mail para [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Comece abrindo um [caso de suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Ligue. [Localizar um número de venda local](https://azure.microsoft.com/overview/sales-number/).
