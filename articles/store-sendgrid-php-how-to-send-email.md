<properties 
    pageTitle="Como usar o serviço de email de SendGrid (PHP) | Microsoft Azure" 
    description="Saiba como enviar emails com o serviço de email SendGrid no Azure. Exemplos de código escritos em PHP." 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Como usar o serviço de Email de SendGrid do PHP

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Azure. Os exemplos são gravados no PHP.
Os cenários cobertos incluem **Construindo email**, **enviando email**e **Adicionar anexos**. Para obter mais informações sobre SendGrid e enviar email, consulte a seção [Próximas etapas](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de Email SendGrid?

SendGrid é um [serviço de email baseado em nuvem] que fornece confiável [entrega de email transações], escalabilidade e análise em tempo real juntamente com APIs flexível que facilitam a integração personalizada. Cenários comuns de uso de SendGrid incluem:

-   Enviar automaticamente as confirmações aos clientes
-   Administrar distribuição listas para o envio de clientes e mensais-folhetos e ofertas especiais
-   Coletar métricas em tempo real para coisas como e-mail bloqueado e capacidade de resposta do cliente
-   Geração de relatórios para ajudar a identificar tendências
-   Encaminhamento de consultas dos clientes
- Notificações de email de seu aplicativo

Para obter mais informações, consulte [https://sendgrid.com][].

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Usando o SendGrid de seu aplicativo PHP

Usando SendGrid em um aplicativo do Azure PHP requer sem especial configuração ou codificação. Como SendGrid é um serviço, ele pode ser acessado exatamente da mesma forma de um aplicativo em nuvem como ele pode de um aplicativo local.

## <a name="how-to-send-an-email"></a>Como: enviar um Email

Você pode enviar emails usando SMTP ou a API Web fornecidos pelo SendGrid.

### <a name="smtp-api"></a>API DE SMTP

Para enviar email usando a API de SMTP SendGrid, use *Mailer rápido*, uma biblioteca baseada em componente de envio de emails de aplicativos PHP. Você pode baixar a biblioteca de *Mailer rápido* do [http://swiftmailer.org/download][] v5.3.0 (use [compositor] instalar Mailer rápido). Enviar email com a biblioteca envolve a criação de instâncias do <span class="auto-style2">rápido\_SmtpTransport</span>, <span class="auto-style2">rápido\_Mailer</span>, e <span class="auto-style2">rápido\_mensagem</span> classes, definindo as propriedades apropriadas e estiver ligando a <span class="auto-style2">rápido\_Mailer::send</span> método.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>API da Web

Use [curl função do PHP][] para enviar email usando a API do Web SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

API da Web do SendGrid é muito semelhante a uma API REST, embora não é realmente uma API RESTful desde que, na maioria das chamadas, ambas obter e verbos de POSTAGEM podem ser usados alternadamente.

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo

### <a name="smtp-api"></a>API DE SMTP

Enviando um anexo usando a API de SMTP envolve uma linha adicional de código para o script de exemplo para enviar um email com Mailer rápido.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

A linha de código adicional é da seguinte maneira:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Esta linha de código chama o método de anexar a <span class="auto-style2">rápido\_mensagem</span> object e usa o método estático <span class="auto-style2">fromPath</span> na <span class="auto-style2">rápido\_anexo</span> classe obtenha e anexar um arquivo a uma mensagem.

### <a name="web-api"></a>API da Web

Enviando um anexo usando a API Web é muito semelhante ao enviar um email usando a API da Web. No entanto, observe que, no exemplo a seguir, a matriz de parâmetro deve conter esse elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Exemplo:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Como: usar filtros para habilitar a análise, acompanhamento e rodapés

SendGrid fornece a funcionalidade de email adicional por meio do uso 'filtros'. Estas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar a funcionalidade específica como a habilitação clique acompanhamento, do Google analytics, controle de assinatura e assim por diante.

Filtros podem ser aplicados a uma mensagem usando a propriedade de filtros. Cada filtro é especificado por um hash contendo configurações específicas de filtro. O exemplo a seguir permite que o filtro de rodapé e especifica uma mensagem de texto que será acrescentada à parte inferior da mensagem de email.
Neste exemplo, usaremos [php sendgrid biblioteca].
Use [compositor] para instalar a biblioteca:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Exemplo:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas para o serviço de Email de SendGrid, siga estes links para saber mais.

-   Documentação de SendGrid: <https://sendgrid.com/docs>
-   Biblioteca de SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
-   SendGrid oferta especial para clientes do Azure: <https://sendgrid.com/windowsazure.html>

Para obter mais informações, consulte também o [PHP Developer Center](/develop/php/).


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [função ondulação]: http://php.net/curl
  [serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
  [entrega de email transações]: https://sendgrid.com/transactional-email
  [biblioteca de sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Compositor]: https://getcomposer.org/download/
