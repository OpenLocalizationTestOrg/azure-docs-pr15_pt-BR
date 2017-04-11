<properties 
    pageTitle="Como usar o Twilio para voz e SMS (Java) | Microsoft Azure" 
    description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos em Java." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Como usar o Twilio para voz e os recursos do SMS em Java

Este guia demonstra como executar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abordados incluem fazer um telefonema e enviar uma mensagem de serviço de mensagem curta (SMS). Para obter mais informações sobre Twilio e usar voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é uma API de serviço web de telefonia que permite usar seus idiomas de web existente e habilidades para criar aplicativos de SMS e voz. Twilio é um serviço de terceiros (não um recurso do Azure e não um produto Microsoft).

**Voz Twilio** permite que os seus aplicativos para fazer e receber chamadas telefônicas. **Twilio SMS** permite seus aplicativos para fazer e receber mensagens SMS. **Cliente Twilio** permite que os seus aplicativos permitir a comunicação de voz usando conexões existentes de Internet, incluindo as conexões móveis.

## <a id="Pricing"></a>Twilio preços e ofertas especiais
Informações sobre preços Twilio estão disponíveis em [Twilio preços] [twilio_pricing]. Azure clientes receberão uma [oferta especial][special_offer]: um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para inscrever-se para esta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece voz e funcionalidade do SMS para aplicativos. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Bibliotecas de API do Twilio] [twilio_libraries].

Principais aspectos da API Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <a id="Verbs"></a>Verbos Twilio
A API utiliza Twilio verbos; Por exemplo, o ** &lt;diga&gt; ** verbo instrui Twilio audível entregar uma mensagem em uma chamada. 

A seguir está uma lista de verbos Twilio.

* ** &lt;Discagem&gt;**: conecta o chamador para outro telefone.
* ** &lt;Reunir&gt;**: coleta dígitos numéricos inseridos no teclado do telefone.
* ** &lt;Desligamento&gt;**: termina uma chamada.
* ** &lt;Reproduzir&gt;**: reproduz um arquivo de áudio.
* ** &lt;Pausa&gt;**: aguarda silenciosamente um número especificado de segundos.
* ** &lt;Registro&gt;**: registra voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* ** &lt;Redirecionar&gt;**: transfere o controle de uma chamada ou SMS para o TwiML em uma URL diferente.
* ** &lt;Rejeitar&gt;**: rejeita uma chamada de entrada para o seu número de Twilio sem cobrança você
* ** &lt;Diga&gt;**: converte texto em fala que é feito em uma chamada.
* ** &lt;Sms&gt;**: envia uma mensagem SMS.

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML, com base em verbos Twilio que informam Twilio de como uma chamada de processo ou SMS.

Como exemplo, a seguinte TwiML seria converter o texto **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando seu aplicativo chama a API Twilio, um dos parâmetros a API é a URL que retorna a resposta TwiML. Para fins de desenvolvimento, você pode usar URLs fornecidos Twilio para fornecer as respostas de TwiML usadas por seus aplicativos. Você também pode hospedar seus próprio URLs para produzir as respostas TwiML e outra opção é usar o objeto **TwiMLResponse** .

Para obter mais informações sobre verbos Twilio, seus atributos e TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API Twilio, consulte [API de Twilio] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta de Twilio
Quando você estiver pronto para obter uma conta de Twilio, inscreva-se ao [Tentar Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Quando você se inscrever para uma conta de Twilio, você receberá uma ID de conta e um token de autenticação. Ambos serão necessárias para fazer chamadas de API de Twilio. Para impedir o acesso não autorizado à sua conta, proteger o token de autenticação. Sua ID de conta e autenticação de token ficam visíveis na [página da conta Twilio] [twilio_account], no campos rotuladas **SID da conta** e **AUTH TOKEN**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo Java
1. Obtenha o JAR Twilio e adicioná-lo ao seu caminho de compilação de Java e seu conjunto de implantação de guerra. Em [https://github.com/twilio/twilio-java][twilio_java], você pode baixar as fontes GitHub e criar seu próprios JAR ou baixar um JAR pré-criados (com ou sem dependências).
2. Certifique-se de chave **cacerts** do seu JDK contém o certificado de autoridade de certificação segura Equifax com 67:CB:9 de impressão digital MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de autoridade de certificação do certificado para o [https://api.twilio.com] [ twilio_api_service] serviço, que é chamado quando você usar Twilio APIs. Para obter informações sobre como garantir do seu JDK **cacerts** repositório de chaves contém o certificado de CA correto, consulte [Adicionar um certificado ao armazenamento de certificados de autoridade de certificação Java][add_ca_cert].

Instruções detalhadas para usar a biblioteca de cliente Twilio for Java estão disponíveis em [como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas de Twilio
Em seu código, você pode adicionar instruções **Importar** na parte superior dos seus arquivos de origem para os pacotes de Twilio ou classes que você deseja usar em seu aplicativo. 

Para arquivos de origem de Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Para arquivos de origem de página de servidor de Java (JSP):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Dependendo de qual Twilio pacotes ou classes que você deseja usar, suas instruções **Importar** podem ser diferentes.

## <a id="howto_make_call"></a>Como: fazer uma chamada de saída
A seguir mostra como fazer uma chamada de saída usando a classe **CallFactory** . Este código também usa um site fornecidos Twilio para retornar a resposta de linguagem de marcação de Twilio (TwiML). Substitua os valores **de** e **para** números de telefone e verifique o número de telefone **de** para a sua conta de Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Para obter mais informações sobre os parâmetros passados para o método **CallFactory.create** , consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Conforme mencionado, este código usa um site fornecidos Twilio para retornar a resposta de TwiML. Você pode usar seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como fornecer TwiML respostas em um aplicativo Java no Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
A seguir mostra como enviar uma mensagem SMS usando a classe **SmsFactory** . **Do** número, **4155992671**, é fornecido pela Twilio para contas de avaliação para enviar mensagens SMS. O número **para** deve ser verificado para sua conta de Twilio antes de executar o código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Para obter mais informações sobre os parâmetros passados para o método **SmsFactory.create** , consulte [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Como: fornecer respostas de TwiML de seu próprio site
Quando seu aplicativo inicia uma chamada para a API Twilio, por exemplo por meio do método de **CallFactory.create** , Twilio enviará sua solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa o URL fornecido Twilio [http://twimlets.com/message][twimlet_message_url]. (Enquanto TwiML destina-se ao uso pelos serviços da Web, você pode exibir o TwiML no seu navegador. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver um vazio ** &lt;resposta&gt; ** elemento; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] para ver uma ** &lt;resposta&gt; ** elemento que contém uma ** &lt;diga&gt; ** elemento.)

Em vez de depender a URL fornecido Twilio, você pode criar seu próprio site de URL que retorna respostas HTTP. Você pode criar o site em qualquer idioma que retorna respostas HTTP; Este tópico pressupõe que você vai ser hospedando a URL em uma página JSP.

A seguinte página JSP resulta em uma resposta de TwiML que diz **Olá, mundo** na chamada.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

A seguinte página JSP resulta em uma resposta de TwiML que diz algum texto, tem várias pausas e diz informações sobre a versão da API Twilio e o nome da função Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

O parâmetro **ApiVersion** está disponível em solicitações de voz do Twilio (não solicitações SMS). Para ver os parâmetros de solicitação disponível para solicitações SMS e voz Twilio, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. A variável de ambiente de **nome de função** está disponível como parte de uma implantação do Azure. (Se você quiser adicionar variáveis de ambiente personalizado para que eles podem ser selecionados do **System.getenv**, consulte a seção de variáveis de ambiente em [Diversas configurações de função][misc_role_config_settings].)

Quando tiver sua página JSP configurada para fornecer TwiML respostas, use a URL da página JSP como a URL passada para o método **CallFactory.create** . Por exemplo, se você tiver um aplicativo Web chamado MyTwiML implantado em um Azure hospedado no serviço e o nome da página JSP é mytwiml.jsp, a URL pode ser passada para **CallFactory.create** conforme mostrado no seguinte:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Outra opção para responder com TwiML é por meio da classe **TwiMLResponse** , que está disponível no pacote **com.twilio.sdk.verbs** .

Para obter informações adicionais sobre como usar Twilio no Azure com Java, veja [como fazer uma chamada telefônica usando Twilio em um aplicativo Java no Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Como: usar serviços de Twilio adicionais
Além de exemplos mostrados aqui, Twilio oferece APIs baseado na web que você pode usar para aumentar a funcionalidade de Twilio adicional de seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Próximas etapas
Agora que você aprendeu as Noções básicas do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de segurança de Twilio] [twilio_security_guidelines]
* [De Twilio HowTo e códigos de exemplo] [twilio_howtos]
* [Tutoriais Twilio][twilio_quickstarts] 
* [Twilio no GitHub] [twilio_on_github]
* [Fale com o suporte de Twilio] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
