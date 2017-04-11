<properties 
   pageTitle="Criando um processo de B2B no serviço de aplicativo do Azure | Microsoft Azure" 
   description="Visão geral de como criar um processo de negócio-a-negócio" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Criando um processo de B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Cenário de negócios 
Contoso e Northwind estão dois parceiros de negócios. Contoso (o revendedor) envia ordens de compra para Northwind (o fornecedor) por um transporte nível do setor como AS2. Northwind armazena todos os pedidos de entrada em seu armazenamento em nuvem. As ordens de compra são mensagens XML entre esses dois parceiros. Depois que a mensagem é armazenada no armazenamento em nuvem do Northwind processos internos do Northwind manipulam a ordem desse ponto em.
 
O objetivo deste tutorial é estabelecer como Northwind pode estabelecer um processo de negócios via que possa receber mensagens (ordens de compra no XML) de seu parceiro Contoso pela AS2 e depois mantê-lo em seu armazenamento em nuvem.


## <a name="capabilities-demonstrated"></a>Recursos demonstrados 
Este tutorial ajuda a apresentar os seguintes recursos: 

- **Transporte de mensagem**: O revendedor e o fornecedor podem estar em diferentes plataformas, mas eles ainda podem obter a comunicação entre os dois. Neste tutorial estão se comunicando sobre AS2 (aplicabilidade Statement 2). AS2 é uma maneira popular de dados de transporte entre parceiros comerciais em comunicações para empresas.
- **Persistência de dados**: quando a mensagem foi recebida sobre AS2 depois Northwind deseja mantê-lo antes de processamento posterior. Ele pode usar um conector para manter mensagens em seu armazenamento em nuvem. Neste tutorial Blobs do Azure está sendo utilizada como o armazenamento em nuvem para Northwind.
- **Criando um processo de negócios**: em um fluxo, a vários aplicativos de API podem ser costura juntos para obter um resultado de negócios conforme demonstrou aqui.


## <a name="before-you-begin"></a>Antes de começar
Este tutorial supõe que você tenha um entendimento básico de serviços de aplicativo do Azure, saber como criar aplicativos de API e compor um fluxo.


## <a name="steps-to-achieve-the-business-scenario"></a>Etapas para atingir o cenário de negócios
**Criar e configurar os aplicativos da API necessários**

1. Crie uma instância do **Conector de Blob de armazenamento do Azure**. Isso requer as credenciais para uma conta de armazenamento do Azure. Certifique-se de que ele está pronto antes de você começar a criar isso.
2. Crie uma instância do **Gerenciamento de parceiros de negociação BizTalk**. Isso requer um banco de dados do SQL em branco à função. Certifique-se de que ele está pronto antes de você começar a criar isso.
3. Crie uma instância do **Conector de AS2**. Isso também requer um banco de dados do SQL em branco à função. Certifique-se de que ele está pronto antes de você começar a criar isso. Além disso, se você quiser arquivar mensagens como parte do AS2 processamento, você pode fornecer credenciais para um Blob do Azure durante sua criação.
4. Configure o serviço TPM (gerenciamento de parceiro negociação) que é criado:  
    1. Navegue até a instância do serviço TPM criado como parte das etapas acima.
    2. Use a opção de **parceiros** em *componentes* para **Adicionar** um novo parceiro chamado **Contoso** e no seu perfil adicione a identidade de AS2 necessária.
    3. Use a opção de **parceiros** em *componentes* para **Adicionar** um novo parceiro chamado **Northwind** e em seu perfil adicione a identidade de AS2 necessária.
    4. Use a opção de **contratos** em *componentes* para **Adicionar** um novo AS2 contrato entre Northwind e Contoso. Northwind será o parceiro hospedado aqui e Contoso será o parceiro de convidado. Conforme apropriado configurar autenticação, criptografia, compactação e confirmações durante criação este contrato. No caso de certificados precisam ser usada, eles podem ser carregados por meio da opção de **certificados** ao navegar o serviço TPM que será criado.


## <a name="create-a-flow--business-process"></a>Criar um fluxo / processos comerciais
1. Crie um novo fluxo no qual a primeira etapa é AS2. Arraste e solte o **Conector AS2** e escolha a instância que já criada. Escolha disparador como a funcionalidade:  
    ![][1]  
2. Em seguida arraste e solte o **Conector de Blob de armazenamento do Azure** e escolha a instância que já criada. Escolha ação como a funcionalidade e no que, selecione **Carregar Blob** como a funcionalidade desejada. Configure conforme apropriado.
3. Agora crie/implante o fluxo.


## <a name="message-processing--troubleshooting"></a>Processamento de mensagem e solução de problemas
1. É hora de testar o fluxo que podemos implantou. Envie que mensagens de XML quebrado em AS2 (conforme o contrato de AS2 criado acima) para o ponto de extremidade AS2 expos pela AS2Connector instância que você criou. Talvez você precise configurar a autenticação para o ponto de extremidade para que fique publicamente acessível.
2. Informações de execução sobre o fluxo é expos, navegue para o fluxo e, em seguida, revisão para a instância de fluxo que você tem executada
3. Para obter informações de processamento de AS2, navegue até a instância de AS2Connector envolvida e siga passando para a parte de acompanhamento. Você pode usar os filtros envolvidos restringir a exibição para as informações que desejar.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
