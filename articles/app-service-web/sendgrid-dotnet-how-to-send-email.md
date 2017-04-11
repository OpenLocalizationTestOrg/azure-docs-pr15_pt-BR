<properties 
    pageTitle="Como usar o serviço de email de SendGrid (.NET) | Microsoft Azure" 
    description="Saiba como enviar emails com o serviço de email SendGrid no Azure. Amostras escritas em c# de código e usar a API .NET." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Como Enviar Email usando o SendGrid com o Azure


## <a name="overview"></a>Visão geral

Este guia demonstra como executar tarefas comuns de programação com o serviço de email SendGrid no Azure. Os exemplos são escritos em C\#
e usar a API .NET. Os cenários cobertos incluem **Construindo email**, **enviando email**, **Adicionar anexos**e **usando filtros**. Para obter mais informações sobre SendGrid e enviar email, consulte a seção [próximas etapas][] .

## <a name="what-is-the-sendgrid-email-service"></a>O que é o serviço de email SendGrid?

SendGrid é um [serviço de email baseado em nuvem] que fornece confiável [entrega de email transações], escalabilidade e análise em tempo real juntamente com APIs flexível que facilitam a integração personalizada. Cenários comuns de uso de SendGrid incluem:

-   Enviar automaticamente confirmações aos clientes.
-   Administrar distribuição listas para o envio de clientes e mensais-folhetos e ofertas especiais.
-   Coletando métricas em tempo real para coisas como email bloqueado e capacidade de resposta do cliente.
-   Geração de relatórios para ajudar a identificar tendências.
-   Encaminhando consultas de cliente.
-   Processar emails de entrada.

Para obter mais informações, consulte [https://sendgrid.com](https://sendgrid.com) ou nossa [biblioteca c#][sendgrid csharp]

## <a name="create-a-sendgrid-account"></a>Criar uma conta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referência a biblioteca de classes do .NET SendGrid

O [pacote do SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) é a maneira mais fácil para obter a API SendGrid e configurar o aplicativo com todas as dependências. NuGet é uma extensão do Visual Studio incluída no Microsoft Visual Studio 2015 que torna mais fácil instalar e atualizar bibliotecas e ferramentas. 

> [AZURE.NOTE] Para instalar o NuGet se você estiver executando uma versão do Visual Studio anterior ao Visual Studio 2015, visite [http://www.nuget.org](http://www.nuget.org)e clique no botão **Instalar NuGet** .

Para instalar o pacote SendGrid NuGet em seu aplicativo, faça o seguinte:

1.  Criou um novo projeto.

    ![Criar um novo projeto][create-new-project]

2.  Selecione um modelo.

    ![Selecione um modelo][select-a-template]

3.  No **Solution Explorer**, clique com botão direito **referências**, clique em **Gerenciar pacotes do NuGet**.

4.  Pesquise **SendGrid** e selecione o item **SendGrid** na lista de resultados.

    ![Pacote SendGrid NuGet][SendGrid-NuGet-package]

5.  Clique em **instalar** para concluir a instalação e, em seguida, feche esta caixa de diálogo.

Biblioteca de classes .NET do SendGrid é chamada **SendGridMail**. Ele contém os namespaces a seguir:

-   **SendGridMail** para criar e trabalhar com itens de email.
-   **SendGridMail.Transport** para enviar email usando o protocolo **SMTP** ou o protocolo HTTP 1.1 com **Web/restante**.

Adicionar as seguintes declarações de namespace de código na parte superior de qualquer C\# arquivo no qual você deseja acessar o serviço de email SendGrid programaticamente.
**System.Net** e **System.Net.Mail** são namespaces do .NET Framework que estão incluídos porque eles incluem tipos que você normalmente usará com as APIs SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Como: criar um email

Use o objeto **SendGridMessage** para criar uma mensagem de email. Depois de criado o objeto de mensagem, você pode definir propriedades e métodos, incluindo o remetente de email, o destinatário do email e o assunto e o corpo do email.

O exemplo a seguir demonstra como criar um objeto de email totalmente preenchida:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Para saber mais sobre todas as propriedades e métodos suportados pelo tipo **SendGrid** , consulte [sendgrid-csharp][] no GitHub.

## <a name="how-to-send-an-email"></a>Como: enviar um email

Depois de criar uma mensagem de email, você poderá enviá-lo usando a API Web fornecidos pelo SendGrid. Como alternativa, você pode usar [. Líquido integrado biblioteca](https://sendgrid.com/docs/Code_Examples/csharp.html).

Enviando email requer que você forneça suas credenciais de conta de SendGrid (nome de usuário e senha) ou a chave de API do SendGrid. Chave API é o método preferencial. Se você precisar ver detalhes sobre como configurar as teclas de API, visite nossa [documentação](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Você pode armazenar essas credenciais por meio de seu Portal do Azure clicando em configurar e adicionar os pares de chave/valor em "configurações de aplicativo".

 ![Configurações de aplicativo do Azure][azure_app_settings]

 Em seguida, você pode acessá-los da seguinte maneira: 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Usando credenciais:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Usando a chave API:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Os exemplos a seguir mostram como enviar uma mensagem usando a API da Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Como: adicionar um anexo

Anexos podem ser adicionados a uma mensagem chamando o método **AddAttachment** e especificando o nome e o caminho do arquivo que deseja anexar.
Você pode incluir vários anexos chamando este método uma vez para cada arquivo que deseja anexar. O exemplo a seguir demonstra a adição de um anexo a uma mensagem:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Você também pode adicionar anexos a partir dos dados **fluxo**. Isso pode ser feito ligando para o mesmo método acima, **AddAttachment**, mas passando o fluxo de dados e o nome do arquivo que você quer exibir como a mensagem. Nesse caso, você precisará adicionar a biblioteca de System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Como: usar os aplicativos para habilitar a análise, acompanhamento e rodapés

SendGrid fornece a funcionalidade de email adicional por meio do uso aplicativos. Estas são as configurações que podem ser adicionadas a uma mensagem de email para habilitar a funcionalidade específica como clique acompanhamento, do Google analytics, assinatura de acompanhamento, e assim por diante. Para obter uma lista completa de aplicativos, consulte [Configurações do aplicativo][].

Aplicativos podem ser aplicados a mensagens de email de **SendGrid** usando métodos implementados como parte da classe **SendGrid** .

Os exemplos a seguir demonstram o rodapé e clique em filtros de controle:

### <a name="footer"></a>Rodapé

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Clique em acompanhamento

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Como: usar serviços de SendGrid adicionais

SendGrid oferece APIs e webhooks que você pode usar para aumentar a funcionalidade de SendGrid adicional de seu aplicativo do Azure baseado na web. Para obter detalhes completos, consulte a [documentação da API SendGrid][].

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas para o serviço de Email de SendGrid, siga estes links para saber mais.

*   SendGrid C\# repo de biblioteca: [sendgrid csharp][]
*   Documentação da API SendGrid: <https://sendgrid.com/docs>
*   SendGrid oferta especial para clientes do Azure: [https://sendgrid.com](https://sendgrid.com)

  [Próximas etapas]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Configurações do aplicativo]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentação da API SendGrid]: https://sendgrid.com/docs
  
  [serviço de email baseado em nuvem]: https://sendgrid.com/email-solutions
  [entrega de email transações]: https://sendgrid.com/transactional-email
 
