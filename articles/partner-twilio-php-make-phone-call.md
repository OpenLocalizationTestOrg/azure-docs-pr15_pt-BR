<properties
    pageTitle="Como fazer uma chamada telefônica de Twilio (PHP) | Microsoft Azure"
    description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Amostras são para aplicativo PHP."
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como fazer uma chamada telefônica usando Twilio em um aplicativo de PHP no Azure

O exemplo a seguir mostra como você pode usar Twilio para fazer uma chamada de uma página da web PHP hospedado no Azure. O aplicativo resultante solicitará que o usuário para valores de telefonema, conforme mostrado na captura de tela a seguir.

![Formulário de chamada Azure usando Twilio e PHP][twilio_php]

Você precisará fazer o seguinte para usar o código neste tópico:

1. Adquirir uma conta de Twilio e a autenticação de token. Para começar a usar Twilio, avaliar preço no [http://www.twilio.com/pricing][twilio_pricing]. Você pode se inscrever para uma conta de avaliação em [https://www.twilio.com/try-twilio][try_twilio]. Para obter informações sobre a API fornecida pelo Twilio, consulte [http://www.twilio.com/api][twilio_api].
2. Obtenha a [biblioteca de Twilio para PHP](https://github.com/twilio/twilio-php) ou instalá-lo como um pacote de PEREIRAS. Para obter mais informações, consulte o [arquivo Leiame](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o SDK do Azure para PHP. Para obter uma visão geral das SDK e instruções sobre instalá-lo, consulte [Configurar o SDK do Azure para PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da web para fazer uma chamada

O seguinte código HTML mostra como criar uma página da web (**callform.html**) que recupera dados de usuário para fazer uma chamada:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Criar o código para fazer a chamada
O código a seguir mostra como criar uma página da web (**makecall.php**) que é chamado quando o usuário envia o formulário exibido por **callform.html**. O código mostrado abaixo cria a mensagem de chamada e gera a chamada. (Use sua conta de Twilio e a autenticação de token em vez de valores de espaço reservado atribuídos a **$sid** e **$token** no código a seguir).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Além de fazer a chamada, **makecall.php** exibe alguns metadados de chamada (exemplo mostrado na captura de tela abaixo). Para obter mais informações sobre metadados de chamada, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Resposta de chamada Azure usando Twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar o aplicativo
A próxima etapa é implantar seu aplicativo para sites do Azure. Os artigos a seguir contêm as informações para criar um site e implantar seu código com gito, FTP ou WebMatrix (embora nem todas as informações em cada artigo sejam relevantes):

* [Criar um Site do Azure PHP MySQL e implantar usando gito][website-git]
* [Criar um Site da Web Azure PHP MySQL e implantar usando FTP][website-ftp]

## <a name="next-steps"></a>Próximas etapas
Este código foi fornecido para mostrar a funcionalidade básica usando Twilio no PHP no Azure. Antes de implantar no Azure em produção, você talvez queira adicionar mais de tratamento de erro ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, você poderia usar blobs de armazenamento do Azure ou banco de dados SQL para armazenar números de telefone e texto de chamadas. Para obter informações sobre como usar blobs de armazenamento do Azure no PHP, consulte [Usando o armazenamento do Azure com aplicativos PHP][howto_blob_storage_php]. Para obter informações sobre como usar o banco de dados SQL no PHP, consulte [Usando o banco de dados de SQL com aplicativos PHP][howto_sql_azure_php].
* O código de **makecall.php** usa URL fornecido Twilio ([http://twimlets.com/message][twimlet_message_url]) para fornecer uma resposta de linguagem de marcação de Twilio (TwiML) informando Twilio para prosseguir com a chamada. Por exemplo, o TwiML retornado pode conter um `<Say>` verbo que resulta em texto dito para o destinatário da chamada. Em vez de usar a URL fornecido Twilio, você pode criar seu próprio serviço para responder a solicitação do Twilio; Para obter mais informações, consulte [como Twilio de uso para voz e os recursos do SMS no PHP][howto_twilio_voice_sms_php]. Mais informações sobre TwiML podem ser encontradas no [http://www.twilio.com/docs/api/twiml][twiml]e mais informações sobre `<Say>` e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança de Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Consulte também
* [Como usar o Twilio para voz e os recursos do SMS no PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
