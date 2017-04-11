<properties
   pageTitle="Tutorial: Processar faturas EDIFACT usando os serviços do Azure BizTalk | Serviços de BizTalk do Microsoft Azure"
   description="Como criar e configurar o aplicativo de conector de caixa ou API e usá-lo em um aplicativo de lógica no serviço de aplicativo do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Tutorial: Usar os serviços do Azure BizTalk faturas do processo EDIFACT
Você pode usar o Portal de serviços do BizTalk para configurar e implantar X12 e os contratos de EDIFACT. Neste tutorial, examinaremos como criar um contrato EDIFACT para trocar faturas entre parceiros comerciais. Este tutorial é escrito em torno de uma solução de negócios de ponta a ponta envolvendo dois parceiros comerciais, Northwind e Contoso que trocar EDIFACT mensagens.  

## <a name="sample-based-on-this-tutorial"></a>Exemplo com base neste tutorial
Este tutorial é escrito em torno de uma amostra, **Enviando EDIFACT faturas usando os Serviços BizTalk**, que está disponível para download na [Galeria de código do MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Você pode usar a amostra e percorra este tutorial para compreender como a amostra foi criada. Ou, você pode usar este tutorial para criar sua própria solução Terra para cima. Este tutorial é dirigido a segunda abordagem para que você compreender como esta solução foi criada. Além disso, tanto quanto possível, o tutorial é consistente com a amostra e usa os mesmos nomes para artefatos (por exemplo, esquemas, transformações) como usados na amostra.  

>[AZURE.NOTE] Como esta solução envolve enviando uma mensagem de uma ponte EAI para uma ponte EDI, ele reutiliza a amostra de [ponte de Serviços BizTalk encadeamento amostra](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  

## <a name="what-does-the-solution-do"></a>O que faz a solução?

Nesta solução, Northwind recebe faturas EDIFACT da Contoso. Essas faturas não estão em um formato EDI padrão. Portanto, antes de enviar a fatura para Northwind, ele deve ser transformado para um documento de fatura (também chamado de INVOIC) EDIFACT. No recebimento, Northwind deve processar a fatura EDIFACT e retornar uma mensagem de controle (também chamada de CONTRL) para Contoso.

![][1]  

Para obter este cenário de negócios, Contoso usa os recursos fornecidos com o Microsoft Azure BizTalk Services.

*   Contoso usa pontes EAI para transformar a fatura original para EDIFACT INVOIC.

*   A ponte EAI envia a mensagem a uma ponte de envio EDI implantada como parte de um contrato configurado no Portal de Serviços BizTalk.

*   A ponte de envio EDI processa o INVOIC EDIFACT e direciona para Northwind.

*   Depois de receber a fatura, Northwind retorna um CONTRL mensagem o EDI recebe ponte implantado como parte do contrato.  

> [AZURE.NOTE] Opcionalmente, essa solução também demonstra como usar o serviço de processamento em lotes de mensagens para enviar as faturas em lotes, em vez de enviar cada fatura separadamente.  

Para concluir o cenário, usamos filas de barramento de serviço para enviar fatura da Contoso Northwind ou receber a confirmação da Northwind. Essas filas podem ser criadas usando um aplicativo de cliente, que está disponível como um download e está incluído no pacote de exemplo que está disponível como parte deste tutorial.  

## <a name="prerequisites"></a>Pré-requisitos

*   Você deve ter um namespace barramento de serviço. Para obter instruções sobre como criar um namespace, consulte [como: criar ou modificar um Namespace de serviço do serviço barramento](https://msdn.microsoft.com/library/azure/hh674478.aspx). Vamos supor que você já tem um namespace de barramento de serviço provisionado, chamado **edifactbts**.

*   Você deve ter uma assinatura de Serviços BizTalk. Para obter instruções, consulte [criar um serviço BizTalk usando o portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=302280). Para este tutorial, vamos supor que você possui uma assinatura de Serviços BizTalk, chamada **contosowabs**.

*   Registre sua assinatura de Serviços BizTalk no Portal de serviços do BizTalk. Para obter instruções, consulte [Registrando uma implantação de serviço BizTalk no Portal de serviços do BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   Você deve ter o Visual Studio instalado.

*   Você deve ter o SDK dos serviços do BizTalk instalado. Você pode baixar o SDK do [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Etapa 1: Criar as filas de barramento de serviço  
Esta solução usa filas de barramento de serviço a troca de mensagens entre parceiros comerciais. Contoso e Northwind enviar mensagens às filas de onde as pontes EAI e/ou EDI usá-los. Para esta solução, você precisará de três filas de barramento de serviço:

*   **northwindreceive** – Northwind recebe a fatura da Contoso sobre essa fila.

*   **contosoreceive** – Contoso recebe a confirmação da Northwind sobre essa fila.

*   **suspenso** – todos suspenso mensagens são roteadas para essa fila. Mensagens estão suspensos se eles falharem durante o processamento.

Você pode criar essas filas de barramento de serviço usando um aplicativo de cliente incluído no pacote de amostra.  

1.  Do local onde você baixou o exemplo, abra o **Tutorial enviando faturas usando BizTalk serviços EDI Bridges.sln**.

2.  Pressione **F5** para compilar e iniciar o aplicativo **Cliente do Tutorial** .

3.  Na tela, insira o namespace ACS de barramento de serviço, nome do emissor e chave emissor.

    ![][2]  
4.  Uma caixa de mensagem solicita que três filas serão criadas no seu namespace barramento de serviço. Clique em **Okey**.

5.  Deixe o cliente de Tutorial em execução. Abrir, clique em **Barramento de serviço** > **_seu namespace barramento de serviço_** > **filas**e verificar se as três filas são criadas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Etapa 2: Criar e implantar negociação contrato de parceiro
Crie um contrato de parceiro negociação entre Contoso e Northwind. Um contrato de parceiro negociação define um contrato comercial entre os parceiros de duas negócios, como qual esquema de mensagem a ser usado, qual protocolo mensagens usar, etc. Um contrato de parceiro negociação inclui duas pontes EDI, um para enviar mensagens para parceiros comerciais (chamados de **ponte EDI enviar**) e outro para receber mensagens de parceiros comerciais (chamados de **ponte EDI receber**).

No contexto desta solução, a ponte de envio EDI corresponde ao lado do envio do contrato e é usada para enviar a fatura EDIFACT da Contoso para Northwind. Da mesma forma, a ponte de recebimento EDI corresponde ao lado do recebimento do contrato e é usado para confirmações de recebimento Northwind.  

### <a name="create-the-trading-partners"></a>Criar os parceiros comerciais

Começar, crie parceiros comerciais da Contoso e Northwind.  

1.  No Portal de serviços do BizTalk, na guia **parceiros** , clique em **Adicionar**.

2.  Na página novo parceiro, digite **Contoso** como um nome de parceiro e clique em **Salvar**.

3.  Repita a etapa para criar o segundo parceiro, **Northwind**.  

### <a name="create-the-agreement"></a>Criar o contrato
Acordos de parceiro de negociação são criados entre os perfis de negócios dos parceiros comerciais. Esta solução usa os perfis de parceiro padrão que são criados automaticamente quando criamos os parceiros.  

1.  No Portal de serviços do BizTalk, clique em **contratos** > **Adicionar**.

2.  Na página de **Configurações gerais** do novo contrato, especifique os valores conforme mostrado na imagem abaixo e, em seguida, clique em **continuar**.

    ![][3]  

    Após você clicar em **continuar**, duas guias, **As configurações de receber** e **Enviar** se tornam disponíveis.

3.  Crie o contrato de envio entre Contoso e Northwind. Este contrato controla como Contoso envia a fatura EDIFACT para Northwind.

    1.  Clique em **Enviar configurações**.

    2.  Manter os valores padrão nas guias **URL de entrada**, **transformar**e **lotes** .

    3.  Na guia **protocolo** , sob a seção de **esquemas** , carregue o esquema de **EFACT_D93A_INVOIC.xsd** . Este esquema está disponível com o pacote de amostra.

        ![][4]  
    4.  Na guia **Transporte** , especifique os detalhes para as filas de barramento de serviço. Para o contrato de envio lado, podemos usar fila **northwindreceive** para enviar a fatura EDIFACT para Northwind e fila **suspenso** para rotear todas as mensagens que falham durante o processamento e são suspensos. Você criou essas filas no **etapa 1: criar as filas de barramento de serviço** (neste tópico).

        ![][5]  

        Em **configurações de transporte > tipo de transporte** e **configurações de suspensão de mensagens > tipo de transporte**, selecione barramento de serviço do Azure e forneça os valores conforme mostrado na imagem.

4.  Crie o contrato de recebimento entre Contoso e Northwind. Este contrato controla como Contoso recebe a confirmação da Northwind.

    1.  Clique em **configurações de recebimento**.

    2.  Manter os valores padrão nas guias **transformar** e **Transporte** .

    3.  Na guia **protocolo** , sob a seção de **esquemas** , carregue o esquema de **EFACT_4.1_CONTRL.xsd** . Este esquema está disponível com o pacote de amostra.

    4.  Na guia **rota** , crie um filtro para garantir que apenas confirmações Northwind são roteadas para Contoso. Em **Configurações da rota**, clique em **Adicionar** para criar o filtro de roteamento.

        ![][6]  
        1.  Forneça valores para o **Nome da regra**, **rota regra**e **destino rota** conforme mostrado na imagem.

        2.  Clique em **Salvar**.

    5.  Na guia **rotear** novamente, especifique onde confirmações suspensas (confirmações que falham durante o processamento) são roteadas para. Definir o tipo de transporte para barramento de serviço do Azure, direcionar o tipo de destino para **fila**, tipo de autenticação de **Assinatura de acesso compartilhado** (SAS), forneça a cadeia de conexão SAS do namespace barramento de serviço e insira o nome da fila como **suspenso**.

5.  Por fim, clique em **implantar** para implantar o contrato. Observe os pontos de extremidade onde enviar e receber contratos são implantados.

    *   Na guia **Configurações de envio** , em **URL de entrada**, observe o ponto de extremidade. Para enviar uma mensagem da Contoso para Northwind usando a ponte de envio EDI, você deve enviar uma mensagem para esse ponto de extremidade.

    *   Na guia **Configurações de recebimento** , em **Transporte**, observe o ponto de extremidade. Para enviar uma mensagem de Northwind para Contoso usando o EDI receber ponte, você deve enviar uma mensagem para esse ponto de extremidade.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Etapa 3: Criar e implantar o projeto de Serviços BizTalk

Na etapa anterior, você implantado enviar EDI e recebe contratos para processar faturas EDIFACT e confirmações. Esses contratos podem apenas as mensagens de processo que estão em conformidade com o esquema de mensagem EDIFACT padrão. No entanto, por cenário para esta solução, Contoso envia uma fatura para Northwind em um esquema de proprietário interno. Portanto, antes que a mensagem é enviada à ponte enviar EDI, ele deve transformado do esquema interno no esquema de fatura EDIFACT padrão. O projeto de BizTalk serviços EAI faz isso.

O projeto de Serviços BizTalk, **InvoiceProcessingBridge**, que transforma a mensagem também está incluído como parte do exemplo que você baixou. O projeto inclui os seguintes artefatos:

*   **INHOUSEINVOICE. XSD** – esquema da fatura interna que é enviada para Northwind.

*   **EFACT_D93A_INVOIC. XSD** – esquema da fatura EDIFACT padrão.

*   **EFACT_4.1_CONTRL. XSD** – esquema da confirmação de EDIFACT Northwind envia para Contoso.

*   **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – a transformação que mapeia o esquema de fatura interna para o esquema de fatura EDIFACT padrão.  

### <a name="create-the-biztalk-services-project"></a>Crie o projeto de Serviços BizTalk
1.  Na solução Visual Studio, expanda o projeto InvoiceProcessingBridge e abra o arquivo **MessageFlowItinerary.bcs** .

2.  Clique em qualquer lugar na tela e defina a **URL do serviço BizTalk** na caixa de propriedade para especificar o nome da sua assinatura de Serviços BizTalk. Por exemplo, `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  Na caixa de ferramentas, arraste uma **Ponte de One-Way de Xml** à tela. Defina as propriedades de **Entidade nome** e **Endereço relativo** da ponte para **ProcessInvoiceBridge**. Clique duas vezes **ProcessInvoiceBridge** para abrir a superfície de configuração de ponte.

4.  Dentro da caixa de **Tipos de mensagem** , clique no sinal de adição (**+**) botão para especificar o esquema da mensagem de entrada. Como a mensagem de entrada para a ponte EAI é sempre a fatura interna, defina **INHOUSEINVOICE**.

    ![][8]  
5.  Clique na forma de **Transformação de Xml** e, na caixa da propriedade, para a propriedade de **mapas** , clique no botão de reticências (**…**). Na caixa de diálogo **Seleção de mapas** , selecione o arquivo de transformação **INHOUSEINVOICE_to_D93AINVOIC** e clique em **Okey**.

    ![][9]  
6.  Voltar ao **MessageFlowItinerary.bcs**e da caixa de ferramentas, arraste um **Ponto de extremidade de serviço de externos bidirecional** à direita da **ProcessInvoiceBridge**. Defina a propriedade de seu **Nome de entidade** como **EDIBridge**.

7.  No Gerenciador de soluções, expanda a **MessageFlowItinerary.bcs** e clique duas vezes no arquivo **EDIBridge.config** . Substitua o conteúdo do **EDIBridge.config** a seguir.

    > [AZURE.NOTE] Por que eu preciso para editar o arquivo. config? O ponto de extremidade do serviço externo que adicionamos à tela designer ponte representa as pontes EDI que estamos implantados anteriormente. Pontes EDI são pontes bidirecionais, com o envio de e recebem lado. No entanto, a ponte EAI que adicionamos ao designer de ponte é uma ponte unidirecional. Portanto, para lidar com os padrões de troca de mensagem diferente das duas pontes, podemos usar um comportamento de ponte personalizado, incluindo sua configuração no arquivo. config. Além disso, o comportamento personalizado também trata a autenticação para o ponto de extremidade de ponte de envio do EDI. Esse comportamento personalizado está disponível como uma amostra separada em [ponte de Serviços BizTalk encadeamento sample - EAI EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Essa solução reutiliza a amostra.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Atualizar o arquivo EDIBridge.config para incluir detalhes de configuração

    *   Em _<behaviors>_, forneça o ACS namespace e a chave associada a assinatura de Serviços BizTalk.

    *   Em _<client>_, forneça o ponto de extremidade onde o contrato de envio EDI é implantado.

    Salve as alterações e feche o arquivo de configuração.

9.  Na caixa de ferramentas, clique no **conector** e ingresse os componentes **ProcessInvoiceBridge** e **EDIBridge** . Selecione o conector e, na caixa de propriedades, defina a **Condição de filtro** para **Corresponder tudo**. Isso garante que todas as mensagens processadas por ponte EAI são roteadas para a ponte EDI.

    ![][10]  
10.  Salve alterações para a solução.  

### <a name="deploy-the-project"></a>Implantar o projeto

1.  No computador onde você criou o projeto de Serviços BizTalk, baixe e instale o certificado SSL de sua assinatura de Serviços BizTalk. De, em Serviços BizTalk, clique em **Painel de controle**e clique em **Baixar um certificado SSL**. Clique duas vezes no certificado e siga os prompts para concluir a instalação. Verifique se que você instalar o certificado no repositório de certificados de **Autoridades de certificação raiz confiável** .

2.  No Visual Studio Solution Explorer, clique com botão direito no projeto **InvoiceProcessingBridge** e, em seguida, clique em **implantar**.

3.  Forneça os valores conforme mostrado na imagem e clique em **implantar**. Você pode obter as credenciais do ACS para Serviços BizTalk clicando em **Informações de Conexão** no painel de Serviços BizTalk.

    ![][11]  

    A partir do painel de saída, copie o ponto de extremidade onde a ponte EAI for implantada, por exemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Posteriormente, você precisará esta URL de ponto de extremidade.  

## <a name="step-4-test-the-solution"></a>Etapa 4: Testar a solução


Neste tópico, examinaremos como testar a solução usando o aplicativo de **Cliente do Tutorial** fornecido como parte da amostra.  

1.  No Visual Studio, pressione F5 para iniciar o **Tutorial cliente**.

2.  A tela deve ter os valores pré-populadas da etapa onde criamos filas barramento de serviço. Clique em **Avançar**.

3.  Na próxima janela, forneça as credenciais do ACS para assinatura de Serviços BizTalk e os pontos de extremidade onde EAI e EDI (receber) pontes são implantados.

    Você tinha copiado o ponto de extremidade de ponte EAI na etapa anterior. Para EDI recebe o ponto de extremidade de ponte, no Portal de Serviços BizTalk, vá para o contrato > configurações de recebimento > transporte > ponto de extremidade.

    ![][12]  
4.  Na próxima janela, em Contoso, clique no botão de **Enviar internos fatura** . No arquivo de caixa de diálogo Abrir, abra o arquivo INHOUSEINVOICE.txt. Examinar o conteúdo do arquivo e clique em **Okey** para enviar a fatura.

    ![][13]  
5.  Em alguns segundos a fatura é recebida no Northwind. Clique no link **Exibir mensagem** para ver a fatura recebida pelo Northwind. Observe como a fatura recebida pelo Northwind está no esquema EDIFACT padrão enquanto o enviadas por Contoso foi um esquema interno.

    ![][14]  
6.  Selecione a fatura e clique em **Enviar confirmação**. Na caixa de diálogo pop-up, observe que a ID de troca é mesma da fatura recebida e a confirmação está sendo enviado. Clique em Okey na caixa de diálogo **Enviar confirmação** .

    ![][15]  
7.  Em alguns segundos, a confirmação é recebida com êxito da Contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Etapa 5 (opcional): fatura enviar EDIFACT em lotes 
Pontes de EDI do BizTalk Services também suporta processamento em lotes de mensagens de saída. Esse recurso é útil para receber parceiros que preferem receber um lote de mensagens (determinado critério da reunião) em vez de mensagens individuais.

O aspecto mais importante ao trabalhar com lotes é a versão real do lote, também chamado os critérios de lançamento. Os critérios de lançamento podem ser baseados em como o parceiro recebimento quer receber mensagens. Se lotes estiver habilitada, a ponte EDI não enviar a mensagem de saída para o parceiro de recebimento até que o critério de lançamento é atendida. Por exemplo, um critério lotes com base em despachos de tamanho de mensagem um lote somente quando ' n' mensagens forem colocados em lote. Um critério de lote também pode ser um baseada em tempo, para que um lote é enviado em um horário fixo diariamente. Nesta solução, tentamos os critérios com base do tamanho da mensagem.

1.  No Portal de serviços do BizTalk, clique no contrato que você criou anteriormente. Clique em configurações de enviar > Lote > Adicionar lote.

2.  Para nome do lote, insira **InvoiceBatch**, forneça uma descrição e clique em **Avançar**.

3.  Especifica critérios lote, que define quais mensagens devem ser em lote. Nesta solução, podemos lote todas as mensagens. Então, selecione a opção usar definições opção avançada e insira **1 = 1**. Esta é uma condição que será sempre true e, portanto, todas as mensagens serão em lote. Clique em **Avançar**.

    ![][17]  
4.  Especifica critérios lote lançamento. Na caixa de soltar, selecione **MessageCountBased**e para **contagem**, especifique **3**. Isso significa que um lote de três mensagens será enviado para o Northwind. Clique em **Avançar**.

    ![][18]  
5.  Revise o resumo e clique em **Salvar**. Clique em **implantar** para reimplantar o contrato.

6.  Volte para o **Cliente do Tutorial**, clique em **Enviar fatura internos**e siga os prompts para enviar a fatura. Você notará que nenhuma fatura é recebida no Northwind porque o tamanho do lote não for atendido. Repita esta etapa mais duas vezes, para que você tenha três da fatura mensagens enviadas para Northwind. Isso atenda aos critérios de lançamento do lote de 3 mensagens e agora você deve ver uma fatura em Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

