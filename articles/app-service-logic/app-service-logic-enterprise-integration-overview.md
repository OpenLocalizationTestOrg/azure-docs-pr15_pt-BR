<properties 
    pageTitle="Visão geral da integração do Enterprise | Serviço de aplicativo do Microsoft Azure | Microsoft Azure" 
    description="Usar os recursos de integração corporativa para habilitar cenários de processo e integração de negócios usando aplicativos de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>Visão geral do pacote de integração do Enterprise

## <a name="what-is-the-enterprise-integration-pack"></a>O que é o pacote de integração do Enterprise?
O pacote de integração do Enterprise é solução baseado em nuvem da Microsoft para habilitar perfeitamente comunicações do business-to-business (B2B). O pacote usa protocolos padrão do setor inclusive [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md)e [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) a troca de mensagens entre parceiros comerciais. As mensagens podem ser protegidas opcionalmente usando criptografia e assinaturas digitais. 

O pacote de formatos para trocar mensagens eletronicamente, transformando os diferentes formatos em um formato que sistemas de ambas as organizações podem interpretar e agir em e permite que as organizações que usam protocolos diferentes. 

Se você estiver familiarizado com o BizTalk Server ou serviços da Microsoft Azure BizTalk, você encontrará-fácil de usar que a integração do Enterprise recursos como a maioria dos conceitos é semelhante. Uma grande diferença é que integração corporativa usa contas de integração para simplificar o armazenamento e gerenciamento de artefatos usados nas comunicações B2B. 

Arquitetura, o pacote de integração do Enterprise baseia-se em **contas de integração** que armazena todos os artefatos que podem ser usados para projetar, implantar e manter seus aplicativos B2B. Uma conta de integração é basicamente um contêiner baseado em nuvem onde armazenar artefatos como esquemas, parceiros, certificados, mapas e contratos. Esses artefatos, em seguida, podem ser usados nos aplicativos de lógica criar fluxos de trabalho B2B. Antes de poder usar os artefatos em um aplicativo de lógica, você precisa vincular a sua conta de integração para o seu aplicativo de lógica. Após vinculá-las, seu aplicativo de lógica terá acesso a artefatos da conta integração.  

## <a name="why-should-you-use-enterprise-integration"></a>Por que você deve usar integração corporativa?
- Com a integração de empresa, você é capazes de armazenar todos os seus artefatos em um só lugar, qual é a sua conta de integração. 
- Você pode aproveitar o mecanismo de aplicativos lógica e todos os seus conectores para criar fluxos de trabalho B2B e integrar com 3º aplicativos SaaS de terceiros, aplicativos de locais, bem como aplicativos personalizados
- Você também pode aproveitar o Azure funções

## <a name="how-to-get-started-with-enterprise-integration"></a>Como começar com a integração do enterprise?
Você pode criar e gerenciar aplicativos de B2B usando o pacote de integração de Enterprise por meio do designer de aplicativos lógica no **portal do Azure**.  

Você também pode usar o [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "lógica aplicativos tópicos do PowerShell") para gerenciar seus aplicativos de lógica. 

Veja aqui uma visão geral das etapas necessárias antes de criar aplicativos no portal do Azure: ![imagem de visão geral](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Quais são alguns cenários comuns?

Integração corporativa dá suporte a esses padrões do setor:   

- EDI - troca de dados eletrônicos  
- EAI - integração de aplicativo empresarial  

## <a name="heres-what-you-need-to-get-started"></a>Veja aqui o que você precisa para começar
- Uma assinatura do Azure com uma conta de integração
- Visual Studio de 2015 para criar mapas e esquemas
- [Ferramentas de integração de aplicativos empresariais do Microsoft Azure lógica para Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Experimente
[Experimente agora](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) para implantar uma amostra totalmente operacional AS2 enviar e receber o aplicativo de lógica que usa os recursos de B2B dos aplicativos de lógica.

## <a name="learn-more-about"></a>Saiba mais sobre:
- [Contratos] (./app-service-logic-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração do enterprise")
- [Cenários do Business to Business (B2B)] (./app-service-logic-enterprise-integration-b2b.md "Aprenda a criar aplicativos de lógica com recursos de B2B")  
- [Certificados] (./app-service-logic-enterprise-integration-certificates.md "Saiba mais sobre certificados de integração do enterprise")
- [Arquivo simples codificação/decodificar] (./app-service-logic-enterprise-integration-flatfile.md "Saiba como codificar e decodificar o conteúdo do arquivo simples")  
- [Contas de integração] (./app-service-logic-enterprise-integration-accounts.md "Saiba mais sobre contas de integração")
- [Mapas] (./app-service-logic-enterprise-integration-maps.md "Saiba mais sobre a integração do enterprise mapas")
- [Parceiros] (./app-service-logic-enterprise-integration-partners.md "Saiba mais sobre os parceiros de integração de empresa")
- [Esquemas] (./app-service-logic-enterprise-integration-schemas.md "Saiba mais sobre os esquemas de integração do enterprise")
- [Validação de mensagem XML] (./app-service-logic-enterprise-integration-xml.md "Saiba como validar a mensagens XML com aplicativos de lógica")
- [Transformar XML] (./app-service-logic-enterprise-integration-transform.md "Saiba mais sobre a integração do enterprise mapas")
- [Conectores de integração do Enterprise] (../connectors/apis-list.md "Saiba mais sobre conectores de pacote de integração do enterprise")



