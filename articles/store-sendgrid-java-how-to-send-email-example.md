<properties 
    pageTitle="Store-sendgrid-Java-How-to-Send-email-example" 
    description="Como enviar email usando SendGrid de Java em uma implantação do Azure" 
    services="" 
    documentationCenter="java" 
    authors="thinkingserious" 
    manager="sendgrid" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Como Enviar Email usando SendGrid de Java em uma implantação do Azure

O exemplo a seguir mostra como você pode usar SendGrid para enviar emails de uma página da web hospedado no Azure. O aplicativo resultante solicitará que o usuário para valores de email, conforme mostrado na captura de tela a seguir.

![Formulário de email][emailform]

O email resultante terá aparência semelhante à seguinte imagem capturada.

![Mensagem de email][emailsent]

Você precisará fazer o seguinte para usar o código neste tópico:

1. Obtenha os JARs javax.mail, por exemplo, de <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Adicione os JARs ao seu caminho de compilação de Java.
3. Se você estiver usando o Eclipse para criar este aplicativo Java, você pode incluir as bibliotecas de SendGrid em seu arquivo de implantação de aplicativo (guerra) usando o recurso de conjunto de implantação do Eclipse. Se você não estiver usando o Eclipse para criar este aplicativo Java, verifique as bibliotecas são incluídas dentro da mesma função Azure como seu aplicativo Java e adicionadas ao caminho de classe do seu aplicativo.


Você também deve ter seu próprio SendGrid nome de usuário e senha, para poder enviar o email. Para começar a usar SendGrid, veja [como enviar email usando SendGrid de Java](store-sendgrid-java-how-to-send-email.md).

Além disso, familiaridade com as informações na [criação de um aplicativo Hello World Azure no Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944)ou com outras técnicas para hospedar aplicativos de Java no Azure se você não estiver usando o Eclipse, é altamente recomendável.

## <a name="create-a-web-form-for-sending-email"></a>Criar um formulário da web para enviar email

O código a seguir mostra como criar um formulário da web para recuperar dados de usuário para o envio de emails. Para fins deste conteúdo, o arquivo JSP é chamado **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>Criar o código para enviar o email

O código a seguir, que é chamado quando você concluir o formulário em emailform.jsp, cria a mensagem de email e o envia. Para fins deste conteúdo, o arquivo JSP é chamado **sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
    
         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);
    
         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Além de enviar o email, emailform.jsp fornece um resultado para o usuário; um exemplo é a captura de tela a seguir:

![Enviar resultado de email][emailresult]

## <a name="next-steps"></a>Próximas etapas

Implantar seu aplicativo para o emulador de computação e dentro de um navegador executar emailform.jsp, insira valores no formulário, clique em **Enviar e-mail**e ver os resultados em sendemail.jsp.

Este código foi fornecido para mostrar a você como usar SendGrid em Java no Azure. Antes de implantar no Azure em produção, você talvez queira adicionar mais de tratamento de erro ou outros recursos. Por exemplo: 

* Você pode usar o banco de dados do SQL ou blobs de armazenamento do Azure para armazenar os endereços de email e mensagens de email, em vez de usar um formulário da web. Para obter informações sobre como usar blobs de armazenamento do Azure em Java, consulte [como usar o serviço de armazenamento de Blob do Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Para obter informações sobre como usar o banco de dados SQL em Java, consulte [Usando de banco de dados SQL em Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Você poderia usar `RoleEnvironment.getConfigurationSettings` para recuperar o nome de usuário SendGrid e a senha de definições de configuração da sua implantação, em vez de usar o formulário da web para recuperar esses valores. Para obter informações sobre o `RoleEnvironment` classe, consulte [usando a biblioteca de tempo de execução de serviço do Azure no JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) e a documentação do pacote de tempo de execução do Azure serviço em <http://dl.windowsazure.com/javadoc>.
* Para obter mais informações sobre como usar o SendGrid em Java, veja [como enviar email usando SendGrid de Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
