<properties 
    pageTitle="Como usar o Twilio para voz e SMS (Ruby) | Microsoft Azure" 
    description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos em Ruby." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Como usar o Twilio para voz e os recursos do SMS em Ruby
Este guia demonstra como executar tarefas comuns de programação com o serviço de Twilio API no Azure. Os cenários abordados incluem fazer um telefonema e enviar uma mensagem de serviço de mensagem curta (SMS). Para obter mais informações sobre Twilio e usar voz e SMS em seus aplicativos, consulte a seção [Próximas etapas](#NextSteps) .

## <a id="WhatIs"></a>O que é Twilio?
Twilio é uma API de serviço web de telefonia que permite usar seus idiomas de web existente e habilidades para criar aplicativos de SMS e voz. Twilio é um serviço de terceiros (não um recurso do Azure e não um produto Microsoft).

**Voz Twilio** permite que os seus aplicativos para fazer e receber chamadas telefônicas. **Twilio SMS** permite seus aplicativos para fazer e receber mensagens SMS. **Cliente Twilio** permite que os seus aplicativos permitir a comunicação de voz usando conexões existentes de Internet, incluindo as conexões móveis.

## <a id="Pricing"></a>Twilio preços e ofertas especiais
Informações sobre preços Twilio estão disponíveis em [Twilio preços] [twilio_pricing]. Azure clientes receberão uma [oferta especial][special_offer]: um crédito gratuito de 1000 textos ou 1000 minutos de entrada. Para inscrever-se para esta oferta ou obter mais informações, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceitos
A API Twilio é uma API RESTful que fornece voz e funcionalidade do SMS para aplicativos. Bibliotecas de cliente estão disponíveis em vários idiomas; Para obter uma lista, consulte [Bibliotecas de API do Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML é um conjunto de instruções baseado em XML que informam Twilio de como uma chamada de processo ou SMS.

Como exemplo, a seguinte TwiML seria converter o texto **Olá, mundo** em fala.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos os documentos de TwiML tem `<Response>` como seu elemento raiz. A partir daí, você pode usar verbos Twilio para definir o comportamento do seu aplicativo.

### <a id="Verbs"></a>Verbos TwiML
Verbos de Twilio são marcas XML que informam Twilio o que **fazer**. Por exemplo, o ** &lt;diga&gt; ** verbo instrui Twilio audível entregar uma mensagem em uma chamada. 

A seguir está uma lista de verbos Twilio.

* ** &lt;Discagem&gt;**: conecta o chamador para outro telefone.
* ** &lt;Reunir&gt;**: coleta dígitos numéricos inseridos no teclado do telefone.
* ** &lt;Desligamento&gt;**: termina uma chamada.
* ** &lt;Reproduzir&gt;**: reproduz um arquivo de áudio.
* ** &lt;Pausar&gt;**: aguarda silenciosamente um número especificado de segundos.
* ** &lt;Registro&gt;**: registra voz do chamador e retorna uma URL de um arquivo que contém a gravação.
* ** &lt;Redirecionar&gt;**: transfere o controle de uma chamada ou SMS para o TwiML em uma URL diferente.
* ** &lt;Rejeitar&gt;**: rejeita uma chamada de entrada para o seu número de Twilio sem cobrança você
* ** &lt;Diga&gt;**: converte texto em fala que é feito em uma chamada.
* ** &lt;Sms&gt;**: envia uma mensagem SMS.

Para obter mais informações sobre verbos Twilio, seus atributos e TwiML, consulte [TwiML] [twiml]. Para obter informações adicionais sobre a API Twilio, consulte [API de Twilio] [twilio_api].

## <a id="CreateAccount"></a>Criar uma conta de Twilio
Quando você estiver pronto para obter uma conta de Twilio, inscreva-se ao [Tentar Twilio] [try_twilio]. Você pode começar com uma conta gratuita e atualizar sua conta mais tarde.

Quando você se inscrever para uma conta de Twilio, você receberá um número de telefone gratuito para seu aplicativo. Você também receberá uma conta SID e um token de autenticação. Ambos serão necessárias para fazer chamadas de API de Twilio. Para impedir o acesso não autorizado à sua conta, proteger o token de autenticação. Sua conta SID e token de auth ficam visíveis na [página da conta Twilio][twilio_account], no campos rotuladas **SID da conta** e **AUTH TOKEN**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Verifique se os números de telefone
Além do número é fornecidos por Twilio, também é possível verificar os números que você controle (ou seja, seu telefone celular ou residencial número de telefone) para uso em seus aplicativos. 

Para obter informações sobre como verificar se um número de telefone, consulte [Gerenciar números] [verify_phone].

## <a id="create_app"></a>Criar um aplicativo Ruby
Um aplicativo Ruby que usa o serviço de Twilio e está em execução no Azure não será diferente de qualquer outro aplicativo Ruby que usa o serviço de Twilio. Enquanto os serviços de Twilio são RESTful e podem ser chamados de Ruby de várias maneiras, este artigo se concentrará sobre como usar os serviços de Twilio com [a biblioteca de auxiliar Twilio para Ruby][twilio_ruby].

Primeiro, [Configurar uma nova VM Linux Azure] [ azure_vm_setup] para agir como um host para seu novo aplicativo web Ruby. Ignore as etapas que envolvem a criação de um aplicativo Rails, configurados apenas a máquina virtual. Verifique se que você criar um ponto de extremidade com uma porta externa de 80 e uma porta interna de 5000.

Nos exemplos a seguir, vamos usar [Sinatra][sinatra], uma estrutura de web muito simples para Ruby. Mas você pode usar a biblioteca de auxiliar Twilio para Ruby com qualquer outro framework de web, incluindo Ruby on Rails.

SSH em sua máquina virtual novo e crie um diretório para seu novo aplicativo. Dentro do diretório, crie um arquivo chamado Gemfile e copie o seguinte código para ela:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Na linha de comando executar `bundle install`. Isto irá instalar as dependências acima. Em seguida, crie um arquivo denominado `web.rb`. Este será onde reside o código para seu aplicativo web. Cole o seguinte código ele:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Neste ponto você deve poder executar o comando `ruby web.rb -p 5000`. Isto irá rotação-up um servidor web pequeno na porta 5000. Você deve ser capaz de navegar para este aplicativo em seu navegador visitando a URL que você configurados para sua máquina virtual do Azure. Depois que você pode acessar o aplicativo da web no navegador, você está pronto para começar a criar um aplicativo de Twilio.

## <a id="configure_app"></a>Configurar seu aplicativo para usar Twilio
Você pode configurar o seu aplicativo web para usar a biblioteca de Twilio atualizando seus `Gemfile` para incluir esta linha:

    gem 'twilio-ruby'

Na linha de comando, execute `bundle install`. Agora, abra `web.rb` e esta linha na parte superior, incluindo:

    require 'twilio-ruby'

Você está agora tudo pronto para usar a biblioteca de auxiliar Twilio para Ruby em seu aplicativo web.

## <a id="howto_make_call"></a>Como: fazer uma chamada de saída
A seguir mostra como fazer uma chamada de saída. Conceitos-chave incluem usando a biblioteca de auxiliar de Twilio para Ruby para fazer chamadas de API REST e renderização TwiML. Substitua os valores **de** e **para** números de telefone e verifique o número de telefone **de** para a sua conta de Twilio antes de executar o código.

Adicione esta função para `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Se você abrir backup `http://yourdomain.cloudapp.net/make_call` em um navegador, que acionará a chamada para a API Twilio para fazer a chamada telefônica. Os dois primeiros parâmetros no `client.account.calls.create` são bastante claros: é o número de chamada `from` e é o número de chamada `to`. 

O terceiro parâmetro (`url`) é a URL que as solicitações de Twilio para obter instruções sobre o que fazer depois de conectar a chamada. Nesse caso nós configurados um URL (`http://yourdomain.cloudapp.net`) que retorna um documento TwiML simples e usa o `<Say>` verbo fazer algum texto em fala e diga "Hello macaco" para a pessoa que está recebendo a chamada.

## <a id="howto_recieve_sms"></a>Como: receber uma mensagem SMS
No exemplo anterior, podemos iniciou um telefonema de **saída** . Esta vez, vamos usar o número de telefone que Twilio nos deu durante a inscrição para processar uma mensagem SMS de **entrada** .

Primeiro, faça login no seu [Painel de Twilio][twilio_account]. Clique em "Números" no painel de navegação superior e clique no número Twilio que foram fornecidas. Você verá dois URLs que podem ser configuradas. URL de solicitação de uma URL de solicitação de voz e um SMS. Estas são as URLs que Twilio chama sempre que uma chamada telefônica é feita ou um SMS é enviado para o seu número. As URLs são também conhecidas como "web de fixação".

Podemos gostaria de processar mensagens de SMS de entrada, portanto, vamos atualizar a URL para `http://yourdomain.cloudapp.net/sms_url`. Prossiga e clique em Salvar alterações na parte inferior da página. Agora, o novamente `web.rb` vamos programar nosso aplicativo para lidar com isso:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Depois de fazer a alteração, certifique-se iniciar novamente o seu aplicativo web. Agora, retirar seu telefone e enviar um SMS para o seu número de Twilio. Você deve notificar obter uma resposta SMS que diz "Ei, agradecemos ping! Twilio e Azure Rocha! ".

## <a id="additional_services"></a>Como: usar serviços de Twilio adicionais
Além de exemplos mostrados aqui, Twilio oferece APIs baseado na web que você pode usar para aumentar a funcionalidade de Twilio adicional de seu aplicativo do Azure. Para obter detalhes completos, consulte a [documentação da API Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Próximas etapas
Agora que você aprendeu as Noções básicas do serviço Twilio, siga estes links para saber mais:

* [Diretrizes de segurança de Twilio] [twilio_security_guidelines]
* [Twilio HowTos e códigos de exemplo] [twilio_howtos]
* [Tutoriais Twilio][twilio_quickstarts] 
* [Twilio no GitHub] [twilio_on_github]
* [Fale com o suporte de Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
