<properties
    pageTitle="Como usar o Twilio para voz e SMS (PHP) | Microsoft Azure"
    description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos em PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Como usar o Twilio para voz e os recursos do SMS no PHP
Este guia demonstra como executar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abordados incluem fazer um telefonema e enviar uma mensagem de serviço de mensagem curta (SMS). Para obter mais informações sobre Twilio e usar voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é alimentar o futuro da comunicação de negócios, permitindo que os desenvolvedores incorporar voz, VoIP e mensagens em aplicativos. Eles virtualização de todos os infraestrutura necessária em um ambiente baseado em nuvem, global, expor-la por meio da plataforma de comunicações API Twilio. Aplicativos são simples para construir e escaláveis. Aproveite flexibilidade com pagamento medida que vá preços e beneficiar confiabilidade de nuvem.

**Voz Twilio** permite que os seus aplicativos para fazer e receber chamadas telefônicas. **Twilio SMS** permite que o seu aplicativo para enviar e receber mensagens de texto. **Cliente Twilio** permite que você faça chamadas de VoIP de qualquer telefone, tablet ou navegador e suporta WebRTC.

## <a id="Pricing"></a>Twilio preços e ofertas especiais

Clientes Azure receberão uma [oferta especial] $10 de crédito Twilio ao atualizar sua conta de Twilio. Esse crédito Twilio podem ser aplicado a qualquer uso de Twilio (crédito de US $10 equivalente a enviar até 1.000 mensagens SMS ou receber até 1000 minutos entrados de voz, dependendo da localização do seu destino de número e a mensagem ou a chamada do telefone). Recuperar esse crédito Twilio e introdução ao: [ahoy.twilio.com/azure].

Twilio é um serviço flexível. Há sem taxas configurados e você pode fechar sua conta a qualquer momento. Você pode encontrar mais detalhes em [Twilio preços] [twilio_pricing].

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece voz e funcionalidade do SMS para aplicativos. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Bibliotecas de API do Twilio] [twilio_libraries].

Principais aspectos da API Twilio são Twilio verbos e linguagem de marcação de Twilio (TwiML).

### <a id="Verbs"></a>Verbos Twilio
A API utiliza Twilio verbos; Por exemplo, o ** &lt;diga&gt; ** verbo instrui Twilio audível entregar uma mensagem em uma chamada.

A seguir está uma lista de verbos Twilio. Saiba mais sobre os outros verbos e recursos via [documentação de linguagem de marcação de Twilio] [http://www.twilio.com/docs/api/twiml].

* ** &lt;Discagem&gt;**: conecta o chamador para outro telefone.
* ** &lt;Reunir&gt;**: coleta dígitos numéricos inseridos no teclado do telefone.
* ** &lt;Desligamento&gt;**: termina uma chamada.
* ** &lt;Reproduzir&gt;**: reproduz um arquivo de áudio.
* ** &lt;Pausa&gt;**: aguarda silenciosamente um número especificado de segundos.
* ** &lt;Registro&gt;**: registra a voz do chamador e retorna uma URL de um arquivo que contém a gravação.
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

Quando você se inscrever para uma conta de Twilio, você recebe uma ID de conta e um token de autenticação. Ambos serão necessárias para fazer chamadas de API de Twilio. Para impedir o acesso não autorizado à sua conta, proteger o token de autenticação. Sua ID de conta e autenticação de token ficam visíveis na [página da conta Twilio] [twilio_account], no campos rotuladas **SID da conta** e **AUTH TOKEN**, respectivamente.

## <a id="create_app"></a>Criar um aplicativo PHP
Um aplicativo PHP que usa o serviço de Twilio e está em execução no Azure não será diferente de qualquer outro aplicativo PHP que usa o serviço de Twilio. Enquanto os serviços de Twilio são baseados em REST e podem ser chamados de PHP de várias maneiras, este artigo se concentrará sobre como usar os serviços de Twilio com [Twilio biblioteca para PHP do GitHub][twilio_php]. Para obter mais informações sobre como usar a biblioteca de Twilio para PHP, consulte [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Instruções detalhadas para criar e implantar um aplicativo de Twilio/PHP no Azure estão disponíveis em [como fazer uma chamada telefônica usando Twilio em um aplicativo PHP no Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurar seu aplicativo para usar bibliotecas de Twilio
Você pode configurar seu aplicativo para usar a biblioteca de Twilio para PHP de duas maneiras:

1. Baixar a biblioteca de Twilio para PHP do GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e adicionar o diretório de **Serviços** ao seu aplicativo.

    -OU-

2. Instale a biblioteca de Twilio para PHP como um pacote de PEREIRAS. Ele pode ser instalado com os seguintes comandos:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Depois que você instalou a biblioteca de Twilio para PHP, em seguida, você pode adicionar uma declaração de **require_once** na parte superior dos seus arquivos PHP para fazer referência a biblioteca:

        require_once 'Services/Twilio.php';

Para obter mais informações, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Como: fazer uma chamada de saída
A seguir mostra como fazer uma chamada de saída usando a classe **Services_Twilio** . Este código também usa um site fornecidos Twilio para retornar a resposta de linguagem de marcação de Twilio (TwiML). Substitua os valores **de** e **para** números de telefone e verifique o número de telefone **de** para a sua conta de Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Conforme mencionado, este código usa um site fornecidos Twilio para retornar a resposta de TwiML. Você pode usar seu próprio site para fornecer a resposta de TwiML; Para obter mais informações, consulte [como fornecer respostas TwiML do seu próprio Site](#howto_provide_twiml_responses).


- **Observação**: para solucionar erros de validação de certificado SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Como: enviar uma mensagem SMS
A seguir mostra como enviar uma mensagem SMS usando a classe **Services_Twilio** . O número **de** é fornecido pelo Twilio para contas de avaliação para enviar mensagens SMS. O número **para** deve ser verificado para sua conta de Twilio antes de executar o código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Como: fornecer respostas de TwiML de seu próprio site
Quando seu aplicativo inicia uma chamada para a API Twilio, Twilio envia a solicitação para uma URL que deve retornar uma resposta TwiML. O exemplo acima usa o URL fornecido Twilio [http://twimlets.com/message][twimlet_message_url]. (Enquanto TwiML destina-se ao uso por Twilio, você pode exibir o it no seu navegador. Por exemplo, clique em [http://twimlets.com/message] [ twimlet_message_url] para ver um vazio `<Response>` elemento; como outro exemplo, clique em [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] para ver uma `<Response>` elemento que contém uma `<Say>` elemento.)

Em vez de depender a URL fornecido Twilio, você pode criar seu próprio site que retorna respostas HTTP. Você pode criar o site em qualquer idioma que retorna respostas XML; Este tópico pressupõe que você usará os PHP para criar o TwiML.

A seguinte página PHP resulta em uma resposta de TwiML que diz **Olá, mundo** na chamada.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Como você pode ver no exemplo acima, a resposta de TwiML é simplesmente um documento XML. A biblioteca de Twilio para PHP contém classes que irão gerar TwiML para você. O exemplo a seguir gera a resposta equivalente conforme mostrado acima, mas usa o **serviços\_Twilio\_Twiml** classe na biblioteca Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para saber mais sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference].

Quando tiver sua página PHP configurada para fornecer respostas TwiML, use o URL da página PHP conforme a URL passado para o `Services_Twilio->account->calls->create` método. Por exemplo, se você tiver um aplicativo Web chamado **MyTwiML** implantado em um Azure hospedado no serviço e o nome da página PHP é **mytwiml.php**, a URL pode ser passada para **Services_Twilio -> conta -> chamadas -> criar** conforme mostrado no exemplo a seguir:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Para obter informações adicionais sobre como usar Twilio no Azure com PHP, veja [como fazer uma chamada telefônica usando Twilio em um aplicativo PHP no Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Como: usar serviços de Twilio adicionais
Além de exemplos mostrados aqui, Twilio oferece APIs baseado na web que você pode usar para aumentar a funcionalidade de Twilio adicional de seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Próximas etapas
Agora que você aprendeu os fundamentos do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de segurança de Twilio] [twilio_security_guidelines]
* [Guias de Twilio HowTo e códigos de exemplo] [twilio_howtos]
* [Tutoriais Twilio][twilio_quickstarts]
* [Twilio no GitHub] [twilio_on_github]
* [Fale com o suporte de Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

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
