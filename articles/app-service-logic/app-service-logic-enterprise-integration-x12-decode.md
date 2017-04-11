<properties 
    pageTitle="Saiba mais sobre Enterprise Pack integração decodificar X12 mensagem Connctor | Serviço de aplicativo do Microsoft Azure | Microsoft Azure" 
    description="Saiba como usar parceiros com aplicativos do pacote de integração de empresa e lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-decode-x12-message"></a>Introdução ao decodificar X12 mensagem

Valida EDI e propriedades específicas do parceiro, gera documento XML para cada conjunto de transação e confirmação de transação processada.

## <a name="create-the-connection"></a>Criar a conexão

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure; Você pode criar uma [conta gratuita](https://azure.microsoft.com/free)

* Uma conta de integração é necessário para usar o conector de mensagem decodificar X12. Ver detalhes sobre como criar uma [Conta de integração](./app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](./app-service-logic-enterprise-integration-partners.md) e [X12 contrato](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>Conectar-se a mensagem de decodificar X12 usando as seguintes etapas:

1. [Criar um aplicativo de lógica](./app-service-logic-create-a-logic-app.md) fornece um exemplo

2. Esse conector não ter disparadores. Use outros disparadores para iniciar o aplicativo de lógica, como um gatilho de solicitação.  No criador de aplicativo de lógica, adicione um disparador e adicione uma ação.  Selecione Microsoft Mostrar listam de APIs gerenciadas no menu suspenso e insira "x12" na caixa de pesquisa.  Selecione X12 – decodificar X12 mensagem

    ![Pesquisar x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)  

3. Se você ainda não criou anteriormente quaisquer conexões a integração de conta, você será solicitado para os detalhes de conexão

    ![conexão de conta de integração](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)    

4. Insira os detalhes da conta de integração.  Propriedades com um asterisco são necessárias

  	| Propriedade | Detalhes |
  	| -------- | ------- |
  	| Nome de Conexão * | Insira um nome para sua conexão |
  	| Conta de integração * | Insira o nome da conta de integração. Certifique-se de que sua conta de integração e o aplicativo de lógica estão no mesmo local Azure |

    Após a conclusão, os detalhes da conexão aparência semelhantes à seguinte
    
    ![conexão de conta de integração criado](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png) 

5. Selecione **criar**
    
6. Observe que a conexão tiver sido criado.

    ![detalhes de conexão da conta de integração](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png) 

7. Selecione X12 simples mensagem de arquivo para decodificar

    ![fornecer campos obrigatórios](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>X12 decodificar faz acompanhando

* Valida o envelope contra negociação contrato de parceiro
* Gera um documento XML para cada conjunto de transação.
* Valida EDI e propriedades específicas do parceiro
    * Validação estrutural EDI e validação de esquema estendido
    * Validação da estrutura do envelope troca.
    * Validação de esquema do envelope em relação ao esquema de controle.
    * Validação de esquema dos elementos de dados do conjunto de transação em relação ao esquema de mensagem.
    * Validação de EDI realizada em elementos de dados do conjunto de transação 
* Verifica se os números de controle de conjunto de troca, de grupo e transação não estão duplicatas
    * Verifica o número de controle de troca contra cruzamentos recebidos anteriormente.
    * Verifica o número de controle de grupo contra outros números de controle de grupo na troca.
    * Verifica que a transação definir o número de controle em relação a outros números de controle de conjunto de transação nesse grupo.
* Converte a troca inteira em XML 
    * Troca de divisão como conjuntos de transação - suspender conjuntos de transação no erro: analisa cada transação conjunto em um intercâmbio em um documento XML separado. Se uma ou mais transação define na troca falham na validação, X12 decodificar suspende apenas os conjuntos de transação.
    * Troca de divisão como conjuntos de transação - suspender troca no erro: analisa cada transação conjunto em um intercâmbio em um documento XML separado.  Se uma ou mais transação define na troca falham na validação, X12 decodificar suspende a troca inteira.
    * Preservar troca - suspender conjuntos de transação no erro: cria um documento XML para a troca de lote inteiro. X12 decodificar suspende apenas os conjuntos de transação que falham na validação, enquanto continua processar todos os outra transação conjuntos
    * Preservar troca - suspender troca no erro: cria um documento XML para a troca de lote inteiro. Se uma ou mais transação define na troca falham na validação, X12 decodificar suspende a troca inteira, 
* Gera uma confirmação técnicos e/ou funcionais (se configurado).
    * Uma confirmação técnico gera como resultado de validação de cabeçalho. A confirmação técnica reporta o status do processamento de um cabeçalho de troca e marcador pelo destinatário endereço.
    * Uma confirmação funcional gera como resultado de validação de corpo. Cada erro encontrado durante o processamento do documento recebido de relatórios a confirmação funcional

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o pacote de integração do Enterprise] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa") 


