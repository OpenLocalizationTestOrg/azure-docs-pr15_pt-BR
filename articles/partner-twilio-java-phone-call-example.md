<properties 
    pageTitle="Como fazer uma chamada telefônica de Twilio (Java) | Microsoft Azure" 
    description="Saiba como fazer uma chamada telefônica de uma página da web usando Twilio em um aplicativo de Java no Azure." 
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

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Como fazer uma chamada telefônica usando Twilio em um aplicativo de Java no Azure 

O exemplo a seguir mostra como você pode usar Twilio para fazer uma chamada de uma página da web hospedado no Azure. O aplicativo resultante solicitará que o usuário para valores de telefonema, conforme mostrado na captura de tela a seguir.

![Formulário de chamada Azure usando Twilio e Java][twilio_java]

Você precisará fazer o seguinte para usar o código neste tópico:

1. Adquirir uma conta de Twilio e a autenticação de token. Para começar a usar Twilio, avaliar preço no [http://www.twilio.com/pricing][twilio_pricing]. Você pode se inscrever em [https://www.twilio.com/try-twilio][try_twilio]. Para obter informações sobre a API fornecida pelo Twilio, consulte [http://www.twilio.com/api][twilio_api].
2. Obter o Twilio JAR. Em [https://github.com/twilio/twilio-java][twilio_java_github], você pode baixar as fontes GitHub e criar seu próprios JAR ou baixar um JAR pré-criados (com ou sem dependências).
O código neste tópico foi escrito usando o JAR TwilioJava 3.3.8 com dependências pré-criados.
3. Adicione o JAR ao seu caminho de compilação de Java.
4. Se você estiver usando o Eclipse para criar este aplicativo Java, inclua o JAR Twilio em seu arquivo de implantação de aplicativo (guerra) usando o recurso de conjunto de implantação do Eclipse. Se você não estiver usando o Eclipse para criar este aplicativo Java, verifique o JAR Twilio é incluído em função do Azure mesma como seu aplicativo Java e adicionado ao caminho de classe do seu aplicativo.
5. Certifique-se de que sua chave cacerts contém o certificado de autoridade de certificação segura Equifax com 67:CB:9 de impressão digital MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (o número de série é 35:DE:F4:CF e a impressão digital SHA1 é D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Este é o certificado de autoridade de certificação do certificado para o [https://api.twilio.com] [ twilio_api_service] serviço, que é chamado quando você usar Twilio APIs. Para obter informações sobre como adicionar este certificado de autoridade de certificação ao armazenamento de cacert do seu JDK, consulte [Adicionando um certificado para o armazenamento de certificado de autoridade de certificação Java][add_ca_cert].

Além disso, familiaridade com as informações na [criação de um Hello World aplicativo usando o Kit de ferramentas do Azure para Eclipse][azure_java_eclipse_hello_world], ou com outras técnicas para hospedar aplicativos de Java no Azure se você não estiver usando o Eclipse, é altamente recomendável.

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da web para fazer uma chamada

O código a seguir mostra como criar um formulário da web para recuperar dados de usuário para fazer uma chamada. Para fins deste exemplo, um novo projeto de dinâmico da web, chamado **TwilioCloud**, foi criado e **callform.jsp** foi adicionado como um arquivo JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Criar o código para fazer a chamada
O código a seguir, que é chamado quando o usuário conclui o formulário exibido por callform.jsp, cria a mensagem de chamada e gera a chamada. Para fins deste exemplo, o arquivo JSP é chamado **makecall.jsp** e foi adicionado ao projeto **TwilioCloud** . (Use sua conta de Twilio e a autenticação de token em vez de valores de espaço reservado atribuídos a **accountSID** e **authToken** no código a seguir).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Além de fazer a chamada, makecall.jsp exibe o ponto de extremidade de Twilio, versão da API e o status de chamada. Um exemplo é a captura de tela a seguir:

![Resposta de chamada Azure usando Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Executar o aplicativo
A seguir é as etapas de alto nível para executar o aplicativo; detalhes para estas etapas podem ser encontradas na [criação de um Hello World aplicativo usando o Kit de ferramentas do Azure para Eclipse][azure_java_eclipse_hello_world].

1. Exporte seu guerra TwilioCloud à pasta Azure **approot** . 
2. Modifique **startup.cmd** para descompactar seu guerra TwilioCloud.
3. Compile o aplicativo para o emulador de computação.
4. Inicie sua implantação no emulador computação.
5. Abra um navegador e executar **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Insira valores no formulário, clique **fazer esta chamada**e, em seguida, ver os resultados em makecall.jsp.

Quando estiver pronto para implantar Azure, recompilar para implantação na nuvem, implantar no Azure e executar http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp no navegador (substitua seu valor para *your_hosted_name*).

## <a name="next-steps"></a>Próximas etapas
Este código foi fornecido para mostrar a funcionalidade básica usando Twilio em Java no Azure. Antes de implantar no Azure em produção, você talvez queira adicionar mais de tratamento de erro ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, você poderia usar blobs de armazenamento do Azure ou banco de dados SQL para armazenar números de telefone e texto de chamadas. Para obter informações sobre como usar blobs de armazenamento do Azure em Java, veja [como usar o serviço de armazenamento de Blob do Java][howto_blob_storage_java]. Para obter informações sobre como usar o banco de dados SQL em Java, consulte [Usando de banco de dados SQL em Java][howto_sql_azure_java].
* Você pode usar **RoleEnvironment.getConfigurationSettings** para recuperar a ID da conta Twilio e token de autenticação de definições de configuração da sua implantação, em vez de embutir valores em makecall.jsp. Para obter informações sobre a classe de **RoleEnvironment** , consulte [usando a biblioteca de tempo de execução de serviço do Azure no JSP] [ azure_runtime_jsp] e a documentação do pacote de tempo de execução de serviço do Azure em [http://dl.windowsazure.com/javadoc][azure_javadoc].
* O código de makecall.jsp atribui uma URL fornecido Twilio, [http://twimlets.com/message][twimlet_message_url], para a variável de **Url** . Essa URL fornece uma resposta de linguagem de marcação de Twilio (TwiML) informando Twilio para prosseguir com a chamada. Por exemplo, o TwiML retornado pode conter um ** &lt;diga&gt; ** verbo que resulta em texto dito para o destinatário da chamada. Em vez de usar a URL fornecido Twilio, você pode criar seu próprio serviço para responder a solicitação do Twilio; Para obter mais informações, consulte [como Twilio de uso para voz e os recursos do SMS em Java][howto_twilio_voice_sms_java]. Mais informações sobre TwiML podem ser encontradas no [http://www.twilio.com/docs/api/twiml][twiml]e mais informações sobre ** &lt;diga&gt; ** e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança de Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Consulte também
* [Como usar o Twilio para voz e os recursos do SMS em Java][howto_twilio_voice_sms_java]
* [Adicionar um certificado ao armazenamento de certificados de autoridade de certificação Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
