<properties 
   pageTitle="Criar um contrato de parceiro negociação no serviço de aplicativo do Azure | Microsoft Azure" 
   description="Criar negociação contratos de parceiros" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Criar um contrato de parceiro comercial   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Parceiros comerciais são as entidades envolvidas no B2B comunicações (para empresas). Quando dois parceiros estabelecer uma relação, isso é conhecido como um *contrato*. O contrato definido se baseia a comunicação os dois parceiros deseja atingir e é protocolo ou transporte específico. Os diversos protocolos B2B e transportes suportados pelo serviço de aplicativo do Azure incluem:

- AS2 (instrução de aplicabilidade 2)
- EDIFACT (intercâmbio de dados United Nations/eletrônicos para administração, comércio e transporte UN/EDIFACT ())
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>Aplicativos de API do BizTalk que suportam cenários de B2B
Os seguintes aplicativos de API habilitar esses recursos usando uma experiência avançada e intuitiva no Portal do Azure:


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk negociação gerenciamento de parceiro (TPM)
- Criação e gerenciamento de parceiros, perfis e identidades
- Armazenamento e gerenciamento de esquemas EDI
- Armazenamento e gerenciamento de certificados (usado no protocolo AS2)
- Criação e gerenciamento de contratos AS2
- Criação e gerenciamento de contratos EDIFACT (inclui lotes no lado de envio)
- Criação e gerenciamento de X12 contratos (inclui lotes no lado de envio)

![][1]


## <a name="as2-connector"></a>Conector AS2
- Executa AS2 contratos conforme definido na instância do aplicativo de API do TPM relacionada
- Emerge as informações de acompanhamento de processamento/AS2 para solução de problemas


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Executa EDIFACT contratos conforme definido na instância do aplicativo de API do TPM relacionada
- Emerge as informações de acompanhamento de processamento/EDIFACT para solução de problemas
- Fornece gerenciamento de estado de lotes (início e parada), conforme definido na EDIFACT ão na instância do aplicativo de API do TPM relacionada


## <a name="biztalk-x12"></a>BizTalk X12
- Executa X12 contratos conforme definido na instância do aplicativo de API do TPM relacionada 
- Revela X12 informações para solucionar problemas de processamento/acompanhamento
- Fornece gerenciamento de estado de lotes (início e parada), conforme definido na X12 ão na instância do aplicativo de API do TPM relacionada

Como mencionado anteriormente, o AS2, X 12 e EDIFACT API aplicativos exigem um aplicativo de API TPM à função conforme esperado.


## <a name="getting-started"></a>Guia de Introdução
Para criar acordos de parceiro de negociação:

1. Crie uma instância do conector **Gerenciamento de parceiros de negociação BizTalk** . Isso requer um banco de dados do SQL em branco à função. Antes de iniciar Certifique-se tiver um banco de dados em branco disponível e pronto para uso.
2. Carregar esquemas e certificados conforme exigido pelos contratos. Para fazer isso, a instância TPM criadas e depuração em parte 'Esquemas' e/ou 'Certificados' de navegação
3. Navegue até a instância TPM criada e passar para a parte de **parceiros**
4. Crie parceiros conforme desejado. Também editar os perfis conforme apropriado e adicionar as identidades necessárias
5. Agora, use a parte de **contratos** para criar contratos. Quando você cria um contrato, você deve selecionar o protocolo que será usado. As opções de configuração restantes se baseiam o protocolo que você selecionou.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
